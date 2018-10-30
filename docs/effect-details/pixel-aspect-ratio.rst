.. _effect-details/pixel-aspect-ratio:

Pixel Aspect Ratio
################################################################################

Effects must respond correctly to footage with non-square pixels, and non-uniform downsampling factors. Even different layer parameters can have different pixel aspect ratios! Doing so isn't difficult once you understand the concepts involved.

Simple effects needn't do any work to match up :ref:`point parameters <effect-basics/parameters>` to the actual pixels in the output. Point parameters are given to the effect scaled for downsample factor and pixel aspect ratio; they are in the coordinate system of the input buffer. This provides an implicit "pixel coordinate system." This coordinate system is handy and easy to understand. But effects that use absolute pixel measurements or geometry must take a deeper look at the relationship between the input buffer and the final rendered image.

----

Don't Assume Pixels Are Square, Or 1-To-1
================================================================================

First, it is not necessarily a square coordinate system, due to both pixel aspect ratio and non- uniform downsample factor. The final rendered image can be stretched or squashed horizontally, relative to the pixels your effect processes. Circles will appear as ellipses, squares as rectangles. The distance between two points varies based on their angle in this coordinate system; anything rotated in this system is skewed, in the final output.

Second, even if it *is* a square coordinate system, it's not necessarily the same size as the final output. This means that any slider which defines a size in pixels will be a problem when the image is rendered downsampled; the width of anti-aliasing filters changes based on downsample factor.

Sometimes these issues aren't a problem. Any effect that colors pixels based solely on a linear function of the x and y coordinates need not bother with pixel aspect ratio and downsample factor at all. Staying in the input coordinate space is an option, though you must account for pixel aspect ratio and downsample factor elsewhere.

Suppose you're writing a particle system effect that sprays textured sprites from a source position defined by an effect control point. Using pixel coordinates to represent the particle positions seems fine (as long as the particles don't have to rotate around a point), but when you go to actually *render* the particle textures, you'll have to scale them by pixel aspect ratio and downsample factor.

If an effect already has coordinate transformation machinery in its pipeline, there's an alternative that's often simpler. Many algorithms require some sort of coordinate transformation; using matrices to set up a transformation, for example. But there are other easily adaptable algorithms, for example a texture generation effect that computes the value of each pixel based solely on its position. In this case, the code must take the raw pixel position and account for pixel aspect ratio and downsample factor.

----

Suggested Approach
================================================================================

The simplest way to get all of this right is to work entirely in full resolution square coordinates, then scale by downsample factor and pixel aspect ratio as a final output transformation. Since point parameters are always reported in input buffer coordinates, convert them to full-resolution square coordinates before use. With this approach you don't need to worry about sliders which define a size in pixels; just interpret them as defining size in full-resolution vertical pixels.

1) When getting your point parameters, go immediately to floating point and a full resolution square pixel system, like this.

::

  x *= in_data>pixel_aspect_ratio.num / (float)in_data>pixel_aspect_ratio.den;
  x *= in_data>downsample_x.den / (float)in_data>downsample_x.num;
  y *= in_data>downsample_y.den / (float)in_data>downsample_y.num;

2) Perform all setup (define transformation matrices, generate coordinates for later scan conversion, compute values based on the distance between points, rotating things, et cetera) in this coordinate space. Note that you're not actually dealing with pixels in this stage; you're just manipulating coordinates or coordinate transformations.

3) To go back to a coordinate system that corresponds directly to the pixels of the output buffer, undo the transformations from step one. Do this as late as possible, so as little code as possible needs to deal with this non-square space. If you're using matrices, this would be a final output transformation. For an effect which renders something based on the coordinate of each pixel, iterate over the output pixels and convert pixel coordinates to square coordinates before doing any processing for that pixel.

..

This may seem like extra work, but most reasonably complex effects like this have a coordinate transformation step anyway; and if they don't, they still need one to handle pixel aspect ratio and downsample factor correctly.

----

Applying User Input In Pixels
================================================================================

After Effects does all of its stretching horizontally so as to not to introduce unnecessary field interpolations; when pixels are used as a unit, we think of them as vertical pixels.

----

Test Test Test!
================================================================================

Test at 1/2, 1/4, and custom resolutions and compare the output. Use an anamorphic (2:1) pixel aspect ratio composition to track down bugs in pixel aspect ratio handling (it really makes them obvious), and be sure to test with different horizontal and vertical downsample factors.

Some developers have reported problems with the downsample factors provided by some "After Effects compatible" plug-in hosts being zero. Check for zero before dividing.
