.. _effect-basics/entry-point:

Entry Point
################################################################################


All communication between After Effects and an effect plug-in is initiated by After Effects, and it all happens by the host (After Effects) calling a single entry point function.

For all effect plug-ins, the entry point function must have the following signature::

  PF_Err main (
    PF_Cmd       cmd,
    PF_InData    *in_data,
    PF_OutData   *out_data,
    PF_ParamDef  *params[],
    PF_LayerDef  *output,
    void         *extra)

The name of the entry point function above is "main", but it can be whatever is specified in :ref:`intro/pipl-resources`.

Before each call to the entry point function, After Effects updates :ref:`effect-basics/PF_InData` and the plug- in's parameter array PF_ParamDef[] (except as noted).

After the plug-in returns from its call, After Effects checks :ref:`effect-basics/PF_OutData` for changes and, when appropriate, uses the PF_LayerDef the effect has rendered.

----

Entry Point Function Parameters
================================================================================

+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|         **Argument**         |                                                                                                                                                                                              **Purpose**                                                                                                                                                                                              |
+==============================+=======================================================================================================================================================================================================================================================================================================================================================================================================+
| `cmd <#_bookmark78>`__       | After Effects sets the `command selector <#command-selectors>`__ to tell the plug-in what to do.                                                                                                                                                                                                                                                                                                      |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| `in_data <#_bookmark115>`__  | Information about the application's state and the data the plug-in is being told to act upon. Pointers to numerous interface and image manipulation functions are also provided.                                                                                                                                                                                                                      |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| `out_data <#_bookmark132>`__ | Pass back information to After Effects by setting fields within out_data.                                                                                                                                                                                                                                                                                                                             |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| `params <#parameters>`__     | An array of the plug-in's parameters at the time provided in in_data> current_time. params[0] is the input image (a `PF_EffectWorld <#_bookmark231>`__) to which the effect should be applied. These values are only valid during certain selectors (this is noted in the `selector <#_bookmark78>`__\ `descriptions <#_bookmark78>`__). Parameters are discussed at length `here <#_bookmark211>`__. |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| `output <#_bookmark231>`__   | The output image, to be rendered by the effect plug-in and passed back to After Effects. Only valid during certain selectors.                                                                                                                                                                                                                                                                         |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| `extra <#_bookmark437>`__    | The extra parameter varies with the command sent or (in the case of `PF_Cmd_EVENT <#_bookmark105>`__) the `event type <#_bookmark423>`__. Used primarily for `event management <#_bookmark421>`__ and `parameter supervision <#parameter-supervision>`__.                                                                                                                                             |
+------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
