.. _aegps/overview:

Overview
################################################################################

AEGPs use Plug-In Component Architecture (PICA) function suites to access all functionality.

They may also publish their own function suites, for use by effect plug-ins (since plug-in load order varies, AEGPs can’t depend on suites not provided by After Effects).

AEGPs can also request a suite and, if it’s not present, provide replacement functionality themselves.

----

AEGP Communication With After Effects
================================================================================

For effect plug-ins, all communication with After Effects occurs through a single entry point function. This is not the case with AEGPs.

While After Effects *does* call the entry point function designated in the AEGP’s PiPL (which is still required), all subsequent communication between After Effects and AEGPs is handled by the hook functions the AEGP registers.

This registration must be performed from within the plug-in’s entry function, using the `AEGP_RegisterSuite <#_bookmark559>`__.

----

Different Tasks, Same API
================================================================================

AEGPs work in the same manner, regardless of specialization.

They can be simple, just `adding one menu item <#_bookmark544>`__ to trigger an external application, or complex like Artisans.

While any plug-in can access any function suite, only plug-ins of the appropriate type will have access to all the required parameters.

Only Artisans will have render contexts, and only AEIO plug-ins will receive input and output specifications; messaging is dependent upon which hook functions are registered.

