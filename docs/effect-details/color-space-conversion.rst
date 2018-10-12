.. _effect-details/color-space-conversion:

Color Space Conversion
################################################################################

Different pixel formats are useful for different operations. After Effects exposes its internal functions through PF_ColorCallbacksSuite. Here are the supported formats.

----

Pixel Types For Different Color Spaces
================================================================================

+---------------------------------------------------------------+-----------------------------------------+
|                        **Pixel Type**                         |         **Data Structure**              |
+===============================================================+=========================================+
| 8 bpc ARGB                                                    | ::                                      |
|                                                               |                                         |
|                                                               |   typedef struct {                      |
|                                                               |     A_u_char alpha, red, green, blue;   |
|                                                               |   } PF_Pixel8;                          |
+---------------------------------------------------------------+-----------------------------------------+
| 16 bpc ARGB                                                   | ::                                      |
|                                                               |                                         |
|                                                               |   typedef struct {                      |
|                                                               |     A_u_short alpha, red, green, blue;  |
|                                                               |   } PF_Pixel16;                         |
+---------------------------------------------------------------+-----------------------------------------+
| 32 bpc ARGB                                                   | ::                                      |
|                                                               |                                         |
|                                                               |   typedef struct {                      |
|                                                               |     PF_FpShort alpha, red, green, blue; |
|                                                               |   } PF_PixelFloat, PF_Pixel32;          |
+---------------------------------------------------------------+-----------------------------------------+
| HLS (Hue, Lightness, Saturation)                              | ::                                      |
|                                                               |                                         |
|                                                               |   typedef PF_Fixed PF_HLS_PIXEL[3]      |
+---------------------------------------------------------------+-----------------------------------------+
| YIQ (luminance, in-phase chrominance, quadrature chrominance) | ::                                      |
|                                                               |                                         |
|                                                               |   typedef PF_Fixed PF_YIQ_PIXEL[3]      |
+---------------------------------------------------------------+-----------------------------------------+

----

Plug-ins can draw on image processing algorithms written for nearly any color space by using the following callback functions.

Color Space Conversion Callbacks
================================================================================

+--------------+--------------------------------------------------------------------------------------------------------------------------+-------------------+
| **Function** |                                                       **Purpose**                                                        |   **Replaces**    |
+==============+==========================================================================================================================+===================+
| RGBtoHLS     | Given an RGB pixel, returns an HLS (hue, lightness, saturation) pixel. HLS values are scaled from 0 to 1 in fixed point. | ``PF_RGB_TO_HLS`` |
+--------------+--------------------------------------------------------------------------------------------------------------------------+-------------------+
| HLStoRGB     | Given an HLS pixel, returns an RGB pixel.                                                                                | ``PF_HLS_TO_RGB`` |
+--------------+--------------------------------------------------------------------------------------------------------------------------+-------------------+
| RGBtoYIQ     | Given an RGB pixel, returns a YIQ (luminance, inphase chrominance, quadrature chrominance) pixel.                        | ``PF_RGB_TO_YIQ`` |
|              | Y is 0 to 1 in fixed point, I is -0.5959 to 0.5959 in fixed point, and Q is -0.5227 to 0.5227 in fixed point.            |                   |
+--------------+--------------------------------------------------------------------------------------------------------------------------+-------------------+
| YIQtoRGB     | Given a YIQ pixel, returns an RGB pixel.                                                                                 | ``PF_YIQ_TO_RGB`` |
+--------------+--------------------------------------------------------------------------------------------------------------------------+-------------------+
| Luminance    | Given an RGB pixel, returns 100 times its luminance value (0 to 25500).                                                  | ``PF_LUMINANCE``  |
+--------------+--------------------------------------------------------------------------------------------------------------------------+-------------------+
| Hue          | Given an RGB pixel, eturns its hue angle mapped from 0 to 255, where 0 is 0 degrees and 255 is 360 degrees.              | ``PF_HUE``        |
+--------------+--------------------------------------------------------------------------------------------------------------------------+-------------------+
| Lightness    | Given an RGB pixel, returns its lightness value (0 to 255).                                                              | ``PF_LIGHTNESS``  |
+--------------+--------------------------------------------------------------------------------------------------------------------------+-------------------+
| Saturation   | Given an RGB pixel, returns its saturation value (0 to 255).                                                             | ``PF_SATURATION`` |
+--------------+--------------------------------------------------------------------------------------------------------------------------+-------------------+

