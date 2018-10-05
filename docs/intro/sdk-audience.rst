.. _intro/sdk-audience:

SDK Audience
################################################################################

You must be a proficient C/C++ programmer to write After Effects plug-ins. While we’ll help with issues specific to the After Effects API, we can’t help you learn your IDE or basic programming concepts.

This SDK guide assumes you understand After Effects from a user’s perspective, and basic motion graphics terminology. If you don’t, get the `Adobe After Effects Classroom in a Book <http://www.adobepress.com/store/adobe-after-effects-cc-classroom-in-a-book-2017-release-9780134665320>`__, or any of the other fine instructional books on the market. It will help you understand necessary

topics such as alpha channels, pixel aspect ratio, interlacing, color spaces, and more for After Effects.

----

Development Requirements
================================================================================

The system requirements for After Effects are here: https://helpx.adobe.com/after-effects/system-requirements.html

If you require support for obsolete versions of the application or API, use an old SDK (which we don’t maintain or provide). Six months after the current version is released, we will no longer provide or support the previous version’s SDK.

The SDK samples are created for Xcode 7.3 on macOS 10.11, and Microsoft Visual Studio 2015 update 3 on Windows 7 64 or Windows 10. Yes, we’re being pretty stringent about using the required IDE. No, it’s never pleasant to move to a new compiler, but no, we’re not going to continue to help with older build environments.

In order to use Visual Studio, you may need to adjust some installation settings to install the components for compiling 64-bit plug-ins. Visual Studio Express may be used, but will also require an additional installation to compile 64-bit plug-ins, as described here: http://msdn.microsoft.com/en-us/library/9yb4317s.aspx

To compile using newer versions of Xcode on newer versions of macOS, often all that is required is to update the Base SDK in the Build Settings.
