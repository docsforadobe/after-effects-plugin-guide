.. _aeios/aeios:

AEIOs
################################################################################

AEIOs are AEGPs that perform media file import and/or export. AEIOs do everything for a file of a given type that After Effects (or the plug-ins which ship with After Effects) would normally do. On the import side, AEIOs can open existing files, manage file-specific interpretation options, and provide audio and frames from the file to After Effects in AEGP_SoundWorld and PF_EffectWorld format. Additionally, AEIOs can create files interactively, asking users for the settings they'd like instead of reading them from a file. On the export side, AEIOs can create and manage output options for render queue items, create output files and save frames (provided by After Effects as PF_EffectWorlds) into those files.

AEIOs work with uncompressed video with pixels in ARGB order from low to high-byte. Pixels can be 8-bit, 16-bit, or 32-bit float per channel. AEIOs must handle their own compression/decompression of any codecs supported.

----

AEIO, or AEGP?
================================================================================

AEIOs provide pixels and audio data to After Effects.

If you're writing an importer/exporter for a file format that represents timeline or project format (referencing file formats supported by After Effects or other installed AEIOs), write an AEGP and add its command to the Import/Export submenu.

----

AEIO For Import, Or MediaCore Importer?
================================================================================

After Effects supports MediaCore importer plug-ins. MediaCore is a set of shared libraries that grew out of Premiere Pro; thus the MediaCore APIs are described in the `Premiere Pro <http://www.adobe.com/devnet/premiere/>`__ `SDK <http://www.adobe.com/devnet/premiere/>`__.

Only MediaCore importer plug-ins support an importer priority system: The highest priority importer gets the first opportunity to import a file, and if the particular imported file isn't supported, the next-highest priority importer will then have the opportunity to try importing it, and so on. MediaCore importers cannot defer file import to an AEIO. So if your goal is to take over file handling for any file type for which After Effects already provides a plug-in, you need to develop a MediaCore importer plug-in.

On the other hand, only AEIOs can display a setup dialog in the Interpret Footage < Main > More Options dialog.

If the above constraints haven't already answered whether you need to build an AEIO or MediaCore importer, then you'll likely want to build a MediaCore importer, which can be used across the video and audio applications including Premiere Pro, Media Encoder, Prelude, SpeedGrade, and Audition.

----

How It Works
================================================================================

From within its entry point function, an AEIO populates a structure of function pointers with the names of the functions it wants called in response to certain events. Many of these function hooks are optional.

----

What Would After Effects Do?
================================================================================

For many AEIO hook functions, you can ask After Effects to perform default processing (this capability is noted in each hook's descriptions).

Unless you have compelling reasons to do otherwise, return ``AEIO_Err_USE_DFLT_CALLBACK`` from the function, and let After Effects do the work.

This is also a good way to learn the calling sequence before beginning implementation.

----

Registering Your AEIO
================================================================================

During your plug-in's entry point function, populate a AEIO_ModuleInfo describing the filetype(s) the AEIO supports, and an AEIO_FunctionBlock structure that points to your file handling functions. For some of these functions, you can rely on After Effects' default behavior by returning AEIO_Err_USE_DFLT_CALLBACK. However, you must still provide a function matching the required signature, that does so. Once you've filled out both these structures, call `AEGP_RegisterIO() <#_bookmark563>`__.

In the AEIO_ModuleInfo that you pass in to the register call, you provide the file type and description information that After Effects uses in the Import dialog, for the "Files of type" drop-down on Windows, or the Enable drop-down on MacOS. As of CS6, file extensions cannot be more than three characters long, even though we have a few built-in importers with longer extensions.

----

InSpec, OutSpec
================================================================================

On most import-related functions, an ``AEIO_InSpecH`` is passed. On most output-related functions, an ``AEIO_OutSpecH`` is passed.

What are these mysterious handles? These opaque data handles can be used with the `AEGP_IOInSuite <#_bookmark812>`__ and `AEGP_IOOutSuite <#_bookmark817>`__, to set or query for information about the import or output.

For example, on an import, you'll use ``AEIO_InSpecH`` when calling ``AEGP_SetInSpecDimensions`` in AEGP_IOInSuite.

And during an export, you'll use ``AEIO_OutSpecH`` when calling ``AEGP_GetOutSpecDimensions`` in ``AEGP_IOOutSuite``. So use these handles to exchange information with After Effects about the details of the input or output.
