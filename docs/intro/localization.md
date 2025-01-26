.. _intro/localization:

Localization
################################################################################

Starting in CC, PF App Suite (:ref:`effect-details/useful-utility-functions`) adds ``PF_AppGetLanguage()`` to query the current language so that a plug-in can use the correct language string.

When passing strings to AE, some parts of the API accept Unicode. In other areas, for example when specifying effect parameter names during ``PF_Cmd_PARAM_SETUP``, you'll need to pass the names in a char string. For these non-Unicode strings, AE interprets strings as being multi-byte encoded using the application's current locale. To build these strings, on Windows you can use the ``WideCharToMultiByte()`` function, specifying ``CP_OEMCP`` as the first argument. On macOS, use the encoding returned by ``GetApplicationTextEncoding()``.

Testing with different languages in AE doesn't require an OS reinstallation, but it does require a reinstallation of AE:

For Win, change the system locale to the targeted language (control panel > region and language > administrative tab > change system locale), restart machine, and then install AE in the according language.

For Mac, set targeted language to the primary language in the preferred language list, and then install AE in the according language.
