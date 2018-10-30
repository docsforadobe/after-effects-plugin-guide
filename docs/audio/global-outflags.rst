.. _audio/global-outflags:

Global Outflags
################################################################################

All audio effects must set either ``PF_OutFlag_AUDIO_EFFECT_TOO`` or ``PF_OutFlag_AUDIO_EFFECT_ONLY``.

``PF_OutFlag_I_USE_AUDIO`` is for visual effects that check out audio data, but don't modify it.

``PF_OutFlag_AUDIO_FLOAT_ONLY``, ``PF_OutFlag_AUDIO_IIR`` and ``PF_OutFlag_I_SYNTHESIZE_AUDIO`` provide greater control over audio output (see :ref:`effect-basics/PF_OutData.PF_OutFlags` for more details).

