# Effect UI & Events

Effects can provide custom UI in two areas: (1) the Effect Controls Window (custom ECW UI), and (2) the Composition or Layer Windows (Custom Comp UI).

Effects that use custom UI should set `PF_OutFlag_CUSTOM_UI` (from [PF_OutFlags](../effect-basics/PF_OutData.md#pf_outflags) during `PF_Cmd_GLOBAL_SETUP` (from [Global Selectors](../effect-basics/command-selectors.md#global-selectors)), and handle the PF_Cmd_EVENT selector.

Custom ECW UI allows an effect to provide a parameter with a customized control, which can be used either with standard parameter types or [Arbitrary Data Parameters](../effect-details/arbitrary-data-parameters.md#arbitrary-data-parameters).

Parameters that have a custom UI should set `PF_PUI_CONTROL` (from [Parameter UI Flags](../effect-basics/PF_ParamDef.md#parameter-ui-flags)) when [adding the parameter](../effect-details/interaction-callback-functions.md#interaction-callbacks).

Custom Comp UI allows an effect to provide direct manipulation of the video in the Composition or Layer Windows.

When the effect is selected, the Window can overlay custom controls directly on the video, and can handle user interaction with those controls, to adjust parameters more quickly and naturally.

Effects should register themselves to receive events by calling PF_REGISTER_UI.

After Effects can send events to effects for user interface handling and parameter management, integrating effects into its central message queue.

While many events are sent in response to user input, After Effects also sends events to effects which manage arbitrary data parameters.

The type of event is specified in [PF_EventExtra->e_type](PF_EventExtra.md#pf_eventextra) and the various events are described below.

---

## Events

|          Event           |                                                                                                                                                                            Indicates                                                                                                                                                                            |
| ------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `PF_Event_NEW_CONTEXT`   | The user created a new context (probably by opening a window) for events.<br/><br/>The plug-in is allowed to store state information inside the context using the context handle.<br/><br/>[PF_EventUnion](PF_EventUnion.md#pf_eventunion) contains valid context and type, but everything else should be ignored.                                              |
| `PF_Event_ACTIVATE`      | The user activated a new context (probably by bringing a window into the foreground). [PF_EventUnion](PF_EventUnion.md#pf_eventunion) is empty.                                                                                                                                                                                                                 |
| `PF_Event_DO_CLICK`      | The user clicked within the effect's UI. [PF_EventUnion](PF_EventUnion.md#pf_eventunion) contains a `PF_DoClickEventInfo`.<br/><br/>Handle the mouse click and respond, passing along drag info; see sample code), within a context.<br/><br/>NOTE: As of 7.0, do *not* block until mouse-up; instead, rely on `PF_Event_DRAG`.                                 |
| `PF_Event_DRAG`          | Also a Click Event, [PF_EventUnion](PF_EventUnion.md#pf_eventunion) contains a `PF_DoClickEventInfo`.<br/><br/>Request this by returning `send_drag == TRUE` from `PF_Event_DO_CLICK`.<br/><br/>Do this so After Effects can see new data from the user's changes.                                                                                              |
| `PF_Event_DRAW`          | Draw! [PF_EventUnion](PF_EventUnion.md#pf_eventunion) contains a `PF_DrawEventInfo`.                                                                                                                                                                                                                                                                            |
| `PF_Event_DEACTIVATE`    | The user has deactivated a context (probably by bringing another window into the foreground). `PF_EventUnion` is empty.                                                                                                                                                                                                                                         |
| `PF_Event_CLOSE_CONTEXT` | A context has been closed by the user. `PF_EventUnion` will be empty.                                                                                                                                                                                                                                                                                           |
| `PF_Event_IDLE`          | A context is open but nothing is happening. `PF_EventUnion` is empty.                                                                                                                                                                                                                                                                                           |
| `PF_Event_ADJUST_CURSOR` | The mouse is over the plug-in's UI. Set the cursor by changing the `PF_CursorType` in the `PF_AdjustCursorEventInfo`.<br/><br/>Use OS-specific calls to implement a custom cursor; tell After Effects you've done so by setting `PF_CursorType` to `PF_Cursor_CUSTOM`.<br/><br/>Use an After Effects cursor whenever possible to preserve interface continuity. |
| `PF_Event_KEYDOWN`       | Keystroke. [PF_EventUnion](PF_EventUnion.md#pf_eventunion) contains a `PF_KeyDownEvent`.                                                                                                                                                                                                                                                                        |
| `PF_Event_MOUSE_EXITED`  | New in CS6. Notification that the mouse is no longer over a specific view (layer or comp only).                                                                                                                                                                                                                                                                 |
