# Changing Parameter Orders, the Nice Way

It is possible to add or remove parameters from a plug-in, without forcing users to re-apply all instances of that plug-in to use the updated version. However, some advance planning on your part is necessary to allow for such changes. Your users (and technical support staff) will appreciate the effort.

You must first create a parameter array index. During *PF_Cmd_PARAM_SETUP*, assign index values to each parameter as you add them, using a simple enumeration. The order of enumeration corresponds to the order in which the parameters are registered during *PF_Cmd_PARAM_SETUP*, which in turn determines the order in which they appear in the Effect Control and Timeline panels.

Create another enumeration for disk IDs. The order of this enumeration must *not* be changed, though you may add to the end of this list. Note that the order of this list need not correspond with that of the parameter array index. Parameter disk IDs should range from 1 to 9999. Why not zero? Long story...

*In the early "wild west" days of After Effects plug-in programming, it was fairly common for developers not to bother with setting IDs. After Effects, realizing this, checked the ID of the first parameter added by that effect; if it was zero, it was assumed that the programmer hadn't bothered to ID params; After Effects then assigned each its own ID. This assumption works fine if you never set param IDs, but not so well if you start numbering your IDs from NULL. That's why.*

Before calling PF_ADD_PARAM(), specify the disk ID in the PF_ParamDef.uu.id field. If no value is specified, After Effects makes parameters sequential starting with 1. The parameter's information is tagged with this ID when saved. In this way, After Effects can still understand that, although your "Foobarocity" slider is now the fourth parameter passed, it's the same parameter as when it was second.

To delete a parameter without forcing re-application, remove the code which creates it and its entry in the parameter array index list. However, *do not* remove its entry in the disk ID list. To add a new parameter, add an entry in the appropriate location in the parameter array indices list, add the parameter creation code, and append the disk ID to the end of the disk ID enumeration. To re-order, change the parameter array index list and reorder the parameter creation code appropriately.

---

## Change defaults? Change IDs

If you don't, if someone saves a project with the old default and then reads it in with the new effect installed, that parameter will change to the new default value.

Presto! Instant support call.

This is another prime use case for `PF_ParamFlag_USE_VALUE_FOR_OLD_PROJECTS` from [Parameter Flags](../effect-basics/PF_ParamDef.md#parameter-flags).
