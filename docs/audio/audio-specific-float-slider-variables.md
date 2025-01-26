.. _audio/audio-specific-float-slider-variables:

Audio-Specific Float Slider Variables
################################################################################

``PF_Param_FLOAT_SLIDERs`` contain several parameters not found in other sliders; flags, phase, and curve tolerance.

----

Flags
================================================================================

The only flag available is ``PF_FSliderFlag_WANT_PHASE``.

This registers the effect to receive updated phase information from After Effects during audio rendering.

To understand what this flag does, turn it off and check your output.

----

Phase
================================================================================

This is where the requested phase value is stored.

----

Curve Tolerance
================================================================================

Curve tolerance is used by After Effects to subdivide the audio effects' time-variant parameters. Set this to zero for default behavior (or for non-audio ``FLOAT_SLIDER`` parameters).

----

What's Zero, Really?
================================================================================

When amplitude is zero, After Effects is at -192db.
