.. _intro/where-installers-should-put-plug-ins:

Where Installers Should Put Plug-ins
################################################################################

Installing your plug-ins in the common location will allow them to be loaded by Premiere Pro, if installed.

On Windows, the common plug-ins folder can be found (as an explicit path) in the following registry entry::

  HKLM\SOFTWARE\Adobe\After Effects\[version]\CommonPluginInstallPath

On Mac, the common plug-ins folder is at::

  /Library/Application Support/Adobe/Common/Plug-ins/[version]/MediaCore

Version is locked at 7.0 for all CC versions, or CSx for earlier versions. For example::

  /Library/Application Support/Adobe/Common/Plug-ins/7.0/MediaCore/

Do not use macOS aliases or Windows shortcuts, as these are not traversed by Premiere Pro.

----

Do I Have To Install The Plug-ins To The Common Folder?
================================================================================

You may have good reason to install your plug-in for only After Effects, for example, if your plug-in depends on suites and functionality not available in Premiere Pro. We strongly recommend that you use the common folder whenever possible, but for certain cases, the AE- specific plug-in folder is still available.

On Windows, the app-specific plug-ins folder can be found (as an explicit path) in the following registry entry::

  \\\HKEY_LOCAL_MACHINE\SOFTWARE\Adobe\After Effects\(version)\PluginInstallPath

On macOS, the app-specific plug-ins folder is at::

  /Applications/Adobe After Effects [version]/Plug-ins/

When launched, After Effects recursively descends 10 levels deep into subdirectories of its path. macOS aliases are traversed, but Windows shortcuts are not. Directories terminated by parentheses or preceded by the symbols ¬ (macOS) or ~ (Windows) are not scanned.

Try as you might to build a fence between AE and Premiere Pro, users will still find ways to get across using our lovely integration goodness - Your effects will still be available to Premiere Pro users who create a dynamically linked AE composition with your effect, and put it in a Premiere Pro sequence.

