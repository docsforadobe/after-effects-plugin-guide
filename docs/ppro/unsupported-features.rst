.. _ppro/unsupported-features:

Unsupported Features
################################################################################

Premiere Pro is currently known to not support the following features of the After Effects API:

(If you would like a feature with a "-" bullet, please email `Premiere Pro API Engineering <mailto:bbb@adobe.com>`__ with the feature request. Numbers preceded by an 'F' are feature request numbers, and the others are bug numbers)

- F7233 - extent_hint support
- F7835 - Multiple PiPLs in a single plug-in
- F7836 - AEGP support
- F7517 - Audio support - if a plug-in sets PF_OutFlag_I_USE_AUDIO in PF_Cmd_GLOBAL_SETUP, it will not be loaded at all
- F9355 - Support PF_ParamFlag_COLLAPSE_TWIRLY
- PF World Transform Suite
- PF AE Channel Suite
- AE's implementation of high bit color depth support
- SmartFX
- 3D support
- PF_SUBPIXEL_SAMPLE(), PF_GET_PIXEL_DATA16()

----

But... Why'd You LOAD It If You Can't RUN It?!
================================================================================

Premiere Pro attempts to load AEGP plug-ins. To detect this and avoid any problem behavior, your command hook function can access a suite which is only provided by After Effects; AEGP_CanvasSuite is a fine candidate.

If the suite isn't present, return an error. The plug-in will be placed on Premiere Pro's "don't load these" list.
