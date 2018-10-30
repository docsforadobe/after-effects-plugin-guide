.. _aegps/data-types:

Data Types
################################################################################

Whenever possible, After Effects presents plug-ins with opaque data types, and provides accessor functions for manipulating them. For example, video frames are represented using the opaque AEGP_WorldH. While in some cases it might be more efficient to simply modify the underlying structure, by maintaining the opaqueness of the data types we allow for changes to our implementation without making you recompile (and redistribute) your plug- ins.

----

AEGP API Data Types
================================================================================

+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
|           **Type**           |                                                                  **Describes**                                                                  |                                    **Manage Using**                                     |
+==============================+=================================================================================================================================================+=========================================================================================+
| ``AEGP_MemHandle``           | This structure contains more than just the referenced memory. So it should not be dereferenced directly.                                        | :ref:`AEGP Memory Suite <aegps/aegp-suites.memory-suite>`                               |
|                              | Use ``AEGP_LockMemHandle`` in the AEGP Memory Suite to get a pointer to the memory referenced by the ``AEGP_MemHandle``.                        |                                                                                         |
|                              | And of course, unlock it when you're done.                                                                                                      |                                                                                         |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| ``AEGP_ProjectH``            | The current After Effects project. Projects are a set of elements arranged hierarchically in a tree to preserve semantic relationships.         | :ref:`AEGP Project Suite <aegps/aegp-suites.project-suite>`                             |
|                              | Interior nodes of the tree are folders.                                                                                                         |                                                                                         |
|                              | As of CS6, there will only ever be one open project.                                                                                            |                                                                                         |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| ``AEGP_ItemH``               | An abstraction describing any element of a project, including folders. An item is anything that can be selected.                                | :ref:`AEGP Item Suite <aegps/aegp-suites.item-suite>`                                   |
|                              | Since multiple object types can be selected, we treat them as AEGP_ItemHs until more specificity is required.                                   |                                                                                         |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| ``AEGP_Collection2H``        | A set of selected items.                                                                                                                        | :ref:`AEGP Collection Suite <aegps/aegp-suites.collection-suite>`                       |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| ``AEGP_CompH``               | A composition is a sequence of renderable items that, together, produce output.                                                                 | :ref:`AEGP Composition Suite <aegps/aegp-suites.composition-suite>`                     |
|                              | A composition exists over a time interval.                                                                                                      |                                                                                         |
|                              | Multiple compositions can exist within one project.                                                                                             |                                                                                         |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| ``AEGP_FootageH``            | An item that can be rendered. Folders and compositions are the only items that are not footage.                                                 | :ref:`AEGP Footage Suite <aegps/aegp-suites.footage-suite>`                             |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| ``AEGP_LayerH``              | An element of a composition. Layers are rendered in sequence, which allows for occlusions.                                                      | :ref:`AEGP Layer Suite <aegps/aegp-suites.layer-suite>`                                 |
|                              | Solids, text, paint, cameras, lights, images, and image sequences are all represented as layers.                                                |                                                                                         |
|                              |                                                                                                                                                 |                                                                                         |
|                              | Layers may be defined over sub-intervals of the composition's time interval.                                                                    |                                                                                         |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| ``AEGP_WorldH``              | A frame of pixels.                                                                                                                              | :ref:`AEGP World Suite <aegps/aegp-suites.world-suite>`                                 |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| ``AEGP_EffectRefH``          | An effect applied to a layer. An effect is a function that takes as its argument a layer (and possibly other parameters)                        | :ref:`AEGP Effect Suite <aegps/aegp-suites.effect-suite>`                               |
|                              | and returns an altered version of the layer for rendering.                                                                                      |                                                                                         |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| ``AEGP_StreamRefH``          | Any :ref:`parameter stream <aegps/aegp-suites.diving-into-streams>` attached to a layer, in a composition.                                      | :ref:`AEGP Stream Suite <aegps/aegp-suites.stream-suite>`,                              |
|                              | See the description of ``AEGP_GetNewLayerStream`` from :ref:`aegps/aegp-suites.AEGP_StreamSuite5` for a full list of stream types.              | :ref:`AEGP Dynamic Stream Suite <aegps/aegp-suites.dynamic-stream-suite>`               |
|                              |                                                                                                                                                 | :ref:`AEGP Keyframe Suite <aegps/aegp-suites.keyframe-suite>`                           |
|                              |                                                                                                                                                 |                                                                                         |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| ``AEGP_MaskRefH``            | A mask applied to a layer. An AEGP_MaskRefH is used to access details about the mask stream, not the specific points which constitute the mask. | :ref:`AEGP Mask Suite <aegps/aegp-suites.mask-suite>`                                   |
|                              | A mask is a rasterized path (sequence of vertices) that partitions a layer into two pieces, allowing each to be rendered differently.           |                                                                                         |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| ``AEGP_MaskOutlineValH``     | The specific points which constitute the mask.                                                                                                  | :ref:`AEGP Mask Outline Suite <aegps/aegp-suites.mask-outline-suite>`                   |
|                              | The points in a mask outline are ordered, and the mask need not be closed.                                                                      |                                                                                         |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| ``AEGP_TextDocumentH``       | Represents the actual text associated with a text layer.                                                                                        | :ref:`AEGP Text Document Suite <aegps/aegp-suites.text-document-suite>`                 |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| ``AEGP_TextOutlinesH``       | A reference to all the paths that make up the outlines of a given text layer.                                                                   | :ref:`AEGP Text Layer Suite <aegps/aegp-suites.text-layer-suite>`                       |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| ``AEGP_MarkerVal``           | The data associated with a given timeline marker.                                                                                               | :ref:`AEGP Marker Suite <aegps/aegp-suites.marker-suite>`                               |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| ``AEGP_PersistentBlobH``     | A "blob" of data containing the current preferences.                                                                                            | :ref:`AEGP Persistent Data Suite <aegps/aegp-suites.persistent-data-suite>`             |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| ``AEGP_RenderOptionsH``      | The settings associated with a render request.                                                                                                  | :ref:`AEGP Render Options Suite <aegps/aegp-suites.render-options-suite>`               |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| ``AEGP_LayerRenderOptionsH`` | The settings associated with a layer render request.                                                                                            | :ref:`AEGP Layer Render Options Suite <aegps/aegp-suites.AEGP_LayerRenderOptionsSuite>` |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| ``AEGP_FrameReceiptH``       | A reference to a rendered frame.                                                                                                                | :ref:`AEGP Render Suite <aegps/aegp-suites.render-suite>`                               |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| ``AEGP_RQItemRefH``          | An item in the render queue.                                                                                                                    | :ref:`AEGP Render Queue Suite <aegps/aegp-suites.render-queue-suite>`                   |
|                              |                                                                                                                                                 | :ref:`AEGP Render Queue Item Suite <aegps/aegp-suites.render-queue-iteme-suite>`        |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| ``AEGP_OutputModuleRefH``    | An output module, attached to a specific AEGP_RQItemRef in the render queue.                                                                    | :ref:`AEGP Output Module Suite <aegps/aegp-suites.output-module-suite>`                 |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| ``AEGP_SoundDataH``          | The :ref:`audio settings <aegps/aegp-suites.sound-data-suite>` used for a given layer.                                                          | :ref:`AEGP Sound Data Suite <aegps/aegp-suites.sound-data-suite>`                       |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| ``AEGP_RenderLayerContextH`` | State information at the time of a render request, sent to an Artisan by After Effects.                                                         | :ref:`AEGP Canvas Suite <artisans/artisan-data-types.AEGP_CanvasSuite8>`                |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| ``AEGP_RenderReceiptH``      | Used by Artisans when rendering.                                                                                                                | :ref:`AEGP Canvas Suite <artisans/artisan-data-types.AEGP_CanvasSuite8>`                |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+

----

Nasty, Brutish, and Short
================================================================================

Information about layers, streams, and many other items doesn't survive long; it's often invalidated by user activity.

Anything that modifies the quantity (not quality) of items will invalidate references to those items; adding a keyframe to a stream invalidates references to that stream, but forcing a layer to be rendered doesn't invalidate references to it. Do not cache layer pixels.

Caching references between calls to a specific hook function within your plug-in is not recommended; acquire information when you need it, and forget (release) it as soon as possible.

----

Were You Just Going To Leave That Data Lying Around?
================================================================================

When you ask After Effects to populate and return handles to data structures, it's important that you clean up after yourself. For the following data types, you must call the appropriate disposal routines.

----

Data Types Requiring Disposal
================================================================================

+------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
|        **Data Type**         |                                                             **Disposal function**                                                             |
+==============================+===============================================================================================================================================+
| ``AEGP_Collection2H``        | ``AEGP_DisposeCollection``, from :ref:`aegps/aegp-suites.AEGP_CollectionSuite2`                                                               |
+------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_FootageH``            | ``AEGP_DisposeFootage``, from :ref:`aegps/aegp-suites.AEGP_FootageSuite5`                                                                     |
+------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_WorldH``              | ``AEGP_Dispose``, from :ref:`aegps/aegp-suites.AEGP_WorldSuite3`                                                                              |
|                              |                                                                                                                                               |
|                              | Or ``AEGP_DisposeTexture``, from :ref:`artisans/artisan-data-types.AEGP_CanvasSuite8`, if layer texture created using ``AEGP_RenderTexture``) |
+------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_EffectRefH``          | ``AEGP_DisposeEffect``, from :ref:`aegps/aegp-suites.AEGP_EffectSuite4`                                                                       |
+------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_MaskRefH``            | ``AEGP_DisposeMask``, from :ref:`aegps/aegp-suites.AEGP_MaskSuite6`                                                                           |
+------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_RenderOptionsH``      | ``AEGP_Dispose``, from :ref:`aegps/aegp-suites.AEGP_RenderQueueMonitorSuite1`                                                                 |
+------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_LayerRenderOptionsH`` | ``AEGP_Dispose``, from :ref:`aegps/aegp-suites.AEGP_LayerRenderOptionsSuite`                                                                  |
+------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_RenderReceiptH``      | ``AEGP_DisposeRenderReceipt``, from :ref:`artisans/artisan-data-types.AEGP_CanvasSuite8`                                                      |
+------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+

