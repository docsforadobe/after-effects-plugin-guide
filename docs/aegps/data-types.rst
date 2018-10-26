.. _aegps/data-types:

data-types
################################################################################

Whenever possible, After Effects presents plug-ins with opaque data types, and provides accessor functions for manipulating them. For example, video frames are represented using the opaque AEGP_WorldH. While in some cases it might be more efficient to simply modify the underlying structure, by maintaining the opaqueness of the data types we allow for changes to our implementation without making you recompile (and redistribute) your plug- ins.

----

AEGP API Data Types
================================================================================

+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
|           **Type**           |                                                                  **Describes**                                                                  |                  **Manage Using**                   |
+==============================+=================================================================================================================================================+=====================================================+
| ``AEGP_MemHandle``           | This structure contains more than just the referenced memory. So it should not be dereferenced directly.                                        | `AEGP Memory Suite <#_bookmark550>`__               |
|                              | Use ``AEGP_LockMemHandle`` in the AEGP Memory Suite to get a pointer to the memory referenced by the ``AEGP_MemHandle``.                        |                                                     |
|                              | And of course, unlock it when you're done.                                                                                                      |                                                     |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| ``AEGP_ProjectH``            | The current After Effects project. Projects are a set of elements arranged hierarchically in a tree to preserve semantic relationships.         | `AEGP Proj Suite <#_bookmark565>`__                 |
|                              | Interior nodes of the tree are folders.                                                                                                         |                                                     |
|                              | As of CS6, there will only ever be one open project.                                                                                            |                                                     |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| ``AEGP_ItemH``               | An abstraction describing any element of a project, including folders. An item is anything that can be selected.                                | `AEGP Item Suite <#_bookmark570>`__                 |
|                              | Since multiple object types can be selected, we treat them as AEGP_ItemHs until more specificity is required.                                   |                                                     |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| ``AEGP_Collection2H``        | A set of selected items.                                                                                                                        | `AEGP Collection Suite <#_bookmark572>`__           |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| ``AEGP_CompH``               | A composition is a sequence of renderable items that, together, produce output.                                                                 | `AEGP Comp Suite <#_bookmark577>`__                 |
|                              | A composition exists over a time interval.                                                                                                      |                                                     |
|                              | Multiple compositions can exist within one project.                                                                                             |                                                     |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| ``AEGP_FootageH``            | An item that can be rendered. Folders and compositions are the only items that are not footage.                                                 | `AEGP Footage Suite <#_bookmark584>`__              |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| ``AEGP_LayerH``              | An element of a composition. Layers are rendered in sequence, which allows for occlusions.                                                      | `AEGP Layer Suite <#_bookmark595>`__                |
|                              | Solids, text, paint, cameras, lights, images, and image sequences are all represented as layers.                                                |                                                     |
|                              |                                                                                                                                                 |                                                     |
|                              | Layers may be defined over sub-intervals of the composition's time interval.                                                                    |                                                     |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| ``AEGP_WorldH``              | A frame of pixels.                                                                                                                              | `AEGP World Suite <#_bookmark694>`__                |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| ``AEGP_EffectRefH``          | An effect applied to a layer. An effect is a function that takes as its argument a layer (and possibly other parameters)                        |                                                     |
|                              | and returns an altered version of the layer for rendering.                                                                                      | `AEGP Effect Suite <#_bookmark611>`__               |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| ``AEGP_StreamRefH``          | Any `parameter stream <#_bookmark618>`__ attached to a layer, in a composition.                                                                 | `AEGP Stream <#_bookmark625>`__,                    |
|                              | See the description of `AEGP_GetNewLayerStream <#_bookmark627>`__ for a full list of stream types.                                              | `Suite <#_bookmark625>`__                           |
|                              |                                                                                                                                                 | `AEGP <#_bookmark638>`__                            |
|                              |                                                                                                                                                 | `Dynamic Stream <#_bookmark638>`__                  |
|                              |                                                                                                                                                 | `Suite <#_bookmark638>`__, `AEGP <#_bookmark646>`__ |
|                              |                                                                                                                                                 | `Keyframe Suite <#_bookmark646>`__                  |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| ``AEGP_MaskRefH``            | A mask applied to a layer. An AEGP_MaskRefH is used to access details about the mask stream, not the specific points which constitute the mask. | `AEGP Mask Suite <#_bookmark658>`__                 |
|                              | A mask is a rasterized path (sequence of vertices) that partitions a layer into two pieces, allowing each to be rendered differently.           |                                                     |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| ``AEGP_MaskOutlineValH``     | The specific points which constitute the mask.                                                                                                  | `AEGP Mask Outline Suite <#_bookmark664>`__         |
|                              | The points in a mask outline are ordered, and the mask need not be closed.                                                                      |                                                     |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| ``AEGP_TextDocumentH``       | Represents the actual text associated with a text layer.                                                                                        | `AEGP Text Document Suite <#_bookmark667>`__        |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| ``AEGP_TextOutlinesH``       | A reference to all the paths that make up the outlines of a given text layer.                                                                   | `AEGP Text Layer Suite <#_bookmark669>`__           |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| ``AEGP_MarkerVal``           | The data associated with a given timeline marker.                                                                                               | `AEGP Marker Suite <#_bookmark655>`__               |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| ``AEGP_PersistentBlobH``     | A "blob" of data containing the current preferences.                                                                                            | *AEGP Persistent Data Suite*                        |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| ``AEGP_RenderOptionsH``      | The settings associated with a render request.                                                                                                  | `AEGP Render Options Suite <#_bookmark686>`__       |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| ``AEGP_LayerRenderOptionsH`` | The settings associated with a layer render request.                                                                                            | `AEGP Layer Render Options Suite <#_bookmark687>`__ |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| ``AEGP_FrameReceiptH``       | A reference to a rendered frame.                                                                                                                | `AEGP Render Suite <#_bookmark689>`__               |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| ``AEGP_RQItemRefH``          | An item in the render queue.                                                                                                                    | `AEGP Render <#_bookmark704>`__                     |
|                              |                                                                                                                                                 | `Queue Suite <#_bookmark704>`__,                    |
|                              |                                                                                                                                                 | *AEGP Render Queue Item Suite*                      |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| ``AEGP_OutputModuleRefH``    | An output module, attached to a specific AEGP_RQItemRef in the render queue.                                                                    | `AEGP Output Module Suite <#_bookmark712>`__        |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| ``AEGP_SoundDataH``          | The `audio settings <#_bookmark702>`__ used for a given layer.                                                                                  | `AEGP Sound Data Suite <#_bookmark700>`__           |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| ``AEGP_RenderLayerContextH`` | State information at the time of a render request, sent to an Artisan by After Effects.                                                         | `AEGP Canvas Suite <#_bookmark746>`__               |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+
| ``AEGP_RenderReceiptH``      | Used by Artisans when rendering.                                                                                                                | `AEGP Canvas Suite <#_bookmark746>`__               |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------+

----

Nasty, Brutish, and Short
================================================================================

Information about layers, streams, and many other items doesn’t survive long; it’s often invalidated by user activity.

Anything that modifies the quantity (not quality) of items will invalidate references to those items; adding a keyframe to a stream invalidates references to that stream, but forcing a layer to be rendered doesn’t invalidate references to it. Do not cache layer pixels.

Caching references between calls to a specific hook function within your plug-in is not recommended; acquire information when you need it, and forget (release) it as soon as possible.

----

Were You Just Going To Leave That Data Lying Around?
================================================================================

When you ask After Effects to populate and return handles to data structures, it’s important that you clean up after yourself. For the following data types, you must call the appropriate disposal routines.

----

Data Types Requiring Disposal
================================================================================

+------------------------------+----------------------------------------------------------------------------------------------------+
|        **Data Type**         |                                       **Disposal function**                                        |
+==============================+====================================================================================================+
| ``AEGP_Collection2H``        | `AEGP_DisposeCollection <#_bookmark574>`__                                                         |
+------------------------------+----------------------------------------------------------------------------------------------------+
| ``AEGP_FootageH``            | `AEGP_DisposeFootage <#_bookmark587>`__                                                            |
+------------------------------+----------------------------------------------------------------------------------------------------+
| ``AEGP_WorldH``              | `AEGP_Dispose <#_bookmark696>`__ (in `AEGP_WorldSuite <#_bookmark694>`__)                          |
|                              |                                                                                                    |
|                              | Or `AEGP_DisposeTexture <#_bookmark750>`__, if layer texture created using ``AEGP_RenderTexture``) |
+------------------------------+----------------------------------------------------------------------------------------------------+
| ``AEGP_EffectRefH``          | `AEGP_DisposeEffect <#_bookmark613>`__                                                             |
+------------------------------+----------------------------------------------------------------------------------------------------+
| ``AEGP_MaskRefH``            | `AEGP_DisposeMask <#_bookmark661>`__                                                               |
+------------------------------+----------------------------------------------------------------------------------------------------+
| ``AEGP_RenderOptionsH``      | AEGP_Dispose (in `AEGP_RenderOptionsSuite <#_bookmark686>`__)                                      |
+------------------------------+----------------------------------------------------------------------------------------------------+
| ``AEGP_LayerRenderOptionsH`` | AEGP_Dispose (in `AEGP_LayerRenderOptionsSuite <#_bookmark687>`__)                                 |
+------------------------------+----------------------------------------------------------------------------------------------------+
| ``AEGP_RenderReceiptH``      | AEGP_DisposeRenderReceipt                                                                          |
+------------------------------+----------------------------------------------------------------------------------------------------+

