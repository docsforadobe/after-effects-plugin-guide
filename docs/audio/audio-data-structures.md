# Audio Data Structures

The following data types are used by After Effects to describe audio data.

| **Structure**        | **Description**                                                                                                                                                   |
|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `PF_SoundFormat`     | Indicates whether the audio is in unsigned pulse code modulation (PCM), signed PCM, or floating point format.                                                     |
| `PF_SoundSampleSize` | Samples are in 1, 2, or 4 byte format.                                                                                                                            |
| `PF_SoundChannels`   | Indicates whether the audio is mono or stereo.                                                                                                                    |
| `PF_SoundFormatInfo` | Contains the sampling rate, number of channels, sample size, and format of the audio to which it refers.                                                          |
| `PF_SoundWorld`      | Use `PF_SoundWorlds` to represent audio.<br/>In addition to a `PF_SoundFormatInfo`, they contain the length of the audio, and a pointer to the actual audio data. |

`PF_SoundFormat`, `PF_SoundSampleSize`, and `PF_SoundChannels` are all contained within a `PF_SoundFormatInfo`.

`PF_SoundWorlds` contain a `PF_SoundFormatInfo`, and further instance-specific information.
