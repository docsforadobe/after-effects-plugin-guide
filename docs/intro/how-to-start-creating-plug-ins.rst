.. _intro/how-to-start-creating-plug-ins:

How To Start Creating Plug-ins
################################################################################

Play!
================================================================================

Before you write a line of code, Spend some significant time playing with After Effects, and with the :ref:`intro/sample-projects`. Build the plug-ins into the right folder. Set lots of breakpoints, read the amusing and informative comments.

See a quickstart video on building an effect (on macOS): `adobe.ly/2sjMDwM <https://adobe.ly/2sjMDwM>`__

----

Plan!
================================================================================

Be clear on what your plug-in will attempt to do.

----

Hack!
================================================================================

After experimenting with the samples, find one that does something *like* what you want to do. The temptation to start from scratch may be strong; fight it! For effects, use the Skeleton template project. Avoid the headache of reconstructing projects (including the troublesome custom build steps for Windows PiPL resource generation) by grafting your code into an existing project.

----

Steal!
================================================================================

To make the Skeleton sample your own, copy the entire \\Skeleton directory, renaming it to (for example) \\WhizBang. Using your text editor of choice, search \\WhizBang\*.\* (yes, that includes .NET and Xcode project files) for occurrences of Skeleton and SKELETON, and replace them with WhizBang and WHIZBANG.

You now have a compiling and running plug-in that responds to common commands, handles 8 and 16-bpc color, uses our AEGP_SuiteHandler utility code, and responds to 3D light and camera information. There, was that so hard?

AEGP developers will do well to start with Projector (for After Effects project creation support), Easy Cheese for a keyframe assistant, IO for media file format support, and Persisto for a simple menu command and working with preferences.

----

Test!
================================================================================

If only for testing convenience, you should have a project saved with your effect applied, and all its parameters keyframed to strange values. Between these projects which stress your plug-in, and the tools provided by your development environment, you're well on your way to shipping some tested code.

----

Blame!
================================================================================

If you run into behavior that seems wrong, see if you can reproduce the behavior using one of the unmodified sample projects. This can save you a lot of time, if you can determine whether the bug behavior was introduced by your modifications, or was already there to begin with.

----

Developers Matter
================================================================================

Third party developers drive API and SDK improvement and expansion. Your products enable After Effects to do things we'd never considered. Your efforts make After Effects better; keep it up!

We work hard on the SDK, and welcome your comments and feedback. Almost every change we make to the API is suggested by developers like you. `Talk to us <mailto:bbbb@adobe.com>`__.
