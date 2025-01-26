.. _intro/sample-projects:

Sample Projects
################################################################################

There is at least one sample of every type of plug-in supported by the current API, as well as projects to illustrate particular concepts.

In the sample projects, we've kept the code as simple as possible. A showy implementation might get us good grades in a programming class, but won't help you understand how to use API features.

After the break, we explain how to build the sample projects, so keep reading below!

----

Sample Project Descriptions
================================================================================

+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|      **Project**       |                                                                                        **Description**                                                                                        |
+========================+===============================================================================================================================================================================================+
| AEGPs                  | AEGPs hook directly into After Effects' menus and other areas in the UI.                                                                                                                      |
|                        | See below for specifics on where the AEGP appears in the UI.                                                                                                                                  |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Artie                  | Artie the Artisan takes over rendering of all 3D layers in a given composition.                                                                                                               |
|                        | This is the same API used by our internal 3D renderers; it is very complex, and exposes a great deal of tacit information about the After Effects rendering pipeline.                         |
|                        | Unless you have a compelling reason to replace the way After Effects handles 3D rendering, you need never work with this sample.                                                              |
|                        | Artisans appear in Composition > Composition Settings, in the Advanced tab, in the Rendering Plug-in drop-down.                                                                               |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Easy Cheese            | A keyframer (which shows up on the Animation > Keyframe Assistant submenu), Easy Cheese shows how to manipulate various characteristics of keyframes                                          |
|                        | (in a way that, uncannily, resembles our shipping plug-in, Easy Ease...)                                                                                                                      |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| FBIO                   | Exercises the After Effects Input/Output (AEIO) API. Similar to the IO sample, but supports the frame-based .ffk file format.                                                                 |
|                        | Note that we now recommend developing a :ref:`intro/other-integration-possibilities.premiere-pro-importers` instead.                                                                          |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Grabba                 | Gets frames (formatted as the plug-in requests) from any composition in the project.                                                                                                          |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| IO                     | Exercises the After Effects Input/Output (AEIO) API. Supports the fictitious                                                                                                                  |
|                        |                                                                                                                                                                                               |
|                        | .fak file format, and handles all requests from After Effects for retrieving data from or outputting to such files.                                                                           |
|                        | Note that we now recommend developing a :ref:`intro/other-integration-possibilities.premiere-pro-importers` instead.                                                                          |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Mangler                | Mangler is a keyframer demonstrating the use of an ADM palette, just like our own.                                                                                                            |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Panelator              | Creates a panel that can be docked along with the rest of the standard panels.                                                                                                                |
|                        | Note: It is far more work to create a panel this way than using the HTML5 Panel SDK.                                                                                                          |
|                        | We recommend starting with that SDK instead.                                                                                                                                                  |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Persisto               | Shows how to read and write information from the After Effects preferences file.                                                                                                              |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ProjDumper             | Creates a text file representing every element in an After Effects project.                                                                                                                   |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Projector              | Imports the (fictitious) .sdk file format, and creates a project using AEGP API calls.                                                                                                        |
|                        | Whenever you're wondering how to get or set some characteristic of a project element, look here first.                                                                                        |
|                        | Note: There are some hardcoded paths in Projector.h. If you don't set these to refer to actual media on disk, you WILL get errors while running this plug-in. Don't blame us; change them!    |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| QueueBert              | Pronounced "Cue-BARE!", QueueBert manipulates all aspects of render queue items and the output modules associated with them.                                                                  |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Streamie               | Manipulates streams, both dynamic and fixed.                                                                                                                                                  |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Sweetie                | Sweetie uses the PICA (or "Suite Pea") API to provide a function Suite, for use by other plug-ins.                                                                                            |
|                        | If you're writing multiple plug-ins that rely on the same image processing library, you could provide the library functionality using such a suite.                                           |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Text Twiddler          | Manipulates text layers and their contents.                                                                                                                                                   |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Effects                | All effects appear in the Effects & Presets panel, and in the Effect menu.                                                                                                                    |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Checkout               | Checks out (of After Effects' frame cache) a frame of input from another layer, at a specified time.                                                                                          |
|                        | This is an important concept for all effects with layer parameters. Premiere Pro compatible.                                                                                                  |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Convolutrix            | Exercises our image convolution callbacks. Premiere Pro compatible.                                                                                                                           |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Gamma Table            | Shows how to manage sequence data, and uses our iteration callbacks.                                                                                                                          |
|                        | For nostalgia's sake, we're leaving this one sample in C; it's also compatible with many third-party plug-in hosts, due to its reliance on version 3.x API features.                          |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| GLator                 | New for CC 2017. Demonstrates proper OpenGL context management in an effect plug-in.                                                                                                          |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Paramarama             | Exercises wayward param types not used in other sample. Premiere Pro compatible.                                                                                                              |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| PathMaster             | Shows how to access paths from within an effect.                                                                                                                                              |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Portable               | Shows how to detect and respond to several different plug-in hosts. Premiere Pro compatible.                                                                                                  |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Resizer                | Resizer resizes (surprise!) the output buffer. This is useful for effects like glows and drop shadows, which would be truncated at the layer's edges if they didn't expand the output buffer. |
|                        | Premiere Pro compatible.                                                                                                                                                                      |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| SDK Backwards          | Reverses a layer's audio, and mixes it with a keyframe-able sine wave.                                                                                                                        |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| SDK Noise              | Premiere Pro compatible, demonstrates 32-bit and YUV rendering in Premiere Pro.                                                                                                               |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Shifter                | Shifts an image in the output buffer, and exercises our transform_world and subpixel sampling functions.                                                                                      |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| SmartyPants            | Demonstrates the SmartFX API, required for support of floating point pixels.                                                                                                                  |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Transformer            | Exercises our image transformation callbacks.                                                                                                                                                 |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Effect Template        |                                                                                                                                                                                               |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Skeleton               | Skeleton is the starting point for developing effects. Premiere Pro compatible.                                                                                                               |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Effects with Custom UI |                                                                                                                                                                                               |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| CCU                    | Implements a custom user interface in the composition and layer windows, supporting pixel aspect ratio and downsample ratios. Premiere Pro compatible.                                        |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ColorGrid              | Shows how to use arbitrary data type parameters. Also has a nice custom UI. Premiere Pro compatible.                                                                                          |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Custom ECW UI          | Implements a very boring custom user interface in the effect controls window, and shows how to respond to numerous UI events.                                                                 |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Histogrid              | New for CC 2015 (13.5). An example of how custom UI can access asynchronously-rendered upstream frames for lightweight processing in CC 2015 and later.                                       |
|                        | This effect calculates a sampled 10x10 color grid from the upstream frame, and displays a preview of that color grid.                                                                         |
|                        | In render, a higher-quality grid is calculated and used to modify the output image, creating a blend of a color grid with the original image.                                                 |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Supervisor             | Shows how to control parameters (both values and UI) based on the value of other parameters. Premiere Pro compatible.                                                                         |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| BlitHook               |                                                                                                                                                                                               |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| EMP                    | External Monitor Preview. Use this as a starting point for adding support to output video from the composition panel to video hardware.                                                       |
+------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

----

Building The Sample Projects
================================================================================

We've combined the sample projects into a single master project, stored in the Examples folder of the SDK. For macOS, it is Buildall.xcodeproj; for Windows, it is BuildAll.sln.

In your IDE, you'll need to change the output folder of your project to build into After Effects' plug-in folder.

For development, we recommend using the following path for macOS: ``/Library/Application Support/Adobe/Common/Plug-ins/[version]/MediaCore/``

Version is locked at 7.0 for all CC versions, or CSx for earlier versions.

for example: ``/Library/Application Support/Adobe/Common/Plug-ins/7.0/MediaCore/``

or: ``/Library/Application Support/Adobe/Common/Plug-ins/CS6/MediaCore/``

and the following path for Windows: ``[Program Files]\Adobe\Common\Plug-ins\[version]\MediaCore\``

for example: ``C:\Program Files\Adobe\Common\Plug-ins\7.0\MediaCore\``

or: ``C:\Program Files\Adobe\Common\Plug-ins\CS6\MediaCore\``

Note that this Windows path is only recommended for development purposes. Windows installers should follow the guidelines here: :ref:`intro/where-installers-should-put-plug-ins`.

In Xcode, you can set this path once for all projects in the Xcode Preferences > Locations > Derived Data > Advanced. Under *Build Location* choose *Custom*, and fill in the path.

In Visual Studio, for convenience, we have specified the output path for all sample projects using the environment variable AE_PLUGIN_BUILD_DIR. You'll need to set this as a user environment variable for your system. On Windows 7, right-click *My Computer* > *Properties*

>and in the left sidebar choose *Advanced System Settings*. In the new dialog, hit the *Environment Variables* button. In the User variables area, create a New variable named AE_PLUGIN_BUILD_DIR, and with the path described above. Log out of Windows and log back in so that the variable will be set.

Alternatively, you can set output path for each project individually in Visual Studio by right-clicking a project in the Solution Explorer, choosing Properties, and then in Configuration Properties > Linker > General, set the Output File.

When compiling the plug-ins, if you see a link error such as:

"Cannot open file "[MediaCore plug-ins path]\plugin.prm", make sure to launch Visual Studio in administrator mode. In your Visual Studio installation, right-click devenv.exe, Properties > Compatibility > Privilege Level, click "Run this program as an administrator".
