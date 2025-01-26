<a id="effect-details-parameters-floating-point-values"></a>

# Parameters & Floating Point Values

We have something to admit to you; for years, even though we’ve given you 8 bit color values, we’ve internally used floating point representations behind your back.

That’s right, even with over-bright colors, we’d only ever tell you ‘255, 255, 255’. Yeah, right.

Well, we can’t live the lie any longer! Given a color parameter (passed to you by After Effects in your effect’s parameter array), this function returns a floating point representation, including any high dynamic range component.

---

<a id="effect-details-parameters-floating-point-values-pf-colorparamsuite"></a>

## PF_ColorParamSuite1

| **Function**                                  | **Purpose**                                                                                                                                                                          |
|-----------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `PF_GetFloatingPoint`<br/>`ColorFromColorDef` | ```default<br/>PF_Err PF_GetFloatingPointColorFromColorDef(<br/>  PF_ProgPtr         effect_ref,<br/>  const PF_ParamDef  *color_defP,<br/>  PF_PixelFloat      *fp_colorP);<br/>``` |

---

<a id="effect-details-parameters-floating-point-values-pf-pointparamsuite"></a>

## PF_PointParamSuite1

We also provide a way to get floating point values for point parameters.

| **Function**                                  | **Purpose**                                                                                                                                                                          |
|-----------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `PF_GetFloatingPoint`<br/>`ValueFromPointDef` | ```default<br/>PF_Err PF_GetFloatingPointValueFromPointDef(<br/>  PF_ProgPtr         effect_ref,<br/>  const PF_ParamDef  *point_defP,<br/>  A_FloatPoint       *fp_pointP);<br/>``` |

---

<a id="effect-details-parameters-floating-point-values-pf-angleparamsuite"></a>

## PF_AngleParamSuite1

New in CS6.0.2, we now provide a way to get floating point values for angle parameters.

| **Function**                                  | **Purpose**                                                                                                                                                                          |
|-----------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `PF_GetFloatingPoint`<br/>`ValueFromAngleDef` | ```default<br/>PF_Err PF_GetFloatingPointValueFromAngleDef(<br/>  PF_ProgPtr         effect_ref,<br/>  const PF_ParamDef  *angle_defP,<br/>  A_FloatLong        *fp_valueP);<br/>``` |
