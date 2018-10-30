.. _effect-details/parameters-floating-point-values:

Parameters & Floating Point Values
################################################################################

We have something to admit to you; for years, even though we've given you 8 bit color values, we've internally used floating point representations behind your back. That's right, even with over-bright colors, we'd only ever tell you ‘255, 255, 255'. Yeah, right. Well, we can't live the lie any longer! Given a color parameter (passed to you by After Effects in your

effect's parameter array), this function returns a floating point representation, including any high dynamic range component.

----

.. _effect-details/parameters-floating-point-values.PF_ColorParamSuite:

PF_ColorParamSuite1
================================================================================

+-------------------------+------------------------------------------------+
|      **Function**       |                 **Purpose**                    |
+=========================+================================================+
| ``PF_GetFloatingPoint`` | ::                                             |
| ``ColorFromColorDef``   |                                                |
|                         |   PF_Err PF_GetFloatingPointColorFromColorDef( |
|                         |     PF_ProgPtr         effect_ref,             |
|                         |     const PF_ParamDef  *color_defP,            |
|                         |     PF_PixelFloat      *fp_colorP);            |
+-------------------------+------------------------------------------------+

----

.. _effect-details/parameters-floating-point-values.PF_PointParamSuite:

PF_PointParamSuite1
================================================================================

We also provide a way to get floating point values for point parameters.

+-------------------------+------------------------------------------------+
|      **Function**       |                 **Purpose**                    |
+=========================+================================================+
| ``PF_GetFloatingPoint`` | ::                                             |
| ``ValueFromPointDef``   |                                                |
|                         |   PF_Err PF_GetFloatingPointValueFromPointDef( |
|                         |     PF_ProgPtr         effect_ref,             |
|                         |     const PF_ParamDef  *point_defP,            |
|                         |     A_FloatPoint       *fp_pointP);            |
+-------------------------+------------------------------------------------+

----

New in CS6.0.2, we now provide a way to get floating point values for angle parameters.

PF_AngleParamSuite1
================================================================================

+-------------------------+------------------------------------------------+
|      **Function**       |                 **Purpose**                    |
+=========================+================================================+
| ``PF_GetFloatingPoint`` | ::                                             |
| ``ValueFromAngleDef``   |                                                |
|                         |   PF_Err PF_GetFloatingPointValueFromAngleDef( |
|                         |     PF_ProgPtr         effect_ref,             |
|                         |     const PF_ParamDef  *angle_defP,            |
|                         |     A_FloatLong        *fp_valueP);            |
+-------------------------+------------------------------------------------+
