# What's New

If this is your first time developing an After Effects plug-in, you can skip the What's New section and go directly to [How To Start Creating Plug-ins](how-to-start-creating-plug-ins.md).

---

## What's New In After Effects 2022

After Effects 2022 contains the first full public release with Multi-Frame Rendering support. The associated AE Effects SDK released in October 2021 includes one change for increasing the maximum number of PF_Iterate threads.

---

## What's New in the After Effects SDK in March 2021

### Multi-Frame Rendering Changes

1. The final behavior for the `PF_OutFlag2_SUPPORTS_THREADED_RENDERING` flag is now in place. Setting this flag to indicate support for Multi-Frame Rendering will also enforce the data stored in `sequence_data` to be const/read-only at Render time and access to `sequence_data` is now through a suite, `PF_EffectSequenceDataSuite1`.
2. A new flag, `PF_OutFlag2_MUTABLE_RENDER_SEQUENCE_DATA_SLOWER` is now available to set alongside `PF_OutFlag2_SUPPORTS_THREADED_RENDERING` if your plugin cannot be updated to work with the new sequence_data behavior. After Effects won't be able to apply as much rendering concurrency and therefore performance improvements to effects that set this flag (hence the \_SLOWER flag name).
3. A new suite, the Compute Cache (previously referred to as the 3-way checkout cache) is now available. This suite provides a thread-safe cache that plugins can use as a replacement or supplement to sequence_data to support multiple render threads computing and caching data needed to render frames.

Due to these changes, you must update to, and compile with, the March 2021 SDK to maintain Multi-Frame Rendering compatibility with AE Beta builds moving forward. Plugins compiled with the June 2020 SDK will cease to support Multi-Frame Rendering, even if `PF_OutFlag2_SUPPORTS_THREADED_RENDERING` is set, starting with AE 22.0x6 (released on June 29th 2021).

Please see [Multi-Frame Rendering in AE](../effect-details/multi-frame-rendering-in-ae.md) for more information.

### Apple Silicon Support

* The AE SDK now supports building effects for Apple Silicon natively. While After Effects itself is not yet running on Apple Silicon, Adobe as a company is moving forward with native support across many of our products. Applications such as Premiere Pro now have native versions available, and your effects may be loaded in Premiere Pro by features such as Motion Graphic Templates. When running the native version of Premiere Pro only natively compiled effects will work so it's important to update your effects with Apple Silicon support soon. Please see the [Apple Silicon Support](apple-silicon-support.md) section for more information.

### Exporting Symbols from Effects

* The SDK samples have been updated on not export symbols by default on MacOS. Please see [Exporting Symbols in Effects](symbol-export.md) for more information.

### Downloading the March 2021 SDK

The SDK can be downloaded from the Adobe Developer Console at [https://adobe.io/after-effects/](https://adobe.io/after-effects/)

### After Effects Beta Builds

To gain access to the AE host-side changes for this SDK, you will need to download a new After Effects beta build from the Creative Cloud Desktop App. Builds 18.2x11 and above are supported with the March 2021 SDK.

---

## What's New In After Effects Beta builds after June 2020

AE (currently only in Beta builds) now supports Multi-Frame rendering. See [Multi-Frame Rendering in AE](../effect-details/multi-frame-rendering-in-ae.md) for more details.

---

## What's New In CC 2019 (16.0)?

We've made some changes to how GPU effects are handled. See "GPU Effects Changes" for details.

---

## What's New In 15.0?

After Effects now supports the *GPU effect rendering* previously supported in Premiere Pro. Note that unknown effects with a match name that includes "ADBE " will be excluded from GPU rendering, so make sure any of your GPU effects have your own custom match name. Effects that support GPU rendering will receive the GPU badge in the Effect panel.

The GPU effect sample projects in the Premiere Pro SDK have been updated to register as GPU effects in AE, although the render output still needs work.

A new entry point has been defined, to allow effects to register basic information with the host at runtime, without relying on legacy PiPL resources. An effect can register multiple entry points in a single binary this way. Premiere Pro is the first host to support this entry point, and After Effects will support this in a future release.

The effect sample projects have been updated to use this approach, while maintaining the PiPL for backwards compatibility.

`AEGP_StreamSuite` is now at version 5, where [AEGP_GetExpression()](../aegps/aegp-suites.md#aegp_streamsuite5) and [AEGP_SetExpression()](../aegps/aegp-suites.md#aegp_streamsuite5) have been upgraded to support Unicode.

`PF_AdvTimeSuite` is now at version 4, with a new call [PF_TimeCountFrames()](../effect-details/useful-utility-functions.md#pf_advtimesuite4), that returns the index of the frame in the current comp.

The new AEGP Math Suite provides helpful calls for matrix multiplication.

The application font is now Adobe Clean. Previously, the fonts used in After Effects' UI were Tahoma on Windows and Lucida Grande on macOS X. This is a proprietary font, and we can not make it available for use in your UI.

---

## What's New In CC 2017.1 (14.2)?

- Layer Params can include Masks and Effects

Effects that use layers as an input, such as Set Matte and Displacement Map, can now target the input layer's masks and effects, instead of only the source of the layer. This means that for there is no need to pre-compose layers just so that they can be referenced by an effect.

Where an effect includes a layer parameter, a new menu to the right of the layer selector allows you to choose whether to target the input layer from its source, masks, or effects:

- Source: targets only the source of the layer. Masks and effects are ignored.
- Masks: targets the layer after its masks are applied. Effects are ignored.
- Effects & Masks: targets the layer after its masks and effects are applied.

This control is similar to the View menu at the bottom of the Layer viewer panel, which allows you to render the layer from different positions in the rendering order: from its source, from its masks, or from its individual effects.

As this is a user-facing option, the design is intended to be transparent to the effect. From the effect's perspective, the input simply just includes the upstream effects and masks without any change to the effect. For any effect that uses layer params, here are some testing recommendations:

- Effect continues to work as expected.
- Using new control in the layer param for Source/Mask/Effects works with effect.
- Opening old projects or saving back to a previous version project does not break effect.
- Confirm that effect cannot self-reference; meaning cannot use the effects on the layer as input for the same layer.
- Suite Enhancements

PF_AdvTimeSuite is now at version 3, providing a revised [PF_GetTimeDisplayPref()](../effect-details/useful-utility-functions.md#pf_advtimesuite4) call that uses a revised `PF_TimeDisplayPrefVersion` parameter, that supports higher frame rates.
The previous version 2 of the call can now return an error if there is a problem with the values exceeding the range supported by the structure.

Comp Suite is now at version 11, with a new call, [AEGP_ReorderCompSelection()](../aegps/aegp-suites.md#aegp_compsuite11), to move a selection to a certain layer index.
It should be used along with `AEGP_SetSelection()`.

---

## What's New In CC 2017 (14.1)?

Unicode support for [AEGP Item Suite](../aegps/aegp-suites.md#aegp_itemsuite9) and [AEGP Render Queue Item Suite](../aegps/aegp-suites.md#render-queue-item-suite).

---

## What's New In CC 2017 (14.0)?

The GLator sample is back! It has been updated to demonstrate proper OpenGL context management in an effect plug-in.

---

## What's New In CC 2015.3 (13.8)?

PF_OutFlag_I_AM_OBSOLETE is now supported in Premiere Pro. Also, effect custom UI in Premiere Pro now supports high DPI displays, such as Retina Displays.

---

## What's New In CC 2015 (13.6)?

New AEGP Item View Suite. This provides a way to get playback time for item view. Only the composition case is implemented in this release. The time passed back should be the playback time of the view when playing and the current (needle) time otherwise.

AEGP_RenderNewItemSoundData() has been reworked and provides functionality similar to 13.2.

---

## What's New In CC 2015 (13.5.1)?

This release fixes some audio APIs that broke in 13.5 due to threading changes. In 13.5, when called on the UI thread, AEGP_RenderNewItemSoundData() would return A_Err_GENERIC. This restores the functionality when called on the UI thread.

To avoid a deadlock, in PF_Cmd_UPDATE_PARAMS_UI only, AEGP_RenderNewItemSoundData() will now return silence. This will no longer function as before in this context, but it will continue to work properly elsewhere.

---

## What's New In CC 2015 (13.5)?

- Separate UI and Render Threads

This release of After Effects includes major architectural changes to separate the UI (main) thread from the render thread. The render thread sends selectors such as PF_Cmd_RENDER, PF_Cmd_SMART_PRERENDER, and PF_Cmd_SMART_RENDER to effect plug-ins. The UI thread sends selectors such as PF_Cmd_SEQUENCE_SETUP, PF_Cmd_USER_CHANGED_PARAM, PF_Cmd_DO_DIALOG, and PF_EVENT_DRAW. PF_Cmd_SEQUENCE_RESETUP is

sent on both render and UI threads.

These changes are to improve interactive performance and responsiveness. At the same time, the new design introduces some new requirements and may break assumptions that existing plug-ins relied on. Here are some of the major changes:

1. The project can no longer be modified by the render thread (and in fact the render thread now has its own local copy of the project)
2. Rendering cannot pass modified sequence data back to the UI thread for custom UI updates
3. In general the UI thread should no longer do time-consuming operations such as synchronously rendering frames

Is your plug-in affected? Test for these problems:

1. Render not updating after UI parameter change because it depends on sequence_data, which may not be currently copied to render
2. Render not updating during click/drag in the Composition Window (similar reasons)
3. Custom Effect UI not updating because it depends on sequence_data generated in render (which is no longer available to the UI because it is in a different project, the render project is immutable, and cache contains previously-rendered frames)
4. Errors telling you an operation on the render thread (or UI thread) is not expected

Generally, calculations that will persist or update the UI will now have to be pulled from the UI thread rather than pushed from the render thread. These cases can require use of new

13.5 APIs or different solutions than in past releases.

- The Need For More Efficient Sequence Data Handling

PF_OutFlag2_SUPPORTS_GET_FLATTENED_SEQUENCE_DATA

PF_Cmd_GET_FLATTENED_SEQUENCE_DATA

Up to version 13.2, serializing/flattening sequence_data always involved deallocating and reallocating any data structures. Starting in 13.5, as effect changes are made, serializing/ flattening happens even more often. Why? AE needs to serialize/flatten project changes to send from the UI thread to the render thread, to keep them both synchronized.

To make this process more efficient, starting in 13.5, AE can send PF_Cmd_GET_FLATTENED_SEQUENCE_DATA to request sequence data without requiring the existing data to be deallocated and reallocated. The main difference between this selector and PF_Cmd_SEQUENCE_FLATTEN is that a copy of the correct flattened state is returned without disposing the original structure(s) the effect is currently using. For a code example, refer to the PathMaster sample project.

This will eventually become required for plug-ins that are rebuilt to be thread-safe (see PF_OutFlag2_AE13_5_THREADSAFE below). The venerable PF_Cmd_SEQUENCE_FLATTEN will eventually be unsupported in future versions.

- PF_OutFlag_FORCE_RERENDER Changes

Where possible, we recommend triggering rerenders using one of the following: GuidMixInPtr() (described in the next section), arb data, or PF_ChangeFlag_CHANGED_VALUE. All of these allow cached frames to be reused after an Undo.

Note: As of 14.0, setting PF_ChangeFlag_CHANGED_VALUE for layer or path params is not triggering a rerender. Instead, you may change set the value using AEGP_StreamSuite-

>AEGP_SetStreamValue().

FORCE_RERENDER is still needed for situations where sequence_data needs to be copied from the UI thread to the render project/effect clone to keep them matched.

FORCE_RERENDER is the trigger for this whether the render request uses the cache or not. Once we have the full set of APIs in place needed to manage render state, we will be able to deprecate FORCE_RERENDER.

FORCE_RERENDER doesn't work in every situation it did before, because it needs to synchronize the UI copy of sequence_data with the render thread copy.

FORCE_RERENDER works when set during PF_Cmd_USER_CHANGED_PARAM. It also works in CLICK and DRAG events, but only if PF_Cmd_GET_FLATTENED_SEQUENCE_DATA is implemented. This is required to prevent flattening and loss of UI state in the middle of mouse operations. Without GET_FLATTENED, the new FORCE_RERENDER behavior will NOT be turned on.

- GUIDs for Cached Frames

PF_OutFlag2_I_MIX_GUID_DEPENDENCIES

GuidMixInPtr()

Used by SmartFX only. Use this if custom UI or PF_Cmd_DO_DIALOG changes sequence data, or if the render result depends on anything else not factored in, and rerendering may be needed. During PF_Cmd_SMART_PRERENDER, the effect can call GuidMixInPtr() to mix any additional state that affects the render into our internal GUID for the cached frame. Using this GUID, AE can tell whether the frame already exists or if it needs to be rendered. See an example in SmartyPants sample project.

This is an improvement over the older mechanisms PF_OutFlag_FORCE_RERENDER and PF_Cmd_DO_DIALOG, which would remove the frame from the cache because the host didn't know what else the plug-in was factoring into the rendering. This can also be used rather than PF_OutFlag2_OUTPUT_IS_WATERMARKED.

- Request Frames Asynchronously Without Blocking the UI

PF_OutFlag2_CUSTOM_UI_ASYNC_MANAGER

PF_GetContextAsyncManager() AEGP_CheckoutOrRender_ItemFrame_AsyncManager() AEGP_CheckoutOrRender_LayerFrame_AsyncManager()

For cases where such renders formerly were triggered by side-effect or cancelled implicity

(such as custom UI histogram drawing), and lifetime is less clear from inside the plug-in, use the new "Async Manager" which can handle multiple simultaneous async requests for effect Custom UI and will automatically support interactions with other AE UI behavior.

Note: Async retrieval of frames is preferred for handling passive drawing situations, but not when a user action will update the project state. If you are (1) responding to a specific user click, AND 2) you need to update the project as a result, the synchronous AEGP_RenderAndCheckoutLayerFrame() is recommended.

The new HistoGrid sample in the SDK shows how to do completely asynchronous custom UI DRAW event handling on the UI thread when 1 or more frame renders are needed. e.g. for calculating histograms that are shown in the effect pane. Please note there is still a known bug where drag-changing an upstream param may not refresh the histogram draw until the mouse hovers over it.

- Get Rendered Output of an Effect from its UI

Effects such as keyers or those that draw histograms of post-processed video can retrieve the needed AEGP_LayerRenderOptionsH using the new function AEGP_NewFromDownstreamOfEffect() in AEGP_LayerRenderOptionsSuite. This function may only be called from the UI thread.

- AEGP Usage on Render Thread

We've tightened validation of when AEGP calls could be used dangerously (such as from the wrong thread or making a change to the project state in render). You may see new errors if code is hitting such cases. For example, making these calls on the render thread will result in an error:

suites.UtilitySuite5()->AEGP_StartUndoGroup() suites.StreamSuite2()->AEGP_GetStreamName() suites.StreamSuite2()->AEGP_SetExpressionState() suites.StreamSuite2()->AEGP_SetExpression() suites.StreamSuite2()->AEGP_GetNewLayerStream() suites.StreamSuite2()->AEGP_DisposeStream() suites.EffectSuite3()->AEGP_DisposeEffect() suites.UtilitySuite5()->AEGP_EndUndoGroup()

The solution is to move these calls to the UI thread. Selectors for passive UI updates (such as PF_EVENT_DRAW) are not a place to make changes to project state.

Another example of more strict requirements is AEGP_RegisterWithAEGP(). The documentation has always noted that this function must be called on PF_Cmd_GLOBAL_SETUP. However in previous versions, plug-ins were able to call this function at other times without running into trouble. Not anymore in 13.5! Calling this function at other times can cause crashes!

- PF_Cmd_SEQUENCE_RESETUP Called on UI or Render Thread?

There is now a PF_InFlag_PROJECT_IS_RENDER_ONLY flag that is only valid in PF_Cmd_SEQUENCE_RESETUP that will tell you if the effect instance is for render-only purposes. If so, the project should be treated as completely read-only, and you will not be receiving UI related selectors on that effect instance. This can be used to optimize away any UI-only initialization that render does not need. If this flag is false, you should setup UI as normal. This should not be used to avoid reporting errors in render. Errors in render should be reported as usual via existing SDK mechanisms.

- Changes to Avoid Deadlocks

During development, it was noticed that deadlocks could occur in specific call usage. Seatbelts have been introduced to avoid this. The cases occur in PF_Cmd_UPDATE_PARAMS_UI when using particular calls because of deprecated synchronous behavior in these calls when used in the UI:

In PF_Cmd_UPDATE_PARAMS_UI only, PF_PARAM_CHECKOUT() for layer parameters will behave as before except that it will return a black frame of the same size, etc., rather than actual rendered pixels. Code that used this for enable/disable detection of parameters should still work as before. Code that used this for getting analysis frames, etc. outside of PF_Cmd_UPDATE_PARAMS_UI will work as before.

In PF_Cmd_UPDATE_PARAMS_UI only, PF_GetCurrentState() will now return a random GUID. This will no longer function as before in this context, but it will continue to work properly elsewhere.

The above uses should be rare, but if this affects you please contact us about workarounds.

- Deprecated

AEGP_RenderAndCheckoutFrame() (on the UI Thread). This call should generally not be used on the UI thread since synchronous renders block interactivity.

Use in the render thread is fine. The one case where this may still be useful on the UI thread is a case like a UI button that requires a frame to calculate a parameter which then updates the AE project.

For example, an "Auto Color" button that takes a frame and then adjusts effect params as a result.

A beta of a progress dialog for this blocking operation if it is slow has been implemented, but using this call on the UI thread should be limited to this special cases. The dialog design is not final.

- Flag for Thread-Safe Effects

PF_OutFlag2_AE13_5_THREADSAFE

Plug-ins updated for threading should use this flag to tell AE that the plug-in is expected to be UI thread <> Render thread safe.

This flag tells AE that different threads on different AE project copies can be in the effect at the same time but not accessing the same instance. While multiple render threads are not yet in use, this will be useful in future releases.

- Support for Effect Version greater than 7 (new max is MAJOR version 127)

Effects greater than version 7 will now report properly in 13.5 if built with the current SDK headers. It is possible to use these recompiled effects in AE versions older than 13.5, but internally the version number will wrap modulo 8 (e.g. AE will internally see effect version 8 as version 0).

This can affect the version shown in error dialog display by older AE and affect usage reporting.

Since many older plug-ins were made unloadable in AE with the shift to 64-bit, it should be unlikely this wrapping would cause ambiguity with actual plug-ins in current use (unless these plug-ins have been rapidly increasing version number over the last few years).

However, building with an older SDK and using an 8 or higher version will result in the plug-in reporting an incorrect version to AE, which will then cause mismatch with the PiPL version check for the effect which will have the higher bits set. This is not supported.

If built with an older SDK, you will need to keep the effect version at 7 or below. Increase in version max has been accomplished by adding 4 new higher significant bits to the version that only AE 13.5 and above "sees". These new high version bits are not contiguous with the original, preexisting MAJOR version bits - just ignore the intermediate bits. The new version layout looks like this in hexadecimal or binary.

0x 3C38 0000

^^ original MAJOR version bits as a hex mask 0-7

^^ new HIGH bits extending the original MAJOR version bits 8-127

0b 0011 1100 0011 1000 0000 0000 0000 0000

^^ ^ original MAJOR version bits as a hex mask 0-7

^^ ^^ ignore / do not use

^^ ^^ new HIGH bits extend the original MAJOR version bits 8-127.

These bits are ignored in AE versions older than 13.5.

- New Installer Hints for macOS

Developers can find paths to the default location of plug-ins, scripts, and presets on macOS X in a new plist file (same as the paths in the Windows registry): /Library/Preferences/ com.Adobe.After Effects.paths.plist

You can use the values in this plist to direct where your installers or scripts write files, in the same way that you would use the paths keys in the registry on Windows: HKEY_LOCAL_MACHINESOFTWAREAdobeAfter Effects13.5

- Work In Progress

AEGP_RenderAndCheckoutLayerFrame_Async() AEGP_CancelAsyncRequest()

This APIs are in progress, and should not be used yet.

---

## What's New In CC 2014.1 (13.1)?

PF_CreateNewAppProgressDialog()

It won't open the dialog unless it detects a slow render. (2 seconds timeout).

---

## What's New In CC 2014 (13.0)?

Starting in CC 2014, After Effects will now honor a change to a custom UI height made using [PF_UpdateParamUI](../effect-details/parameter-supervision.md#pf_paramutilsuite3).

[AEGP Effect Suite](../aegps/aegp-suites.md#aegp_effectsuite4) is now at version 4, adding new functions to work with effect masks. [AEGP_RenderSuite](../aegps/aegp-suites.md#aegp_rendersuite4) is now at version 4, adding a new function `AEGP_RenderAndCheckoutLayerFrame`, which allows frame checkout of the current

layer with effects applied at non-render time. This is useful for an operation that requires the frame, for example, when a button is clicked and it is acceptable to wait for a moment while it is rendering.

!!! note
 Since it is not asynchronous, it will not solve the general problem where custom UI needs to draw based on the frame.

The layer render options are specified using the new [AEGP_LayerRenderOptionsSuite](../aegps/aegp-suites.md#aegp_renderoptionssuite4).

[Mercury Transmit](other-integration-possibilities.md#mercury-transmit) plug-ins and [HTML5 Panels](other-integration-possibilities.md#html5-panels) are now supported.

---

## What's New In CC (12.0)?

Effect names can now be up to 47 characters long, up from 31 characters previously.

We added the [PF_AngleParamSuite](../effect-details/parameters-floating-point-values.md#pf_angleparamsuite1), providing a way to get floating point values for angle parameters. [PF App Suite](../effect-details/useful-utility-functions.md) version 5 adds `PF_AppGetLanguage` to query the current language so that a plug-in can use the correct language string, as well as several new PF_App_ColorType enum values for new elements whose colors can be queried.

[AEGP Persistent Data Suite](../aegps/aegp-suites.md#persistent-data-suite) is now at version 4, adding a new parameter to AEGP_GetApplicationBlob to choose between retrieving several different application blobs. There are also new functions to get/set time and ARGB values.

[AEGP Composition Suite](../aegps/aegp-suites.md#aegp_compositesuite2) is now at version 10, adding new functions to check/modify whether layer names or source names are shown, and whether the blend modes column is shown or not. Also added are new functions to get and set the Motion Blur Adaptive Sample Limit.

[AEGP Layer Suite](../aegps/aegp-suites.md#aegp_layersuite9) is now at version 8, adding new functions to set/get the layer sampling quality. [AEGP_CanvasSuite](../artisans/artisan-data-types.md#aegp_canvassuite8) is also now at version 8. The new function `AEGP_MapCompToLayerTime` handles time remapping with collapsed or nested comps, unlike AEGP_ConvertCompToLayerTime.

[AEGP_UtilitySuite](../aegps/aegp-suites.md#aegp_utilitysuite6) is now at version 6, adding a new Unicode-aware function: `AEGP_ReportInfoUnicode`. Another new function, `AEGP_GetPluginPaths`, provides some useful paths related to the plug-in and the After Effects executable itself.

The behavior for `AEGP_NewPlaceholderFootageWithPath` has been updated, so that the file_type should now be properly set, otherwise a warning will appear.

`AEGP_InsertMenuCommand` can now insert menu items in the File>New submenu.

[AEGP_IOInSuite](../aeios/new-kids-on-the-function-block.md#aegp_ioinsuite5) is now at version 5, adding new functions to get/set/clear the native start time, and to get/set the drop-frame setting of footage.

---

## What's New In CS6.0.1 (11.0.1)?

New in 11.0.1, the AE effect API version has been incremented to 13.3.

This allows effects to distinguish between 11.0 and 11.0.1.

There is a bug in 11.0 with the Global Performance Cache, when a SmartFX effect uses both `PF_OutFlag2_AUTOMATIC_WIDE_TIME_INPUT` & `PF_OutFlag_NON_PARAM_VARY`.

Calling `checkout_layer` during `PF_Cmd_SMART_PRE_RENDER` returns empty rects in `PF_CheckoutResult`.

The workaround is to simply make the call again. This workaround is no longer needed in 11.0.1.

---

## What's New In CS6 (11.0)?

We've made several refinements for better parameter UI handling. `PF_PUI_INVISIBLE` parameter UI flag is now supported in After Effects, which is useful if your plug-in needs hidden parameters that affect rendering. Now when a plug-in disables a parameter using [PF_UpdateParamUI](../effect-details/parameter-supervision.md#pf_paramutilsuite3), we now save that state in the UI flags so that the plug-in can check the flag in the future to see if it is disabled. A new flag, `PF_ParamFlag_SKIP_REVEAL_WHEN_UNHIDDEN`, allows a parameter to be unhidden without twirling open any parents and without scrolling the parameter into view in the Effect Controls panel and the Timeline panel.

Effects that render a watermark over the output when the plug-in is in trial mode can now tell After Effects whether watermark rendering mode is on or off, using the new `PF_OutFlag2_OUTPUT_IS_WATERMARKED`.

The new Global Performance Cache means you must tell After Effects to discard old cached frames [when changing your effect's rendering](../effect-details/tips-tricks.md#caching-behavior).

We've removed `PF_HasParamChanged` and `PF_HaveInputsChangedOverTimeSpan`, providing [PF_AreStatesIdentical](../effect-details/parameter-supervision.md#pf_paramutilsuite3) instead.

Effects that provide custom UI can now receive `PF_Event_MOUSE_EXITED`, to gain notification that the mouse exited the layer or comp panel. `PF_ParamUtilsSuite` is now at version 3.

`PF_GET_PLATFORM_DATA` now has new selectors for getting the wide character path of the executable and resource file: `PF_PlatData_EXE_FILE_PATH_W` and `PF_PlatData_RES_FILE_PATH_W`. The previous non-wide selectors are now deprecated.

3D is a major theme of AE CS6. A new `AEGP_LayerFlag_ENVIRONMENT_LAYER` has been added. Many new [layer streams](../aegps/aegp-suites.md#aegp_streamsuite5) were added.

Additionally, `AEGP_LayerStream_SPECULAR_COEFF` was renamed to `AEGP_LayerStream_SPECULAR_INTENSITY`, `AEGP_LayerStream_SHININESS_COEFF` was renamed to `AEGP_LayerStream_SPECULAR_SHININESS`, and `AEGP_LayerStream_METAL_COEFF` was renamed to just `AEGP_LayerStream_METAL`.

A new suite, [AEGP_RenderQueueMonitorSuite](../aegps/aegp-suites.md#render-queue-monitor-suite), provides all the info a render queue manager needs to figure out what is happening at any point in a render.

[AEGP Mask Suite](../aegps/aegp-suites.md#aegp_masksuite6) is now at version 6, and provides functions to get and set the mask feather falloff type. [AEGP Mask Outline Suite](../aegps/aegp-suites.md#aegp_maskoutlinesuite3) is now at version 3, and provides access to get and set mask outline feather information.

Effects that depend on masks now have a new flag available, `PF_OutFlag2_DEPENDS_ON_UNREFERENCED_MASKS`.

[AEGP Composition Suite](../aegps/aegp-suites.md#aegp_compositesuite2) is now at version 9. AEGP_CreateTextLayerInComp and

AEGP_CreateBoxTextLayerInComp now have a new parameter, select_new_layerB.

[AEGP Render Suite](../aegps/aegp-suites.md#aegp_rendersuite4) is now at version 3, adding a new function to get the GUID for a render receipt.

Finally, we have added two new read-only [Dynamic Stream](../aegps/aegp-suites.md#aegp_dynamicstreamsuite4) flags: `AEGP_DynStreamFlag_SHOWN_WHEN_EMPTY` and `AEGP_DynStreamFlag_SKIP_REVEAL_WHEN_UNHIDDEN`.

For effects running in Premiere Pro CS6, we have added the ability to get 32-bit float and YUV frames from `PF_CHECKOUT_PARAM`.

---

## …and what was new before CS6?

For history this far back, see obsolete copies of the SDK (which we don't provide; if someone wants you do develop for antique software, they'd best provide the SDK).
