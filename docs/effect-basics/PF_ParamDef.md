.. _effect-basics/PF_ParamDef:

PF_ParamDef
################################################################################

After Effects passes effects an array of PF_ParamDefs with each selector, describing the plug-in's parameters at the current time. The values in the params array are only valid during some selectors (this is noted in the :ref:`selector descriptions <effect-basics/calling-sequence>`).

----

.. _effect-basics/PF_ParamDef.param-zero:

Param Zero
================================================================================

The first parameter, params[0], is the input image (a :ref:`effect-basics/PF_EffectWorld`) to which the effect should be applied.

----

The Rest Of The Parameters
================================================================================

All parameter types are represented by a PF_ParamDef. Unions are used, so that only the pertinent parts of the PF_ParamDef need be (or should be) populated.

PF_ParamDef Members
================================================================================

+-----------------------------------------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
|                             **Data Type**                             |     **Name**     |                                                                       **Description**                                                                       |
+=======================================================================+==================+=============================================================================================================================================================+
| ``A_long``                                                            | ``id``           | The ID of this parameter. You can re-order parameters in future versions of your plug-in and not cause users to re-apply your effect,                       |
|                                                                       |                  |                                                                                                                                                             |
|                                                                       |                  | if you maintain the parameter's ID across versions.                                                                                                         |
+-----------------------------------------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_ChangeFlags``                                                    | ``change_flags`` | Set if you've changed a parameter value. Only valid during drag (not click!) events,                                                                        |
|                                                                       |                  |                                                                                                                                                             |
|                                                                       |                  | :ref:`PF_Cmd_USER_CHANGED_PARAM <effect-basics/command-selectors.messaging>` or :ref:`PF_Cmd_UPDATE_PARAMS_UI <effect-basics/command-selectors.messaging>`. |
+-----------------------------------------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
| :ref:`PF_ParamUIFlags <effect-basics/PF_ParamDef.parameter-ui-flags>` | ``ui_flags``     | Specify a parameter's UI behavior before adding; only                                                                                                       |
|                                                                       |                  |                                                                                                                                                             |
|                                                                       |                  | ``PF_PUI_DISABLED`` may be set during event handling.                                                                                                       |
+-----------------------------------------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``A_short``                                                           | ``ui_width``     | Width of the parameter's user interface (for non-standard parameters only).                                                                                 |
+-----------------------------------------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``A_short``                                                           | ``ui_height``    | Height of the parameter's user interface (for non-standard parameters only).                                                                                |
+-----------------------------------------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
| :ref:`PF_ParamType <effect-basics/parameters.parameter-types>`        | ``param_type``   | Type of parameter.                                                                                                                                          |
+-----------------------------------------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``A_char[32]````                                                      | ``name``         | Name of parameter. Can be changed during event handling.                                                                                                    |
|                                                                       |                  |                                                                                                                                                             |
|                                                                       |                  | Yes, longer parameter names have been requested since After Effects 1.0.                                                                                    |
|                                                                       |                  |                                                                                                                                                             |
|                                                                       |                  | Think of adequately describing your world-altering effect in 31 mere characters as a language challenge, like haiku.                                        |
+-----------------------------------------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
| :ref:`PF_ParamFlags <effect-basics/PF_ParamDef.parameter-flags>`      | ``flags``        | Specify a parameter's UI behavior before adding; only ``PF_ParamFlag_COLLAPSE_TWIRLY`` may be set during event handling.                                    |
+-----------------------------------------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_ParamDefUnion``                                                  | ``u``            | A union of all possible :ref:`effect-basics/parameters.parameter-types`.                                                                                    |
|                                                                       |                  |                                                                                                                                                             |
|                                                                       |                  | Only the type specified by ``param_type`` contains meaningful data.                                                                                         |
+-----------------------------------------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+

----

.. _effect-basics/PF_ParamDef.parameter-ui-flags:

Parameter UI Flags
================================================================================

Control a parameter's user interface with these flags.

Don't confuse UI flags with behavior flags; they reside in different fields within your parameter's definition, and will cause unpredictable behavior if misapplied.

+-------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|           **Flag**            |                                                                                 **Description**                                                                                 |
+===============================+=================================================================================================================================================================================+
| ``PF_PUI_TOPIC``              | Set this flag if you handle ``PF_Cmd_EVENTs`` for the "topic" of the parameter.                                                                                                 |
|                               |                                                                                                                                                                                 |
|                               | The "topic" is the portion of the param UI in the Effect Controls Window (ECW) that is still visible when the twirly-arrow is twirled up for that param.                        |
|                               |                                                                                                                                                                                 |
|                               | If you set this flag, you must also set ``PF_OutFlag_CUSTOM_UI`` at PF_Cmd_GLOBAL_SETUP time.                                                                                   |
+-------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_PUI_CONTROL``            | Set this flag if you handle ``PF_Cmd_EVENTs`` for the control area (area that becomes invisible when you twirl up a parameter's spinner) in the ECP.                            |
|                               |                                                                                                                                                                                 |
|                               | If you set this flag, you must also set ``PF_OutFlag_CUSTOM_UI`` at ``PF_Cmd_GLOBAL_SETUP`` time.                                                                               |
|                               |                                                                                                                                                                                 |
|                               | See :ref:`effect-ui-events/effect-ui-events` for more details.                                                                                                                  |
+-------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_PUI_STD_CONTROL_ONLY``   | Set this flag if you want the standard control only -- No data stream will be associated with this parameter, and thus no keyframes will be available in the Timeline panel.    |
|                               |                                                                                                                                                                                 |
|                               | You might want to do this to control something in your sequence data with a standard control.                                                                                   |
|                               |                                                                                                                                                                                 |
|                               | Or in your arb data, or custom UI in the comp window, or to group-set multiple other controls.                                                                                  |
|                               |                                                                                                                                                                                 |
|                               | This flag cannot be used with:                                                                                                                                                  |
|                               |                                                                                                                                                                                 |
|                               |   - ``PF_Param_CUSTOM``,                                                                                                                                                        |
|                               |   - ``PF_Param_NO_DATA``,                                                                                                                                                       |
|                               |   - ``PF_Param_LAYER``,                                                                                                                                                         |
|                               |   - ``PF_Param_ARBITRARY_DATA``,                                                                                                                                                |
|                               |   - ``PF_Param_PATH``.                                                                                                                                                          |
|                               |                                                                                                                                                                                 |
|                               | If you set this flag, you must also set ``PF_ParamFlag_SUPERVISE``                                                                                                              |
|                               | (otherwise you would never find out about value changes, and the setting would never be used for anything).                                                                     |
|                               |                                                                                                                                                                                 |
|                               | This flag does not require that the :ref:`PF_OutFlag_CUSTOM_UI <effect-basics/PF_OutData.PF_OutFlags>` flag be set.                                                             |
|                               |                                                                                                                                                                                 |
|                               | If you want a standard control for ``PF_Param_ARBITRARY_DATA``, just add one (or more) using ``PF_PUI_STD_CONTROL_ONLY`` with the supported param types,                        |
|                               | and then when handling :ref:`PF_Cmd_USER_CHANGED_PARAM <effect-basics/command-selectors.messaging>` you can modify your arb data.                                               |
+-------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_PUI_NO_ECW_UI``          | Set this flag if you want no UI to appear in the Effect Controls Window.                                                                                                        |
|                               |                                                                                                                                                                                 |
|                               | Presumably, you are setting the value of the parameter through some other method                                                                                                |
|                               | (e.g. custom UI in the comp window, or while handling ``PF_Cmd_USER_CHANGED_PARAM`` for a different param with ``PF_ParamFlag_SUPERVISE`` set).                                 |
|                               |                                                                                                                                                                                 |
|                               | In AE, this doesn't affect keyframe visibility in the timeline. In PPro it does remove the entire row, so you won't see keyframes.                                              |
+-------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_PUI_ECW_SEPARATOR``      | Not used in After Effects, but used in Premiere. Set this flag if you'd like a thick line above this parameter in the effect control window.                                    |
|                               |                                                                                                                                                                                 |
|                               | This is provided so that parameters can be grouped visually, if needed (without adding groups). This flag can be changed at runtime through the ``PF_UpdateParamUI()`` method.  |
+-------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_PUI_DISABLED``           | Disables (grays out) the parameter, usually in response to :ref:`PF_Cmd_USER_CHANGED_PARAM <effect-basics/command-selectors.messaging>`.                                        |
+-------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_PUI_DONT_ERASE_TOPIC``   | After Effects won't erase parameter's topic.                                                                                                                                    |
+-------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_PUI_DONT_ERASE_CONTROL`` | After Effects won't erase parameter's control.                                                                                                                                  |
+-------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_PUI_RADIO_BUTTON``       | Not used in After Effects, but used in Premiere. Display parameter as a radio-button group. Only valid for ``PF_Param_POPUP``.                                                  |
+-------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_PUI_INVISIBLE``          | First supported in Premiere, and now supported in After Effects CS6 and later. This hides the parameter UI in both the Effect Controls and Timeline.                            |
|                               |                                                                                                                                                                                 |
|                               | Premiere only: The flag is dynamic and parameter visibility can be toggled during the :ref:`PF_UpdateParamUI <effect-detals/parameter-supervision.PF_ParamUtilSuite>` callback. |
+-------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

In addition to these flags, an effect parameter may be hidden or shown by using :ref:`AEGP_GetDynamicStreamFlags <aegps/aegp-suites.dynamic-stream-suite>`.

----

.. _effect-basics/PF_ParamDef.parameter-flags:

Parameter Flags
================================================================================

Behavior flags and UI flags describe different qualities of a parameter. Set them *before* adding the parameter during :ref:`PF_Cmd_PARAM_SETUP <effect-basics/command-selectors.global-selectors>`. Flags which may be set during events are noted.

+---------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|                     **Flag**                      |                                                                                                                                     **Meaning**                                                                                                                                      |
+===================================================+======================================================================================================================================================================================================================================================================================+
| ``PF_ParamFlag_CANNOT_TIME_VARY``                 | Parameter does not vary with time; no keyframe control will be provided in the Timeline panel.                                                                                                                                                                                       |
+---------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_ParamFlag_CANNOT_INTERP``                    | Values are not algebraically interpolated.                                                                                                                                                                                                                                           |
|                                                   |                                                                                                                                                                                                                                                                                      |
|                                                   | You can still use discontinuous (hold) interpolation. Useful for parameters which are either on or off. Accelerates rendering.                                                                                                                                                       |
+---------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_ParamFlag_COLLAPSE_TWIRLY``                  | Set this flag during :ref:`PF_Cmd_USER_CHANGED_PARAM <effect-basics/command-selectors.messaging>`.                                                                                                                                                                                   |
|                                                   |                                                                                                                                                                                                                                                                                      |
|                                                   | This bit can now be set & cleared when handling :ref:`PF_Cmd_UPDATE_PARAMS_UI <effect-basics/command-selectors.messaging>` and :ref:`PF_Cmd_USER_CHANGED_PARAM <effect-basics/command-selectors.messaging>` messages, so as to twirl your parameters and groups up and down at will. |
+---------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_ParamFlag_SUPERVISE``                        | Set to receive :ref:`PF_Cmd_USER_CHANGED_PARAM <effect-basics/command-selectors.messaging>` messages for this parameter.                                                                                                                                                             |
|                                                   |                                                                                                                                                                                                                                                                                      |
|                                                   | See :ref:`effect-detals/parameter-supervision` for more information.                                                                                                                                                                                                                 |
+---------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_ParamFlag_START_COLLAPSED``                  | Controls the twirl-state of a topic spinner.                                                                                                                                                                                                                                         |
|                                                   |                                                                                                                                                                                                                                                                                      |
|                                                   | Can be changed during parameter supervision, not just during :ref:`PF_Cmd_PARAM_SETUP <effect-basics/command-selectors.global-selectors>`.                                                                                                                                           |
|                                                   |                                                                                                                                                                                                                                                                                      |
|                                                   | This flag will not be honored unless :ref:`PF_OutFlag2_PARAM_GROUP_START_COLLAPSED <effect-basics/PF_OutData.PF_OutFlags>` is set.                                                                                                                                                   |
+---------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_ParamFlag_USE_VALUE_FOR_OLD_PROJECTS``       | This only affects the loading of projects saved with an older version of the effect which lacks parameters added later.                                                                                                                                                              |
|                                                   |                                                                                                                                                                                                                                                                                      |
|                                                   | When set, the ``PF_ParamDef.value`` field set in ``PF_ADD_PARAM()`` will be used to initialize the missing parameter, but the dephault field will still be used for initial value of the parameter when the effect is newly applied or reset.                                        |
|                                                   |                                                                                                                                                                                                                                                                                      |
|                                                   | This is useful for when you want a parameter to default to one value but need it set to something else to preserve rendering behavior for older projects.                                                                                                                            |
+---------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_ParamFlag_LAYER_PARAM_IS_TRACKMATTE``        | Premiere Pro only: Only valid for layer parameters. Indicates that a layer param is used as a track-matte with applied filters.                                                                                                                                                      |
|                                                   |                                                                                                                                                                                                                                                                                      |
|                                                   | Ignored in After Effects.                                                                                                                                                                                                                                                            |
+---------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_ParamFlag_EXCLUDE_FROM_HAVE_INPUTS_CHANGED`` | Only relevant if the effect sets :ref:`PF_OutFlag2_AUTOMATIC_WIDE_TIME_INPUT <effect-basics/PF_OutData.PF_OutFlags>` and will call                                                                                                                                                   |
|                                                   | :ref:`PF_AreStatesIdentical <effect-detals/parameter-supervision.PF_ParamUtilSuite>` or :ref:`PF_HaveInputsChangedOverTimeSpan <effect-detals/parameter-supervision.PF_ParamUtilSuite>`                                                                                              |
+---------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_ParamFlag_SKIP_REVEAL_WHEN_UNHIDDEN``        | New in CS6. If this parameter is unhidden, then this flag tells After Effects to not twirl open any parents and to not scroll the parameter into view in the Effect Controls panel and the Timeline panel.                                                                           |
|                                                   |                                                                                                                                                                                                                                                                                      |
|                                                   | After Effects uses this behavior internally when paint strokes are made, so as not to distract the user by revealing the parameter.                                                                                                                                                  |
|                                                   | However, in another case, when turning on Time Remapping, that parameter is revealed.                                                                                                                                                                                                |
|                                                   |                                                                                                                                                                                                                                                                                      |
|                                                   | So we provide you the same control over parameters in your own effects.                                                                                                                                                                                                              |
+---------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

----

PF_ValueDisplayFlags
================================================================================

Within PF_ParamDefUnion, PF_FloatSliderDef and PF_FixedSliderDef both have a member variable, PF_ValueDisplayFlags, which allows them to respond to the user's pixel value display preference (which they set in the info palette). If this is set, the parameter's value will be displayed as 0-1, 0-255, 0-32768, or 0.0 to 1.0, depending on the preference. You can also set the first bit (PF_ValueDisplayFlag_PERCENT) to append a percent sign to the parameter's displayed value.

We know you'd never do anything like this, but if you create a parameter which displays as a percentage, don't confuse the user by allowing any range other than 0 to 100. Please. Percent means 'out of one hundred'.
