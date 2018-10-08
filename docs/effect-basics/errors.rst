.. _effect-basics/errors:

Errors
################################################################################

Always, always, *always* (always!) return a PF_Err from main(). Plug-ins must pass all errors back to After Effects.

It is vitally important that you pass any errors (returned to you by callbacks and PICA suites) to After Effects, unless you’ve handled them.

Be vigilant about returning the right error code, and disposing of any memory you’ve allocated.

Really. We’re serious.

----

Error Codes
================================================================================

+-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|             **Error**             |                                                                                                                                           **Meaning**                                                                                                                                           |
+===================================+=================================================================================================================================================================================================================================================================================================+
| PF_Err_NONE                       | Success.                                                                                                                                                                                                                                                                                        |
+-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| PF_Err_OUT_OF_MEMORY              | Memory allocation failed. Note that RAM preview will cause this condition, so After Effects will be expecting to receive this error from your plug-in.                                                                                                                                          |
+-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| PF_Err_INTERNAL_STRUCT_DAMAGED    | Problems using a data structure.                                                                                                                                                                                                                                                                |
+-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| PF_Err_INVALID_INDEX              | Problems finding/using array member.                                                                                                                                                                                                                                                            |
+-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| PF_Err_UNRECOGNIZED_PARAM_TYPE    | Problem with parameter data.                                                                                                                                                                                                                                                                    |
+-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| PF_Err_INVALID_CALLBACK           | Problems accessing function through pointer.                                                                                                                                                                                                                                                    |
+-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| PF_Err_BAD_CALLBACK_PARAM         | Problems using a parameter passed to a callback.                                                                                                                                                                                                                                                |
+-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| PF_Interrupt_CANCEL               | Both effect and AEGP callbacks can return this to effects, if a user action aborts a render. If the effect gets this error from a callback, it should stop processing the frame and return the error to the host. Failure to pass the error back may result in misrendered frames being cached. |
+-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| PF_Err_CANNOT_PARSE_KEYFRAME_TEXT | Return this from PF_Arbitrary_SCAN_FUNC when problems occur parsing the clipboard into keyframe data.                                                                                                                                                                                           |
+-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

----

Error Reporting Policy
================================================================================

After Effects has a consistent policy for error handling; follow it.

If you encounter an error in your plug-in’s code, report it to the user immediately, before returning from your plug-in to After Effects. After Effects considers errors from the operating system, encountered during your plug-in’s execution, to be yours. If you get an error code back from one of our callback functions, pass it back to After Effects; we’ve already reported it. Out-of-memory errors are never reported by After Effects. Error reporting is always suppressed during RAM preview, and when After Effects is running in - noui mode.

To report an error from within a plug-in, set PF_OutFlag_DISPLAY_ERROR_MESSAGE, and describe the error in `PF_OutData>return_msg <#_bookmark145>`__. Doing so will enter your error into the render log, and prevent system hangs in renders driven by a render engine or scripting.

----

Dig In!
================================================================================

Now you have a basic understanding of effect plug-ins, and are ready to start experimenting with some real code. Go ahead and get started!

After getting the basics of your plug-in setup, you may have some questions about reuseable code, advanced functionality, and how to optimize your code to make it faster. To this end, After Effects exposes a tremendous amount of its internal functionality via function suites. By relying on After Effects code for utility functions, you should be able to get your image processing algorithms implemented quickly. This will discussed in the next chapter.
