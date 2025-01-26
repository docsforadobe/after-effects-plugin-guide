.. _artisans/artisan-data-types:

Artisan Data Types
################################################################################

Below are the data types most commonly used in the Artisan API.

----

Data Types Used In The Artisan API
================================================================================

+------------------------------+-----------------------------------------------------------------------------------------+
|           **Type**           |                                      **Describes**                                      |
+==============================+=========================================================================================+
| ``AEGP_RenderLayerContextH`` | State information at the time of a render request, sent to an Artisan by After Effects. |
+------------------------------+-----------------------------------------------------------------------------------------+
| ``PR_RenderContextH``        | A collection of settings defining what is to be rendered, and how.                      |
+------------------------------+-----------------------------------------------------------------------------------------+
| ``AEGP_SoundDataH``          | The audio settings used for a given layer.                                              |
+------------------------------+-----------------------------------------------------------------------------------------+
| ``AEGP_RenderReceiptH``      | Used by Artisans when rendering.                                                        |
| ``AEGP_FrameReceiptH``       |                                                                                         |
+------------------------------+-----------------------------------------------------------------------------------------+
| ``AEGP_WorldH``              | A frame of pixels.                                                                      |
+------------------------------+-----------------------------------------------------------------------------------------+
| ``AEGP_RenderOptionsH``      | The settings associated with a render queue item.                                       |
+------------------------------+-----------------------------------------------------------------------------------------+

----

Horz? Vert?
================================================================================

After Effects' matrix is row based; OpenGL's is column based. This means more work for you. Yay, billable hours!

----

Implementation And Design
================================================================================

An Artisan is nearly an application unto itself. Because we realized early in the After Effects 5.0 that there are many ways to approach the problems inherent in 3D rendering; intersections and shading, for example.

We provided an API with which we and third parties (yes, we really do use our own APIs) could implement any 3D rendering scheme desired.

----

3D Compositing, Not Modeling
================================================================================

After Effects is *not* a 3D modeling application. Users work in a responsive mode, switching to higher quality only at for proofing or final output. Consider providing at least two quality modes, one for layout and another for final output. Be conscious of render time in low quality mode.

----

Registering An Artisan
================================================================================

An Artisan is an AEGP, and has a single entry point. Artisans must also register their own function entry points and have a special callback for this purpose. See ``AEGP_RegisterArtisan()`` from :ref:`aegps/aegp-suites.AEGP_RegisterSuites`.

This tables shows the functions that Artisans can support as defined by ``PR_ArtisanEntryPoints``: only ``render_func`` is required.

Artisan Entry Points
********************************************************************************

+------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
|  **PR_ArtisanEntryPoints**   |                                                                                                                                      |
+==============================+======================================================================================================================================+
| ``global_setup_func0``       | Called only once, right after ``GP_Main``. The global data is common across all instances of the plug-in.                            |
|                              | If you allocate memory during Global Setup, you must free it during your ``global_setdown_func``.                                    |
|                              |                                                                                                                                      |
|                              | ::                                                                                                                                   |
|                              |                                                                                                                                      |
|                              |   PR_GlobalSetupFunc(                                                                                                                |
|                              |     const PR_InData    *in_dataP,                                                                                                    |
|                              |     PR_GlobalContextH  global_contextH,                                                                                              |
|                              |     PR_GlobalDataH     *global_dataPH);                                                                                              |
+------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
| ``global_setdown_func0``     | Dispose of any global data you allocated.                                                                                            |
|                              |                                                                                                                                      |
|                              | ::                                                                                                                                   |
|                              |                                                                                                                                      |
|                              |   PR_GlobalSetdownFunc(                                                                                                              |
|                              |     const PR_InData    *in_dataP,                                                                                                    |
|                              |     PR_GlobalContextH  global_contextH,                                                                                              |
|                              |     PR_GlobalDataH     global_dataH);                                                                                                |
+------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
| ``global_do_about_func0``    | Tell the world about yourself! Use ``in_dataP>msg_func`` to display your dialog.                                                     |
|                              |                                                                                                                                      |
|                              | ::                                                                                                                                   |
|                              |                                                                                                                                      |
|                              |   PR_GlobalDoAboutFunc(                                                                                                              |
|                              |     const PR_InData    *in_dataP,                                                                                                    |
|                              |     PR_GlobalContextH  global_contextH,                                                                                              |
|                              |     PR_GlobalDataH     global_dataH);                                                                                                |
+------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
| ``setup_instance_func0``     | Allocate and instantiate any data specific to this instance of your Artisan.                                                         |
|                              |                                                                                                                                      |
|                              | ::                                                                                                                                   |
|                              |                                                                                                                                      |
|                              |   PR_InstanceSetupFunc(                                                                                                              |
|                              |     const PR_InData      *in_dataP,                                                                                                  |
|                              |     PR_GlobalContextH    global_contextH,                                                                                            |
|                              |     PR_InstanceContextH  instance_contextH,                                                                                          |
|                              |     PR_GlobalDataH       global_dataH,                                                                                               |
|                              |     PR_InstanceFlags     flags,                                                                                                      |
|                              |     PR_FlatHandle        flat_dataH0,                                                                                                |
|                              |     PR_InstanceDataH     *instance_dataPH);                                                                                          |
+------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
| ``setdown_instance_func0``   | Deallocate and free any data specific to this instance of your Artisan.                                                              |
|                              |                                                                                                                                      |
|                              | ::                                                                                                                                   |
|                              |                                                                                                                                      |
|                              |   PR_InstanceSetdownFunc(                                                                                                            |
|                              |     const PR_InData      *in_dataP,                                                                                                  |
|                              |     PR_GlobalContextH    global_contextH,                                                                                            |
|                              |     PR_InstanceContextH  instance_contextH,                                                                                          |
|                              |     PR_GlobalDataH       global_dataH,                                                                                               |
|                              |     PR_InstanceDataH     instance_dataH);                                                                                            |
+------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
| ``flatten_instance_func0``   | Flatten your data in preparation to being written to disk. (making sure it's OS independent, if your Artisan is).                    |
|                              |                                                                                                                                      |
|                              | ::                                                                                                                                   |
|                              |                                                                                                                                      |
|                              |   PR_FlattenInstanceFunc(                                                                                                            |
|                              |     const PR_InData      *in_dataP,                                                                                                  |
|                              |     PR_GlobalContextH    global_contextH,                                                                                            |
|                              |     PR_InstanceContextH  instance_contextH,                                                                                          |
|                              |     PR_GlobalDataH       global_dataH,                                                                                               |
|                              |     PR_InstanceDataH     instance_dataH,                                                                                             |
|                              |     PR_FlatHandle        *flatH);                                                                                                    |
+------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
| ``do_instance_dialog_func0`` | If your Artisan has a additional parameters (accessed through its Options dialog), this function will be called to get and set them. |
|                              |                                                                                                                                      |
|                              | ::                                                                                                                                   |
|                              |                                                                                                                                      |
|                              |   PR_DoInstanceDialogFunc(                                                                                                           |
|                              |     const PR_InData      *in_dataP,                                                                                                  |
|                              |     PR_GlobalContextH    global_contextH,                                                                                            |
|                              |     PR_InstanceContextH  instance_contextH,                                                                                          |
|                              |     PR_GlobalDataH       global_dataH,                                                                                               |
|                              |     PR_InstanceDataH     instance_dataH,                                                                                             |
|                              |     PR_DialogResult      *resultP);                                                                                                  |
|                              |                                                                                                                                      |
|                              | ``PR_DialogResultis`` is either ``PR_DialogResult_NO_CHANGE`` or ``PR_DialogResult_CHANGE_MADE``.                                    |
+------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
| ``frame_setup_func0``        | Perform any setup necessary to render a frame (called immediately before rendering).                                                 |
|                              |                                                                                                                                      |
|                              | ::                                                                                                                                   |
|                              |                                                                                                                                      |
|                              |   PR_FrameSetupFunc(                                                                                                                 |
|                              |     const PR_InData      *in_dataP,                                                                                                  |
|                              |     PR_GlobalContextH    global_contextH,                                                                                            |
|                              |     PR_InstanceContextH  instance_contextH                                                                                           |
|                              |     PR_RenderContextH    render_contextH,                                                                                            |
|                              |     PR_GlobalDataH       global_dataH,                                                                                               |
|                              |     PR_InstanceDataH     instance_dataH,                                                                                             |
|                              |     PR_RenderDataH       *render_dataPH);                                                                                            |
+------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
| ``frame_setdown_func0``      | Dispose of any setup data allocated during ``frame_setup`` (sent immediately after rendering).                                       |
|                              |                                                                                                                                      |
|                              | ::                                                                                                                                   |
|                              |                                                                                                                                      |
|                              |   PR_FrameSetdownFunc(                                                                                                               |
|                              |     const PR_InData      *in_dataP,                                                                                                  |
|                              |     PR_GlobalContextH    global_contextH,                                                                                            |
|                              |     PR_InstanceContextH  instance_contextH                                                                                           |
|                              |     PR_RenderContextH    render_contextH,                                                                                            |
|                              |     PR_GlobalDataH       global_dataH,                                                                                               |
|                              |     PR_InstanceDataH     instance_dataH,                                                                                             |
|                              |     PR_RenderDataH       render_dataH);                                                                                              |
+------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
| ``render_func``              | Render the scene.                                                                                                                    |
|                              |                                                                                                                                      |
|                              | ::                                                                                                                                   |
|                              |                                                                                                                                      |
|                              |   PR_FrameRenderFunc(                                                                                                                |
|                              |     const PR_InData      *in_dataP,                                                                                                  |
|                              |     PR_GlobalContextH    global_contextH,                                                                                            |
|                              |     PR_InstanceContextH  instance_contextH                                                                                           |
|                              |     PR_RenderContextH    render_contextH,                                                                                            |
|                              |     PR_GlobalDataH       global_dataH,                                                                                               |
|                              |     PR_InstanceDataH     instance_dataH,                                                                                             |
|                              |     PR_RenderDataH       render_dataH);                                                                                              |
+------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
| ``query_func0``              | Artisans can draw their own projection axes, should the need arise.                                                                  |
|                              | After Effects will call this function to obtain the transform between the composition world and those axes,                          |
|                              | as well as for a number of other functions related to on- and off-screen                                                             |
|                              | preview drawing (the former is relevant only to interactive artisans).                                                               |
|                              |                                                                                                                                      |
|                              | ::                                                                                                                                   |
|                              |                                                                                                                                      |
|                              |   PR_QueryFunc(                                                                                                                      |
|                              |     const PR_InData      *in_dataP,                                                                                                  |
|                              |     PR_GlobalContextH    global_contextH,                                                                                            |
|                              |     PR_InstanceContextH  instance_contextH                                                                                           |
|                              |     PR_QueryContextH     query_contextH,                                                                                             |
|                              |     PR_QueryType         query_type,                                                                                                 |
|                              |     PR_GlobalDataH       global_dataH,                                                                                               |
|                              |     PR_InstanceDataH     instance_dataH);                                                                                            |
|                              |                                                                                                                                      |
|                              | ``PR_QueryType`` can be one of the following:                                                                                        |
|                              |                                                                                                                                      |
|                              |   - ``PR_QueryType_NONE = 0``,                                                                                                       |
|                              |   - ``PR_QueryType_TRANSFORM``,                                                                                                      |
|                              |   - ``PR_QueryType_INTERACTIVE_WINDOW_DISPOSE``,                                                                                     |
|                              |   - ``PR_QueryType_INTERACTIVE_WINDOW_CLEAR``,                                                                                       |
|                              |   - ``PR_QueryType_INTERACTIVE_WINDOW_FROZEN_PROXY``,                                                                                |
|                              |   - ``PR_QueryType_INTERACTIVE_SWAP_BUFFER``,                                                                                        |
|                              |   - ``PR_QueryType_INTERACTIVE_DRAW_PROCS``,                                                                                         |
|                              |   - ``PR_QueryType_PREPARE_FOR_LINE_DRAWING``,                                                                                       |
|                              |   - ``PR_QueryType_UNPREPARE_FOR_LINE_DRAWING``,                                                                                     |
|                              |   - ``PR_QueryType_GET_CURRENT_CONTEXT_SAFE_FOR_LINE_DRAWING``,                                                                      |
|                              |   - ``PR_QueryType_GET_ARTISAN_QUALITY``                                                                                             |
|                              |                                                                                                                                      |
|                              | New in CS6.                                                                                                                          |
+------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+

----

The World Is Your Canvas
================================================================================

``AEGP_RenderTexture()`` supplies the raw pixels of a layer, untransformed, into an arbitrarily-sized buffer.

``AEGP_RenderLayer()`` invokes the entire After Effects render pipeline, including transforms, masking, et cetera, providing the layer as it appears in its composition, in a composition-sized buffer.

If the layer being rendered is 3D, the default (Standard 3D) Artisan is invoked to perform any 3D geometrics.

Your Artisan can use this to render track matte layers, and apply them only in a strictly 2D sense, to the transformed 3D layer.

Before rendering, the Artisans that ship with After Effects apply an inverse transform to get square pixels, then re-apply the transform before display.

For example, if the pixel aspect ratio is 10/11 (DV NTSC), we multiply by 11/10 to get square pixels. We process and composite 3D layers, then re-divide to get back to the original pixel aspect ratio.

The following suite supplies the layers, compositions, texture and destination buffers. This is a vital suite for all artisans.

.. _artisans/artisan-data-types.AEGP_CanvasSuite:

AEGP_CanvasSuite8
********************************************************************************

+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
|                 **Function**                 |                                                          **Purpose**                                                          |
+==============================================+===============================================================================================================================+
| ``AEGP_GetCompToRender``                     | Given the render context provided to the Artisan at render time, returns a handle to the composition.                         |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_GetCompToRender(                                                                                                       |
|                                              |     PR_RenderContextH  render_contextH,                                                                                       |
|                                              |     AEGP_CompH         *compPH)                                                                                               |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetNumLayersToRender``                | Given the render context, returns the number of layers the Artisan needs to render.                                           |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_GetNumLayersToRender(                                                                                                  |
|                                              |     PR_RenderContextH  render_contextH,                                                                                       |
|                                              |     A_long             *num_to_renderPL)                                                                                      |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetNthLayerContextToRender``          | Used to build a list of layers to render after determining the total number of layers that need rendering by the Artisan.     |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_GetNthLayerContextToRender(                                                                                            |
|                                              |     PR_RenderContextH         render_contextH,                                                                                |
|                                              |     A_long                    n,                                                                                              |
|                                              |     AEGP_RenderLayerContextH  *layer_indexPH)                                                                                 |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetLayerFromLayerContext``            | Given a ``AEGP_RenderLayerContextH``,retrieves the associated ``AEGP_LayerH`` (required by many suite functions).             |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_GetLayerFromLayerContext(                                                                                              |
|                                              |     const PR_RenderContextH   render_contextH,                                                                                |
|                                              |     AEGP_RenderLayerContextH  layer_contextH,                                                                                 |
|                                              |     AEGP_LayerH               *layerPH);                                                                                      |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetLayerAndSubLayerFromLayerContext`` | Allows for rendering of sub-layers (as within a Photoshop file).                                                              |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_GetLayerAndSubLayerFromLayerContext(                                                                                   |
|                                              |     const PR_RenderContextH   render_contextH,                                                                                |
|                                              |     AEGP_RenderLayerContextH  layer_contextH,                                                                                 |
|                                              |     AEGP_LayerH               *layerPH,                                                                                       |
|                                              |     AEGP_SubLayerIndex        *sublayerP);                                                                                    |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetTopLayerFromLayerContext``         | With collapsed geometrics "on" this gives the layer in the root composition containing the layer context.                     |
|                                              |                                                                                                                               |
|                                              | With collapsed geometrics off this is the same as ``AEGP_GetLayerFromLayerContext``.                                          |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_GetTopLayerFromLayerContext(                                                                                           |
|                                              |     const PR_RenderContextH   r_contextH,                                                                                     |
|                                              |     AEGP_RenderLayerContextH  l_contextH,                                                                                     |
|                                              |     AEGP_LayerH               *layerPH);                                                                                      |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetCompRenderTime``                   | Given the render context, returns the current point in (composition) time to render.                                          |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_GetNthLayerIndexToRender(                                                                                              |
|                                              |     PR_RenderContextH  render_contextH,                                                                                       |
|                                              |     A_long             *time,                                                                                                 |
|                                              |     A_long             *time_step)                                                                                            |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetCompDestinationBuffer``            | Given the render context, returns a buffer in which to place the final rendered output.                                       |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_GetCompToRender(                                                                                                       |
|                                              |     PR_RenderContextH  render_contextH,                                                                                       |
|                                              |     AEGP_CompH         compH,                                                                                                 |
|                                              |     PF_EffectWorld     *dst);                                                                                                 |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetROI``                              | Given the render context provided to the Artisan at render time, returns a handle to the composition.                         |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_GetROI(                                                                                                                |
|                                              |     PR_RenderContextH  render_contextH,                                                                                       |
|                                              |     A_LegacyRect       *roiPR);                                                                                               |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_RenderTexture``                       | Given the render context and layer, returns the layer texture.                                                                |
|                                              |                                                                                                                               |
|                                              | All parameters with a trailing '0' are optional; the returned ``PF_EffectWorld`` can be NULL.                                 |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_RenderTexture(                                                                                                         |
|                                              |     PR_RenderContextH  render_contextH,                                                                                       |
|                                              |     AEGP_LayerH        layerH,                                                                                                |
|                                              |     AEGP_RenderHints   render_hints,                                                                                          |
|                                              |     A_FloatPoint       *suggested_scaleP0,                                                                                    |
|                                              |     A_FloatRect        *suggsted_src_rectP0,                                                                                  |
|                                              |     A_Matrix3          *src_matrixP0,                                                                                         |
|                                              |     PF_EffectWorld     *render_bufferP);                                                                                      |
|                                              |                                                                                                                               |
|                                              | ``AEGP_RenderHints`` contains one or more of the following:                                                                   |
|                                              |                                                                                                                               |
|                                              |   - ``AEGP_RenderHints_NONE``                                                                                                 |
|                                              |   - ``AEGP_RenderHints_IGNORE_EXTENTS``                                                                                       |
|                                              |   - ``AEGP_RenderHints_NO_TRANSFER_MODE``                                                                                     |
|                                              |                                                                                                                               |
|                                              | ``AEGP_RenderHints_NO_TRANSFER_MODE`` prevents application of opacity & transfer mode; for use with ``RenderLayer`` calls.    |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_DisposeTexture``                      | Disposes of an acquired layer texture.                                                                                        |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_DisposeTexture(                                                                                                        |
|                                              |     PR_RenderContextH  render_contextH,                                                                                       |
|                                              |     AEGP_LayerH        layerH,                                                                                                |
|                                              |     AEGP_WorldH        *dst0);                                                                                                |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetFieldRender``                      | Returns the field settings of the given ``PR_RenderContextH``.                                                                |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_GetFieldRender(                                                                                                        |
|                                              |     PR_RenderContextH  render_contextH,                                                                                       |
|                                              |     PF_Field           *field);                                                                                               |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_ReportArtisanProgress``               | Given the render context provided to the Artisan at render time, returns a handle to the composition.                         |
|                                              |                                                                                                                               |
|                                              | Note: this is NOT thread-safe on macOS; only use this function when the current thread ID is 0.                               |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_ReportArtisanProgress(                                                                                                 |
|                                              |     PR_RenderContextH  render_contextH,                                                                                       |
|                                              |     A_long             countL,                                                                                                |
|                                              |     A_long             totalL);                                                                                               |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetRenderDownsampleFactor``           | Returns the downsample factor of the ``PR_RenderContextH``.                                                                   |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_GetRenderDownsampleFactor(                                                                                             |
|                                              |     PR_RenderContextH      render_contextH,                                                                                   |
|                                              |     AEGP_DownsampleFactor  *dsfP);                                                                                            |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_IsBlankCanvas``                       | Determines whether the ``PR_RenderContextH`` is blank (empty).                                                                |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_IsBlankCanvas(                                                                                                         |
|                                              |     PR_RenderContextH  render_contextH,                                                                                       |
|                                              |     A_Boolean          *is_blankPB);                                                                                          |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetRenderLayerToWorldXform``          | Given a render context and a layer (at a given time), retrieves the 4 by 4 transform to move between their coordinate spaces. |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_GetRenderLayerToWorldXform(                                                                                            |
|                                              |     PR_RenderContextH         render_contextH,                                                                                |
|                                              |     AEGP_RenderLayerContextH  layer_contextH,                                                                                 |
|                                              |     const A_Time              *comp_timeP,                                                                                    |
|                                              |     A_Matrix4                 *transform);                                                                                    |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetRenderLayerBounds``                | Retrieves the bounding rectangle of the layer_contextH (at a given time) within the render_contextH.                          |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_GetRenderLayerBounds(                                                                                                  |
|                                              |     PR_RenderContextH         render_contextH,                                                                                |
|                                              |     AEGP_RenderLayerContextH  layer_contextH,                                                                                 |
|                                              |     const A_Time              *comp_timeP,                                                                                    |
|                                              |     A_LegacyRect              *boundsP);                                                                                      |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetRenderOpacity``                    | Returns the opacity of the given layer context at the given time, within the render context.                                  |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_GetRenderOpacity(                                                                                                      |
|                                              |     PR_RenderContextH         render_contextH,                                                                                |
|                                              |     AEGP_RenderLayerContextH  layer_contextH,                                                                                 |
|                                              |     const A_Time              *comp_timePT,                                                                                   |
|                                              |     A_FpLong                  *opacityPF);                                                                                    |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_IsRenderLayerActive``                 | Returns whether or not a given layer context is active within the render context, at the given time.                          |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_IsRenderLayerActive(                                                                                                   |
|                                              |     PR_RenderContextH         render_contextH,                                                                                |
|                                              |     AEGP_RenderLayerContextH  layer_contextH,                                                                                 |
|                                              |     const A_Time              *comp_timePT,                                                                                   |
|                                              |     A_Boolean                 *activePB);                                                                                     |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetArtisanLayerProgress``             | Sets the progress information for a rendering Artisan. countL is the number of layers completed,                              |
|                                              |                                                                                                                               |
|                                              | ``num_layersL`` is the total number of layers the Artisan is rendering.                                                       |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_SetArtisanLayerProgress(                                                                                               |
|                                              |     PR_RenderContextH  render_contextH,                                                                                       |
|                                              |     A_long             countL,                                                                                                |
|                                              |     A_long             num_layersL);                                                                                          |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_RenderLayerPlus``                     | Similar to ``AEGP_RenderLayer``, but takes into account the ``AEGP_RenderLayerContextH``.                                     |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_RenderLayerPlus(                                                                                                       |
|                                              |     PR_RenderContextH          r_contextH,                                                                                    |
|                                              |     AEGP_LayerH                layerH,                                                                                        |
|                                              |     AEGP_RenderLayerContextH   l_contextH,                                                                                    |
|                                              |     AEGP_RenderHints           render_hints,                                                                                  |
|                                              |     AEGP_WorldH                *bufferP);                                                                                     |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetTrackMatteContext``                | Retrieves the ``AEGP_RenderLayerContextH`` for the specified render and fill contexts.                                        |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_GetTrackMatteContext(                                                                                                  |
|                                              |     PR_RenderContextH         rnder_contextH,                                                                                 |
|                                              |     AEGP_RenderLayerContextH  fill_contextH,                                                                                  |
|                                              |     AEGP_RenderLayerContextH  *mattePH);                                                                                      |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_RenderTextureWithReceipt``            | Renders a texture into an ``AEGP_WorldH``, and provides an ``AEGP_RenderReceiptH`` for the operation.                         |
|                                              |                                                                                                                               |
|                                              | The returned ``receiptPH`` must be disposed of with ``AEGP_DisposeRenderReceipt``.                                            |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_RenderTextureWithReceipt(                                                                                              |
|                                              |     PR_RenderContextH         render_contextH,                                                                                |
|                                              |     AEGP_RenderLayerContextH  layer_contextH,                                                                                 |
|                                              |     AEGP_RenderHints          render_hints,                                                                                   |
|                                              |     A_FloatPoint              *suggested_scaleP0,                                                                             |
|                                              |     A_FloatRect               *suggest_src_rectP0,                                                                            |
|                                              |     A_Matrix3                 *src_matrixP0,                                                                                  |
|                                              |     AEGP_RenderReceiptH       *receiptPH,                                                                                     |
|                                              |     AEGP_WorldH               *dstPH);                                                                                        |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetNumberOfSoftwareEffects``          | Returns the number of software effects applied in the given ``AEGP_RenderLayerContextH``.                                     |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_GetNumberOfSoftwareEffects(                                                                                            |
|                                              |     PR_RenderContextH         ren_contextH,                                                                                   |
|                                              |     AEGP_RenderLayerContextH  lyr_contextH,                                                                                   |
|                                              |     A_short                   *num_sft_FXPS);                                                                                 |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_RenderLayerPlusWithReceipt``          | An improvement over ``AEGP_RenderLayerPlus``, this function also provides an ``AEGP_RenderReceiptH`` for caching purposes.    |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_RenderLayerPlusWithReceipt(                                                                                            |
|                                              |     PR_RenderContextH            render_contextH,                                                                             |
|                                              |     AEGP_LayerH                  layerH,                                                                                      |
|                                              |     AEGP_RenderLayerContextH     layer_contextH,                                                                              |
|                                              |     AEGP_RenderHints             render_hints,                                                                                |
|                                              |     AEGP_NumEffectsToRenderType  num_effectsS,                                                                                |
|                                              |     AEGP_RenderReceiptH          *receiptPH,                                                                                  |
|                                              |     AEGP_WorldH                  *bufferPH);                                                                                  |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_DisposeRenderReceipt``                | Frees an ``AEGP_RenderReceiptH``.                                                                                             |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_DisposeRenderReceipt(                                                                                                  |
|                                              |     AEGP_RenderReceiptH  receiptH);                                                                                           |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_CheckRenderReceipt``                  | Checks with After Effects' internal caching to determine whether a given ``AEGP_RenderReceiptH`` is still valid.              |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_CheckRenderReceipt(                                                                                                    |
|                                              |     PR_RenderContextH            current_contextH,                                                                            |
|                                              |     AEGP_RenderLayerContextH     current_lyr_ctxtH,                                                                           |
|                                              |     AEGP_RenderReceiptH          old_receiptH,                                                                                |
|                                              |     A_Boolean                    check_aceB,                                                                                  |
|                                              |     AEGP_NumEffectsToRenderType  num_effectsS,                                                                                |
|                                              |     AEGP_RenderReceiptStatus     *receipt_statusP);                                                                           |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GenerateRenderReceipt``               | Generates a ``AEGP_RenderReceiptH`` for a layer as if the first ``num_effectsS`` have been rendered.                          |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_GenerateRenderReceipt(                                                                                                 |
|                                              |     PR_RenderContextH            current_contextH,                                                                            |
|                                              |     AEGP_RenderLayerContextH     current_lyr_contextH,                                                                        |
|                                              |     AEGP_NumEffectsToRenderType  num_effectsS,                                                                                |
|                                              |     AEGP_RenderReceiptH          *render_receiptPH);                                                                          |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetNumBinsToRender``                  | Returns the number of bins After Effects wants the artisan to render.                                                         |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_GetNumBinsToRender(                                                                                                    |
|                                              |     const PR_RenderContextH  contextH,                                                                                        |
|                                              |     A_long                   *num_binsPL);                                                                                    |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetNthBin``                           | Sets the given render context to be the n-th bin to be rendered by After Effects.                                             |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_SetNthBin(                                                                                                             |
|                                              |     const PR_RenderContextH  contextH,                                                                                        |
|                                              |     A_long                   n);                                                                                              |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetBinType``                          | Retrieves the type of the given bin.                                                                                          |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_GetBinType(                                                                                                            |
|                                              |     const PR_RenderContextH  contextH,                                                                                        |
|                                              |     AEGP_BinType             *bin_typeP);                                                                                     |
|                                              |                                                                                                                               |
|                                              | ``AEGP_BinType`` will be one of the following:                                                                                |
|                                              |                                                                                                                               |
|                                              |   - ``AEGP_BinType_NONE``                                                                                                     |
|                                              |   - ``AEGP_BinType_2D``                                                                                                       |
|                                              |   - ``AEGP_BinType_3D``                                                                                                       |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetRenderLayerToWorldXform2D3D``      | Retrieves the transform to correctly orient the layer being rendered with the output world.                                   |
|                                              |                                                                                                                               |
|                                              | Pass ``TRUE`` for ``only_2dB`` to constrain the transform to two dimensions.                                                  |
|                                              |                                                                                                                               |
|                                              | ::                                                                                                                            |
|                                              |                                                                                                                               |
|                                              |   AEGP_GetRenderLayerToWorldXform2D3D(                                                                                        |
|                                              |     PR_RenderContextH         render_contextH,                                                                                |
|                                              |     AEGP_RenderLayerContextH  layer_contextH,                                                                                 |
|                                              |     const A_Time              *comp_timeP,                                                                                    |
|                                              |     A_Boolean                 only_2dB,                                                                                       |
|                                              |     A_Matrix4                 *transformP);                                                                                   |
+----------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+

.. note ::

  Functions below are for interactive artisans only.

+-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
|               **Function**                |                                                            **Purpose**                                                             |
+===========================================+====================================================================================================================================+
| ``AEGP_GetPlatformWindowRef``             | Retrieves the platform-specific window context into which to draw the given ``PR_RenderContextH``.                                 |
|                                           |                                                                                                                                    |
|                                           | ::                                                                                                                                 |
|                                           |                                                                                                                                    |
|                                           |   AEGP_GetPlatformWindowRef(                                                                                                       |
|                                           |     const PR_RenderContextH  contextH,                                                                                             |
|                                           |     AEGP_PlatformWindowRef   *window_refP);                                                                                        |
+-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetViewportScale``                 | Retrieves the source-to-frame downsample factor for the given ``PR_RenderContextH``.                                               |
|                                           |                                                                                                                                    |
|                                           | ::                                                                                                                                 |
|                                           |                                                                                                                                    |
|                                           |   AEGP_GetViewportScale(                                                                                                           |
|                                           |     const PR_RenderContextH  contextH,                                                                                             |
|                                           |     A_FpLong                 *scale_xPF,                                                                                           |
|                                           |     A_FpLong                 *scale_yPF);                                                                                          |
+-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetViewportOrigin``                | Retrieves to origin of the source, within the frame (necessary to translate between the two), for the given ``PR_RenderContextH``. |
|                                           |                                                                                                                                    |
|                                           | ::                                                                                                                                 |
|                                           |                                                                                                                                    |
|                                           |   AEGP_GetViewportOrigin(                                                                                                          |
|                                           |     const PR_RenderContextH  contextH,                                                                                             |
|                                           |     A_long                   *origin_xPL,                                                                                          |
|                                           |     A_long                   *origin_yPL);                                                                                         |
+-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetViewportRect``                  | Retrieves the bounding rectangle for the area to be drawn, for the given ``PR_RenderContextH``.                                    |
|                                           |                                                                                                                                    |
|                                           | ::                                                                                                                                 |
|                                           |                                                                                                                                    |
|                                           |   AEGP_GetViewportRect(                                                                                                            |
|                                           |     const PR_RenderContextH  contextH,                                                                                             |
|                                           |     A_LegacyRect             *v_rectPR);                                                                                           |
+-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetFallowColor``                   | Retrieves the color used for the fallow regions in the given ``PR_RenderContextH``.                                                |
|                                           |                                                                                                                                    |
|                                           | ::                                                                                                                                 |
|                                           |                                                                                                                                    |
|                                           |   AEGP_GetFallowColor(                                                                                                             |
|                                           |     const PR_RenderContextH  contextH,                                                                                             |
|                                           |     PF_Pixel8                *fallow_colorP);                                                                                      |
+-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetInteractiveCheckerboard``       | Retrieves whether or not the checkerboard is currently active for the given ``PR_RenderContextH``.                                 |
|                                           |                                                                                                                                    |
|                                           | ::                                                                                                                                 |
|                                           |                                                                                                                                    |
|                                           |   AEGP_GetInteractiveCheckerboard(                                                                                                 |
|                                           |     const PR_RenderContextH  contextH,                                                                                             |
|                                           |     A_Boolean                *cboard_onPB);                                                                                        |
+-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetInteractiveCheckerboardColors`` | Retrieves the colors used in the checkerboard.                                                                                     |
|                                           |                                                                                                                                    |
|                                           | ::                                                                                                                                 |
|                                           |                                                                                                                                    |
|                                           |   AEGP_GetInteractiveCheckerboardColors(                                                                                           |
|                                           |     const PR_RenderContextH  contextH,                                                                                             |
|                                           |     PF_Pixel                 *color1P,                                                                                             |
|                                           |     PF_Pixel                 *color2P);                                                                                            |
+-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetInteractiveCheckerboardSize``   | Retrieves the width and height of one checkerboard square.                                                                         |
|                                           |                                                                                                                                    |
|                                           | ::                                                                                                                                 |
|                                           |                                                                                                                                    |
|                                           |   AEGP_GetInteractiveCheckerboardSize(                                                                                             |
|                                           |     const PR_RenderContextH  contextH,                                                                                             |
|                                           |     A_u_long                 *cbd_widthPLu,                                                                                        |
|                                           |     A_u_long                 *cbd_heightPLu);                                                                                      |
+-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetInteractiveCachedBuffer``       | Retrieves the cached AEGP_WorldH last used for the ``PR_RenderContextH``.                                                          |
|                                           |                                                                                                                                    |
|                                           | ::                                                                                                                                 |
|                                           |                                                                                                                                    |
|                                           |   AEGP_GetInteractiveCachedBuffer(                                                                                                 |
|                                           |     const PR_RenderContextH  contextH,                                                                                             |
|                                           |     AEGP_WorldH              *buffer);                                                                                             |
+-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_ArtisanMustRenderAsLayer``         | Determines whether or not the artisan must render the current ``AEGP_RenderLayerContextH`` as a layer.                             |
|                                           |                                                                                                                                    |
|                                           | ::                                                                                                                                 |
|                                           |                                                                                                                                    |
|                                           |   AEGP_ArtisanMustRenderAsLayer(                                                                                                   |
|                                           |     const PR_RenderContextH   contextH,                                                                                            |
|                                           |     AEGP_RenderLayerContextH  layer_contextH,                                                                                      |
|                                           |     A_Boolean                 *use_txturePB);                                                                                      |
+-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetInteractiveDisplayChannel``     | Returns which channels should be displayed by the interactive artisan.                                                             |
|                                           |                                                                                                                                    |
|                                           | ::                                                                                                                                 |
|                                           |                                                                                                                                    |
|                                           |   AEGP_GetInteractiveDisplayChannel(                                                                                               |
|                                           |     const PR_RenderContextH  contextH,                                                                                             |
|                                           |     AEGP_DisplayChannelType  *channelP);                                                                                           |
|                                           |                                                                                                                                    |
|                                           | ``AEGP_DisplayChannelType`` will be one of the following:                                                                          |
|                                           |                                                                                                                                    |
|                                           |   - ``AEGP_DisplayChannel_NONE``                                                                                                   |
|                                           |   - ``AEGP_DisplayChannel_RED``                                                                                                    |
|                                           |   - ``AEGP_DisplayChannel_GREEN``                                                                                                  |
|                                           |   - ``AEGP_DisplayChannel_BLUE``                                                                                                   |
|                                           |   - ``AEGP_DisplayChannel_ALPHA``                                                                                                  |
|                                           |   - ``AEGP_DisplayChannel_RED_ALT``                                                                                                |
|                                           |   - ``AEGP_DisplayChannel_GREEN_ALT``                                                                                              |
|                                           |   - ``AEGP_DisplayChannel_BLUE_ALT``                                                                                               |
|                                           |   - ``AEGP_DisplayChannel_ALPHA_ALT``                                                                                              |
+-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetInteractiveExposure``           | Returns the exposure for the given ``PR_RenderContextH``, expressed as a floating point number.                                    |
|                                           |                                                                                                                                    |
|                                           | ::                                                                                                                                 |
|                                           |                                                                                                                                    |
|                                           |   AEGP_GetInteractiveExposure(                                                                                                     |
|                                           |     const PR_RenderContextH  rcH,                                                                                                  |
|                                           |     A_FpLong                 *exposurePF);                                                                                         |
+-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetColorTransform``                | Returns the color transform for the given ``PR_RenderContextH``.                                                                   |
|                                           |                                                                                                                                    |
|                                           | ::                                                                                                                                 |
|                                           |                                                                                                                                    |
|                                           |   AEGP_GetColorTransform)(                                                                                                         |
|                                           |     const PR_RenderContextH  render_contextH,                                                                                      |
|                                           |     A_Boolean                *cms_onB,                                                                                             |
|                                           |     A_u_long                 *xform_keyLu,                                                                                         |
|                                           |     void                      *xformP);                                                                                            |
+-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetCompShutterTime``               | Returns the shutter angle for the given ``PR_RenderContextH``.                                                                     |
|                                           |                                                                                                                                    |
|                                           | ::                                                                                                                                 |
|                                           |                                                                                                                                    |
|                                           |   AEGP_GetCompShutterTime)(                                                                                                        |
|                                           |     PR_RenderContextH  render_contextH,                                                                                            |
|                                           |     A_Time             *shutter_time,                                                                                              |
|                                           |     A_Time             *shutter_dur);                                                                                              |
+-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_MapCompToLayerTime``               | New in CC. Unlike :ref:`AEGP_ConvertCompToLayerTime <aegps/aegp-suites.AEGP_LayerSuite>`,                                          |
|                                           | this handles time remapping with collapsed or nested comps.                                                                        |
|                                           |                                                                                                                                    |
|                                           | ::                                                                                                                                 |
|                                           |                                                                                                                                    |
|                                           |   AEGP_MapCompToLayerTime(                                                                                                         |
|                                           |     PR_RenderContextH         render_contextH,                                                                                     |
|                                           |     AEGP_RenderLayerContextH  layer_contextH,                                                                                      |
|                                           |     const A_Time              *comp_timePT,                                                                                        |
|                                           |     A_Time                    *layer_timePT);                                                                                      |
+-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+

----

Convert Between Different Contexts
================================================================================

Convert between render and instance contexts, and manage global data specific to the artisan.

AEGP_ArtisanUtilSuite1
********************************************************************************

+----------------------------------------------+---------------------------------------------------------------------+
|                 **Function**                 |                             **Purpose**                             |
+==============================================+=====================================================================+
| ``AEGP_GetGlobalContextFromInstanceContext`` | Given an instance context, returns a handle to the global context.  |
|                                              |                                                                     |
|                                              | ::                                                                  |
|                                              |                                                                     |
|                                              |   AEGP_GetGlobalContextFromInstanceContext(                         |
|                                              |     const PR_InstanceContextH  instance_contextH,                   |
|                                              |     PR_GlobalContextH          *global_contextPH);                  |
+----------------------------------------------+---------------------------------------------------------------------+
| ``AEGP_GetInstanceContextFromRenderContext`` | Given the render context, returns a handle to the instance context. |
|                                              |                                                                     |
|                                              | ::                                                                  |
|                                              |                                                                     |
|                                              |   AEGP_GetInstanceContextFromRenderContext(                         |
|                                              |     const PR_RenderContextH  render_contextH,                       |
|                                              |     PR_InstanceContextH      *instnc_ctextPH);                      |
+----------------------------------------------+---------------------------------------------------------------------+
| ``AEGP_GetInstanceContextFromQueryContext``  | Given a query context, returns a handle to the instance context.    |
|                                              |                                                                     |
|                                              | ::                                                                  |
|                                              |                                                                     |
|                                              |   AEGP_GetInstanceContextFromQueryContext(                          |
|                                              |     const PR_QueryContextH  query_contextH,                         |
|                                              |     PR_InstanceContextH     *instnce_contextPH);                    |
+----------------------------------------------+---------------------------------------------------------------------+
| ``AEGP_GetGlobalData``                       | Given the global context, returns a handle to global data.          |
|                                              |                                                                     |
|                                              | ::                                                                  |
|                                              |                                                                     |
|                                              |   AEGP_GetGlobalData(                                               |
|                                              |     const PR_GlobalContextH  global_contextH,                       |
|                                              |     PR_GlobalDataH           *global_dataPH);                       |
+----------------------------------------------+---------------------------------------------------------------------+
| ``AEGP_GetInstanceData``                     | Given an instance context, return the associated instance data.     |
|                                              |                                                                     |
|                                              | ::                                                                  |
|                                              |                                                                     |
|                                              |   AEGP_GetInstanceData(                                             |
|                                              |     const PR_InstanceContextH  instance_contextH,                   |
|                                              |     PR_InstanceDataH           *instance_dataPH);                   |
+----------------------------------------------+---------------------------------------------------------------------+
| ``AEGP_GetRenderData``                       | Given a render context, returns the associated render data.         |
|                                              |                                                                     |
|                                              | ::                                                                  |
|                                              |                                                                     |
|                                              |   AEGP_GetRenderData(                                               |
|                                              |     const PR_RenderContextH  render_contextH,                       |
|                                              |     PR_RenderDataH           *render_dataPH);                       |
+----------------------------------------------+---------------------------------------------------------------------+

----

Smile! Cameras
================================================================================

Obtains the camera geometry, including camera properties (type, lens, depth of field, focal distance, aperture, et cetera).

AEGP_CameraSuite2
********************************************************************************

+-----------------------------------------------+-----------------------------------------------------------------------------------------------------+
|                 **Function**                  |                                             **Purpose**                                             |
+===============================================+=====================================================================================================+
| ``AEGP_GetCamera``                            | Given a layer handle and time, returns the current camera layer handle.                             |
|                                               |                                                                                                     |
|                                               | ::                                                                                                  |
|                                               |                                                                                                     |
|                                               |   AEGP_GetCamera(                                                                                   |
|                                               |     PR_RenderContextH  render_contextH,                                                             |
|                                               |     const A_Time       *comp_timeP,                                                                 |
|                                               |     AEGP_LayerH        *camera_layerPH);                                                            |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------+
| ``AEGP_GetCameraType``                        | Given a layer, returns the camera type of the layer.                                                |
|                                               |                                                                                                     |
|                                               | ::                                                                                                  |
|                                               |                                                                                                     |
|                                               |   AEGP_GetCameraType(                                                                               |
|                                               |     AEGP_LayerH      aegp_layerH,                                                                   |
|                                               |     AEGP_CameraType  *camera_typeP;                                                                 |
|                                               |                                                                                                     |
|                                               | The camera type can be the following:                                                               |
|                                               |                                                                                                     |
|                                               |   - ``AEGP_CameraType_NONE = -1``                                                                   |
|                                               |   - ``AEGP_CameraType_PERSPECTIVE``                                                                 |
|                                               |   - ``AEGP_CameraType_ORTHOGRAPHIC``                                                                |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------+
| ``AEGP_GetDefaultCameraDistanceToImagePlane`` | Given a composition handle, returns the camera distance to the image plane.                         |
|                                               |                                                                                                     |
|                                               | ::                                                                                                  |
|                                               |                                                                                                     |
|                                               |   AEGP_GetDefaultCamera DistanceToImagePlane(                                                       |
|                                               |     AEGP_CompH  compH,                                                                              |
|                                               |     A_FpLong    *dist_to_planePF)                                                                   |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------+
| ``AEGP_GetCameraFilmSize``                    | Retrieves the size (and units used to measure that size) of the film used by the designated camera. |
|                                               |                                                                                                     |
|                                               | ::                                                                                                  |
|                                               |                                                                                                     |
|                                               |   AEGP_GetCameraFilmSize(                                                                           |
|                                               |     AEGP_LayerH         camera_layerH,                                                              |
|                                               |     AEGP_FilmSizeUnits  *film_size_unitsP,                                                          |
|                                               |     A_FpLong            *film_sizePF0);                                                             |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------+
| ``AEGP_SetCameraFilmSize``                    | Sets the size (and unites used to measure that size) of the film used by the designated camera.     |
|                                               |                                                                                                     |
|                                               | ::                                                                                                  |
|                                               |                                                                                                     |
|                                               |   AEGP_SetCameraFilmSize)(                                                                          |
|                                               |     AEGP_LayerH         camera_layerH,                                                              |
|                                               |     AEGP_FilmSizeUnits  film_size_units,                                                            |
|                                               |     A_FpLong            *film_sizePF0);                                                             |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------+

----

Notes Regarding Camera Behavior
================================================================================

Camera orientation is in composition coordinates, and the rotations are in layer (the camera's layer) coordinates.

If the camera layer has a parent, the position is in a coordinate space relative to the parent.

----

Orthographic Camera Matrix
================================================================================

Internally, we use composition width and height to set the matrix described by the OpenGL specification as

::

  glOrtho(-width/2, width/2, -height/2, height/2, -1, 100);

The orthographic matrix describes the projection. The position of the camera is described by another, scaled matrix. The inverse of the camera position matrix provides the "eye" coordinates.

----

Focus On Focal
================================================================================

Remember, focal length affects field of view; focal distance only affects depth of field.

----

Film Size
================================================================================

In the real world, film size is measured in millimeters. In After Effects, it's measured in pixels. Multiply by 72 and divide by 25.4 to move from millimeters to pixels.

Field of view is more complex;

ϴ = 1/2 field of view

tan(ϴ) = 1/2 composition height / focal length

focal length = 2 tan(ϴ) / composition height

----

Hit The Lights!
================================================================================

Get and set the type of lights in a composition.

AEGP_LightSuite2
********************************************************************************

+-----------------------+-----------------------------------------------------------------+
|     **Function**      |                           **Purpose**                           |
+=======================+=================================================================+
| ``AEGP_GetLightType`` | Retrieves the ``AEGP_LightType`` of the specified camera layer. |
|                       |                                                                 |
|                       | ::                                                              |
|                       |                                                                 |
|                       |   AEGP_GetLightType(                                            |
|                       |     AEGP_LayerH     light_layerH,                               |
|                       |     AEGP_LightType  *light_typeP);                              |
|                       |                                                                 |
|                       | ``AEGP_LightType`` will be one of the following:                |
|                       |                                                                 |
|                       |   - ``AEGP_LightType_PARALLEL``                                 |
|                       |   - ``AEGP_LightType_SPOT``                                     |
|                       |   - ``AEGP_LightType_POINT``                                    |
|                       |   - ``AEGP_LightType_AMBIENT``                                  |
+-----------------------+-----------------------------------------------------------------+
| ``AEGP_SetLightType`` | Sets the ``AEGP_LightType`` for the specified camera layer.     |
|                       |                                                                 |
|                       | ::                                                              |
|                       |                                                                 |
|                       |   AEGP_SetLightType(                                            |
|                       |     AEGP_LayerH     light_layerH,                               |
|                       |     AEGP_LightType  light_type);                                |
+-----------------------+-----------------------------------------------------------------+

Notes On Light Behavior
*********************************************************************************

The formula for parallel lights is found in Foley and Van Dam's "Introduction to Computer Graphics" (ISBN 0-201-60921-5) as is the formula for point lights.

We use the half angle variant proposed by Jim Blinn instead.

Suppose we have a point on a layer and want to shade it with the light.

Let V be the unit vector from the layer point to the eye point.
Let L be the unit vector to the light (in the parallel light case this is constant). Let H be (V+L)/2 (normalized).
Let N be the unit normal vector to the layer.

The amount of specular reflected light is S * power(H Dot N, shine), where S is the specular coefficient.

----

How Should I Draw That?
================================================================================

After Effects relies upon Artisans to draw 3D layer handles. If your Artisan chooses not to respond to this call, the default Artisan will draw 3D layer handles for you. Querying transforms is important for optimization of After Effects' caching.

The coordinate system is positive x to right, positive y down, positive z into the screen. The origin is the upper left corner. Rotations are x then y then z. For matrices the translate is the bottom row, orientations are quaternions (which are applied first), then any x-y-z rotation after that. As a general rule, use orientation or rotation but not both. Also use rotations if you need control over angular velocity.

----

Query Transform Functions
================================================================================

These functions give artisans information about the transforms they'll need in order to correctly place layers within a composition and respond appropriately to the various queries After Effects will send to their ``PR_QueryFunc`` entry point function.

As that entry point is optional, so is your artisan's response to the queries; however, if you don't, your users may be disappointed that (while doing interactive preview drawing) all the camera and light indicators vanish, until they stop moving! Artisans are complex beasts; contact us if you have any questions.

AEGP_QueryXFormSuite2
********************************************************************************

+---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
|             **Function**              |                                                                    **Purpose**                                                                    |
+=======================================+===================================================================================================================================================+
| ``AEGP_QueryXformGetSrcType``         | Given a query context, returns trasnsform source currently being modified.                                                                        |
|                                       |                                                                                                                                                   |
|                                       | ::                                                                                                                                                |
|                                       |                                                                                                                                                   |
|                                       |   AEGP_QueryXformGetSrcType(                                                                                                                      |
|                                       |     PR_QueryContextH     query_contextH,                                                                                                          |
|                                       |     AEGP_QueryXformType  *src_type);                                                                                                              |
|                                       |                                                                                                                                                   |
|                                       | The query context will be one of the following:                                                                                                   |
|                                       |                                                                                                                                                   |
|                                       |   - ``AEGP_Query_Xform_LAYER``,                                                                                                                   |
|                                       |   - ``AEGP_Query_Xform_WORLD``,                                                                                                                   |
|                                       |   - ``AEGP_Query_Xform_VIEW``,                                                                                                                    |
|                                       |   - ``AEGP_Query_Xform_SCREEN``                                                                                                                   |
+---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_QueryXformGetDstType``         | Given a query context, returns the currently requested transform destination.                                                                     |
|                                       |                                                                                                                                                   |
|                                       | ::                                                                                                                                                |
|                                       |                                                                                                                                                   |
|                                       |   AEGP_QueryXformGetDstType(                                                                                                                      |
|                                       |     PR_QueryContextH     query_contextH,                                                                                                          |
|                                       |     AEGP_QueryXformType  *dst_type);                                                                                                              |
+---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_QueryXformGetLayer``           | Used if the source or destination type is a layer. Given a query context, returns the layer handle.                                               |
|                                       |                                                                                                                                                   |
|                                       | ::                                                                                                                                                |
|                                       |                                                                                                                                                   |
|                                       |   AEGP_QueryXformGetLayer(                                                                                                                        |
|                                       |     PR_QueryContextH  query_contextH,                                                                                                             |
|                                       |     AEGP_LayerH       *layerPH);                                                                                                                  |
+---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_QueryXformGetComp``            | Given a query context, returns the current composition handle.                                                                                    |
|                                       |                                                                                                                                                   |
|                                       | ::                                                                                                                                                |
|                                       |                                                                                                                                                   |
|                                       |   AEGP_QueryXformGetComp(                                                                                                                         |
|                                       |     PR_QueryContextH  query_contextH,                                                                                                             |
|                                       |     AEGP_CompH        *compPH);                                                                                                                   |
+---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_QueryXformGetTransformTime``   | Given a query context, returns the time of the transformation.                                                                                    |
|                                       |                                                                                                                                                   |
|                                       | ::                                                                                                                                                |
|                                       |                                                                                                                                                   |
|                                       |   AEGP_QueryXformGetTransformTime(                                                                                                                |
|                                       |     PR_QueryContextH  query_contextH,                                                                                                             |
|                                       |     A_Time            *time);                                                                                                                     |
+---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_QueryXformGetViewTime``        | Given a query context, returns the time of the associated view.                                                                                   |
|                                       |                                                                                                                                                   |
|                                       | ::                                                                                                                                                |
|                                       |                                                                                                                                                   |
|                                       |   AEGP_QueryXformGetViewTime(                                                                                                                     |
|                                       |     PR_QueryContextH  query_contextH,                                                                                                             |
|                                       |     A_Time            *time);                                                                                                                     |
+---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_QueryXformGetCamera``          | Given a query context, returns the current camera layer handle.                                                                                   |
|                                       |                                                                                                                                                   |
|                                       | ::                                                                                                                                                |
|                                       |                                                                                                                                                   |
|                                       |   AEGP_QueryXformGetCamera(                                                                                                                       |
|                                       |     PR_QueryContextH  query_contextH,                                                                                                             |
|                                       |     AEGP_LayerH       *camera_layerPH);                                                                                                           |
+---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_QueryXformGetXform``           | Given a query context, returns the current matrix transform.                                                                                      |
|                                       |                                                                                                                                                   |
|                                       | ::                                                                                                                                                |
|                                       |                                                                                                                                                   |
|                                       |   AEGP_QueryXformGetXform(                                                                                                                        |
|                                       |     PR_QueryContextH  query_contextH,                                                                                                             |
|                                       |     A_Matrix4         *xform);                                                                                                                    |
+---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_QueryXformSetXform``           | Given a query context, return the matrix transform you compute in ``xform``.                                                                      |
|                                       |                                                                                                                                                   |
|                                       | ::                                                                                                                                                |
|                                       |                                                                                                                                                   |
|                                       |   AEGP_QueryXformSetXform(                                                                                                                        |
|                                       |     PR_QueryContextH  query_contextH,                                                                                                             |
|                                       |     A_Matrix4         *xform);                                                                                                                    |
+---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_QueryWindowRef``               | Sets the window reference to be used (by After Effects) for the given ``PR_QueryContextH``.                                                       |
|                                       |                                                                                                                                                   |
|                                       | ::                                                                                                                                                |
|                                       |                                                                                                                                                   |
|                                       |   AEGP_QueryWindowRef(                                                                                                                            |
|                                       |     PR_QueryContextH        q_contextH,                                                                                                           |
|                                       |     AEGP_PlatformWindowRef  *window_refP);                                                                                                        |
+---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_QueryWindowClear``             | Returns which ``AEGP_PlatformWindowRef`` (and ``A_Rect``) to clear, for the given ``PR_QueryContextH``.                                           |
|                                       |                                                                                                                                                   |
|                                       | ::                                                                                                                                                |
|                                       |                                                                                                                                                   |
|                                       |   AEGP_QueryWindowClear(                                                                                                                          |
|                                       |     PR_QueryContextH        q_contextH,                                                                                                           |
|                                       |     AEGP_PlatformWindowRef  *window_refP,                                                                                                         |
|                                       |     A_LegacyRect            *boundsPR);                                                                                                           |
+---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_QueryFrozenProxy``             | Returns whether or not the textures used in the given ``PR_QueryContextH`` should be frozen.                                                      |
|                                       |                                                                                                                                                   |
|                                       | ::                                                                                                                                                |
|                                       |                                                                                                                                                   |
|                                       |   AEGP_QueryFrozenProxy(                                                                                                                          |
|                                       |     PR_QueryContextH  q_contextH,                                                                                                                 |
|                                       |     A_Boolean         *onPB);                                                                                                                     |
+---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_QuerySwapBuffer``              | Sent after rendering and camera/light handle drawing is complete; After Effects returns the buffer into which the artisan should draw its output. |
|                                       |                                                                                                                                                   |
|                                       | ::                                                                                                                                                |
|                                       |                                                                                                                                                   |
|                                       |   AEGP_QuerySwapBuffer(                                                                                                                           |
|                                       |     PR_QueryContextH        q_contextH,                                                                                                           |
|                                       |     AEGP_PlatformWindowRef  *window_refP,                                                                                                         |
|                                       |     AEGP_WorldH             *dest_bufferp);                                                                                                       |
+---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_QueryDrawProcs``               | Sets the interactive drawing functions After Effects will call while drawing camera and lighting handles into the artisan's provided context.     |
|                                       |                                                                                                                                                   |
|                                       | ::                                                                                                                                                |
|                                       |                                                                                                                                                   |
|                                       |   AEGP_QueryDrawProcs(                                                                                                                            |
|                                       |     PR_QueryContextH         query_contextH,                                                                                                      |
|                                       |     PR_InteractiveDrawProcs  *window_refP);                                                                                                       |
+---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_QueryPrepareForLineDrawing``   | Informs After Effects about the context into which it will be drawing.                                                                            |
|                                       |                                                                                                                                                   |
|                                       | ::                                                                                                                                                |
|                                       |                                                                                                                                                   |
|                                       |   AEGP_QueryPrepareForLineDrawing(                                                                                                                |
|                                       |     PR_QueryContextH        query_contextH,                                                                                                       |
|                                       |     AEGP_PlatformWindowRef  *window_refP,                                                                                                         |
|                                       |     A_LegacyRect            *viewportP,                                                                                                           |
|                                       |     A_LPoint                *originP,                                                                                                             |
|                                       |     A_FloatPoint            *scaleP);                                                                                                             |
+---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_QueryUnprepareForLineDrawing`` | As far as After Effects is concerned, the artisan is done drawing lines.                                                                          |
|                                       |                                                                                                                                                   |
|                                       | ::                                                                                                                                                |
|                                       |                                                                                                                                                   |
|                                       |   AEGP_QueryUnprepareForLineDrawing(                                                                                                              |
|                                       |     PR_QueryContextH        query_contextH,                                                                                                       |
|                                       |     AEGP_PlatformWindowRef  *window_refP);                                                                                                        |
+---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+

----

Interactive Drawing Functions
================================================================================

We've added the ability for artisans to provide functions After Effects can use to do basic drawing functions for updating the comp window display during preview, including camera, light, and wireframe preview modeling.

PR_InteractiveDrawProcs
********************************************************************************

+---------------------------+------------------------------+
|       **Function**        |         **Purpose**          |
+===========================+==============================+
| ``PR_Draw_MoveToFunc``    | ::                           |
|                           |                              |
|                           |   PR_Draw_MoveToFunc(        |
|                           |     short  x,                |
|                           |     short  y);               |
+---------------------------+------------------------------+
| ``PR_Draw_LineToFunc``    | ::                           |
|                           |                              |
|                           |   PR_Draw_LineToFunc(        |
|                           |     short  x,                |
|                           |     short  y);               |
+---------------------------+------------------------------+
| ``PR_Draw_ForeColorFunc`` | ::                           |
|                           |                              |
|                           |   PR_Draw_ForeColorFunc(     |
|                           |     const A_Color  *fore_colo|
+---------------------------+------------------------------+
| ``PR_Draw_FrameRectFunc`` | ::                           |
|                           |                              |
|                           |   PR_Draw_FrameRectFunc(     |
|                           |     const A_Rect  *rectPR ); |
+---------------------------+------------------------------+
| ``PR_Draw_PaintRectFunc`` | ::                           |
|                           |                              |
|                           |   PR_Draw_PaintRectFunc(     |
|                           |     const A_Rect  *rectPR ); |
+---------------------------+------------------------------+

----

Notes On Query Time Functions
================================================================================

``AEGP_QueryXformGetTransformTime()`` and ``AEGP_QueryXformGetViewTime()`` are both necessary for an artisan to build a representation of the scene to render.

``AEGP_QueryXformGetTransformTime()`` gets the time of the transform, which is then passed to ``AEGP_GetCompShutterFrameRange()`` from :ref:`aegps/aegp-suites.AEGP_CompSuite`.

``AEGP_QueryXformGetViewTime()`` gets the time of the view, which is used in calling ``AEGP_GetLayerToWorldXformFromView()`` from :ref:`aegps/aegp-suites.AEGP_LayerSuite`.

