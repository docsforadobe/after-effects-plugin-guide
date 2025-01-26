.. _effect-ui-events/PF_EventExtra:

PF_EventExtra
################################################################################

This structure provide context information for the current event. After Effects passes a pointer to this structure in the extra parameter of the :ref:`effect-basics/entry-point` function.

The ``PF_EventUnion`` (sent in the ``PF_EventExtra``) varies with the event type, and contains information specific to that event.

+-------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|    **Member**     |                                                                                       **Purpose**                                                                                          |
+===================+============================================================================================================================================================================================+
| ``contextH``      | Handle to the ``PF_Context``.                                                                                                                                                              |
|                   |                                                                                                                                                                                            |
|                   | This drawing context is used with the :ref:`Drawbot suites <effect-ui-events/custom-ui-and-drawbot>` for drawing,                                                                          |
|                   | and also for the :ref:`effect-ui-events/ui-callbacks`.                                                                                                                                     |
+-------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``e_type``        | Which :ref:`event <effect-ui-events/effect-ui-events>` is occurring.                                                                                                                       |
+-------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``u``             | A :ref:`effect-ui-events/PF_EventUnion` containing information specific to the event.                                                                                                      |
+-------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``effect_win``    | A ``PF_EffectWindowInfo`` about the event if it occurs within the effects window.                                                                                                          |
|                   |                                                                                                                                                                                            |
|                   | Otherwise, as of After Effects 5.0, effect_win can be replaced by a ``PF_WindowUnion``.                                                                                                    |
|                   |                                                                                                                                                                                            |
|                   | This struct contains both a ``PF_EffectWindowInfo`` and an ``PF_ItemWindowInfo``, which (for now) is simply the port rectangle for the item window.                                        |
|                   |                                                                                                                                                                                            |
|                   | Replacement only occurs if ``PF_USE_NEW_WINDOW_UNION`` was defined during compilation; otherwise, it will continue to be just a ``PF_EffectWindowInfo``.                                   |
+-------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``cbs``           | Pointer to :ref:`effect-ui-events/ui-callbacks`, which are needed to translate points between layer, composition, and screen coordinate systems.                                           |
+-------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``evt_in_flags``  | Event Input Flags. This currently contains only one value, ``PF_EI_DONT_DRAW``, which you should check before drawing!                                                                     |
+-------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``evt_out_flags`` | One or more of the following, combined with a bitwise OR operation:                                                                                                                        |
|                   |                                                                                                                                                                                            |
|                   |   - ``PF_EO_NONE``                                                                                                                                                                         |
|                   |   - ``PF_EO_HANDLED_EVENT`` tells After Effects you've handled the event.                                                                                                                  |
|                   |   - ``PF_EO_ALWAYS_UPDATE`` forces After Effects to rerender the composite in response to every click or drag; this is the same behavior generated by 'alt-scrubbing' the parameter value. |
|                   |   - ``PF_EO_NEVER_UPDATE`` prevents After Effects from rerendering the composite until the user stops clicking and dragging.                                                               |
|                   |   - ``PF_EO_UPDATE_NOW`` tells After Effects to update the view immediately after the event returns after calling PF_InvalidateRect                                                        |
+-------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

----

PF_Context
================================================================================

PF_Context details the event's UI context.

+----------------------+--------------------------------------------------------------------------------------------------------+
|      **Member**      |                                              **Purpose**                                               |
+======================+========================================================================================================+
| ``magic``            | Do not change.                                                                                         |
+----------------------+--------------------------------------------------------------------------------------------------------+
| ``w_type``           | The window type. If you have Custom Comp and ECW UIs in the same plug-in,                              |
|                      | this is the way to differentiate between them (what kind of masochist are you, anyway?).               |
|                      |                                                                                                        |
|                      |   - ``PF_Window_COMP``,                                                                                |
|                      |   - ``PF_Window_LAYER``,                                                                               |
|                      |   - ``PF_Window_EFFECT``                                                                               |
+----------------------+--------------------------------------------------------------------------------------------------------+
| ``reserved_flt``     | Do not change.                                                                                         |
+----------------------+--------------------------------------------------------------------------------------------------------+
| ``plugin_state[4]``  | An array of 4 ``A_longs`` which the plug-in can use to store state information for a given context.    |
+----------------------+--------------------------------------------------------------------------------------------------------+
| ``reserved_drawref`` | A ``DRAWBOT_DrawRef`` for use with the :ref:`Drawbot suites <effect-ui-events/custom-ui-and-drawbot>`. |
+----------------------+--------------------------------------------------------------------------------------------------------+
| ``*reserved_paneP``  | Do not change.                                                                                         |
+----------------------+--------------------------------------------------------------------------------------------------------+

----

PF_EffectWindowInfo
================================================================================

If an event occurs in the ECP, an PF_EffectWindowInfo is sent in PF_EventExtra.

+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
|      **Member**       |                                                                **Purpose**                                                                 |
+=======================+============================================================================================================================================+
| ``index``             | This indicates which parameter in the effect window is being affected. The controls are numbered from 0 to the number of controls minus 1. |
+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| ``area``              | This indicates if the control title (``PF_EA_PARAM_TITLE``) or the control itself (``PF_EA_CONTROL``) are being affected.                  |
|                       | The title is the area still visible when the parameter's topic ("twirly") is spun up.                                                      |
+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| ``current_frame``     | A PF_Rect indicating the full frame of the area occupied by the control.                                                                   |
+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| ``param_title_frame`` | A PF_Rect indicating the title area of the control.                                                                                        |
+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| ``horiz_offset``      | A horizontal offset from the left side of the title area in which to draw into the title.                                                  |
+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
