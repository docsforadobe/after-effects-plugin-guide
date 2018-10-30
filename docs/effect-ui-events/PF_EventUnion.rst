.. _effect-ui-events/PF_EventUnion:

PF_EventUnion
################################################################################

The PF_EventUnion in PF_EventExtra is a union of the four following structures.

----

Click
================================================================================

A mouse click or drag occurred within the custom UI's area.

PF_DoClickEventInfo
********************************************************************************

+------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|       **Member**       |                                                                                     **Purpose**                                                                                     |
+========================+=====================================================================================================================================================================================+
| ``when``               | The (OS-level) time at which the click occurred.                                                                                                                                    |
+------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``screen_point``       | Where, in screen coordinates, the click occurred. For Custom Comp UI, these coordinates can be converted to composition coordinates using the :ref:`effect-ui-events/ui-callbacks`. |
|                        | See the CCU sample project for an example.                                                                                                                                          |
+------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``num_clicks``         | The number of clicks that occurred.                                                                                                                                                 |
+------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``modifiers``          | Which modifier keys (if any) were held down during click.                                                                                                                           |
+------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``continue_refcon[4]`` | An array of 4 ``A_intptr_t`` the plug-in can use to store information during a click-drag-drag sequence.                                                                            |
+------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``send_drag``          | Set this flag to ``TRUE`` to indicate continued dragging. The next click event will then effectively be a drag event.                                                               |
+------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``last_time``          | Set when the drag event ends (the user has released the mouse button).                                                                                                              |
+------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

----

Draw
================================================================================

After Effects needs your custom UI to refresh.

Note: when handling draw requests, use the image dimensions provided in :ref:`effect-basics/PF_InData` (rather that the dimensions of your input layer, as you would during :ref:`PF_Cmd_RENDER <effect-basics/command-selectors.frame-selectors>`).

PF_DrawEventInfo
********************************************************************************

+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|   **Member**    |                                                                                          **Purpose**                                                                                          |
+=================+===============================================================================================================================================================================================+
| ``update_rect`` | The rectangle in which to draw, in the context window's coordinate system. These coordinates can be converted to different coordinate systems using the :ref:`effect-ui-events/ui-callbacks`. |
|                 | See the CCU sample project for an example.                                                                                                                                                    |
+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``depth``       | Pixel depth of the drawing context.                                                                                                                                                           |
+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

----

Keydown
================================================================================

The user pressed a key, and the effect's UI is active.

Use the macros in AE_EffectUI.h to access and manipulate the key codes received.

PF_KeyDownEvent
********************************************************************************

+------------------+-----------------------------------------------------------------------------------------------------------------------------------+
|    **Member**    |                                                            **Purpose**                                                            |
+==================+===================================================================================================================================+
| ``when``         | Time at which the click occurred.                                                                                                 |
+------------------+-----------------------------------------------------------------------------------------------------------------------------------+
| ``screen_point`` | Screen coordinate of the mouse pointer when the key was pressed.                                                                  |
|                  | For Custom Comp UI, these coordinates can be converted to composition coordinates using the :ref:`effect-ui-events/ui-callbacks`. |
|                  | See the CCU sample project for an example.                                                                                        |
+------------------+-----------------------------------------------------------------------------------------------------------------------------------+
| ``key_code``     | Either a character code (for printable characters, we use the unshifted upper case version; A not a, 7 not &), or a control code: |
|                  |                                                                                                                                   |
|                  |   - ``PF_ControlCode_Unknown``                                                                                                    |
|                  |   - ``PF_ControlCode_Space``                                                                                                      |
|                  |   - ``PF_ControlCode_Backspace``                                                                                                  |
|                  |   - ``PF_ControlCode_Tab``                                                                                                        |
|                  |   - ``PF_ControlCode_Return``                                                                                                     |
|                  |   - ``PF_ControlCode_Enter``                                                                                                      |
|                  |   - ``PF_ControlCode_Escape``                                                                                                     |
|                  |   - ``PF_ControlCode_F1``                                                                                                         |
|                  |                                                                                                                                   |
|                  | ...                                                                                                                               |
|                  |                                                                                                                                   |
|                  |   - ``PF_ControlCode_F24``                                                                                                        |
|                  |   - ``PF_ControlCode_PrintScreen``                                                                                                |
|                  |   - ``PF_ControlCode_ScrollLock``                                                                                                 |
|                  |   - ``PF_ControlCode_Pause``                                                                                                      |
|                  |   - ``PF_ControlCode_Insert``                                                                                                     |
|                  |   - ``PF_ControlCode_Delete``                                                                                                     |
|                  |   - ``PF_ControlCode_Home``                                                                                                       |
|                  |   - ``PF_ControlCode_End``                                                                                                        |
|                  |   - ``PF_ControlCode_PageUp``                                                                                                     |
|                  |   - ``PF_ControlCode_PageDown``                                                                                                   |
|                  |   - ``PF_ControlCode_Help``                                                                                                       |
|                  |   - ``PF_ControlCode_Clear``                                                                                                      |
|                  |   - ``PF_ControlCode_Left``                                                                                                       |
|                  |   - ``PF_ControlCode_Right``                                                                                                      |
|                  |   - ``PF_ControlCode_Up``                                                                                                         |
|                  |   - ``PF_ControlCode_Down``                                                                                                       |
|                  |   - ``PF_ControlCode_NumLock``                                                                                                    |
|                  |   - ``PF_ControlCode_Command``                                                                                                    |
|                  |   - ``PF_ControlCode_Option``                                                                                                     |
|                  |   - ``PF_ControlCode_Alt`` = ``PF_ControlCode_Option``                                                                            |
|                  |   - ``PF_ControlCode_Control``                                                                                                    |
|                  |   - ``PF_ControlCode_Shift``                                                                                                      |
|                  |   - ``PF_ControlCode_CapsLock``                                                                                                   |
|                  |   - ``PF_ControlCode_ContextMenu``                                                                                                |
+------------------+-----------------------------------------------------------------------------------------------------------------------------------+
| ``modifiers``    | Which (if any) modifier keys were down during the key press.                                                                      |
|                  |                                                                                                                                   |
|                  |   - ``PF_Mod_NONE``                                                                                                               |
|                  |   - ``PF_Mod_CMD_CTRL_KEY`` (cmd on Mac, ctrl on Windows)                                                                         |
|                  |   - ``PF_Mod_SHIFT_KEY``                                                                                                          |
|                  |   - ``PF_Mod_CAPS_LOCK_KEY``                                                                                                      |
|                  |   - ``PF_Mod_OPT_ALT_KEY`` (option on Mac, alt on Windows)                                                                        |
|                  |   - ``PF_Mod_MAC_CONTROL_KEY``                                                                                                    |
+------------------+-----------------------------------------------------------------------------------------------------------------------------------+

----

AdjustCursor
================================================================================

The cursor has moved onto (but not off of) the effect's custom UI, to allow the effect to change the cursor.

PF_AdjustCursorEventInfo
********************************************************************************

+------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|    **Member**    |                                                                                        **Purpose**                                                                                         |
+==================+============================================================================================================================================================================================+
| ``screen_point`` | Screen coordinate of the mouse pointer. For Custom Comp UI, these coordinates can be converted to composition coordinates using the :ref:`effect-ui-events/ui-callbacks`.                  |
|                  | See the CCU sample project for an example.                                                                                                                                                 |
+------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``modifiers``    | What, if any, modifier keys were held down when the message was sent.                                                                                                                      |
+------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``set_cursor``   | Set this to your desired cursor, or ``PF_Cursor_CUSTOM`` if you have set the cursor yourself using OS-specific calls. See AE_EffectUI.h for a complete enumeration of built-in cursors.    |
|                  | If you don't want to override the cursor, set this to ``PF_Cursor_NONE``, or simply ignore this message.                                                                                   |
+------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

----

Arbitrary Parameters Event
================================================================================

After Effects needs your plug-in to manage it's arbitrary data parameter(s).

Though arbitrary data types are not required for custom UI support, ``PF_ArbParamsExtra`` follows the EventInfo model.

PF_ArbParamsExtra
********************************************************************************

+----------------------------+-----------------------------------------------------------------------------------------------------------+
|             **Member**     |                                                **Purpose**                                                |
+============================+===========================================================================================================+
| ``which_function``         | A ``PF_FunctionSelector`` indicating which function is called                                             |
+----------------------------+-----------------------------------------------------------------------------------------------------------+
| ``id``                     | Used by After Effects; will match the ID assigned to the arbitrary data type during *PF_Cmd_PARAM_SETUP*. |
+----------------------------+-----------------------------------------------------------------------------------------------------------+
| ``padding``                | Used for byte-alignment                                                                                   |
+----------------------------+-----------------------------------------------------------------------------------------------------------+
| ::                         | (One of these will be passed; see :ref:`effect-details/arbitrary-data-parameters`)                        |
|                            |                                                                                                           |
|   u {                      |                                                                                                           |
|     new_func_params        |                                                                                                           |
|     dispose_func_params    |                                                                                                           |
|     copy_func_params       |                                                                                                           |
|     flat_size_func_params  |                                                                                                           |
|     flatten_func_params    |                                                                                                           |
|     unflatten_func_params  |                                                                                                           |
|     interp_func_params     |                                                                                                           |
|     compare_func_params    |                                                                                                           |
|     print_size_func_params |                                                                                                           |
|     print_func_params      |                                                                                                           |
|     scan_func_params       |                                                                                                           |
|   }                        |                                                                                                           |
+----------------------------+-----------------------------------------------------------------------------------------------------------+


