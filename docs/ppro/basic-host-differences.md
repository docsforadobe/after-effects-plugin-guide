# Basic Host Differences

We've tried to provide robust compatibility for After Effects effect plug-ins in Premiere Pro.

There are underlying differences in the render pipeline that lead to differences, and we realize the API implementation may not be perfect.

Below is an overview of some differences the plug-in will encounter when running in Premiere Pro.

---

## Time Values

Premiere Pro uses slightly different time values in PF_InData. For example in CS4:

Rendering in NTSC, time_scale is 60000, time_step is 1001, field gives field order (in After Effects, for field rendering, scale is 2997, step is 50, or for progressive rendering, scale is 2997, step is 100).

Rendering in PAL, time_scale is 50, time_step is 1, field gives field order (in After Effects, for field rendering, scale is 3200, step is 64, or for progressive rendering, scale is 3200, step is 128).

It's the ratio of time-related values that produces the time value, not specifically the time_scale value. It's possible Premiere Pro will use different time_scales in the future, so please don't hard code. Just be aware that it does not necessarily use the exact same values as After Effects.

---

## Rendering Frames

Premiere is optimized for responsive editing. When scrubbing in the timeline, and changing effect parameters, Premiere will immediately request a low-quality render for immediate display, followed by a high-quality render. So the effect may receive two requests for the same effective time, one at a low resolution, low bit-depth, followed by one at full-resolution, full bit-depth. The resolution requested for each render with take into account the Playback and Paused Resolution set in the Source and Program Monitors: The first request will be at the Playback Resolution, and the second request will be at the Paused Resolution.

Premiere will also perform speculative rendering, to render a set of frames ahead in the timeline, so that if/when the editor starts playback, the initial frames will be ready. This means that when repositioning the time needle, or when changing effect parameters, Premiere will ask the effect to render a set of frames ahead of the current time. If the frames have previously been rendered and cached, the effect will not see these render requests because the cached frames will be used.

When rendering frames in Premiere-native pixel formats, Premiere will send PF_Cmd_RENDER once for each field, rather than for each frame. The `PF_InData->field` will indicate which field is being rendered, the `PF_LayerDef->height` will be half of the frame height, and the `PF_LayerDef->rowbytes` will be double the normal value.

---

## Render Order

Premiere Pro was built to provide real-time playback of footage with effects wherever possible. The render scheduling is much more aggressive and multithreaded rendering is a basic requirement. This is quite different than After Effects, where users are building layers upon layers of effects and more willing to wait for a RAM preview.

Multithreaded rendering in Premiere applies to AE effects too. When rendering an AE effect, the request from Premiere passes through a critical section which is used for all commands, except those relating to arbitrary data. The critical section prevents two threads from calling the same instance of the effect at the same time. However, Premiere creates multiple instances of the effect, which can be called concurrently from separate threads.

Therefore, an effect should not expect to receive render requests in order of increasing time. Also, effects should not depend on static, non-constant variables.

---

## Frame Dimensions

Differences between source footage and the project/composition are handled differently.

For example, in CS4, when importing an NTSC clip in a PAL sequence, `PF_InData>width,height` are `(598,480)` and `PF_InData->pixel_aspect_ratio` is `(768,702)`.

In AE, `width,height` are `(720,480)` and `pixel_aspect_ratio` is `(10,11)`.

---

## PF_InData

Premiere Pro handles field rendering differently than After Effects. While field rendering, `PF_InData>field` gives the current field being rendered, ignoring whether or not `PF_OutFlag_PIX_INDEPENDENT` flag was set.

In Premiere Pro, effects receive the quality setting of the monitor window in [PF_InData>quality](../effect-basics/PF_InData.md#pf_indata-members). This differs from After Effects, where the source layer's quality setting is provided here.

---

## Parameter UI

Premiere Pro does not honor the [PF_ParamFlag_START_COLLAPSED](../effect-basics/PF_ParamDef.md#parameter-flags) flag. Parameters are always initialized with their twirlies collapsed, and cannot be automatically twirled open by parameter supervision.

Premiere Pro supports the macro `PF_ADD_FLOAT_EXPONENTIAL_SLIDER()`, which lets you define an exponent. Although this macro is newly added for the CC 2015 release 2 SDK, Premiere Pro has used this for some time in the Fast Color Corrector, in the Input Grey Level parameter. The exponent is used so that although the range is from 0.10 to 10, 1.0 is about in the middle of the slider. The exponent we used was 2.5. Typical values would be from 0.01 to 100.

Starting in CC 2015, effects will not be sent `PF_Cmd_UPDATE_PARAMS_UI` or `PF_Event_DRAW` when the time needle is moved and there are no keyframes, unless the effect sets `PF_OutFlag_NON_PARAM_VARY`. Effects such as those that draw histograms in the Effect Controls panel will need to be aware of this optimization.

---

## Missing Suites

Many suites supported by After Effects are not implemented in the Premiere Pro host. In several cases, even if a suite is missing in Premiere Pro, an equivalent macro function is available. Here are a few examples:

|        After Effects suite call         | Premiere Pro equivalent function |
| --------------------------------------- | -------------------------------- |
| `WorldTransformSuite1()->copy()`        | `PF_COPY()`                      |
| `WorldTransformSuite1()->convolve()`    | `in_data->utils->convolve()`     |
| `FillMatteSuite2()->fill()`             | `PF_FILL()`                      |
| `PF_PixelDataSuite1->get_pixel_data8()` | `PF_GET_PIXEL_DATA8()`           |

The sample projects demonstrate alternate ways of handling a missing suite, by checking for the host application and version. The Portable sample project demonstrates both host application and version checking.

---

## A Special Suite for AE Effects Running in Premiere Pro

No AEGP calls are supported by Premiere Pro. However, there are some interesting parallels in the header PrSDKAESupport.h. For example, you can use the Utility Suite in that header to get the frame rate or field type of the source footage, or to get the speed applied to the clip.

Note that other suites from the Premiere Pro SDK cannot be used in AE effects.
