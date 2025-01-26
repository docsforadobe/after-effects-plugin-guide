.. _effect-basics/command-selectors:

Command Selectors
################################################################################

Commands are, simply, what After Effects wants your effect to do.

Responses to some selectors are required; most are optional, though recall that we did add them for a *reason*...

With each command selector sent, effects receive information from After Effects in :ref:`effect-basics/PF_InData`, input and parameter values in PF_ParamDef[] (an array of parameter descriptions including the input layer), and access to callbacks and function suites.

They send information back to After Effects in :ref:`effect-basics/PF_OutData`, and (when appropriate) render output to a PF_LayerDef, also called a :ref:`PF_EffectWorld <effect-basics/PF_EffectWorld>`.

During events, they receive event-specific information in :ref:`effect-ui-events/PF_EventExtra`.

----

.. _effect-basics/calling-sequence:

Calling Sequence
================================================================================

Only the first few command selectors are predictable; the rest of the calling sequence is dictated by user action.

When first applied, a plug-in receives ``PF_Cmd_GLOBAL_SETUP``, then ``PF_Cmd_PARAM_SETUP``. Each time the user adds the effect to a layer, ``PF_Cmd_SEQUENCE_SETUP`` is sent.

For each frame rendered by a basic non-SmartFX effect, After Effects sends ``PF_Cmd_FRAME_SETUP``, then ``PF_Cmd_RENDER``, then ``PF_Cmd_FRAME_SETDOWN``.

All effect plug-ins must respond to ``PF_Cmd_RENDER``

For SmartFX, ``PF_Cmd_SMART_PRE_RENDER`` may be sent any number of times, before a single ``PF_Cmd_SMART_RENDER`` is sent.

``PF_Cmd_SEQUENCE_SETDOWN`` is sent on exit, when the user removes an effect or closes the project. ``PF_Cmd_SEQUENCE_RESETUP`` is sent when a project is loaded or when the layer to which it's applied changes. ``PF_Cmd_SEQUENCE_FLATTEN`` is sent when the After Effects project is written out to disk.

``PF_Cmd_ABOUT`` is sent when the user chooses *About…* from the Effect Controls Window (ECW).

``PF_Cmd_GLOBAL_SETDOWN`` is sent when After Effects closes, or when the last instance of the effect is removed. Do not rely on this message to determine when your plug-in is being removed from memory; use OS-specific entry points.

----

Command Selectors Table
================================================================================

.. _effect-basics/command-selectors.global-selectors:

Global Selectors
********************************************************************************

All plug-ins must respond to these selectors.

+---------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------+
|       **Selector**        |                                                                      **Response**                                                                       |
+===========================+=========================================================================================================================================================+
| ``PF_Cmd_ABOUT``          | Display a dialog describing the plug-in. Populate out_data>return_msg and After Effects will display it in a simple modal dialog.                       |
|                           |                                                                                                                                                         |
|                           | Include your plug- in's version information in the dialog. On macOS, the current resource file will be set to your effects module during this selector. |
+---------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Cmd_GLOBAL_SETUP``   | Set any required flags and ``PF_OutData`` fields (including out_data>my_version) to describe your plug-in's behavior.                                   |
+---------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Cmd_GLOBAL_SETDOWN`` | Free all global data (only required if you allocated some).                                                                                             |
+---------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Cmd_PARAM_SETUP``    | Describe your parameters and register them using :ref:`PF_ADD_PARAM <effect-details/interaction-callback-functions.interaction-callbacks>`.             |
|                           |                                                                                                                                                         |
|                           | Also, register custom user interface elements.                                                                                                          |
|                           |                                                                                                                                                         |
|                           | Set :ref:`PF_OutData>num_params <effect-basics/PF_OutData>` to match your parameter count.                                                              |
+---------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _effect-basics/command-selectors.sequence-selectors:

Sequence Selectors
********************************************************************************

These control sequence data handling.

+-----------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|        **Selector**         |                                                                                           **Response**                                                                                            |
+=============================+===================================================================================================================================================================================================+
| ``PF_Cmd_SEQUENCE_SETUP``   | Allocate and initialize any sequence-specific data. Sent when the effect is first applied. :ref:`effect-basics/PF_InData` is initialized at this time.                                            |
+-----------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Cmd_SEQUENCE_RESETUP`` | Re-create (usually unflatten) sequence data. Sent after sequence data is read from disk, during pre-composition, or when the effect is copied;                                                    |
|                             |                                                                                                                                                                                                   |
|                             | After Effects flattens sequence data before duplication. During duplication, ``PF_Cmd_SEQUENCE_RESETUP`` is sent for both the old and new sequences.                                              |
|                             |                                                                                                                                                                                                   |
|                             | Don't expect a ``PF_Cmd_SEQUENCE_FLATTEN`` between ``PF_Cmd_SEQUENCE_RESETUPs``.                                                                                                                  |
+-----------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Cmd_SEQUENCE_FLATTEN`` | Sent when saving and when duplicating the sequence. Flatten sequence data containing pointers or handles so it can be written to disk.                                                            |
|                             |                                                                                                                                                                                                   |
|                             | This will saved with the project file. Free the unflat data and set the ``out_data>sequence_data`` to point to the new flattened data. Flat data must be correctly byte-ordered for file storage. |
|                             |                                                                                                                                                                                                   |
|                             | As of 6.0, if an effect's sequence data has recently been flattened, the effect may be deleted without receiving an additional ``PF_Cmd_SEQUENCE_SETDOWN``.                                       |
|                             |                                                                                                                                                                                                   |
|                             | In this case, After Effects will dispose of your flat sequence data.                                                                                                                              |
+-----------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Cmd_SEQUENCE_SETDOWN`` | Free all sequence data.                                                                                                                                                                           |
+-----------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _effect-basics/command-selectors.frame-selectors:

Frame Selectors
********************************************************************************

Passed for each frame (or set of audio samples) to be rendered by your plug-in.

+-----------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|        **Selector**         |                                                                                                                           **Response**                                                                                                                            |
+=============================+===================================================================================================================================================================================================================================================================+
| ``PF_Cmd_FRAME_SETUP``      | Allocate any frame-specific data. This is sent immediately before each frame is rendered, to allow for frame-specific setup data.                                                                                                                                 |
|                             | If your effect changes the size of its output buffer, specify the new output height, width, and relative origin. All parameters except the input layer are valid.                                                                                                 |
|                             |                                                                                                                                                                                                                                                                   |
|                             | If you set width and height to 0, After Effects ignores your response to the following *PF_Cmd_RENDER*.                                                                                                                                                           |
|                             |                                                                                                                                                                                                                                                                   |
|                             | NOTE: If :ref:`PF_Outflag_I_EXPAND_BUFFER <effect-basics/PF_OutData.PF_OutFlags>` is set, you will receive this selector (and *PF_Cmd_FRAME_SETDOWN)* twice, once without *PF_Cmd_RENDER* between them.                                                           |
|                             |                                                                                                                                                                                                                                                                   |
|                             | This is so we know whether or not the given layer will be visible.                                                                                                                                                                                                |
|                             |                                                                                                                                                                                                                                                                   |
|                             | Frame data dates from the days when machines might have 8MB of RAM. Given the calling sequence (above), it's much more efficient to just allocate during *PF_Cmd_RENDER*.                                                                                         |
+-----------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Cmd_RENDER``           | Render the effect into the output, based on the input frame and any parameters.                                                                                                                                                                                   |
|                             |                                                                                                                                                                                                                                                                   |
|                             | This render call can only support 8-bit or 16-bit per channel rendering. 32-bit per channel rendering must be handled in PF_Cmd_SMART_RENDER.                                                                                                                     |
|                             |                                                                                                                                                                                                                                                                   |
|                             | All fields in PF_InData are valid.                                                                                                                                                                                                                                |
|                             |                                                                                                                                                                                                                                                                   |
|                             | If your response to this selector is interrupted (your calls to PF_ABORT or PF_PROGRESS returns an error code), your results will not be used.                                                                                                                    |
|                             |                                                                                                                                                                                                                                                                   |
|                             | You cannot delete frame_data during this selector; you must wait until PF_Cmd_FRAME_SETDOWN.                                                                                                                                                                      |
+-----------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Cmd_FRAME_SETDOWN``    | Free any frame data allocated during PF_Cmd_FRAME_SETUP.                                                                                                                                                                                                          |
+-----------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Cmd_AUDIO_SETUP``      | Sent before every audio render. Request a time span of input audio. Allocate and initialize any sequence-specific data.                                                                                                                                           |
|                             |                                                                                                                                                                                                                                                                   |
|                             | If your effect requires input from a time span other than the output time span, update the startsampL and endsampL field in PF_OutData.                                                                                                                           |
+-----------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Cmd_AUDIO_RENDER``     | Populate :ref:`PF_OutData.dest_snd <effect-basics/PF_OutData>` with effect-ed audio. All fields in PF_InData are valid.                                                                                                                                           |
|                             |                                                                                                                                                                                                                                                                   |
|                             | If your response to this selector is interrupted (your calls to ``PF_ABORT`` or ``PF_PROGRESS`` returns an error code), your results will not be used.                                                                                                            |
+-----------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Cmd_AUDIO_SETDOWN``    | Free memory allocated during PF_Cmd_AUDIO_SETUP.                                                                                                                                                                                                                  |
+-----------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Cmd_SMART_PRE_RENDER`` | SmartFX only. Identify the area(s) of input the effect will need to produce its output, based on whatever criteria the effect implements.                                                                                                                         |
|                             |                                                                                                                                                                                                                                                                   |
|                             | maybe sent up to twice when MediaCore is hosting. The first will come during GetFrameDependencies to collect the inputs.                                                                                                                                          |
|                             |                                                                                                                                                                                                                                                                   |
|                             | The source checkouts can return full frame dimensions here. Once the sources are rendered, if they are different in size than the first call                                                                                                                      |
|                             | then this selector will be emitted a second time with the actual source sizes in order to get a correct output size.                                                                                                                                              |
|                             |                                                                                                                                                                                                                                                                   |
|                             | Note that MediaCore wants all of the output, so PF_PreRenderOutput::max_result_rect will be used.                                                                                                                                                                 |
|                             |                                                                                                                                                                                                                                                                   |
|                             | **New in 16.0**                                                                                                                                                                                                                                                   |
|                             |                                                                                                                                                                                                                                                                   |
|                             | Set ``PF_RenderOutputFlag_GPU_RENDER_POSSIBLE`` in ``PF_PreRenderOutput`` to render on the GPU.                                                                                                                                                                   |
|                             |                                                                                                                                                                                                                                                                   |
|                             | If this flag is not set the requested render is not possible with the requested GPU, because of parameters or render settings.                                                                                                                                    |
|                             |                                                                                                                                                                                                                                                                   |
|                             | The host may re-call PreRender with another what_gpu option (or PF_GPU_Framework_None).                                                                                                                                                                           |
|                             |                                                                                                                                                                                                                                                                   |
|                             | ::                                                                                                                                                                                                                                                                |
|                             |                                                                                                                                                                                                                                                                   |
|                             |   typedef struct {                                                                                                                                                                                                                                                |
|                             |     PF_RenderRequest  output_request; // what the effect is being asked to render                                                                                                                                                                                 |
|                             |     short             bitdepth;       // bitdepth the effect is being driven in (in bpc)                                                                                                                                                                          |
|                             |     const             void *gpu_data; // (new AE 16.0)                                                                                                                                                                                                            |
|                             |     PF_GPU_Framework  what_gpu;       // (new AE 16.0)                                                                                                                                                                                                            |
|                             |     A_u_long          device_index;   // (new AE 16.0) For use in conjunction with PrSDKGPUDeviceSuite                                                                                                                                                            |
|                             |   } PF_PreRenderInput;                                                                                                                                                                                                                                            |
+-----------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Cmd_SMART_RENDER``     | SmartFX only. Perform rendering and provide output for the area(s) the effect was asked to render.                                                                                                                                                                |
+-----------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _effect-basics/command-selectors.messaging:

Messaging
********************************************************************************

The communication channel between After Effects and your plug-in.

+--------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|             **Selector**             |                                                                                  **Response**                                                                                  |
+======================================+================================================================================================================================================================================+
| ``PF_Cmd_EVENT``                     | This selector makes use of the extra parameter; the type of event to be handled is indicated by the e_type field, a member of the structure pointed to by extra.               |
|                                      |                                                                                                                                                                                |
|                                      | See :ref:`effect-ui-events/effect-ui-events`.                                                                                                                                  |
+--------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Cmd_USER_CHANGED_PARAM``        | The user changed a parameter value. You will receive this command only if you've set the ``PF_ParamFlag_SUPERVISE`` flag.                                                      |
|                                      |                                                                                                                                                                                |
|                                      | You modify the parameter to control values, or make one parameter's value affect others. A parameter can be modified by different actions.                                     |
|                                      |                                                                                                                                                                                |
|                                      | ``in_data.current_time`` is set to the time of the frame that the user is looking at in the UI                                                                                 |
|                                      | (internally, the current time of the comp converted into layer time) while they are changing the param that triggered the ``PF_Cmd_USER_CHANGED_PARAM``.                       |
|                                      |                                                                                                                                                                                |
|                                      | It's also the time of a keyframe that is added automatically (if there isn't one already, and the stopwatch is enabled).                                                       |
|                                      |                                                                                                                                                                                |
|                                      | This is usually the same as the value passed for the PF_Cmd_RENDER that follows immediately after (unless caps lock is down), but not necessarily –                            |
|                                      | there could be other comp windows open that cause a render at a different time in response to the changed param.                                                               |
+--------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Cmd_UPDATE_PARAMS_UI``          | The effect controls palette (ECP) needs to be updated. This might occur after opening the ECP or moving to a new time within the composition.                                  |
|                                      |                                                                                                                                                                                |
|                                      | You can modify parameter characteristics (enabling or disabling them, for example) by calling ``PF_UpdateParamUI()``.                                                          |
|                                      |                                                                                                                                                                                |
|                                      | Only cosmetic changes may be made in response to this command. Don't change parameter values while responding to ``PF_Cmd_UPDATE_PARAMS_UI``;                                  |
|                                      | do so during ``PF_Cmd_USER_CHANGED_PARAM`` instead.                                                                                                                            |
|                                      |                                                                                                                                                                                |
|                                      | This command will only be sent regularly if ``PF_OutFlag_SEND_UPDATE_PARAMS_UI`` was set in the PiPL, and during ``PF_Cmd_GLOBAL_SETUP``.                                      |
|                                      |                                                                                                                                                                                |
|                                      | NOTE: Never check out parameters during this selector. Recursive badness is almost guaranteed to result.                                                                       |
+--------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Cmd_DO_DIALOG``                 | Display an options dialog. this is sent when the Options button is clicked (or a menu command has been selected).                                                              |
|                                      |                                                                                                                                                                                |
|                                      | This selector will only be sent if the effect has previously indicated that it has a dialog                                                                                    |
|                                      |                                                                                                                                                                                |
|                                      | (by setting the global ``PF_OutFlag_I_DO_DIALOG`` flag in response to ``PF_Cmd_GLOBAL_SETUP``).                                                                                |
|                                      |                                                                                                                                                                                |
|                                      | In version 3.x, the params passed with ``PF_Cmd_DO_DIALOG`` were invalid.                                                                                                      |
|                                      |                                                                                                                                                                                |
|                                      | This is no longer the case; plug-ins can access non-layer parameters, check out parameters at other times, and perform UI updates during ``PF_Cmd_DO_DIALOG``.                 |
|                                      |                                                                                                                                                                                |
|                                      | They still may not change the parameter's values.                                                                                                                              |
+--------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Cmd_ARBITRARY_CALLBACK``        | Manage your arbitrary data type. You'll only receive this if you've registered a custom data type parameter.                                                                   |
|                                      |                                                                                                                                                                                |
|                                      | The extra parameter indicates which handler function is being called.                                                                                                          |
|                                      |                                                                                                                                                                                |
|                                      | Custom data types are discussed further in :ref:`effect-details/arbitrary-data-parameters.implementing-arbitrary-data`.                                                        |
+--------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Cmd_GET_EXTERNAL_DEPENDENCIES`` | Only sent if ``PF_OutFlag_I_HAVE_EXTERNAL_DEPENDENCIES`` was set during ``PF_Cmd_GLOBAL_SETUP``.                                                                               |
|                                      |                                                                                                                                                                                |
|                                      | Populate a string handle (in the PF_ExtDependenciesExtra pointed to by extra) with a description of your plug-in's dependencies,                                               |
|                                      | making sure to allocate space for the terminating NULL character.                                                                                                              |
|                                      |                                                                                                                                                                                |
|                                      | Return just a ``NULL`` pointer for the string handle if there are no dependencies to report.                                                                                   |
|                                      |                                                                                                                                                                                |
|                                      | If the check type is ``PF_DepCheckType_ALL_DEPENDENCIES``, report everything that might be required for your plug-in to render.                                                |
|                                      |                                                                                                                                                                                |
|                                      | Report only missing items (or a null string if nothing's missing) if the check type is ``PF_DepCheckType_MISSING_DEPENDENCIES``.                                               |
+--------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Cmd_COMPLETELY_GENERAL``        | Respond to an AEGP. The extra parameter points to whatever parameter the AEGP sent.                                                                                            |
|                                      |                                                                                                                                                                                |
|                                      | AEGPs can only communicate with effects which respond to this selector.                                                                                                        |
+--------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Cmd_QUERY_DYNAMIC_FLAGS``       | Sent only to plug-ins which have specified ``PF_OutFlag2_SUPPORTS_QUERY_DYNAMIC_FLAGS`` in ``PF_OutFlags2``, in their PiPL and during ``PF_Cmd_GLOBAL_SETUP``.                 |
|                                      |                                                                                                                                                                                |
|                                      | With all of the dynamic flags, if you will ever change them during this command, you must have set the flag on during ``PF_Cmd_GLOBAL_SETUP``.                                 |
|                                      |                                                                                                                                                                                |
|                                      | This selector will be sent at arbitrary times.                                                                                                                                 |
|                                      |                                                                                                                                                                                |
|                                      | In response, the effect should access its (non-layer) parameters using ``PF_CHECKOUT_PARAM``, and                                                                              |
|                                      | decide whether any of the flags that support ``PF_Cmd_QUERY_DYNAMIC_FLAGS`` should be set, such as:                                                                            |
|                                      |                                                                                                                                                                                |
|                                      |   - ``PF_OutFlag_WIDE_TIME_INPUT``                                                                                                                                             |
|                                      |   - ``PF_OutFlag_NON_PARAM_VARY``                                                                                                                                              |
|                                      |   - ``PF_OutFlag_PIX_INDEPENDENT``                                                                                                                                             |
|                                      |   - ``PF_OutFlag_I_USE_SHUTTER_ANGLE``                                                                                                                                         |
|                                      |   - ``PF_OutFlag2_I_USE_3D_CAMERA``                                                                                                                                            |
|                                      |   - ``PF_OutFlag2_I_USE_3D_LIGHTS``                                                                                                                                            |
|                                      |   - ``PF_OutFlag2_DOESNT_NEED_EMPTY_PIXELS``                                                                                                                                   |
|                                      |   - ``PF_OutFlag2_REVEALS_ZERO_ALPHA``                                                                                                                                         |
|                                      |   - ``PF_OutFlag2_DEPENDS_ON_UNREFERENCED_MASKS``                                                                                                                              |
|                                      |   - ``PF_OutFlag2_OUTPUT_IS_WATERMARKED``                                                                                                                                      |
|                                      |                                                                                                                                                                                |
|                                      | After Effects uses this information for caching and optimization purposes, so try to respond as quickly as possible.                                                           |
+--------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Cmd_GPU_DEVICE_SETUP``          | This selector can be called at any time by the host. It will be called not more than once for each GPU device.                                                                 |
|                                      |                                                                                                                                                                                |
|                                      | Multiple GPU devices may be in the setup state at one time.                                                                                                                    |
|                                      |                                                                                                                                                                                |
|                                      | It will be called after GlobalSetup and before SequenceSetup.                                                                                                                  |
|                                      |                                                                                                                                                                                |
|                                      | The intent is for the effect to do GPU initialization if necessary and to give the effect an opportunity to                                                                    |
|                                      | opt out of a GPU device based solely on the properties of that device, and not any render context (frame size, etc).                                                           |
|                                      |                                                                                                                                                                                |
|                                      | If the effect rejects the GPU device it will get called for CPU render.                                                                                                        |
|                                      |                                                                                                                                                                                |
|                                      | ``PF_InData::what_gpu != PF_GPU_Framework_None`` is expected.                                                                                                                  |
|                                      |                                                                                                                                                                                |
|                                      | Effect is expected to set one or both of the ``PF_OutFlag2_SUPPORTS_GPU_RENDER_Fxx`` flags in ``PF_OutData::out_flags2`` if the device and framework in what_gpu is supported. |
|                                      |                                                                                                                                                                                |
|                                      | Note that only ``PF_OutFlag2_SUPPORTS_GPU_RENDER_F32`` will be in AE 16.0.                                                                                                     |
|                                      |                                                                                                                                                                                |
|                                      | Effects that do not set flags here will NOT be considered to support GPU rendering for any of these devices.                                                                   |
|                                      |                                                                                                                                                                                |
|                                      | ``PF_GPUDeviceSetupOutput::gpu_data`` is a plug-in owned pointer that must be released with a the ``PF_Cmd_GPU_DEVICE_SETDOWN`` selector.                                      |
|                                      |                                                                                                                                                                                |
|                                      | This pointer is also available at render time.                                                                                                                                 |
+--------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Cmd_GPU_DEVICE_SETDOWN``        | Release any resources associated with gpu_data. In AE this will be called just before GPU device release.                                                                      |
|                                      |                                                                                                                                                                                |
|                                      | ::                                                                                                                                                                             |
|                                      |                                                                                                                                                                                |
|                                      |   typedef struct {                                                                                                                                                             |
|                                      |     void              *gpu_data;  // effect must dispose.                                                                                                                      |
|                                      |     PF_GPU_Framework  what_gpu;                                                                                                                                                |
|                                      |     A_u_long          device_index; // For use in conjunction with PrSDKGPUDeviceSuite                                                                                         |
|                                      |   } PF_GPUDeviceSetdownInput;                                                                                                                                                  |
|                                      |                                                                                                                                                                                |
|                                      |   typedef struct {                                                                                                                                                             |
|                                      |     PF_GPUDeviceSetdownInput  input;                                                                                                                                           |
|                                      |   } PF_GPUDeviceSetdownExtra;                                                                                                                                                  |
+--------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Cmd_GPU_SMART_RENDER_GPU``      | GPU equivalent to the existing ``PF_Cmd_SMART_RENDER`` selector.                                                                                                               |
|                                      |                                                                                                                                                                                |
|                                      | At render time, either the ``PF_Cmd_SMART_RENDER`` or the ``PF_Cmd_SMART_RENDER_GPU`` selector will be called,                                                                 |
|                                      | depending on whether the effect is expected to produce a CPU or GPU frame as output.                                                                                           |
|                                      |                                                                                                                                                                                |
|                                      | ``PF_Cmd_SMART_RENDER_GPU`` will only be called when ``what_gpu != PF_GPU_Framework_None``, and has effects on any input / output ``PF_LayerDef``'s.                           |
|                                      |                                                                                                                                                                                |
|                                      | All frame check-ins and check-outs will operate on GPU frames when this selector is in progress. Note ``PF_Cmd_SMART_RENDER`` shares the ``Extra`` structs.                    |
|                                      |                                                                                                                                                                                |
|                                      | ::                                                                                                                                                                             |
|                                      |                                                                                                                                                                                |
|                                      |   typedef struct {                                                                                                                                                             |
|                                      |     PF_RenderRequest  output_request;   // what the effect is being asked to render                                                                                            |
|                                      |     short             bitdepth;         // bitdepth the effect is being driven in (in bpc)                                                                                     |
|                                      |     void              *pre_render_data; // passed back from value placed in extra->output->pre_render_data during PF_Cmd_PRE_RENDER                                            |
|                                      |     const void        *gpu_data;        // (new AE 16.0)                                                                                                                       |
|                                      |     PF_GPU_Framework  what_gpu;         // (new AE 16.0)                                                                                                                       |
|                                      |     A_u_long          device_index;     // (new AE 16.0)                                                                                                                       |
|                                      |   } PF_SmartRenderInput;                                                                                                                                                       |
|                                      |                                                                                                                                                                                |
|                                      |   typedef struct {                                                                                                                                                             |
|                                      |     PF_SmartRenderInput *input;                                                                                                                                                |
|                                      |     PF_SmartRenderCallbacks *cb;                                                                                                                                               |
|                                      |   } PF_SmartRenderExtra;                                                                                                                                                       |
|                                      |                                                                                                                                                                                |
|                                      | The ``what_gpu`` and ``device_index`` fields are in the extra input for GPU-related selectors indicates to the plug-in the GPU framework to be used for rendering.             |
|                                      |                                                                                                                                                                                |
|                                      | Input and output buffers will be prepared on this framework and device.                                                                                                        |
|                                      |                                                                                                                                                                                |
|                                      | The device, context, command queue, and other associated GPU state can be queried with ``PrSDKGPUDeviceSuite::GetDeviceInfo``.                                                 |
|                                      |                                                                                                                                                                                |
|                                      | ``what_gpu`` will be the same between ``PF_Cmd_SMART_PRE_RENDER`` and ``PF_Cmd_SMART_RENDER_GPU`` selector calls.                                                              |
+--------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

----

What's The Difference?
================================================================================

There is a subtle difference between ``PF_Cmd_USER_CHANGED_PARAM`` and ``PF_Cmd_UPDATE_PARAMS_UI``.

Effects need to distinguish between the user actually changing a parameter value (``PF_Cmd_USER_CHANGED_PARAM``), and just scrubbing around the timeline (``PF_Cmd_UPDATE_PARAMS_UI``), which is also sent when the plug-in is first loaded).

Only the first few command selectors are predictable; the rest of the calling sequence is dictated by user action.

When first applied, a plug-in receives ``PF_Cmd_GLOBAL_SETUP``, then ``PF_Cmd_PARAM_SETUP``. Each time the user adds the effect to a layer, ``PF_Cmd_SEQUENCE_SETUP`` is sent.

For each frame rendered by a basic non-SmartFX effect, After Effects sends ``PF_Cmd_FRAME_SETUP``, then ``PF_Cmd_RENDER``, then ``PF_Cmd_FRAME_SETDOWN``. All effect plug-ins must respond to ``PF_Cmd_RENDER``\ *.*

For SmartFX, ``PF_Cmd_SMART_PRE_RENDER`` may be sent any number of times, before a single ``PF_Cmd_SMART_RENDER`` is sent.

``PF_Cmd_SEQUENCE_SETDOWN`` is sent on exit, when the user removes an effect or closes the project. ``PF_Cmd_SEQUENCE_RESETUP`` is sent when a project is loaded or when the layer to which it's applied changes. ``PF_Cmd_SEQUENCE_FLATTEN`` is sent when the After Effects project is written out to disk.

``PF_Cmd_ABOUT`` is sent when the user chooses *About…* from the Effect Controls Window (ECW).

``PF_Cmd_GLOBAL_SETDOWN`` is sent when After Effects closes, or when the last instance of the effect is removed. Do not rely on this message to determine when your plug-in is being removed from memory; use OS-specific entry points.
