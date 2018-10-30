.. _aeios/calling-sequence:

Calling Sequence
################################################################################

As with all AEGPs, the entry point function exported in the plug-in's PiPL is called during launch. During this function, the AEIO must provide function pointers to required functions and describe their capabilities, then pass the appropriate structures to :ref:`AEGP_RegisterIO() <aegps/aegp-suites.AEGP_RegisterSuites>`.

----

Import
================================================================================

When users select a file in the file import dialog which is of a type handled by your AEIO, its :ref:`AEIO_VerifyFileImportable() <aeios/new-kids-on-the-function-block.AEIO_FunctionBlock>` function will be called; it's called again for each such file the user imports. :ref:`AEIO_InitInSpecFromFile() <aeios/new-kids-on-the-function-block.AEIO_FunctionBlock>` will be called for each file; parse the file, and use the various set functions to describe it to After Effects. Also, construct any options data associated with the file, and save that data using :ref:`AEGP_SetInSpecOptionsHandle() <aeios/new-kids-on-the-function-block.AEGP_IOInSuite>`.

After Effects then calls the plug-in's :ref:`AEIO_GetInSpecInfo() <aeios/new-kids-on-the-function-block.AEIO_FunctionBlock>` function, to get descriptive text about the file for display in the project window. As noted in the description of this function, it may be called for folders as well; we recommend that, if there is no valid options data for the file, you do nothing and return no error (that's what our AEIOs do).

:ref:`AEIO_CountUserData() <aeios/new-kids-on-the-function-block.AEIO_FunctionBlock>` is then sent; if the AEIO indicates that there is user data present, :ref:`AEIO_GetUserData() <aeios/new-kids-on-the-function-block.AEIO_FunctionBlock>` will follow. After Effects will then request that the plug-in draw a frame of video (for the project window thumbnail) by sending :ref:`AEIO_DrawSparseFrame() <aeios/new-kids-on-the-function-block.AEIO_FunctionBlock>`.

Once the supported file is added to a composition, user interaction will generate calls to ``AEIO_DrawSparseFrame()`` and :ref:`AEIO_GetSound() <aeios/new-kids-on-the-function-block.AEIO_FunctionBlock>`.

When the project is saved, and if there is options data associated with the AEIO_InSpec, After Effects will send :ref:`AEIO_FlattenOptions() <aeios/new-kids-on-the-function-block.AEIO_FunctionBlock>` during which the AEIO parses the options data, and creates a representation of it that contains no references to external memory. Likewise, the presence of any AEIO_OutSpec options data will result in :ref:`AEIO_GetFlatOutputOptions() <aeios/new-kids-on-the-function-block.AEIO_FunctionBlock>` being sent.

----

Export
================================================================================

If the user adds an item to the render queue and chooses the AEIO's supported output format, :ref:`AEIO_InitOutputSpec() <aeios/new-kids-on-the-function-block.AEIO_FunctionBlock>` will be sent. Use the various get functions to obtain information about the output settings, and store any pertinent information using :ref:`AEGP_SetOutSpecOptionsHandle() <aeios/new-kids-on-the-function-block.AEIO_FunctionBlock>`, followed by ``AEIO_GetFlatOutputOptions()``. :ref:`AEIO_GetDepths() <aeios/new-kids-on-the-function-block.AEIO_FunctionBlock>` is sent so After Effects can determine what output pixel bit depths the AEIO supports. :ref:`AEIO_GetOutputInfo() <aeios/new-kids-on-the-function-block.AEIO_FunctionBlock>` is sent so that file name, type and subtype information can be displayed in the output module details.

When the user clicks on the Format Options button, in the render queue, :ref:`AEIO_UserOptionsDialog() <aeios/new-kids-on-the-function-block.AEIO_FunctionBlock>` is called.

When the user actually clicks on the "Render" button, :ref:`AEIO_SetOutputFile() <aeios/new-kids-on-the-function-block.AEIO_FunctionBlock>` will be called, followed by :ref:`AEIO_GetSizes() <aeios/new-kids-on-the-function-block.AEIO_FunctionBlock>` (your AEIO is responsible for determining whether the destination has sufficient disk space available).

Before the video frames are sent, :ref:`AEIO_StartAdding() <aeios/new-kids-on-the-function-block.AEIO_FunctionBlock>` is sent for the AEIO to open the file handle and write out the file header. If the AEIO supports a video or audio format, :ref:`AEIO_AddSoundChunk() <aeios/new-kids-on-the-function-block.AEIO_FunctionBlock>` is sent for each audio chunk, and an :ref:`AEIO_AddFrame() <aeios/new-kids-on-the-function-block.AEIO_FunctionBlock>` for each video frame.

If the AEIO supports sequences of still images, :ref:`AEIO_OutputFrame() <aeios/new-kids-on-the-function-block.AEIO_FunctionBlock>` is called repeatedly. After Effects sends a PF_EffectWorld representation of the frame to be output.

:ref:`AEIO_WriteLabels() <aeios/new-kids-on-the-function-block.AEIO_FunctionBlock>` is called (for each frame) to give the plug-in a chance to write out field and alpha interpretation information. :ref:`AEIO_EndAdding() <aeios/new-kids-on-the-function-block.AEIO_FunctionBlock>` is sent when there are no more frames (or audio) to be output. Close the output file.
