# Debugging Plug-ins

The best way to learn the interaction(s) between After Effects and plug-ins is running the samples in your debugger. Spending some quality time in your compiler's debugger, and a sample project that closely resembles your plug-in, can really pay off.

Once you've got the plug-in building directly into the plug-ins folder as explained above, here's how to specify After Effects as the application to run during debug sessions:

### Windows

1. In the Visual Studio solution, in the Solution Explorer panel, choose the project you want to debug
2. Right-click it and choose Set as StartUp Project
3. Right-click it again and choose Properties
4. In Configuration Properties > Debugging > Command, provide the path to the executable file of the host application the plug-ins will be running in (this may be After Effects or Premiere Pro)
5. From there you can either hit the Play button, or you can launch the application and later at any point choose Debug > Attach to Process...

### macOS

1. In Xcode, in the Project Navigator, choose the xcodeproj you want to debug
2. Choose Product > Scheme > Edit Scheme...
3. Under Run, in the Info tab, for Executable, choose the host application the plug-ins will be running in (this may be After Effects or Premiere Pro)
4. From there you can either hit the Play button to build and run the current scheme, or you can launch the application and later at any point choose Debug > Attach to Process.

#### Signing requirments and loading unsigned plug-ins

macOS versions 15+ prevent the loading of unsigned plugins. During development, you can avoid this difficulty by adding ad-hoc signing as a custom build step.

`codesign --force --deep --sign - /path/to/plugin.dylib`

Note: Yes, that trailing '-' after '--sign' is important.

When you are ready to release, ensure that you do _not_ make changes to the plug-in package after signing, as this will invalidate said signing and prevent the plug-in from loading.

---

## Deleting Preferences

During the course of developing a plug-in, your plug-in may pass settings information to After Effects, which is then stored in its preferences file.

You may delete the preferences and restart After Effects with a clean slate by holding down Ctrl-Alt-Shift / Cmd-Opt-Shift during launch.

On Windows, the preferences are stored here: `[user folder]\AppData\Roaming\Adobe\After Effects\[version]\Adobe After Effects [version]-x64 Prefs.txt`

On macOS, they are stored here: `~/Library/Preferences/Adobe/After Effects/[version]/Adobe After Effects [version]-x64 Prefs`
