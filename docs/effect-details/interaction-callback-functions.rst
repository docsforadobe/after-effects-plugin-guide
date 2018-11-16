.. _effect-details/interaction-callback-functions:

Interaction Callback Functions
################################################################################

While the un-macro'd function pointers are provided in :ref:`effect-basics/PF_InData`, use the provided macros to access them. See how stringent we are about deprecating macro usage? Let's let this be our little secret.

----

.. _effect-details/interaction-callback-functions.interaction-callbacks:

Interaction Callbacks
================================================================================

+-----------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|        **Function**         |                                                                                                                    **Purpose**                                                                                                                    |
+=============================+===================================================================================================================================================================================================================================================+
| ``PF_ADD_PARAM``            | Enumerate your plug-in's parameters to After Effects during :ref:`PF_Cmd_PARAM_SETUP <effect-basics/command-selectors.global-selectors>`, using multiple calls to this function.                                                                  |
|                             |                                                                                                                                                                                                                                                   |
|                             | Note: Failing to completely clear out a PF_ParamDef prior to PF_ADD_PARAM() can cause many problems.                                                                                                                                              |
|                             | Always use ``AEFX_CLR_STRUCT`` before adding parameters.                                                                                                                                                                                          |
|                             |                                                                                                                                                                                                                                                   |
|                             | ::                                                                                                                                                                                                                                                |
|                             |                                                                                                                                                                                                                                                   |
|                             |   PF_Err PF_ADD_PARAM (                                                                                                                                                                                                                           |
|                             |     PF_InData       *in_data,                                                                                                                                                                                                                     |
|                             |     PF_ParamIndex   index,                                                                                                                                                                                                                        |
|                             |     PF_ParamDefPtr  def);                                                                                                                                                                                                                         |
|                             |                                                                                                                                                                                                                                                   |
|                             | We provide convenience macros for specific parameter types, in Utils/ Param_Utils.h:                                                                                                                                                              |
|                             |                                                                                                                                                                                                                                                   |
|                             |   - ``PF_ADD_COLOR``,                                                                                                                                                                                                                             |
|                             |   - ``PF_ADD_ARBITRARY``,                                                                                                                                                                                                                         |
|                             |   - ``PF_ADD_SLIDER``,                                                                                                                                                                                                                            |
|                             |   - ``PF_ADD_FIXED``,                                                                                                                                                                                                                             |
|                             |   - ``PF_ADD_FLOAT_SLIDERX``,                                                                                                                                                                                                                     |
|                             |   - ``PF_ADD_CHECKBOXX``,                                                                                                                                                                                                                         |
|                             |   - ``PF_ADD_BUTTON``,                                                                                                                                                                                                                            |
|                             |   - ``PF_ADD_ANGLE``,                                                                                                                                                                                                                             |
|                             |   - ``PF_ADD_NULL``,                                                                                                                                                                                                                              |
|                             |   - ``PF_ADD_LAYER``,                                                                                                                                                                                                                             |
|                             |   - ``PF_ADD_255_SLIDER``,                                                                                                                                                                                                                        |
|                             |   - ``PF_ADD_PERCENT``,                                                                                                                                                                                                                           |
|                             |   - ``PF_ADD_POINT``,                                                                                                                                                                                                                             |
|                             |   - ``PF_ADD_POINT_3D``,                                                                                                                                                                                                                          |
|                             |   - ``PF_ADD_TOPICX``,                                                                                                                                                                                                                            |
|                             |   - ``PF_END_TOPIC``,                                                                                                                                                                                                                             |
|                             |   - ``PF_ADD_POPUPX``,                                                                                                                                                                                                                            |
|                             |   - ``PF_ADD_FLOAT_SLIDERX_DISABLED``                                                                                                                                                                                                             |
+-----------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_ABORT``                | Returns non-zero if the user has cancelled; return that value to After Effects.                                                                                                                                                                   |
|                             | Wrap your render routine in a "while abort has not been requested" while loop.                                                                                                                                                                    |
|                             |                                                                                                                                                                                                                                                   |
|                             | ::                                                                                                                                                                                                                                                |
|                             |                                                                                                                                                                                                                                                   |
|                             |   PF_Err PF_ABORT (PF_InData *in_data);                                                                                                                                                                                                           |
+-----------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_PROGRESS``             | Displays a progress bar during processing; current and total describe the percentage complete.                                                                                                                                                    |
|                             | Returns non-zero if you should suspend or abort your current processing; return that value to After Effects.                                                                                                                                      |
|                             | Call once per scanline, unless your effect is very slow.                                                                                                                                                                                          |
|                             |                                                                                                                                                                                                                                                   |
|                             | If total is 0, PF_ABORT is used instead (presenting the user with different choices).                                                                                                                                                             |
|                             |                                                                                                                                                                                                                                                   |
|                             | ::                                                                                                                                                                                                                                                |
|                             |                                                                                                                                                                                                                                                   |
|                             |   PF_Err PF_PROGRESS (                                                                                                                                                                                                                            |
|                             |     PF_InData  *in_data,                                                                                                                                                                                                                          |
|                             |     A_long     current,                                                                                                                                                                                                                           |
|                             |     A_long     total );                                                                                                                                                                                                                           |
+-----------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_CHECKOUT_PARAM``       | Obtains parameter values, or the source video layer, at a specified time. After Effects makes caching decisions based on the checkout state of parameters.                                                                                        |
|                             |                                                                                                                                                                                                                                                   |
|                             | Allocate a new :ref:`effect-basics/PF_ParamDef` to hold the result; those passed to the plug-in are read-only.                                                                                                                                    |
|                             | If you check out a layer parameter that's set to <none>, the layer returned will be filled with zeros.                                                                                                                                            |
|                             | Masks are not included with checked-out layers.                                                                                                                                                                                                   |
|                             |                                                                                                                                                                                                                                                   |
|                             | Do not check out layer parameters during UI event handling.                                                                                                                                                                                       |
|                             |                                                                                                                                                                                                                                                   |
|                             | ::                                                                                                                                                                                                                                                |
|                             |                                                                                                                                                                                                                                                   |
|                             |   PF_Err PF_CHECKOUT_PARAM (                                                                                                                                                                                                                      |
|                             |     PF_InData      *in_data,                                                                                                                                                                                                                      |
|                             |     PF_ParamIndex  index,                                                                                                                                                                                                                         |
|                             |     A_long         what_time,                                                                                                                                                                                                                     |
|                             |     A_long         step,                                                                                                                                                                                                                          |
|                             |     A_long         time_scale,                                                                                                                                                                                                                    |
|                             |     PF_ParamDef    *param);                                                                                                                                                                                                                       |
|                             |                                                                                                                                                                                                                                                   |
|                             | If checking out the source layer, a deinterlaced frame will be returned. If you ask for the time that references the upper field, you will receive back the upper field with a filter used to generate the extra scanlines.                       |
|                             | For example, assuming line 0 and 2 are upper fields, and line 1 is a lower field, if you check out the upper fields, line 0 and 2 will be passed back directly from the source footage, and line 1 will be calculated by averaging lines 0 and 2. |
|                             | If you want to reassemble a full resolution source frame with both fields present, you can call PF_CHECKOUT_PARAM twice to get both fields, and reinterlace the footage.                                                                          |
|                             |                                                                                                                                                                                                                                                   |
|                             | What happens when checking out a layer at a time that is not frame-aligned? All items have essentially infinite time resolution, so when asking for a time at any value, AE renders the item at that time.                                        |
|                             | For a composition, that involves interpolating all of the keyframes values to the subframe time.                                                                                                                                                  |
|                             | For footage, AE returns a full image that corresponds to the time asked, which is the nearest-to-left frame.                                                                                                                                      |
|                             | If the user has frame-blending on that layer, an interpolated frame is generated.                                                                                                                                                                 |
+-----------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_CHECKIN_PARAM``        | Balance every ``PF_CHECKOUT_PARAM``, with a ``PF_CHECKIN_PARAM``.                                                                                                                                                                                 |
|                             |                                                                                                                                                                                                                                                   |
|                             | Not doing so causes dismal performance and leaks memory. Once checked in, the fields in the :ref:`effect-basics/PF_ParamDef` will no longer be valid.                                                                                             |
|                             |                                                                                                                                                                                                                                                   |
|                             | ::                                                                                                                                                                                                                                                |
|                             |                                                                                                                                                                                                                                                   |
|                             |   PF_Err PF_CHECKIN_PARAM (                                                                                                                                                                                                                       |
|                             |     PF_InData    *in_data,                                                                                                                                                                                                                        |
|                             |     PF_ParamDef  *param );                                                                                                                                                                                                                        |
+-----------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_REGISTER_UI``          | Register a custom user interface element. See :ref:`effect-ui-events/effect-ui-events`.                                                                                                                                                           |
|                             |                                                                                                                                                                                                                                                   |
|                             | ::                                                                                                                                                                                                                                                |
|                             |                                                                                                                                                                                                                                                   |
|                             |   PF_Err PF_REGISTER_UI (                                                                                                                                                                                                                         |
|                             |     PF_InData        *in_data,                                                                                                                                                                                                                    |
|                             |     PF_CustomUIInfo  *cust_info );                                                                                                                                                                                                                |
+-----------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_CHECKOUT_LAYER_AUDIO`` | Given an index, start_time, duration, time_scale, rate, bytes_per_sample, num_channels, and fmt_signed, After Effects will return a corresponding PF_LayerAudio.                                                                                  |
|                             | After Effects will perform any necessary resampling.                                                                                                                                                                                              |
|                             |                                                                                                                                                                                                                                                   |
|                             | ::                                                                                                                                                                                                                                                |
|                             |                                                                                                                                                                                                                                                   |
|                             |   PF_Err PF_CHECKOUT_LAYER_AUDIO (                                                                                                                                                                                                                |
|                             |     PF_InData      *in_data,                                                                                                                                                                                                                      |
|                             |     PF_ParamIndex  index,                                                                                                                                                                                                                         |
|                             |     A_long         start_time,                                                                                                                                                                                                                    |
|                             |     A_long         duration,                                                                                                                                                                                                                      |
|                             |     A_u_long       time_scale,                                                                                                                                                                                                                    |
|                             |     PF_UFixed      rate,                                                                                                                                                                                                                          |
|                             |     A_long         bytes_per_sample,                                                                                                                                                                                                              |
|                             |     A_long         num_channels,                                                                                                                                                                                                                  |
|                             |     A_long         fmt_signed,                                                                                                                                                                                                                    |
|                             |     PF_LayerAudio  *audio);                                                                                                                                                                                                                       |
+-----------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_CHECKIN_LAYER_AUDIO``  | Balance all calls to PF_CHECKOUT_LAYER_AUDIO, regardless of error conditions, with matching calls to PF_CHECKIN_LAYER_AUDIO.                                                                                                                      |
|                             |                                                                                                                                                                                                                                                   |
|                             | ::                                                                                                                                                                                                                                                |
|                             |                                                                                                                                                                                                                                                   |
|                             |   PF_Err PF_CHECKIN_LAYER_AUDIO (                                                                                                                                                                                                                 |
|                             |     PF_InData      *in_data,                                                                                                                                                                                                                      |
|                             |     PF_LayerAudio  audio );                                                                                                                                                                                                                       |
+-----------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_GET_AUDIO_DATA``       | Returns information about the PF_LayerAudio.                                                                                                                                                                                                      |
|                             |                                                                                                                                                                                                                                                   |
|                             | All the parameters after audio are optional; pass 0 for any value in which you aren't interested. rate0 is unsigned, and fmt_signed0 should be non-zero for signed, zero for unsigned.                                                            |
|                             | This callback is for visual effects that read audio information. To *alter* audio, write an audio filter.                                                                                                                                         |
|                             |                                                                                                                                                                                                                                                   |
|                             | ::                                                                                                                                                                                                                                                |
|                             |                                                                                                                                                                                                                                                   |
|                             |   PF_Err PF_GET_AUDIO_DATA (                                                                                                                                                                                                                      |
|                             |     PF_InData        *in_data,                                                                                                                                                                                                                    |
|                             |     PF_LayerAudio    audio,                                                                                                                                                                                                                       |
|                             |     PF_SndSamplePtr  *data0,                                                                                                                                                                                                                      |
|                             |     A_long           *num_samples0,                                                                                                                                                                                                               |
|                             |     PF_UFixed        *rate0,                                                                                                                                                                                                                      |
|                             |     A_long           *bytes_per_sample0,                                                                                                                                                                                                          |
|                             |     A_long           *num_channels0,                                                                                                                                                                                                              |
|                             |     A_long           *fmt_signed0);                                                                                                                                                                                                               |
+-----------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

----

Parameter Checkout vs. Param Zero
================================================================================

Effects are applied to an image in order from 0 to n within the Effect Control (and Composition) panel.

The output from effect[n-1] is the input (:ref:`param[0] <effect-basics/PF_ParamDef.param-zero>`) of effect[n].

On the other hand, when a normal effect checks out a layer using ``PF_CHECKOUT_PARAM``, it receives the raw (un-effected) source layer, regardless of its order.

However, when a :ref:`smartfx/smartfx` effect checks out its input parameter (params[0]), previous effects *are* applied.

----

Parameter Checkout Behavior
================================================================================

Regardless of whether the layer in and out point have been trimmed, you will get valid frames from the start of the source footage to the end, and then transparent before and after that.

Layer params with a lower frame rate than the composition in which they're checked out are only refreshed as often as necessitated by the lower frame rate.

A 10fps layer checked out in a 30fps composition will only need to be refreshed every third frame. if your effect wants to change it's output every frame despite the static input layer, you'd need to set :ref:`PF_Outflag_NON_PARAM_VARY <effect-basics/PF_OutData.PF_OutFlags>`.

When an effect checks out a continuously-rasterized Adobe Illustrator layer, After Effects renders the Illustrator layer with geometrics applied, in a composition-sized buffer.

----

Parameter Checkout And Re-Entrancy
================================================================================

Plug-ins that check out layers at different times can generate re-entrant behavior. Consider an instance where the Checkout sample plug-in is applied to a layer in composition B, and B is pre-composed into composition A where Checkout is applied to it as well.

When composition A is rendered, Checkout[A] will be sent *PF_Cmd_RENDER*, during which it checks out a layer (composition B) from a time other than the current time.

In order to provide that checked-out layer, After Effects sends *PF_Cmd_RENDER* to ``Checkout[B]``.

Presto, recursion!

If you're going to check out parameters, your effects must handle re-entrant render requests appropriately.

Don't use globals, or read or write static variables...but you weren't going to anyway, right?

----

Progress During Iteration
================================================================================

After Effects strives to be as responsive as possible to user interaction, even while rendering. Do the same through appropriate use of PF_ITERATE(). For example, perhaps you're using a PF_ITERATE'd function three times during your response to ``PF_Cmd_RENDER``.

In this case, you'd start off with::

  lines_per_iterateL = in_data>extent_hint.top - in_data>extent_hint.bottom;
  total_linesL = 3 * lines_per_iterateL;
  lines_so_farL = 0;

After each iteration, you'd add the already-completed lines to the current position::

  suites.iterate8suite()>iterate( lines_so_farL,
                                  total_linesL,
                                  input_worldP,
                                  &output>extent_hint,
                                  refcon,
                                  WhizBangPreProcessFun,
                                  output_worldP);

  lines_so_farL += lines_per_iterateL;

  ERR(PF_PROGRESS(lines_so_farL, total_linesL));

  suites.iterate8suite()>iterate( lines_so_farL,
                                  total_linesL,
                                  input_worldP,
                                  &output>extent_hint,
                                  refcon,
                                  WhizBangRenderFunc,
                                  output_worldP);

  lines_so_far += lines_per_iterateL;

  ERR(PF_PROGRESS(lines_so_farL, total_linesL));

  suites.iterate8suite()>iterate( lines_so_farL,
                                  total_linesL,
                                  input_worldP,
                                  &output>extent_hint,
                                  refcon,
                                  WhizBangPostProcessFunc,
                                  output_worldP);

  ERR(PF_PROGRESS(lines_so_farL, total_linesL));
