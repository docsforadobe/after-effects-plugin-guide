.. _aegps/aegp-details:

AEGP Details
################################################################################

Have A Cookie
================================================================================

In cases where After Effects must preserve state information around the functions your AEGP calls (as when an artisan is rendering a frame, or a keyframer is adding and removing a series of keyframes from the same stream), you'll call begin() and end() functions.

Typically, the begin function will return an opaque identifier, or 'cookie', which you must then pass to the functions being used. The end function will properly dispose of the cookie. See `AEGP_StartAddKeyframes() <#_bookmark649>`__ for an example.

----

Modifying Items In The Render Queue
================================================================================

If you call `AEGP_AddCompToRenderQueue <#_bookmark705>`__ (from `AEGP_RenderQueueSuite <#_bookmark704>`__), or if the user manually adds or removes a composition from the render queue, all references to render queue items are invalidated. Similarly, adding or removing output modules invalidates any such references for each render queue item.

----

Names And Solids
================================================================================

Solids have names in the After Effects UI, but not in their `PF_LayerDef <#_bookmark233>`__. Consequently, their names cannot be retrieved by `AEGP_GetItemName <#_bookmark571>`__ or `AEGP_GetLayerName <#_bookmark597>`__.

However, you can use the ItemH associated with them to `AEGP_GetItemName <#_bookmark571>`__.

----

Reporting Errors And Problems
================================================================================

Use AEGP_ItemSuite>AEGP_ReportInfo() to report information to users, and identify your plug-in. AEIO plug-ins use the msg_func pointer contained in the AEIO_BasicData they're passed (with every function) instead.

----

Transforms: What Happens First?
================================================================================

After Effects computes rotation based on auto-orientation (towards path, or point of interest), then computes Orientation, then computes X, Y, and Z rotation.

----

Accessing Pixels From Effect Layer Parameters
================================================================================

Use `AEGP_GetNewStreamValue <#_bookmark632>`__ to get the layer's layer_id, then the new `AEGP_GetLayerFromLayerID <#_bookmark606>`__ to get the AEGP_LayerH.
