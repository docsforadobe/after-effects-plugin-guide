.. _effect-details/image-buffer-management-functions:

Image Buffer Management Functions
################################################################################

Use these functions to create and destroy `PF_EffectWorld <#_bookmark232>`__\ s, and to find out their bit- depth.

----

PF_WorldSuite2
================================================================================

+-----------------------+------------------------------------------------------------+
|     **Function**      |                    **Description**                         |
+=======================+============================================================+
| ``PF_NewWorld``       | Creates a new ``PF_EffectWorld``.                          |
|                       |                                                            |
|                       | ::                                                         |
|                       |                                                            |
|                       |   PF_Err PF_NewWorld(                                      |
|                       |     PF_ProgPtr      effect_ref,                            |
|                       |     A_long          widthL,                                |
|                       |     A_long          heightL,                               |
|                       |     PF_Boolean      clear_pixB,                            |
|                       |     PF_PixelFormat  pixel_format,                          |
|                       |     PF_EffectWorld  *worldP);                              |
+-----------------------+------------------------------------------------------------+
| ``PF_DisposeWorld``   | Disposes of a ``PF_EffectWorld``.                          |
|                       |                                                            |
|                       | ::                                                         |
|                       |                                                            |
|                       |   PF_Err PF_DisposeWorld(                                  |
|                       |     PF_ProgPtr      effect_ref,                            |
|                       |     PF_EffectWorld  *worldP);                              |
+-----------------------+------------------------------------------------------------+
| ``PF_GetPixelFormat`` | Get the pixel format for a given ``PF_EffectWorld``.       |
|                       |                                                            |
|                       | ::                                                         |
|                       |                                                            |
|                       |   PF_Err PF_GetPixelFormat(                                |
|                       |     const PF_EffectWorld  *worldP,                         |
|                       |     PF_PixelFormat        *pixel_formatP);                 |
|                       |                                                            |
|                       | ``pixel_formatP`` can be:                                  |
|                       |   - ``PF_PixelFormat_ARGB32`` - standard 8-bit RGB         |
|                       |   - ``PF_PixelFormat_ARGB64`` - 16-bit RGB                 |
|                       |   - ``PF_PixelFormat_ARGB128`` - 32-bit floating point RGB |
+-----------------------+------------------------------------------------------------+
