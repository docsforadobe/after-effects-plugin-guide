.. _aeios/implementation-details:

Implementation Details
################################################################################

Export Bit-Depth
================================================================================

In the Output Module Settings, the user can choose a Depth based on the options the AEIO declares support for in `AEIO_GetDepths() <#_bookmark808>`__.

If a plug-in supports higher bit-depth exports, it should be able to handle these higher bit-depth PF_EffectWorlds passed in `AEIO_AddFrame() <#_bookmark802>`__ or `AEIO_OutputFrame() <#_bookmark804>`__, even when the export setting is not set to the same depth.

The frame delivered to the AEIO, and the final output will not necessarily be the same depth.

You may get frames passed in the project bit-depth instead of the final output if After Effects thinks that will be higher quality.

----

User Data vs. Options
================================================================================

It’s possible to use either user data allocations or options handles to store metadata about a file.

We use user data for information that’s to be embedded in the file (presuming the file format supports such information); marker data, field labels, etc.

We use option handles for information about the file; output settings, dimensions, details of compression settings used.
