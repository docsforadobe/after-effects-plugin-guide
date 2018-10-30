.. _ppro/bigger-differences:

Bigger Differences
################################################################################

As long as an effect only supports the basic ARGB_8u pixel format supported by After Effects, Premiere Pro will try to imitate the After Effects hosting behavior and hide various differences because of the different render pipeline architecture. But if an effect wants to support additional pixel formats, such as 32-bit RGB, be prepared to handle further divergent behavior.

----

Pixel Formats
================================================================================

Premiere Pro provides function suites for declaring support for pixel formats other than the 8-bit RGB format used by After Effects - ARGB_8u. These pixel formats include the Premiere Pro native 8-bit RGB format - BGRA_8u, as well as YUV, 32-bit formats, and more. For a more detailed discussion of the various pixel formats, see the `Premiere Pro SDK <http://www.adobe.com/devnet/premiere.html>`__ Guide, chapter 3, in the section "Pixel Formats and Colorspaces".

Use the PF Pixel Format Suite (defined in PrAESDKSupport.h) to register for `PF_EffectWorlds <#_bookmark233>`__ in other pixel formats. Use the Premiere Pixel Format Suite (defined in the aptly-named PrSDKPixelFormatSuite.h) to get black and white values in those pixel formats.

After Effects functions such as PF_BLEND() have not been enhanced to work with pixel formats beyond 8-bit RGB.

----

32-Bit Float Support
================================================================================

Premiere Pro does not support After Effects 16-bit rendering or SmartFX. For 32-bit rendering in Premiere Pro, you'll need to declare support for one of the 32-bit pixel formats (see previous section), and then implement 32-bit rendering for PF_Cmd_RENDER. You can support multiple render depths this way. See the SDK Noise sample project for an example.

Depending on the clip(s) to which an effect is applied, 32-bit processing is not always necessary to preserve the quality of the source input. But there are settings to force 32-bit rendering, to give effects processing finer granularity and more headroom, if desired. Go to Settings>Sequence Settings> Video Previews>Maximum Bit Depth, to control previewing from the timeline. For export to file, use Export Settings>Video>Basic Settings>Render at Maximum Depth.

----

PF_CHECKOUT_PARAM and Pixel Formats
================================================================================

Before CS6, PF_CHECKOUT_PARAM() only returned 8-bit ARGB buffers, regardless of the pixel format currently being used for rendering. Starting in CS6, an effect can opt in to get frames in the same format as the render request, whether it is 32-bit float, YUV, etc.

Plug-ins may request this behavior, but existing plug-ins will continue working receiving 8- bit ARGB frames. The call is EffectWantsCheckedOutFramesToMatch RenderPixelFormat(), in the PF Utility Suite, defined in PrSDKAESupport.h. The call should be made on PF_Cmd_GLOBAL_SETUP, the same selector where an effect would already advertise support beyond 8-bit RGB using AddSupportedPixelFormat().
