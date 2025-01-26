# Audio Considerations

The After Effects audio API supports sampling rates up to 96Khz, in as many formats as possible.

In the same way that plug-ins' pixel manipulation functions should remain "resolution independent", audio plug-ins should be sample rate- and bit depth-independent.

Your plug-in can't know anything about the final output format of the audio in question; it might get stretched, normalized, truncated, or phase-inverted between the application of your plug-in and the final output.

Audio filters encounter different issues than do image filters.

Investigate the SDK sample for one possible implementation of audio rendering.
