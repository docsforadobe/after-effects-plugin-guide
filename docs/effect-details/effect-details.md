.. _effect-details/effect-details:

Effect Details
################################################################################

Now that we've covered the basics of effect plug-ins, we'll cover some of the finer points to polish off your effect. Not every section will be relevant to every plug-in, so feel free to use the PDF document bookmarks to skip to the sections pertinent to your current project.

----

Free Code == Good
================================================================================

After Effects provides effect plug-ins with as much information and supporting code as possible. Use our function suites and callbacks to obtain the value of parameters (including source footage) at different times. Use our memory allocation suite to avoid competing with the host for resources. Use our image processing suites to copy, fill, blend and convolve images, and convert between color spaces. Obtain information about the masks applied to a layer. ANSI emulation and math utility suites are also provided, as well as information about the application, user, serial number, and current drawing context.

Previous versions of After Effects have provided functions for many common tasks. As we moved to support deeper color, these were moved to function suites. Use the newer function suites whenever possible; things will just be better.

Using our function suites keeps your plug-in compact; you write and test less code. The functions are tested, optimized, and used by our own plug-ins. The functions are distributed to multiple processors and take advantage of available hardware acceleration.

No, really, use the provided functions. Seriously.
