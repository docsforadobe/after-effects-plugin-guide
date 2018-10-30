.. _aeios/calling-sequence:

Calling Sequence
################################################################################

As with all AEGPs, the entry point function exported in the plug-in's PiPL is called during launch. During this function, the AEIO must provide function pointers to required functions and describe their capabilities, then pass the appropriate structures to `AEGP_RegisterIO() <#_bookmark563>`__.

----

Import
================================================================================

When users select a file in the file import dialog which is of a type handled by your AEIO, its `AEIO_VerifyFileImportable() <#_bookmark811>`__ function will be called; it's called again for each such file the user imports. `AEIO_InitInSpecFromFile() <#_bookmark791>`__ will be called for each file; parse the file, and use the various set functions to describe it to After Effects. Also, construct any options data associated with the file, and save that data using `AEGP_SetInSpecOptionsHandle() <#_bookmark816>`__.

After Effects then calls the plug-in's `AEIO_GetInSpecInfo() <#_bookmark793>`__ function, to get descriptive text about the file for display in the project window. As noted in the description of this function, it may be called for folders as well; we recommend that, if there is no valid options data for the file, you do nothing and return no error (that's what our AEIOs do).

`AEIO_CountUserData() <#_bookmark809>`__ is then sent; if the AEIO indicates that there is user data present, `AEIO_GetUserData() <#_bookmark810>`__ will follow. After Effects will then request that the plug-in draw a frame of video (for the project window thumbnail) by sending `AEIO_DrawSparseFrame() <#_bookmark794>`__.

Once the supported file is added to a composition, user interaction will generate calls to `AEIO_DrawSparseFrame() <#_bookmark794>`__ and `AEIO_GetSound() <#_bookmark795>`__.

When the project is saved, and if there is options data associated with the AEIO_InSpec, After Effects will send `AEIO_FlattenOptions() <#_bookmark792>`__ during which the AEIO parses the options data, and creates a representation of it that contains no references to external memory. Likewise, the presence of any AEIO_OutSpec options data will result in `AEIO_GetFlatOutputOptions() <#_bookmark797>`__ being sent.

----

Export
================================================================================

If the user adds an item to the render queue and chooses the AEIO's supported output format, `AEIO_InitOutputSpec() <#_bookmark796>`__ will be sent. Use the various get functions to obtain information about the output settings, and store any pertinent information using `AEGP_SetOutSpecOptionsHandle() <#_bookmark819>`__ , followed by `AEIO_GetFlatOutputOptions() <#_bookmark797>`__. `AEIO_GetDepths() <#_bookmark808>`__ is sent so After Effects can determine what output pixel bit depths the AEIO supports. `AEIO_GetOutputInfo() <#_bookmark799>`__ is sent so that file name, type and subtype information can be displayed in the output module details.

When the user clicks on the Format Options button, in the render queue, `AEIO_UserOptionsDialog() <#_bookmark798>`__ is called.

When the user actually clicks on the "Render" button, `AEIO_SetOutputFile() <#_bookmark800>`__ will be called, followed by `AEIO_GetSizes() <#_bookmark806>`__ (your AEIO is responsible for determining whether the destination has sufficient disk space available).

Before the video frames are sent, `AEIO_StartAdding() <#_bookmark801>`__ is sent for the AEIO to open the file handle and write out the file header. If the AEIO supports a video or audio format, `AEIO_AddSoundChunk() <#_bookmark807>`__ is sent for each audio chunk, and an `AEIO_AddFrame() <#_bookmark802>`__ for each video frame.

If the AEIO supports sequences of still images, `AEIO_OutputFrame() <#_bookmark804>`__ is called repeatedly. After Effects sends a PF_EffectWorld representation of the frame to be output.

`AEIO_WriteLabels() <#_bookmark805>`__ is called (for each frame) to give the plug-in a chance to write out field and alpha interpretation information. `AEIO_EndAdding() <#_bookmark803>`__ is sent when there are no more frames (or audio) to be output. Close the output file.
