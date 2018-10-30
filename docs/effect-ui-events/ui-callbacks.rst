.. _effect-ui-events/ui-callbacks:

UI Callbacks
################################################################################

After Effects provides callbacks for transposing between coordinate systems, and obtaining OS-specific information about drawing contexts, without guesswork or asking the OS directly. Use these callbacks! Pointers to these callbacks are provided in PF_EventCallbacks. Use the macros in AE_EffectUI.h and AE_EffectCB.h to access these routines.

It is possible to build a functioning plug-in which utilizes a custom UI without implementing the coordinate system transposition callbacks. However, the moment a user zooms into the layer panel or rotates a layer, your plug-in will behave badly. We added these macros and callbacks so that custom user interfaces could be easily integrated into the After Effects UI, without inflicting user interface overhead on developers. Again, please use them!

These macros default the refcon and context handle for simplicity. The refcon assumes you have a local variable named "extra". The default context is the current context. These default parameters are defined in the PF_EventCallbacks structure (in AE_EffectUI.h). You can override the defaults by accessing the callbacks through the PF_EventExtra structure. We don't recommend (or support) modification of the macros in the header file. Don't do it!

+--------------------------+-----------------------------------------------------------------------------------------------------------+
|       **Function**       |                                                **Purpose**                                                |
+==========================+===========================================================================================================+
| ``layer_to_comp``        | Transforms layer panel coordinates to the composition panel coordinates.                                  |
|                          |                                                                                                           |
|                          | ::                                                                                                        |
|                          |                                                                                                           |
|                          |   PF_Err layer_to_comp (                                                                                  |
|                          |     void           *refcon,                                                                               |
|                          |     PF_ContextH    context,                                                                               |
|                          |     A_long         curr_time,                                                                             |
|                          |     A_long         time_scale,                                                                            |
|                          |     PF_FixedPoint  *pt);                                                                                  |
+--------------------------+-----------------------------------------------------------------------------------------------------------+
| ``comp_to_layer``        | Transforms composition panel coordinates to the layer panel coordinates.                                  |
|                          |                                                                                                           |
|                          | ::                                                                                                        |
|                          |                                                                                                           |
|                          |   PF_Err comp_to_layer (                                                                                  |
|                          |     void           *refcon,                                                                               |
|                          |     PF_ContextH    context,                                                                               |
|                          |     A_long         curr_time,                                                                             |
|                          |     A_long         time_scale,                                                                            |
|                          |     PF_FixedPoint  *pt);                                                                                  |
+--------------------------+-----------------------------------------------------------------------------------------------------------+
| ``get_comp2layer_xform`` | Returns the matrix used to convert from the composition panel to the layer panel.                         |
|                          | If ``*exists`` returns ``FALSE``, the matrix cannot be computed because the layer scales to zero.         |
|                          |                                                                                                           |
|                          | ::                                                                                                        |
|                          |                                                                                                           |
|                          |   PF_Err get_comp2layer_xform (                                                                           |
|                          |     void            *refcon,                                                                              |
|                          |     PF_ContextH     context,                                                                              |
|                          |     A_long          curr_time,                                                                            |
|                          |     long            time_scale,                                                                           |
|                          |     long            *exists,                                                                              |
|                          |     PF_FloatMatrix  *comp2layer);                                                                         |
+--------------------------+-----------------------------------------------------------------------------------------------------------+
| ``get_layer2comp_xform`` | Returns the transformation matrix used to convert from the layer panel to the composition panel.          |
|                          | This always exists.                                                                                       |
|                          |                                                                                                           |
|                          | ::                                                                                                        |
|                          |                                                                                                           |
|                          |   PF_Err get_layer2comp_xform (                                                                           |
|                          |     void            *refcon,                                                                              |
|                          |     PF_ContextH     context,                                                                              |
|                          |     A_long          curr_time,                                                                            |
|                          |     A_long          time_scale,                                                                           |
|                          |     PF_FloatMatrix  *layer2comp);                                                                         |
+--------------------------+-----------------------------------------------------------------------------------------------------------+
| ``source_to_frame``      | Transforms the source coordinates in the current context to screen coordinates.                           |
|                          | Screen (frame) coordinates are affected by the current zoom level.                                        |
|                          |                                                                                                           |
|                          | ::                                                                                                        |
|                          |                                                                                                           |
|                          |   PF_Err source_to_frame(                                                                                 |
|                          |     void           *refcon,                                                                               |
|                          |     PF_ContextH    context,                                                                               |
|                          |     PF_FixedPoint  *pt);                                                                                  |
+--------------------------+-----------------------------------------------------------------------------------------------------------+
| ``frame_to_source``      | Transforms the screen coordinates identified by ``*pt`` to the source coordinates of the current context. |
|                          |                                                                                                           |
|                          | ::                                                                                                        |
|                          |                                                                                                           |
|                          |   PF_Err frame_to_source(                                                                                 |
|                          |     void           *refcon,                                                                               |
|                          |     PF_ContextH    context,                                                                               |
|                          |     PF_FixedPoint  *pt);                                                                                  |
+--------------------------+-----------------------------------------------------------------------------------------------------------+
| ``PF_GET_PLATFORM_DATA`` | Retrieves platform-specific data. For plug-ins loaded with localized resource files,                      |
|                          | ``PF_PlatData_RES_FILE_PATH`` will point to the external file, not the plug-in file.                      |
|                          | Use ``PF_PlatData_EXE_FILE_PATH`` if you want the path of your plug-in.                                   |
|                          |                                                                                                           |
|                          | Starting in CS6, use ``PF_PlatData_EXE_FILE_PATH_W`` and ``PF_PlatData_RES_FILE_PATH_W``                  |
|                          | instead of the old non-wide calls.                                                                        |
|                          |                                                                                                           |
|                          | ::                                                                                                        |
|                          |                                                                                                           |
|                          |   PF_Err PF_GET_PLATFORM_DATA (                                                                           |
|                          |     PF_PlatDataID  which,                                                                                 |
|                          |     void           *ppData);                                                                              |
|                          |                                                                                                           |
|                          | PF_PlatDataID can have the following values:                                                              |
|                          |                                                                                                           |
|                          | - ``PF_PlatData_MAIN_WND``                                                                                |
|                          | - ``PF_PlatData_EXE_FILE_PATH_DEPRECATED``                                                                |
|                          | - ``PF_PlatData_RES_FILE_PATH_DEPRECATED``                                                                |
|                          | - ``PF_PlatData_RES_REFNUM`` // macOS                                                                     |
|                          | - ``PF_PlatData_RES_DLLINSTANCE`` // Win                                                                  |
|                          | - ``PF_PlatData_BUNDLE_REF``                                                                              |
|                          | - ``PF_PlatData_EXE_FILE_PATH_W`` // new CS6                                                              |
|                          | - ``PF_PlatData_RES_FILE_PATH_W`` // new CS6                                                              |
+--------------------------+-----------------------------------------------------------------------------------------------------------+
