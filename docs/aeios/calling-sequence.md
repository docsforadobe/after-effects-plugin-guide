# Calling Sequence

As with all AEGPs, the entry point function exported in the plug-in's PiPL is called during launch. During this function, the AEIO must provide function pointers to required functions and describe their capabilities, then pass the appropriate structures to [AEGP_RegisterIO()](../aegps/aegp-suites.md#aegps-aegp-suites-aegp-registersuites).

---

## Import

When users select a file in the file import dialog which is of a type handled by your AEIO, its [AEIO_VerifyFileImportable()](new-kids-on-the-function-block.md#aeios-new-kids-on-the-function-block-aeio-functionblock) function will be called; it's called again for each such file the user imports. [AEIO_InitInSpecFromFile()](new-kids-on-the-function-block.md#aeios-new-kids-on-the-function-block-aeio-functionblock) will be called for each file; parse the file, and use the various set functions to describe it to After Effects. Also, construct any options data associated with the file, and save that data using [AEGP_SetInSpecOptionsHandle()](new-kids-on-the-function-block.md#aeios-new-kids-on-the-function-block-aegp-ioinsuite).

After Effects then calls the plug-in's [AEIO_GetInSpecInfo()](new-kids-on-the-function-block.md#aeios-new-kids-on-the-function-block-aeio-functionblock) function, to get descriptive text about the file for display in the project window. As noted in the description of this function, it may be called for folders as well; we recommend that, if there is no valid options data for the file, you do nothing and return no error (that's what our AEIOs do).

[AEIO_CountUserData()](new-kids-on-the-function-block.md#aeios-new-kids-on-the-function-block-aeio-functionblock) is then sent; if the AEIO indicates that there is user data present, [AEIO_GetUserData()](new-kids-on-the-function-block.md#aeios-new-kids-on-the-function-block-aeio-functionblock) will follow. After Effects will then request that the plug-in draw a frame of video (for the project window thumbnail) by sending [AEIO_DrawSparseFrame()](new-kids-on-the-function-block.md#aeios-new-kids-on-the-function-block-aeio-functionblock).

Once the supported file is added to a composition, user interaction will generate calls to `AEIO_DrawSparseFrame()` and [AEIO_GetSound()](new-kids-on-the-function-block.md#aeios-new-kids-on-the-function-block-aeio-functionblock).

When the project is saved, and if there is options data associated with the AEIO_InSpec, After Effects will send [AEIO_FlattenOptions()](new-kids-on-the-function-block.md#aeios-new-kids-on-the-function-block-aeio-functionblock) during which the AEIO parses the options data, and creates a representation of it that contains no references to external memory. Likewise, the presence of any AEIO_OutSpec options data will result in [AEIO_GetFlatOutputOptions()](new-kids-on-the-function-block.md#aeios-new-kids-on-the-function-block-aeio-functionblock) being sent.

---

## Export

If the user adds an item to the render queue and chooses the AEIO's supported output format, [AEIO_InitOutputSpec()](new-kids-on-the-function-block.md#aeios-new-kids-on-the-function-block-aeio-functionblock) will be sent. Use the various get functions to obtain information about the output settings, and store any pertinent information using [AEGP_SetOutSpecOptionsHandle()](new-kids-on-the-function-block.md#aeios-new-kids-on-the-function-block-aeio-functionblock), followed by `AEIO_GetFlatOutputOptions()`. [AEIO_GetDepths()](new-kids-on-the-function-block.md#aeios-new-kids-on-the-function-block-aeio-functionblock) is sent so After Effects can determine what output pixel bit depths the AEIO supports. [AEIO_GetOutputInfo()](new-kids-on-the-function-block.md#aeios-new-kids-on-the-function-block-aeio-functionblock) is sent so that file name, type and subtype information can be displayed in the output module details.

When the user clicks on the Format Options button, in the render queue, [AEIO_UserOptionsDialog()](new-kids-on-the-function-block.md#aeios-new-kids-on-the-function-block-aeio-functionblock) is called.

When the user actually clicks on the "Render" button, [AEIO_SetOutputFile()](new-kids-on-the-function-block.md#aeios-new-kids-on-the-function-block-aeio-functionblock) will be called, followed by [AEIO_GetSizes()](new-kids-on-the-function-block.md#aeios-new-kids-on-the-function-block-aeio-functionblock) (your AEIO is responsible for determining whether the destination has sufficient disk space available).

Before the video frames are sent, [AEIO_StartAdding()](new-kids-on-the-function-block.md#aeios-new-kids-on-the-function-block-aeio-functionblock) is sent for the AEIO to open the file handle and write out the file header. If the AEIO supports a video or audio format, [AEIO_AddSoundChunk()](new-kids-on-the-function-block.md#aeios-new-kids-on-the-function-block-aeio-functionblock) is sent for each audio chunk, and an [AEIO_AddFrame()](new-kids-on-the-function-block.md#aeios-new-kids-on-the-function-block-aeio-functionblock) for each video frame.

If the AEIO supports sequences of still images, [AEIO_OutputFrame()](new-kids-on-the-function-block.md#aeios-new-kids-on-the-function-block-aeio-functionblock) is called repeatedly. After Effects sends a PF_EffectWorld representation of the frame to be output.

[AEIO_WriteLabels()](new-kids-on-the-function-block.md#aeios-new-kids-on-the-function-block-aeio-functionblock) is called (for each frame) to give the plug-in a chance to write out field and alpha interpretation information. [AEIO_EndAdding()](new-kids-on-the-function-block.md#aeios-new-kids-on-the-function-block-aeio-functionblock) is sent when there are no more frames (or audio) to be output. Close the output file.
