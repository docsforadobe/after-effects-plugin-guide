.. _effect-details/motion-blur:

Motion Blur
################################################################################

Effects handle their own motion blur, using PF_InData>\ `shutter_angle <#_bookmark122>`__ along with PF_InData>\ `shutter_phase <#_bookmark128>`__.

The plug-in must set `PF_OutFlag_I_USE_SHUTTER_ANGLE <#_bookmark164>`__ so After Effects knows it needs this information.

They must `check out <#_bookmark288>`__ their own parameters at other times to examine their change over the shutter interval.

If the plug-in checks out parameters outside this interval, set `PF_OutFlag_WIDE_TIME_INPUT <#_bookmark151>`__.

Doing so allows After Effects to compare the parameters within the sampling interval, and determine if they've changed.

