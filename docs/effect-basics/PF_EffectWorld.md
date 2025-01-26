# PF_EffectWorld / PF_LayerDef

After Effects represents images using PF_EffectWorlds, also called PF_LayerDefs.

---

## PF_EffectWorld Structure

| **Item**           | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
|--------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `world_flags`      | Currently, the only flags are:<br/><br/>> - `PF_WorldFlag_DEEP` - set if the world is 16-bpc<br/>> - `PF_WorldFlag_WRITEABLE` - indicates that you are allowed to alter the image data of the world.<br/><br/>Normally effects cannot alter input image data; only output.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `data`             | Pointer to image data, stored as a `PF_PixelPtr`.<br/>Do not access directly; use the [PF_PixelPtr Accessor Macros](#pf_pixelptr-accessor-macros).<br/><br/>Image data in After Effects is always organized in sequential words each containing Alpha, Red, Green, Blue from the low byte to the high byte.                                                                                                                                                                                                                                                                                                                                                                                    |
| `rowbytes`         | The length, in bytes, of each row in the image's block of pixels.<br/>The block of pixels contains height lines each with width pixels followed by some bytes of padding.<br/>The width pixels (times four, because each pixel is four bytes long) plus optional extra padding adds up to rowbytes bytes.<br/>Use this value to traverse the image data.<br/><br/>Platform-specific padding at the end of rows makes it unwise to traverse the entire buffer.<br/>Instead, find the beginning of each row using height and rowbytes.<br/><br/>NOTE: This value does not vary based on whether field rendering is active.<br/><br/>NOTE: Input and output worlds with the same dimensions can use different rowbytes values. |
| `width`            | Width and height of the pixel buffer.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `height`           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| `extent_hint`      | The smallest rectangle encompassing all opaque (non-zero alpha) pixels in the layer.<br/>This defines the area which needs to be output.<br/><br/>If your plug-in varies with extent (like a diffusion dither), ignore this and render the full frame each time.                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `pix_aspect_ratio` | The pixel aspect ratio expressed as a `PF_Rational`.<br/><br/>NOTE: Effects can use this value for checked out layers, but must use `PF_InData.pixel_aspect_ratio` for the layer to which they're applied. Sorry.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| `platform_ref`     | No longer used in CS5.<br/><br/>Platform-specific reference information.<br/>On Windows, this contains an opaque value.<br/>On macOS, `PF_GET_PLATFORM_REFS` provides a `CGrafPtr` and a `GDeviceHandle` from a `PF_EffectWorld`.<br/><br/>NOTE: You cannot acquire a `platform_ref` during *PF_Cmd_GLOBAL_SETUP*, as there isn't any output context yet. Patience, my pet.                                                                                                                                                                                                                                                                                                                                                 |
| `dephault`         | For layer parameters only.<br/><br/>Either `PF_LayerDefault_MYSELF` or `PF_LayerDefault_NONE`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |

---

## New In 16.0

During PF_Cmd_SMART_RENDER_GPU, PF_LayerDef will be filled out the same as it is for regular CPU renders, but PF_LayerDef.data will be null; all other fields will be valid.

---

## Rowbytes In PF_EffectWorlds

Don't assume that you can get to the next scanline of a `PF_EffectWorld` using `(width * sizeof(current_pixel_type)) + 4`, or whatever; use the PF_EffectWorld's `rowbytes` instead.

Never write outside the indicated region of a PF_EffectWorld; this can corrupt cached image buffers that don't belong to you.

To test whether your effects are honoring the `PF_EffectWorld>rowbytes`, apply the Grow Bounds effect *after* your effect.

The output buffer will have larger rowbytes than the input (though it will still have the same logical size).

---

## Byte Alignment

The pixels in a `PF_EffectWorld` are not guaranteed to be 16-byte-aligned. An effect may get a subregion of a larger PF_EffectWorld. Users of Apple's sample code for pixel processing optimization, you have been warned.

Beyond 8-bit per channel color, After Effects supports 16 bit and 32-bit float per-channel color.

Effects will never receive input and output worlds with differing bit depths, nor will they receive worlds with higher bit depth than they have claimed to be able to handle.

---

## Accessor Macros For Opaque (Data Type) Pixels

Use the following macros to access the data within (opaque) PF_PixelPtrs.

It is, emphatically, *not* safe to simply cast pointers of one type into another! To make it work at all requires a cast, and there's nothing that prevents you from casting it incorrectly. We may change its implementation at a later date (at which time you'll thank us for forcing this level of abstraction).

---

## PF_PixelPtr Accessor Macros

| **Macro**             | **Purpose**                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|-----------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `PF_GET_PIXEL_DATA16` | Obtain a pointer to a 16-bpc pixel within the specified world.<br/><br/>The returned pixel pointer will be NULL if the world is not 16-bpc.<br/><br/>The second parameter is optional; if it is not NULL, the returned pixel will be<br/>an interpretation of the values in the passed-in pixel, as if it were in the specified PF_EffectWorld.<br/><br/>```default<br/>PF_GET_PIXEL_DATA16 (<br/>  PF_EffectWorld wP,<br/>  PF_PixelPtr    pP0,<br/>  PF_Pixel16     *outPP);<br/>``` |
| `PF_GET_PIXEL_DATA8`  | Obtain a pointer to a 8-bpc pixel within the specified world.<br/><br/>The returned pixel pointer will be NULL if the world is not 8- bpc.<br/><br/>The second parameter is optional; if it is not NULL, the returned pixel will be<br/>an interpretation of the values in the passed-in pixel, as if it were in the specified PF_EffectWorld.<br/><br/>```default<br/>PF_GET_PIXEL_DATA8 (<br/>  PF_EffectWorld wP,<br/>  PF_PixelPtr    pP0,<br/>  PF_Pixel8      *outPP);<br/>```   |

Think of `PF_GET_PIXEL_DATA16` and `PF_GET_PIXEL_DATA8` as safe (ahem) casting routines.

The code required is actually very simple to get a `PF_Pixel16*` out of the PF_EffectWorld output:

```default
{
  PF_Pixel16 *deep_pixelP = NULL;
  PF_Err     err = PF_Err_NONE;
  err = PF_GET_PIXEL_DATA16(output, NULL, &deep_pixelP);
}
```

This returns deep_pixelP as NULL if the world does not have deep pixels.

The second parameter is not used very often and should be passed as NULL; pass a PF_PixelPtr that is *not* contained in a PF_EffectWorld to coerce it to the depth of that PF_EffectWorld).
