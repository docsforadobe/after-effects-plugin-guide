# Image Buffer Management Functions

Use these functions to create and destroy [PF_EffectWorld / PF_LayerDef](../effect-basics/PF_EffectWorld.md), and to find out their bit-depth.

---

## PF_WorldSuite2

| Function        | Description                                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `PF_NewWorld`       | Creates a new `PF_EffectWorld`.<br/><pre>PF_Err PF_NewWorld(<br/>  PF_ProgPtr      effect_ref,<br/>  A_long          widthL,<br/>  A_long          heightL,<br/>  PF_Boolean      clear_pixB,<br/>  PF_PixelFormat  pixel_format,<br/>  PF_EffectWorld  \*worldP);</pre>                                                                                                            |
| `PF_DisposeWorld`   | Disposes of a `PF_EffectWorld`.<br/><pre>PF_Err PF_DisposeWorld(<br/>  PF_ProgPtr      effect_ref,<br/>  PF_EffectWorld  \*worldP);</pre>                                                                                                                                                                                                                                           |
| `PF_GetPixelFormat` | Get the pixel format for a given `PF_EffectWorld`.<br/><pre>PF_Err PF_GetPixelFormat(<br/>  const PF_EffectWorld  \*worldP,<br/>  PF_PixelFormat        \*pixel_formatP);</pre><br/><br/>`pixel_formatP` can be:<br/><br/>> - `PF_PixelFormat_ARGB32` - standard 8-bit RGB<br/>> - `PF_PixelFormat_ARGB64` - 16-bit RGB<br/>> - `PF_PixelFormat_ARGB128` - 32-bit floating point RGB |
