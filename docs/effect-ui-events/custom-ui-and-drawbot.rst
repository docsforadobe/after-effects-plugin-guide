.. _effect-ui-events/custom-ui-and-drawbot:

Custom UI & Drawbot
################################################################################

Custom UI uses a composited drawing model using Drawbot. The Drawbot suites can be used for:

1. Basic 2D path drawing: Lines, Rect, Arc, Bezier
2. Stroking/Filling/Shading paths
3. Image drawing: Compositing an ARGB/BGRA buffer onto the surface
4. Pushing/popping surface state
5. Text drawing, if supplier supports it (clients should first check if text drawing is supported before actual drawing)

Drawing may only occur during ``PF_Event_DRAW`` (and not during ``PF_Event_DRAG`` or ``PF_Event_DO_CLICK``).

To use Drawbot, first get the drawing reference by passing in PF_Context to a new suite call :ref:`PF_GetDrawingReference <effect-ui-events/custom-ui-and-drawbot.PF_EffectCustomUISuite>`.

If a non-NULL drawing reference is returned, use it to get the supplier and surface references from :ref:`DRAWBOT_DrawbotSuite <effect-ui-events/custom-ui-and-drawbot.Drawbot_DrawbotSuite>`.

The Drawbot suites include ``DRAWBOT_DrawbotSuite``, ``DRAWBOT_SupplierSuite``, ``DRAWBOT_SurfaceSuite``, ``DRAWBOT_PathSuite``.

----

Make Your Custom UI Look Not So "Custom"
================================================================================

Use the new :ref:`PF_EffectCustomUIOverlayThemeSuite <effect-ui-events/custom-ui-and-drawbot.PF_EffectCustomUIOverlayThemeSuite>` to match the host application UI. Your users will thank you.

----

.. _effect-ui-events/custom-ui-and-drawbot.redrawing:

Redrawing
================================================================================

In order to redraw a specific area of a pane, we recommend the following:

1) Call ``PF_InvalidateRect`` (from :ref:`effect-details/useful-utility-functions.PF_AppSuite`) from the effect. This will cause a lazy display redraw, and will update at the next available idle moment. This rect is in coordinates related to the associated pane. Using a NULL rect will update the entire pane.
2) Set the :ref:`event outflag <effect-ui-events/PF_EventExtra>` to ``PF_EO_UPDATE_NOW``, which will cause an immediate draw event for the specified pane when the current event returns.

If an effect needs to update more than one window at a time, it should set ``PF_OutFlag_REFRESH_UI`` (from :ref:`effect-basics/PF_OutData.PF_OutFlags`), which will cause a redraw of the entire ECW, comp, and layer windows.

----

HiDPI and Retina Display Support
================================================================================

To support HiDPI and Retina Displays, you can use offscreen images that are twice the size, and then use the ``Transform`` function from :ref:`effect-ui-events/custom-ui-and-drawbot.Drawbot_SurfaceSuite` to scale the image down in half before drawing it.

----

.. _effect-ui-events/custom-ui-and-drawbot.PF_EffectCustomUISuite:

PF_EffectCustomUISuite
================================================================================

Enables an effect to get the drawing reference. This is the first call needed to use Drawbot.

PF_EffectCustomUISuite1
********************************************************************************

+----------------------------+-----------------------------------------+
|        **Function**        |             **Purpose**                 |
+============================+=========================================+
| ``PF_GetDrawingReference`` | Get the drawing reference.              |
|                            |                                         |
|                            | ::                                      |
|                            |                                         |
|                            |   PF_GetDrawingReference(               |
|                            |     const PF_ContextH  effect_contextH, |
|                            |     DRAWBOT_DrawRef    *referenceP0);   |
+----------------------------+-----------------------------------------+

----

.. _effect-ui-events/custom-ui-and-drawbot.Drawbot_DrawbotSuite:

Drawbot_DrawbotSuite
================================================================================

Using the Drawbot reference, get the supplier and surface references.

Drawbot_DrawbotSuite1
********************************************************************************

+-----------------+------------------------------------------------------------------------------------+
|  **Function**   |                                    **Purpose**                                     |
+=================+====================================================================================+
| ``GetSupplier`` | Get the supplier reference.                                                        |
|                 |                                                                                    |
|                 | Needed to use :ref:`effect-ui-events/custom-ui-and-drawbot.Drawbot_SupplierSuite`. |
|                 |                                                                                    |
|                 | ::                                                                                 |
|                 |                                                                                    |
|                 |   GetSupplier(                                                                     |
|                 |     DRAWBOT_DrawRef      in_drawbot_ref,                                           |
|                 |     DRAWBOT_SupplierRef  *out_supplierP);                                          |
+-----------------+------------------------------------------------------------------------------------+
| ``GetSurface``  | Get the surface reference.                                                         |
|                 |                                                                                    |
|                 | Needed to use :ref:`effect-ui-events/custom-ui-and-drawbot.Drawbot_SurfaceSuite`.  |
|                 |                                                                                    |
|                 | ::                                                                                 |
|                 |                                                                                    |
|                 |   GetSurface(                                                                      |
|                 |     DRAWBOT_DrawRef     in_drawbot_ref,                                            |
|                 |     DRAWBOT_SurfaceRef  *out_surfaceP);                                            |
+-----------------+------------------------------------------------------------------------------------+

----

.. _effect-ui-events/custom-ui-and-drawbot.Drawbot_SupplierSuite:

Drawbot_SupplierSuite
================================================================================

Calls to create and release drawing tools, get default settings, and query drawing capabilities.

Drawbot_SupplierSuite1
********************************************************************************

+-----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|        **Function**         |                                                                                 **Purpose**                                                                                 |
+=============================+=============================================================================================================================================================================+
| ``NewPen``                  | Create a new pen. Release this using ``ReleaseObject`` from :ref:`effect-ui-events/custom-ui-and-drawbot.Drawbot_SupplierSuite`.                                            |
|                             |                                                                                                                                                                             |
|                             | ::                                                                                                                                                                          |
|                             |                                                                                                                                                                             |
|                             |   NewPen(                                                                                                                                                                   |
|                             |     DRAWBOT_SupplierRef      in_supplier_ref,                                                                                                                               |
|                             |     const DRAWBOT_ColorRGBA  *in_colorP,                                                                                                                                    |
|                             |     float                    in_size,                                                                                                                                       |
|                             |     DRAWBOT_PenRef           *out_penP);                                                                                                                                    |
+-----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``NewBrush``                | Create a new brush. Release this using ``ReleaseObject`` from :ref:`effect-ui-events/custom-ui-and-drawbot.Drawbot_SupplierSuite`.                                          |
|                             |                                                                                                                                                                             |
|                             | ::                                                                                                                                                                          |
|                             |                                                                                                                                                                             |
|                             |   NewBrush(                                                                                                                                                                 |
|                             |     DRAWBOT_SupplierRef      in_supplier_ref,                                                                                                                               |
|                             |     const DRAWBOT_ColorRGBA  *in_colorP,                                                                                                                                    |
|                             |     DRAWBOT_BrushRef         *out_brushP);                                                                                                                                  |
+-----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``SupportsText``            | Check if current supplier supports text.                                                                                                                                    |
|                             |                                                                                                                                                                             |
|                             | ::                                                                                                                                                                          |
|                             |                                                                                                                                                                             |
|                             |   SupportsText(                                                                                                                                                             |
|                             |     DRAWBOT_SupplierRef  in_supplier_ref,                                                                                                                                   |
|                             |     DRAWBOT_Boolean      *out_supports_textB);                                                                                                                              |
+-----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``GetDefaultFontSize``      | Get the default font size.                                                                                                                                                  |
|                             |                                                                                                                                                                             |
|                             | ::                                                                                                                                                                          |
|                             |                                                                                                                                                                             |
|                             |   GetDefaultFontSize(                                                                                                                                                       |
|                             |     DRAWBOT_SupplierRef  in_supplier_ref,                                                                                                                                   |
|                             |     float                *out_font_sizeF);                                                                                                                                  |
+-----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``NewDefaultFont``          | Create a new font with default settings.                                                                                                                                    |
|                             |                                                                                                                                                                             |
|                             | You can pass the default font size from ``GetDefaultFontSize``.                                                                                                             |
|                             |                                                                                                                                                                             |
|                             | Release this using ``ReleaseObject`` from :ref:`effect-ui-events/custom-ui-and-drawbot.Drawbot_SupplierSuite`.                                                              |
|                             |                                                                                                                                                                             |
|                             | ::                                                                                                                                                                          |
|                             |                                                                                                                                                                             |
|                             |   NewDefaultFont(                                                                                                                                                           |
|                             |     DRAWBOT_SupplierRef  in_supplier_ref,                                                                                                                                   |
|                             |     float                in_font_sizeF,                                                                                                                                     |
|                             |     DRAWBOT_FontRef      *out_fontP);                                                                                                                                       |
+-----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``NewImageFromBuffer``      | Create a new image from buffer passed to in_dataP.                                                                                                                          |
|                             |                                                                                                                                                                             |
|                             | Release this using ``ReleaseObject`` from :ref:`effect-ui-events/custom-ui-and-drawbot.Drawbot_SupplierSuite`.                                                              |
|                             |                                                                                                                                                                             |
|                             | ::                                                                                                                                                                          |
|                             |                                                                                                                                                                             |
|                             |   NewImageFromBuffer(                                                                                                                                                       |
|                             |     DRAWBOT_SupplierRef  in_supplier_ref,                                                                                                                                   |
|                             |     int                  in_width,                                                                                                                                          |
|                             |     int                  in_height,                                                                                                                                         |
|                             |     int                  in_row_bytes,                                                                                                                                      |
|                             |     DRAWBOT_PixelLayout  in_pl,                                                                                                                                             |
|                             |     const void           *in_dataP,                                                                                                                                         |
|                             |     DRAWBOT_ImageRef     *out_imageP);                                                                                                                                      |
|                             |                                                                                                                                                                             |
|                             | ``DRAWBOT_PixelLayout`` can be one of the following:                                                                                                                        |
|                             |                                                                                                                                                                             |
|                             |   - ``kDRAWBOT_PixelLayout_24RGB``,                                                                                                                                         |
|                             |   - ``kDRAWBOT_PixelLayout_24BGR``,                                                                                                                                         |
|                             |   - ``kDRAWBOT_PixelLayout_32RGB``,                                                                                                                                         |
|                             |   - ``ARGB`` (A is ignored),                                                                                                                                                |
|                             |   - ``kDRAWBOT_PixelLayout_32BGR``,                                                                                                                                         |
|                             |   - ``BGRA`` (A is ignored),                                                                                                                                                |
|                             |   - ``kDRAWBOT_PixelLayout_32ARGB_Straight``,                                                                                                                               |
|                             |   - ``kDRAWBOT_PixelLayout_32ARGB_Premul``,                                                                                                                                 |
|                             |   - ``kDRAWBOT_PixelLayout_32BGRA_Straight``,                                                                                                                               |
|                             |   - ``kDRAWBOT_PixelLayout_32BGRA_Premul``                                                                                                                                  |
+-----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``NewPath``                 | Create a new path. Release this using ``ReleaseObject`` from :ref:`effect-ui-events/custom-ui-and-drawbot.Drawbot_SupplierSuite`.                                           |
|                             |                                                                                                                                                                             |
|                             | ::                                                                                                                                                                          |
|                             |                                                                                                                                                                             |
|                             |   NewPath(                                                                                                                                                                  |
|                             |     DRAWBOT_SupplierRef  in_supplier_ref,                                                                                                                                   |
|                             |     DRAWBOT_PathRef      *out_pathP);                                                                                                                                       |
+-----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``SupportsPixelLayoutBGRA`` | A given Drawbot implementation can support multiple channel orders, but will likely prefer one over the other.                                                              |
|                             | Use the following four callbacks to get the preferred channel order for any API that takes a ``DRAWBOT_PixelLayout`` (e.g. ``NewImageFromBuffer``).                         |
|                             |                                                                                                                                                                             |
|                             | ::                                                                                                                                                                          |
|                             |                                                                                                                                                                             |
|                             |   SupportsPixelLayoutBGRA(                                                                                                                                                  |
|                             |     DRAWBOT_SupplierRef  in_supplier_ref,                                                                                                                                   |
|                             |     DRAWBOT_Boolean      *out_supports_bgraPB);                                                                                                                             |
+-----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PrefersPixelLayoutBGRA``  | ::                                                                                                                                                                          |
|                             |                                                                                                                                                                             |
|                             |   PrefersPixelLayoutBGRA(                                                                                                                                                   |
|                             |     DRAWBOT_SupplierRef  in_supplier_ref,                                                                                                                                   |
|                             |     DRAWBOT_Boolean      *out_prefers_bgraPB);                                                                                                                              |
+-----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``SupportsPixelLayoutARGB`` | ::                                                                                                                                                                          |
|                             |                                                                                                                                                                             |
|                             |   SupportsPixelLayoutARGB(                                                                                                                                                  |
|                             |     DRAWBOT_SupplierRef  in_supplier_ref,                                                                                                                                   |
|                             |     DRAWBOT_Boolean      *out_supports_argbPB);                                                                                                                             |
+-----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PrefersPixelLayoutARGB``  | ::                                                                                                                                                                          |
|                             |                                                                                                                                                                             |
|                             |   PrefersPixelLayoutARGB(                                                                                                                                                   |
|                             |     DRAWBOT_SupplierRef  in_supplier_ref,                                                                                                                                   |
|                             |     DRAWBOT_Boolean      *out_prefers_argbPB);                                                                                                                              |
+-----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``RetainObject``            | Retain (increase reference count on) any object (pen, brush, path, etc). For example, it should be used when any object is copied and the copied object should be retained. |
|                             |                                                                                                                                                                             |
|                             | ::                                                                                                                                                                          |
|                             |                                                                                                                                                                             |
|                             |   RetainObject(                                                                                                                                                             |
|                             |     DRAWBOT_ObjectRef  in_obj_ref);                                                                                                                                         |
+-----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``ReleaseObject``           | Release (decrease reference count on) any object (pen, brush, path, etc). This function MUST be called for any object created using ``NewXYZ()`` from this suite.           |
|                             | Do not call this function on a ``DRAWBOT_SupplierRef`` and ``DRAWBOT_SupplierRef``, since these are not created by the plug-in.                                             |
|                             |                                                                                                                                                                             |
|                             | ::                                                                                                                                                                          |
|                             |                                                                                                                                                                             |
|                             |   ReleaseObject(                                                                                                                                                            |
|                             |     DRAWBOT_ObjectRef  in_obj_ref);                                                                                                                                         |
+-----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

----

.. _effect-ui-events/custom-ui-and-drawbot.Drawbot_SurfaceSuite:

Drawbot_SurfaceSuite
================================================================================

Calls to draw on the surface, and to query and set drawing settings.

Drawbot_SurfaceSuite1
********************************************************************************

+----------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
|        **Function**        |                                                             **Purpose**                                                              |
+============================+======================================================================================================================================+
| ``PushStateStack``         | Push the current surface state onto the stack. It should be popped to retrieve old state.                                            |
|                            | It is required to restore state if you are going to clip or transform a surface or change the interpolation or anti-aliasing policy. |
|                            |                                                                                                                                      |
|                            | ::                                                                                                                                   |
|                            |                                                                                                                                      |
|                            |   PushStateStack(                                                                                                                    |
|                            |     DRAWBOT_SurfaceRef  in_surface_ref);                                                                                             |
+----------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
| ``PopStateStack``          | Pop the last pushed surface state off the stack.                                                                                     |
|                            |                                                                                                                                      |
|                            | ::                                                                                                                                   |
|                            |                                                                                                                                      |
|                            |   PopStateStack(                                                                                                                     |
|                            |     DRAWBOT_SurfaceRef  in_surface_ref);                                                                                             |
+----------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
| ``PaintRect``              | Paint a rectangle with a color on the surface.                                                                                       |
|                            |                                                                                                                                      |
|                            | ::                                                                                                                                   |
|                            |                                                                                                                                      |
|                            |   PaintRect(                                                                                                                         |
|                            |     DRAWBOT_SurfaceRef       in_surface_ref,                                                                                         |
|                            |     const DRAWBOT_ColorRGBA  *in_colorP,                                                                                             |
|                            |     const DRAWBOT_RectF32    *in_rectPR);                                                                                            |
+----------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
| ``FillPath``               | Fill a path using a brush and fill type.                                                                                             |
|                            |                                                                                                                                      |
|                            | ::                                                                                                                                   |
|                            |                                                                                                                                      |
|                            |   FillPath(                                                                                                                          |
|                            |     DRAWBOT_SurfaceRef  in_surface_ref,                                                                                              |
|                            |     DRAWBOT_BrushRef    in_brush_ref,                                                                                                |
|                            |     DRAWBOT_PathRef     in_path_ref,                                                                                                 |
|                            |     DRAWBOT_FillType    in_fill_type);                                                                                               |
|                            |                                                                                                                                      |
|                            | ``DRAWBOT_FillType`` is one of the following:                                                                                        |
|                            |                                                                                                                                      |
|                            |   - ``kDRAWBOT_FillType_EvenOdd``,                                                                                                   |
|                            |   - ``kDRAWBOT_FillType_Winding``                                                                                                    |
+----------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
| ``StrokePath``             | Stroke a path using a pen.                                                                                                           |
|                            |                                                                                                                                      |
|                            | ::                                                                                                                                   |
|                            |                                                                                                                                      |
|                            |   StrokePath(                                                                                                                        |
|                            |     DRAWBOT_SurfaceRef  in_surface_ref,                                                                                              |
|                            |     DRAWBOT_PenRef      in_pen_ref,                                                                                                  |
|                            |     DRAWBOT_PathRef     in_path_ref);                                                                                                |
+----------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
| ``Clip``                   | Clip the surface.                                                                                                                    |
|                            |                                                                                                                                      |
|                            | ::                                                                                                                                   |
|                            |                                                                                                                                      |
|                            |   Clip(                                                                                                                              |
|                            |     DRAWBOT_SurfaceRef    in_surface_ref,                                                                                            |
|                            |     DRAWBOT_SupplierRef   in_supplier_ref,                                                                                           |
|                            |     const DRAWBOT_Rect32  *in_rectPR);                                                                                               |
+----------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
| ``GetClipBounds``          | Get clip bounds.                                                                                                                     |
|                            |                                                                                                                                      |
|                            | ::                                                                                                                                   |
|                            |                                                                                                                                      |
|                            |   GetClipBounds(                                                                                                                     |
|                            |     DRAWBOT_SurfaceRef  in_surface_ref,                                                                                              |
|                            |     DRAWBOT_Rect32      *out_rectPR);                                                                                                |
+----------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
| ``IsWithinClipBounds``     | Checks whether a rect is within the clip bounds.                                                                                     |
|                            |                                                                                                                                      |
|                            | ::                                                                                                                                   |
|                            |                                                                                                                                      |
|                            |   IsWithinClipBounds(                                                                                                                |
|                            |     DRAWBOT_SurfaceRef    in_surface_ref,                                                                                            |
|                            |     const DRAWBOT_Rect32  *in_rectPR,                                                                                                |
|                            |     DRAWBOT_Boolean       *out_withinPB);                                                                                            |
+----------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
| ``Transform``              | Transform the last surface state.                                                                                                    |
|                            |                                                                                                                                      |
|                            | ::                                                                                                                                   |
|                            |                                                                                                                                      |
|                            |   Transform(                                                                                                                         |
|                            |     DRAWBOT_SurfaceRef       in_surface_ref,                                                                                         |
|                            |     const DRAWBOT_MatrixF32  *in_matrixP);                                                                                           |
+----------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
| ``DrawString``             | Draw a string.                                                                                                                       |
|                            |                                                                                                                                      |
|                            | ::                                                                                                                                   |
|                            |                                                                                                                                      |
|                            |   DrawString(                                                                                                                        |
|                            |     DRAWBOT_SurfaceRef       in_surface_ref,                                                                                         |
|                            |     DRAWBOT_BrushRef         in_brush_ref,                                                                                           |
|                            |     DRAWBOT_FontRef          in_font_ref,                                                                                            |
|                            |     const DRAWBOT_UTF16Char  *in_stringP,                                                                                            |
|                            |     const DRAWBOT_PointF32   *in_originP,                                                                                            |
|                            |     DRAWBOT_TextAlignment    in_alignment_style,                                                                                     |
|                            |     DRAWBOT_TextTruncation   in_truncation_style,                                                                                    |
|                            |     float                    in_truncation_width);                                                                                   |
|                            |                                                                                                                                      |
|                            | ``DRAWBOT_TextAlignment`` is one of the following:                                                                                   |
|                            |                                                                                                                                      |
|                            |   - ``kDRAWBOT_TextAlignment_Left``,                                                                                                 |
|                            |   - ``kDRAWBOT_TextAlignment_Center``,                                                                                               |
|                            |   - ``kDRAWBOT_TextAlignment_Right``                                                                                                 |
|                            |                                                                                                                                      |
|                            | ``DRAWBOT_TextTruncation`` is one of the following:                                                                                  |
|                            |                                                                                                                                      |
|                            |   - ``kDRAWBOT_TextTruncation_None``,                                                                                                |
|                            |   - ``kDRAWBOT_TextTruncation_End``,                                                                                                 |
|                            |   - ``kDRAWBOT_TextTruncation_EndEllipsis``,                                                                                         |
|                            |   - ``kDRAWBOT_TextTruncation_PathEllipsis``                                                                                         |
+----------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
| ``DrawImage``              | Draw an image created using ``NewImageFromBuffer()`` on the surface. Alpha = [0.0f, 1.0f ].                                          |
|                            |                                                                                                                                      |
|                            | ::                                                                                                                                   |
|                            |                                                                                                                                      |
|                            |   DrawImage(                                                                                                                         |
|                            |     DRAWBOT_SurfaceRef      in_surface_ref,                                                                                          |
|                            |     DRAWBOT_ImageRef        in_image_ref,                                                                                            |
|                            |     const DRAWBOT_PointF32  *in_originP,                                                                                             |
|                            |     float                   in_alpha);                                                                                               |
+----------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
| ``SetInterpolationPolicy`` | ::                                                                                                                                   |
|                            |                                                                                                                                      |
|                            |   SetInterpolationPolicy(                                                                                                            |
|                            |     DRAWBOT_SurfaceRef           in_surface_ref,                                                                                     |
|                            |     DRAWBOT_InterpolationPolicy  in_interp);                                                                                         |
|                            |                                                                                                                                      |
|                            | ``DRAWBOT_InterpolationPolicy`` is one of the following:                                                                             |
|                            |                                                                                                                                      |
|                            |   - ``kDRAWBOT_InterpolationPolicy_None``,                                                                                           |
|                            |   - ``kDRAWBOT_InterpolationPolicy_Med``,                                                                                            |
|                            |   - ``kDRAWBOT_InterpolationPolicy_High``                                                                                            |
+----------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
| ``GetInterpolationPolicy`` | ::                                                                                                                                   |
|                            |                                                                                                                                      |
|                            |   GetInterpolationPolicy(                                                                                                            |
|                            |     DRAWBOT_SurfaceRef           in_surface_ref,                                                                                     |
|                            |     DRAWBOT_InterpolationPolicy  *out_interpP);                                                                                      |
+----------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
| ``SetAntiAliasPolicy``     | ::                                                                                                                                   |
|                            |                                                                                                                                      |
|                            |   SetAntiAliasPolicy(                                                                                                                |
|                            |     DRAWBOT_SurfaceRef       in_surface_ref,                                                                                         |
|                            |     DRAWBOT_AntiAliasPolicy  in_policy);                                                                                             |
|                            |                                                                                                                                      |
|                            | ``DRAWBOT_AntiAliasPolicy`` is one of the following:                                                                                 |
|                            |                                                                                                                                      |
|                            |   - ``kDRAWBOT_AntiAliasPolicy_None``,                                                                                               |
|                            |   - ``kDRAWBOT_AntiAliasPolicy_Med``,                                                                                                |
|                            |   - ``kDRAWBOT_AntiAliasPolicy_High``                                                                                                |
+----------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
| ``GetAntiAliasPolicy``     | ::                                                                                                                                   |
|                            |                                                                                                                                      |
|                            |   GetAntiAliasPolicy(                                                                                                                |
|                            |     DRAWBOT_SurfaceRef       in_surface_ref,                                                                                         |
|                            |     DRAWBOT_AntiAliasPolicy  *out_policyP);                                                                                          |
+----------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
| ``Flush``                  | Flush drawing. This is not always needed, and if overused, may cause excessive redrawing and flashing.                               |
|                            |                                                                                                                                      |
|                            | ::                                                                                                                                   |
|                            |                                                                                                                                      |
|                            |   Flush(                                                                                                                             |
|                            |     DRAWBOT_SurfaceRef  in_surface_ref);                                                                                             |
+----------------------------+--------------------------------------------------------------------------------------------------------------------------------------+

----

Drawbot_PathSuite
================================================================================

Calls to draw paths.

Drawbot_PathSuite1
********************************************************************************

+--------------+---------------------------------------------------------+
| **Function** |                       **Purpose**                       |
+==============+=========================================================+
| ``MoveTo``   | Move to a point.                                        |
|              |                                                         |
|              | ::                                                      |
|              |                                                         |
|              |   MoveTo(                                               |
|              |     DRAWBOT_PathRef  in_path_ref,                       |
|              |     float            in_x,                              |
|              |     float            in_y);                             |
+--------------+---------------------------------------------------------+
| ``LineTo``   | Add a line to the path.                                 |
|              |                                                         |
|              | ::                                                      |
|              |                                                         |
|              |   LineTo(                                               |
|              |     DRAWBOT_PathRef  in_path_ref,                       |
|              |     float            in_x,                              |
|              |     float            in_y);                             |
+--------------+---------------------------------------------------------+
| ``BezierTo`` | Add a cubic bezier to the path.                         |
|              |                                                         |
|              | ::                                                      |
|              |                                                         |
|              |   BezierTo(                                             |
|              |     DRAWBOT_PathRef         in_path_ref,                |
|              |     const DRAWBOT_PointF32  *in_pt1P,                   |
|              |     const DRAWBOT_PointF32  *in_pt2P,                   |
|              |     const DRAWBOT_PointF32  *in_pt3P);                  |
+--------------+---------------------------------------------------------+
| ``AddRect``  | Add a rect to the path.                                 |
|              |                                                         |
|              | ::                                                      |
|              |                                                         |
|              |   AddRect(                                              |
|              |     DRAWBOT_PathRef        in_path_ref,                 |
|              |     const DRAWBOT_RectF32  *in_rectPR);                 |
+--------------+---------------------------------------------------------+
| ``AddArc``   | Add a arc to the path. Zero start degrees == 3 o'clock. |
|              | Sweep is clockwise. Units for angle are in degrees.     |
|              |                                                         |
|              | ::                                                      |
|              |                                                         |
|              |   AddArc(                                               |
|              |     DRAWBOT_PathRef         in_path_ref,                |
|              |     const DRAWBOT_PointF32  *in_centerP,                |
|              |     float                   in_radius,                  |
|              |     float                   in_start_angle,             |
|              |     float                   in_sweep);                  |
+--------------+---------------------------------------------------------+
| ``Close``    | Close the path.                                         |
|              |                                                         |
|              | ::                                                      |
|              |                                                         |
|              |   Close(                                                |
|              |     DRAWBOT_PathRef  in_path_ref);                      |
+--------------+---------------------------------------------------------+

----

.. _effect-ui-events/custom-ui-and-drawbot.PF_EffectCustomUIOverlayThemeSuite:

PF_EffectCustomUIOverlayThemeSuite
================================================================================

This suite should be used for stroking and filling paths and vertices on the Composition and Layer Windows. After Effects is using this suite internally, and we have made it available to make custom UI look consistent across effects. The foreground/shadow colors are computed based on the app brightness level so that custom UI is always visible regardless of the application's Brightness setting in the Preferences.

PF_EffectCustomUIOverlayThemeSuite1
********************************************************************************

+------------------------------------+---------------------------------------------------------------------------------------------------------+
|            **Function**            |                                               **Purpose**                                               |
+====================================+=========================================================================================================+
| ``PF_GetPreferredForegroundColor`` | Get the preferred foreground color.                                                                     |
|                                    |                                                                                                         |
|                                    | ::                                                                                                      |
|                                    |                                                                                                         |
|                                    |   PF_GetPreferredForegroundColor(                                                                       |
|                                    |     DRAWBOT_ColorRGBA  *foreground_colorP);                                                             |
+------------------------------------+---------------------------------------------------------------------------------------------------------+
| ``PF_GetPreferredShadowColor``     | Get the preferred shadow color.                                                                         |
|                                    |                                                                                                         |
|                                    | ::                                                                                                      |
|                                    |                                                                                                         |
|                                    |   PF_GetPreferredShadowColor(                                                                           |
|                                    |     DRAWBOT_ColorRGBA  *shadow_colorP);                                                                 |
+------------------------------------+---------------------------------------------------------------------------------------------------------+
| ``PF_GetPreferredStrokeWidth``     | Get the preferred foreground & shadow stroke width.                                                     |
|                                    |                                                                                                         |
|                                    | ::                                                                                                      |
|                                    |                                                                                                         |
|                                    |   PF_GetPreferredStrokeWidth(                                                                           |
|                                    |     float  *stroke_widthPF);                                                                            |
+------------------------------------+---------------------------------------------------------------------------------------------------------+
| ``PF_GetPreferredVertexSize``      | Get the preferred vertex size.                                                                          |
|                                    |                                                                                                         |
|                                    | ::                                                                                                      |
|                                    |                                                                                                         |
|                                    |   PF_GetPreferredVertexSize(                                                                            |
|                                    |     float  *vertex_sizePF);                                                                             |
+------------------------------------+---------------------------------------------------------------------------------------------------------+
| ``PF_GetPreferredShadowOffset``    | Get the preferred shadow offset.                                                                        |
|                                    |                                                                                                         |
|                                    | ::                                                                                                      |
|                                    |                                                                                                         |
|                                    |   PF_GetPreferredShadowOffset(                                                                          |
|                                    |     A_LPoint  *shadow_offsetP);                                                                         |
+------------------------------------+---------------------------------------------------------------------------------------------------------+
| ``PF_StrokePath``                  | Stroke the path with the overlay theme foreground color.                                                |
|                                    | Optionally draw the shadow using the overlay theme shadow color.                                        |
|                                    | Uses overlay theme stroke width for stroking foreground and shadow strokes.                             |
|                                    |                                                                                                         |
|                                    | ::                                                                                                      |
|                                    |                                                                                                         |
|                                    |   PF_StrokePath(                                                                                        |
|                                    |     const DRAWBOT_DrawRef  drawbot_ref,                                                                 |
|                                    |     const DRAWBOT_PathRef  path_ref                                                                     |
|                                    |     PF_Boolean             draw_shadowB);                                                               |
+------------------------------------+---------------------------------------------------------------------------------------------------------+
| ``PF_FillPath``                    | Fills the path with overlay theme foreground color.                                                     |
|                                    | Optionally draw the shadow using the overlay theme shadow color.                                        |
|                                    |                                                                                                         |
|                                    | ::                                                                                                      |
|                                    |                                                                                                         |
|                                    |   PF_FillPath(                                                                                          |
|                                    |     const DRAWBOT_DrawRef  drawbot_ref,                                                                 |
|                                    |     const DRAWBOT_PathRef  path_ref                                                                     |
|                                    |     PF_Boolean             draw_shadowB);                                                               |
+------------------------------------+---------------------------------------------------------------------------------------------------------+
| ``PF_FillVertex``                  | Fills a square vertex around the center point using the overlay theme foreground color and vertex size. |
|                                    |                                                                                                         |
|                                    | ::                                                                                                      |
|                                    |                                                                                                         |
|                                    |   PF_FillVertex(                                                                                        |
|                                    |     const DRAWBOT_DrawRef  drawbot_ref,                                                                 |
|                                    |     const A_FloatPoint     *center_pointP                                                               |
|                                    |     PF_Boolean             draw_shadowB);                                                               |
+------------------------------------+---------------------------------------------------------------------------------------------------------+

