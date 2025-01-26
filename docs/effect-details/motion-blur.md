.. _effect-details/motion-blur:

Motion Blur
################################################################################

Effects handle their own motion blur, using :ref:`PF_InData>shutter_angle <effect-basics/PF_InData.PF_InData-Members>` along with :ref:`PF_InData>shutter_phase <effect-basics/PF_InData.PF_InData-Members>`.

The plug-in must set :ref:`PF_OutFlag_I_USE_SHUTTER_ANGLE <effect-basics/PF_OutData.PF_OutFlags>` so After Effects knows it needs this information.

They must :ref:`check out <effect-details/interaction-callback-functions.interaction-callbacks>` their own parameters at other times to examine their change over the shutter interval.

If the plug-in checks out parameters outside this interval, set :ref:`PF_OutFlag_WIDE_TIME_INPUT <effect-basics/PF_OutData.PF_OutFlags>`.

Doing so allows After Effects to compare the parameters within the sampling interval, and determine if they've changed.

