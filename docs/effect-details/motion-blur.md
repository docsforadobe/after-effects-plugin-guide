<a id="effect-details-motion-blur"></a>

# Motion Blur

Effects handle their own motion blur, using [PF_InData>shutter_angle](../effect-basics/PF_InData.md#effect-basics-pf-indata-pf-indata-members) along with [PF_InData>shutter_phase](../effect-basics/PF_InData.md#effect-basics-pf-indata-pf-indata-members).

The plug-in must set [PF_OutFlag_I_USE_SHUTTER_ANGLE](../effect-basics/PF_OutData.md#effect-basics-pf-outdata-pf-outflags) so After Effects knows it needs this information.

They must [check out](interaction-callback-functions.md#effect-details-interaction-callback-functions-interaction-callbacks) their own parameters at other times to examine their change over the shutter interval.

If the plug-in checks out parameters outside this interval, set [PF_OutFlag_WIDE_TIME_INPUT](../effect-basics/PF_OutData.md#effect-basics-pf-outdata-pf-outflags).

Doing so allows After Effects to compare the parameters within the sampling interval, and determine if they’ve changed.
