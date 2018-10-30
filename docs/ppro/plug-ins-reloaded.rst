.. _ppro/plug-ins-reloaded:

Plug-Ins... Reloaded
================================================================================

On it’s first launch, Premiere Pro loads all the plug-ins, reads the PiPL, and sends PF_Cmd_GLOBAL_SETUP to determine the plug-ins’ capabilities. To save time on future application launches, it saves some of these capabilities in what we call the plug-in cache (the registry on Windows, a Property List file on macOS). The next time the application is launched, the cached information is used wherever possible, rather than loading the plug-ins.

When debugging, you can always force a reload of all the plug-ins by holding down the Shift key when launching Premiere Pro.

If your effect needs to be reloaded each time, there is a way to disable this caching. The plug- in can use the PF Cache On Load Suite in AE_CacheOnLoadSuite.h (from the `Premiere <http://www.adobe.com/devnet/premiere.html>`__ `Pro SDK <http://www.adobe.com/devnet/premiere.html>`__ headers) to call PF_SetNoCacheOnLoad() during PF_Cmd_GLOBAL_SETUP. For the second parameter of that function, pass a non-zero value if you want your effect to show up in the UI. Pass zero if loading failed, but you still want Premiere Pro to attempt to load it again on the next relaunch.

----

Effects Presets
================================================================================

Premiere Pro uses a different preset scheme than After Effects.

From the Premiere Pro SDK Guide:

Effect presets appear in the Presets bin in the Effects panel, and can be applied just like Effects with specific parameter settings and keyframes. Effect presets can be created as follows:

1) Apply a filter to a clip
2) Set the parameters of the filter, adding keyframes if desired
3) Right-click on the filter name in the Effect Controls panel, and select "Save Preset..."
4) Create preset bins if desired by right-clicking in the Effects panel and choosing "New Presets Bin"
5) Organize the presets in the preset folders
6) Select the bins and/or presets you wish to export, right-click, and choose "Export Preset"

Presets should be installed in the Plug-ins directory. Once they are installed in that directory, they will be read-only, and the user will not be able to move them to a different folder or change their names. User-created presets will be modifiable.

On Windows Vista, these are in the user’s hidden AppData folder (e.g. C:\Users\[user name]\AppData\Roaming\Adobe\Premiere Pro\[version]\Effect Presets and Custom Items.prfpset).

On macOS, they are in the user folder, at ~/Library/ Application Support/Adobe/Premiere Pro/[version]/Effect Presets and Custom Items.prfpset.

----

Custom ECW UI Over A Standard Data Type
================================================================================

While this is logged as bug #1235407, there is a simple workaround: Create two separate parameters, and have the custom UI control the slider param using parameter supervision.

