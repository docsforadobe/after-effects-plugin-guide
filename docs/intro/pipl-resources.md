# PiPL Resources

Originating in Adobe Photoshop over two decades ago, Plug-In Property Lists, or PiPLs, are resources which provide basic information about a plug-in's behavior, without executing the plug-in. PiPLs have been largely supplanted within After Effects by `PF_Cmd_GLOBAL_SETUP` (see [Global Selectors](../effect-basics/command-selectors.md)) and dynamic outflags. However, for archaeological reasons, the behaviors indicated during `PF_Cmd_GLOBAL_SETUP` must agree with those in the PiPL.

In the interest of cross-platform compatibility, use a single .r file for both macOS and Windows versions of your plug-in, like the samples do. PiPL properties must always be in macOS-specific byte order. On Windows, PiPLs are compiled by processing a .r file through pipltool.exe, which converts the .r file into a binary .rc file. The Windows sample projects all contain custom build steps which generate a .rc file, using a cross-platform .r file and our cnvtpipl.exe command line utility. Base your development on an existing sample plug-in and the build step will be correctly implemented.

+-------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|            PiPL Property            |                                                                                                                                                     Purpose                                                                                                                                                      |
+=====================================+==================================================================================================================================================================================================================================================================================================================+
| Kind                                | Plug-in type. AEEffect for an Effect.                                                                                                                                                                                                                                                                            |
+-------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Name                                | Display name which can be up to 47 characters long.                                                                                                                                                                                                                                                              |
+-------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Category                            | Effect category for menus and the Effects and Presets panel                                                                                                                                                                                                                                                      |
+-------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Entry Point (one for each platform) | Your plug-in's entry point is exported through the PiPL on Windows and macOS. If the plug-in supports multiple platforms, then multiple entry points must be defined in the PiPL. There is no need for a Windows .def file or manual exports, unless you're also designating some other OS-specific entry point. |
|                                     |                                                                                                                                                                                                                                                                                                                  |
|                                     | The macros defined in entry.h (in the \\SDKExamplesHeaders directory) take care of exporting each sample's entry point function. All the sample project entry point functions are `EffectMain()` for effects, or `EntryPointFunc()` for AEGPs.                                                                   |
+-------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| AE_PiPL_Version                     | Unused                                                                                                                                                                                                                                                                                                           |
+-------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| AE_Effect_Spec_Version              | SDK version the effect was built with                                                                                                                                                                                                                                                                            |
+-------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| AE_Effect_Version                   | The version of the effect                                                                                                                                                                                                                                                                                        |
+-------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| AE_Effect_Info_Flags                | Unused                                                                                                                                                                                                                                                                                                           |
+-------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| AE_Effect_Global_OutFlags           | Must match the `out_flags` set in GlobalSetup                                                                                                                                                                                                                                                                    |
+-------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| AE_Effect_Global_OutFlags_2         | Must match the `out_flags2` set in GlobalSetup                                                                                                                                                                                                                                                                   |
+-------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| AE_Effect_Match_Name                | A unique, constant identifier, unlike a plug-in's display name, which may be changed dynamically.                                                                                                                                                                                                                |
+-------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| AE_Reserved_Info                    | Unused.                                                                                                                                                                                                                                                                                                          |
+-------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| AE_Effect_Support_URL               | **New in AE 23.5!** URL for the effect. Shown in the Effects Manager. A user might click the link for more information about the effect or to find a newer version.                                                                                                                                              |
+-------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| AE_Effect_Search_Keywords           | **New in the 26.5 SDK (Premiere Pro Beta 27.0 only).** Locale-keyed JSON of search terms for the Effects panel search, e.g. `{"en_US": ["glow"], "es_ES": ["resplandor"]}`. Searchable via the English (`en_US`) and the user's current locale. Malformed JSON is ignored.                                       |
+-------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| AE_Effect_Description               | **New in the 26.5 SDK (Premiere Pro Beta 27.0 only).** Short description of the effect, shown in the Effects Manager. Authored as a plain string; use ASCII where possible, as non-ASCII in a PiPL resource uses the system code page.                                                                           |
+-------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

---

## Search Keywords And Description

!!! note
    These features apply to Premiere Pro Beta only, starting with version 27.0. It does not currently apply to After Effects, though After Effects may adopt it in the future.

You can help users find your effect and understand what it does by declaring search keywords and a description.

* `AE_Effect_Search_Keywords` are matched by the Effects panel search field, so users can find your effect by concepts and synonyms, not just its name.
* `AE_Effect_Description` is a short summary shown alongside your effect in the Effects Manager.

Search keywords are authored as a JSON string keyed by locale. Each key is a locale code and each value is an array of keyword strings. Your effect is searchable using both the English (`en_US`) keywords and the keywords for the user's current locale, so always include an `en_US` entry and add localized keywords for whichever additional locales you want to support. If the JSON is malformed, the keywords are ignored.

Add both properties to your effect's PiPL, alongside the existing entries:

```c
AE_Effect_Search_Keywords {
    "{\"en_US\": [\"glow\", \"bloom\", \"halation\"], \"es_ES\": [\"resplandor\"]}"
},
AE_Effect_Description {
    "Adds a soft glow around bright areas of the image."
},
```

On macOS the PiPL is compiled with Apple's `Rez`, which limits each source line to 1023 bytes. If your keyword JSON is long, split it across adjacent string literals — `Rez` joins them into one string, so the compiled result is identical:

```c
AE_Effect_Search_Keywords {
    "{\"en_US\": [\"glow\", \"bloom\"], "
    "\"es_ES\": [\"resplandor\"]}"
},
```

Alternatively, if you register your effect from code rather than via a PiPL resource, supply the same two values through the `PF_REGISTER_EFFECT_EXT3` macro (defined in `entry.h`), which uses version 3 of the plug-in entry point and takes `SEARCH_KEYWORDS` and `EFFECT_DESCRIPTION` as its final two arguments.

---

## PiPL Resources And Microsoft Visual Studio

To use resources from Microsoft Visual Studio .NET with pipltool-generated resources,

#include the output of the custom build steps into the Microsoft-generated .rc file.

```cpp
// in file WhizBang.rc, generated by .NET.
#include "WhizBang_PiPL_temp.rc" // pipltool.exe's output
```

If modifying a sample plug-in, change the name of the file generated by pipltool.exe to something like WhizBang_PiPL_temp.rc, or it will overwrite the Microsoft resources each time you build; not good.

---

## Multiple PiPLs

It is possible, but not recommended, to include multiple plug-ins (both AEGPs and effects) in the same file, using multiple PiPLs. If there are PiPLs for both AEGPs and effects in the same file, the AEGPs must come first!

No other hosts (not even Premiere Pro) support multiple PiPLs pointing to multiple effects within the same .dll or code fragment. Also, if you need to update one plug-in, do you really want to ship a new build of all your plug-ins? We recommend one PiPL, and one plug-in, per code fragment.

---

## Why Do I Need To Know All This?

You don't; After Effects does. If you follow our advice and base your projects on the SDK samples, you can simply change the .r file containing your PiPL definition(s), and your plug-in's resources will be automagically updated the next time you build. Feel the love. Or, if you ever tinker with the custom build steps, feel the pain.
