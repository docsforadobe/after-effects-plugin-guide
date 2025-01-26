# Other Integration Possibilities

Although this SDK describes the majority of integration possibilities with After Effects, there are other possibilities not to be overlooked.

---

## Scripting

Scripting is a relatively nimble and lightweight means to perform automated tasks with After Effects. ScriptUI is one way you can provide UI integration with custom dialogs and panels (see [HTML5 Panels](#intro-other-integration-possibilities-html5) too). And scripting may be used in tandem with plug-in development, in the cases where a certain function is made available via scripting and not via the C APIs described in this document.

Scripting in After Effects is done using ExtendScript, based on JavaScript. After Effects includes the ExtendScript ToolKit, a convenient interface for creating and testing your own scripts. Scripts may be compiled into .jsxbin binary files, to protect intellectual property.

You can access the After Effects Scripting Guide, and find a link to the scripting forums, on the Adobe I/O website at: [https://www.adobe.io/apis/creativecloud/aftereffects.html](https://www.adobe.io/apis/creativecloud/aftereffects.html)

After Effects can be driven by executing scripts from the commandline. In your script, you can open the project and run script actions on it. So for example, you can execute the following statement to run a script from the command line directly:

AfterFX -s "app.quit()"

Or you can execute this statement to run a .jsx script that includes a quit at the end:

AfterFX -r path_to_jsx_script

On Windows, AfterFX.com is the way to get feedback to the console, because AfterFX.com is a command line application.

---

## HTML5 Panels

In CC 2014 and later, After Effects supports HTML5 panels. They are accessed in After Effects from Window > Extensions > (your panel name). Panels can be resized and docked just like any other panel in After Effects. Panels are built using HTML5, After Effects Scripting, and JavaScript. You may download the After Effects Panel SDK from the the Adobe I/O website at: [https://www.adobe.io/apis/creativecloud/aftereffects.html](https://www.adobe.io/apis/creativecloud/aftereffects.html)

---

## AERender

Closely coupled with scripting is the command line interface offered by aerender. aerender is primarily suited to allow automated renders, but can be used to execute any sequence of scripting commands from the command line. An overview is available in the After Effects help documents here: [https://helpx.adobe.com/after-effects/using/automated-rendering-](https://helpx.adobe.com/after-effects/using/automated-rendering-network-rendering.html) [network-rendering.html](https://helpx.adobe.com/after-effects/using/automated-rendering-network-rendering.html)

---

## Premiere Pro Importers

Premiere Pro importers provide support for importing media into applications across most applications in the Adobe Creative Cloud, including Premiere Pro, Media Encoder, Prelude, and Audition. Because of this broader compatibility, unless you need very specific integration with After Effects only available via the AEIO API in this SDK, we recommend developing a Premiere Pro importer. The Premiere Pro SDK is available at: [https://www.adobe.io/apis/](https://www.adobe.io/apis/creativecloud/premierepro.html) [creativecloud/premierepro.html](https://www.adobe.io/apis/creativecloud/premierepro.html)

One advantage of MediaCore importer plug-ins over AEIOs is its priority system: The highest priority importer gets first crack at importing a file, and if the particular imported file isn't supported, the next-highest priority importer will then have the opportunity to try importing it, and so on.

---

## Mercury Transmit

Mercury Transmit plug-ins are used for sending video to output hardware for broadcast-quality monitoring. Transmitters are supported across most applications in the Adobe Creative Cloud, including Premiere Pro, After Effects, Prelude, and Character Animator. The Mercury Transmit API is documented in the Premiere Pro SDK, available at: [https://](https://www.adobe.io/apis/creativecloud/premierepro.html) [www.adobe.io/apis/creativecloud/premierepro.html](https://www.adobe.io/apis/creativecloud/premierepro.html)
