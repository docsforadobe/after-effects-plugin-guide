.. _audio/accessing-audio-data:

Accessing Audio Data
################################################################################

Use `PF_CHECKOUT_LAYER_AUDIO <#_bookmark293>`__ to retrieve an audio layer.

This layer is opaque; use `PF_GET_AUDIO_DATA <#_bookmark295>`__ to access specific details about that audio.

As with pixel data, it's important that you check in the audio as soon as possible.

If your effect requires as input a time span different from the output time span, update the ``startsampL`` and ``endsampL`` field in ``PF_OutData`` during `PF_Cmd_AUDIO_SETUP <#_bookmark99>`__.

----

Extending Audio Clips
================================================================================

You cannot extend the length of an audio clip through the API.

However, it is a relatively simple matter for the user to extend the length of the clip before applying your effect. Apply time remapping to the layer and simply extend the out point.

If you're adding a delay effect to a sounds clip, you'd want to allow it time to fade away instead of truncating the sound at the original end point.

Document the steps users should take when applying your effect.
