# Localization

Starting in CC, PF App Suite ([Useful Utility Functions](../effect-details/useful-utility-functions.md)) adds `PF_AppGetLanguage()` to query the current language so that a plug-in can use the correct language string.

## Locale-Dependent Strings

The following guidance applies to older versions of After Effects and to effects that do not opt into UTF-8.

When passing strings to AE, some parts of the API accept Unicode. In other areas, for example when specifying effect parameter names during `PF_Cmd_PARAM_SETUP`, you'll need to pass the names in a char string. For these non-Unicode strings, AE interprets strings as being multi-byte encoded using the application's current locale. To build these strings, on Windows you can use the `WideCharToMultiByte()` function, specifying `CP_OEMCP` as the first argument. On macOS, use the encoding returned by `GetApplicationTextEncoding()`.

## UTF-8 Strings in After Effects 27.0.0 and Later

For effects targeting After Effects 27.0.0 and later, set `PF_OutFlag2_STRINGS_ARE_UTF8` in `out_flags2` during `PF_Cmd_GLOBAL_SETUP` to indicate that the effect emits and consumes UTF-8 strings instead of locale-dependent multibyte strings. Keep the corresponding PiPL flags consistent with your source code. This flag applies to After Effects only. See [PF_OutFlags2](../effect-basics/PF_OutData.md#pf_outflags2).

!!! note
    UTF-8 support does not increase parameter-name buffer sizes. `PF_DEF_NAME` remains a 32-byte buffer, including the null terminator; multibyte characters can therefore reduce the number of characters that fit.

## Testing with Different Languages

Testing with different languages in AE doesn't require an OS reinstallation, but it does require a reinstallation of AE:

### Windows

- Change the system locale to the targeted language (control panel > region and language > administrative tab > change system locale)
- Restart machine
- Install AE in the according language.

### MacOS

- Set targeted language to the primary language in the preferred language list
- Install AE in the according language.
