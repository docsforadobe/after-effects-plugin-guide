.. _effect-details/arbitrary-data-parameters:

Arbitrary Data Parameters
################################################################################

Some values are not adequately represented by After Effects existing parameter types. You can create and register any data for interpolation by After Effects, by creating parameters of arbitrary data type, or "arb data". You can rely on our interpolation engine and parameter management, without having to force your data into a pre-defined parameter type.

We've created a new messaging structure for custom data types, which are easily conceptualized as member (and friend) functions of a C++ class. You must respond to all selectors detailed here if you use arb data.

These functions deal with custom data structure management. Your arb data will be unloaded and reloaded at the user's whim; provide disk-safe flatten and unflatten functions.

----

Arbitrary Data Selectors
================================================================================

+----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|           **Selector**           |                                                                                                                                                          **Response**                                                                                                                                                           |
+==================================+=================================================================================================================================================================================================================================================================================================================================+
| ``PF_Arbitrary_NEW_FUNC``        | Allocate, populate, and return a handle to a new instance of your arb data.                                                                                                                                                                                                                                                     |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Arbitrary_DISPOSE_FUNC``    | Free and destroy an instance of your arbitrary data type.                                                                                                                                                                                                                                                                       |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Arbitrary_COPY_FUNC``       | Make a copy of an existing instance. You will be passed two handles, but only the source handle contains a valid instance. You must create a new instance, copy the values from the source, and put it in the destination handle. If you are passed a NULL handle, create a default instance of your arb data.                  |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Arbitrary_FLAT_SIZE_FUNC``  | You'll be passed a handle to an instance of your data type, and a variable in which you return the size of a flattened version of that instance.                                                                                                                                                                                |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Arbitrary_FLATTEN_FUNC``    | Flatten the instance you're passed, and place it in the supplied buffer. The buffer will be the size you reported in response to PF_Arbitrary_FLAT_SIZE_FUNC.                                                                                                                                                                   |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Arbitrary_UNFLATTEN_FUNC``  | Unpack the buffer into an instance of your arbitrary data type, and put in the handle which you've been passed.                                                                                                                                                                                                                 |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Arbitrary_INTERP_FUNC``     | Your interpolation function is passed three handles to instances of your arbitrary data type; one containing initial values (0), one final values (1), and a third to hold your interpolated data (somewhere between 0 and 1). You are also passed a float indicating where, between 0 and 1, your interpreted value should be. |
|                                  |                                                                                                                                                                                                                                                                                                                                 |
|                                  | Allocate an instance and fill it with interpolated data. Then put the interpolated instance into the handle you've been passed. The velocity curves have already been accounted for when the normalized time value was calculated.                                                                                              |
|                                  |                                                                                                                                                                                                                                                                                                                                 |
|                                  | NOTE: Never check out parameters if the :ref:`in_data>effect_ref <effect-basics/PF_InData.PF_InData-Members>` is NULL.                                                                                                                                                                                                          |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Arbitrary_COMPARE_FUNC``    | You are passed two instances of your arbitrary data, and a pointer to a comparison result. Populate the result with one of the values for PF_ArbCompareResult (see AE_Effect.h) to indicate whether the first was equal to, less than, more than, or simply not equal to the second.                                            |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Arbitrary_PRINT_SIZE_FUNC`` | Indicate the buffer size you require for printing your parameter's current values by setting print_sizePLu (member of print_size_func_params, part of the PF_ArbParamsExtra structure).                                                                                                                                         |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Arbitrary_PRINT_FUNC``      | Format your arbitrary data for text-based export, and copy the result to the buffer. This can be as elaborate as you would like.                                                                                                                                                                                                |
|                                  | Your plug-in should emulate the cut-and-paste behavior for pasting text representations of parameter settings (into a Microsoft Excel spreadsheet, for example) displayed by the plug-ins shipped with After Effects.                                                                                                           |
|                                  | You have a great deal of flexibility in how you format your output.                                                                                                                                                                                                                                                             |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_Arbitrary_SCAN_FUNC``       | Given a buffer of text data (often from the system clipboard), parse it into your arbitrary data format.                                                                                                                                                                                                                        |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

----

.. _effect-details/arbitrary-data-parameters.implementing-arbitrary-data:

Implementing Arbitrary Data
================================================================================

In addition to the normal command and event selector, arb data requires another set of host interaction. This is transparent for other parameter types, as After Effects manages their representing data. Writing an arb data plug-in will give you insight into the vast amount of parameter management After Effects performs, and the sequence in which those managing actions occur. It may even cause you to rethink your implementation, and use the parameter types After Effects manages *for* you.

Instantiate your arb data (using After Effects' memory allocation functions, of course) and point ParamDef.u.arb_d.dephault at it. Populate it with appropriate default values. No value variable is required to set up the parameter; zero it out for safety's sake.

In your plug-in's entry function, include a case for handling :ref:`PF_Cmd_ARBITRARY_CALLBACK <effect-basics/command-selectors.messaging>`.

Invoke a secondary event handler, ``HandleArbitrary``. It receives a ``PF_ArbParamsExtra`` in extra, which in turn contains a ``PF_FunctionSelector`` identifying the command sent.

Perhaps After Effects has sent ``PF_Cmd_ARBITRARY_CALLBACK`` and the ``PF_FunctionSelector`` is ``PF_Arbitrary_COPY_FUNC``. Pointers to a source and destination Arb are provided in ``PF_ArbParamsExtra.copy_func_params``. Allocate a new Arb, and point ``dest_arbPH`` at it. If ``src_arbH`` is NULL, create a default Arb for ``dest_arbPH``.

The user may select the arb's keyframe data in the Timeline panel, copy it, then switch to another application. You will be sent a ``PF_Arbitrary_PRINT_SIZE_FUNC``; set the size of your output buffer by setting ``print_sizePLu`` in the ``PF_ArbParamsExtra``. You'll then receive ``PF_Arbitrary_PRINT_FUNC``; populate the ``print_bufferPC`` output buffer with a textual representation of the Arb(s) in question.

Users may paste keyframe data into your Arb's timeline. You will receive ``PF_Arbitrary_SCAN_FUNC``. Create an Arb based on the contents of the character buffer handed to you (its size is indicated in ``print_sizeLu``).

----

Arbitrary Data? Re-Entrancy!
================================================================================

Your plug-in code *must* be recursively re-entrant to support custom data types, since it could be called by After Effects for numerous reasons. Your plug-in could check out a layer that, in turn, depends on another instance of your effect. Your plug-in's arbitrary data handling code will be triggered by your attempt to check out a (seemingly) unrelated layer. Watch out for calls to C run-time libraries that rely on static values accessed through global variables. If you're not prepared for this eventuality, you'll hang After Effects, and users will curse and punch their monitors.

----

When Not To Access Arbitrary Parameters
================================================================================

If ``in_data>effect_ref`` is ``NULL``, do not check out arbitrary parameters.

----

Changes During Dialogs
================================================================================

After Effects ignores any changes made to arbitrary data parameters during ``PF_Cmd_DO_DIALOG``.

This is by design; changes made during the display of the options dialog affect the entire effect stream, not just the arbitrary parameter at a given time.

If you must alter your arb's behavior based on these changes, save that information in sequence data and apply it later, often during ``PF_Cmd_USER_CHANGED_PARAM``.
