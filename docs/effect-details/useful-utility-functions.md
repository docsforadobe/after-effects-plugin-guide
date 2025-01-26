.. _effect-details/useful-utility-functions:

Useful Utility Functions
################################################################################

PF_EffectUISuite
================================================================================

Although not strictly concerned with parameters, this suite can change the name of the options button.

+-----------------------------+----------------------------------------------------------------------------------------------------------------+
|        **Function**         |                                                  **Purpose**                                                   |
+=============================+================================================================================================================+
| ``PF_SetOptionsButtonName`` | Changes the text on the options button in the effect controls palette.                                         |
|                             |                                                                                                                |
|                             | NOTE: This must be called during :ref:`PF_Cmd_PARAM_SETUP <effect-basics/command-selectors.global-selectors>`. |
|                             |                                                                                                                |
|                             | ::                                                                                                             |
|                             |                                                                                                                |
|                             |   PF_SetOptionsButtonName(                                                                                     |
|                             |     PF_ProgPtr    effect_ref,                                                                                  |
|                             |     const A_char  *nameZ);                                                                                     |
|                             |                                                                                                                |
|                             | ``nameZ`` may be up to ``A_char[31]`` in length.                                                               |
+-----------------------------+----------------------------------------------------------------------------------------------------------------+

----

.. _effect-details/useful-utility-functions.PF_AppSuite:

PF_AppSuite
================================================================================

Roughly 437 years ago, when we released After Effects 5.0, we published some useful utility callbacks in PF_AppSuite. They're as useful today as they were then. After Effects has user-controllable UI brightness.

In addition to the :ref:`effect-ui-events/custom-ui-and-drawbot.PF_EffectCustomUIOverlayThemeSuite` for custom UI in effects, use these calls to integrate seamlessly into the After Effects UI.

What better way to shame someone into purchasing a copy of your plug-in than by putting their personal information into a watermark, eh? Or set the cursor to add mask vertices, just to confuse people? Heh heh heh. But that would be wrong.

+-----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|        **Function**         |                                                                                    **Purpose**                                                                                    |
+=============================+===================================================================================================================================================================================+
| ``PF_AppGetBgColor``        | Retrieves the current background color.                                                                                                                                           |
|                             |                                                                                                                                                                                   |
|                             | ::                                                                                                                                                                                |
|                             |                                                                                                                                                                                   |
|                             |   PF_AppGetBgColor(                                                                                                                                                               |
|                             |     PF_App_Color  bg_colorP);                                                                                                                                                     |
+-----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_AppGetColor``          | Retrieves the color for the specified UI element. See AE_EffectSuites.h for a complete enumeration of available ``PF_App_Color`` values;                                          |
|                             | basically any color in After Effects' UI can be retrieved.                                                                                                                        |
|                             |                                                                                                                                                                                   |
|                             | CC adds several new ``PF_App_ColorType`` enum values for new elements that can be queried.                                                                                        |
|                             |                                                                                                                                                                                   |
|                             | Note that in CS6, the color definitions are off from ``FILL_LIGHT`` downward.                                                                                                     |
|                             |                                                                                                                                                                                   |
|                             | Use following psuedocode for CS6 only:                                                                                                                                            |
|                             |                                                                                                                                                                                   |
|                             | ::                                                                                                                                                                                |
|                             |                                                                                                                                                                                   |
|                             |   GetColor(enum e)                                                                                                                                                                |
|                             |   {                                                                                                                                                                               |
|                             |     if host_is_CS6 and e >= FILL_LIGHT                                                                                                                                            |
|                             |     e += 3                                                                                                                                                                        |
|                             |       call real GetColor                                                                                                                                                          |
|                             |   }                                                                                                                                                                               |
|                             |                                                                                                                                                                                   |
|                             |   PF_AppGetColor(                                                                                                                                                                 |
|                             |     PF_App_ColorType  color_type,                                                                                                                                                 |
|                             |     PF_App_Color      *app_colorP);                                                                                                                                               |
+-----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_AppGetLanguage``       | New in CC. Retrieves the active displayed language of AE UI so plug-in can match. Here are the possible language codes as of CC:                                                  |
|                             |                                                                                                                                                                                   |
|                             | - Chinese - ``zh_CN``                                                                                                                                                             |
|                             | - English - ``en_US``                                                                                                                                                             |
|                             | - French - ``fr_FR``                                                                                                                                                              |
|                             | - German - ``de_DE``                                                                                                                                                              |
|                             | - Italian - ``it_IT``                                                                                                                                                             |
|                             | - Japanese - ``ja_JP``                                                                                                                                                            |
|                             | - Korean - ``ko_KR``                                                                                                                                                              |
|                             | - Spanish - ``es_ES``                                                                                                                                                             |
|                             |                                                                                                                                                                                   |
|                             | ::                                                                                                                                                                                |
|                             |                                                                                                                                                                                   |
|                             |   PF_AppGetLanguage(                                                                                                                                                              |
|                             |     A_char  lang_tagZ);                                                                                                                                                           |
+-----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_GetPersonalInfo``      | Retrieves the user's registration information.                                                                                                                                    |
|                             |                                                                                                                                                                                   |
|                             | ::                                                                                                                                                                                |
|                             |                                                                                                                                                                                   |
|                             |   PF_GetPersonalInfo(                                                                                                                                                             |
|                             |     PF_AppPersonalTextInfo  *ptiP);                                                                                                                                               |
|                             |                                                                                                                                                                                   |
|                             |   typedef struct PF_AppPersonalTextInfo {                                                                                                                                         |
|                             |     A_char  name[PF_APP_MAX_PERS_LEN + 1];                                                                                                                                        |
|                             |     A_char  org[PF_APP_MAX_PERS_LEN + 1];                                                                                                                                         |
|                             |     A_char  serial_str[PF_APP_MAX_PERS_LEN+1];                                                                                                                                    |
|                             |   } PF_AppPersonalTextInfo;                                                                                                                                                       |
+-----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_GetFontStyleSheet``    | Retrieves font style sheet information for the fonts used in After Effects' UI.                                                                                                   |
|                             |                                                                                                                                                                                   |
|                             | Trivia: The font used in After Effects' UI starting in 15.0 is Adobe Clean.                                                                                                       |
|                             | Before that, it was Tahoma on Windows and Lucida Grande on macOS X.                                                                                                               |
|                             |                                                                                                                                                                                   |
|                             | ::                                                                                                                                                                                |
|                             |                                                                                                                                                                                   |
|                             |   PF_GetFontStyleSheet(                                                                                                                                                           |
|                             |     PF_FontStyleSheet  sheet,                                                                                                                                                     |
|                             |     PF_FontName        *font_nameP0,                                                                                                                                              |
|                             |     A_short            *font_numPS0,                                                                                                                                              |
|                             |     A_short            *sizePS0,                                                                                                                                                  |
|                             |     A_short            *stylePS0);                                                                                                                                                |
+-----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_SetCursor``            | Sets the cursor to any of After Effects' cursors. See AE_EffectUI.h for a complete enumeration.                                                                                   |
|                             |                                                                                                                                                                                   |
|                             | Set to:                                                                                                                                                                           |
|                             |                                                                                                                                                                                   |
|                             | - ``PF_Cursor_NONE`` to allow After Effects to set the cursor.                                                                                                                    |
|                             | - ``PF_Cursor_CUSTOM`` if you've used OS-specific calls to change the cursor (After Effects will honor your changes).                                                             |
|                             |                                                                                                                                                                                   |
|                             | ::                                                                                                                                                                                |
|                             |                                                                                                                                                                                   |
|                             |   PF_SetCursor(                                                                                                                                                                   |
|                             |     PF_CursorType  cursor);                                                                                                                                                       |
+-----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_IsRenderEngine``       | Returns TRUE if After Effects is running in watched folder mode, or is a render engine installation.                                                                              |
|                             |                                                                                                                                                                                   |
|                             | ::                                                                                                                                                                                |
|                             |                                                                                                                                                                                   |
|                             |   PF_IsRenderEngine(                                                                                                                                                              |
|                             |     PF_Boolean  *render_enginePB);                                                                                                                                                |
|                             |                                                                                                                                                                                   |
|                             | As of AE6.5, this function returns ``TRUE`` if the installation is the render engine,                                                                                             |
|                             | or if the After Effects is being run with no UI, or if After Effects is in watched folder mode.                                                                                   |
+-----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_AppColorPickerDialog`` | Displays the After Effects color picker dialog (which may be the system color picker, depending on the user's preferences).                                                       |
|                             |                                                                                                                                                                                   |
|                             | Will return ``PF_Interrupt_CANCEL`` if user cancels dialog. Returned color is in the project's working color space.                                                               |
|                             |                                                                                                                                                                                   |
|                             | ::                                                                                                                                                                                |
|                             |                                                                                                                                                                                   |
|                             |   PF_AppColorPickerDialog(                                                                                                                                                        |
|                             |     const A_char         *dialog_titleZ0,                                                                                                                                         |
|                             |     const PF_PixelFloat  *sample_colorP,                                                                                                                                          |
|                             |     PF_PixelFloat        *result_colorP);                                                                                                                                         |
+-----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_GetMouse``             | Returns the position of the mouse in the custom UI coordinate space.                                                                                                              |
|                             |                                                                                                                                                                                   |
|                             | ::                                                                                                                                                                                |
|                             |                                                                                                                                                                                   |
|                             |   PF_GetMouse(                                                                                                                                                                    |
|                             |     PF_Point  *pointP);                                                                                                                                                           |
+-----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_InvalidateRect``       | Queue up a :ref:`redraw <effect-ui-events/custom-ui-and-drawbot.redrawing>` of a specific area of the custom UI for an effect.                                                    |
|                             |                                                                                                                                                                                   |
|                             | Only valid while handling a non-drawing event in the effect.                                                                                                                      |
|                             |                                                                                                                                                                                   |
|                             | Specify ``rectP0`` as ``NULL`` to invalidate the entire window. The redraw will happen at the next available idle moment after returning from the event.                          |
|                             |                                                                                                                                                                                   |
|                             | Set the ``PF_EO_UPDATE_NOW`` event outflag to update the window immediately after the event returns.                                                                              |
|                             |                                                                                                                                                                                   |
|                             | ::                                                                                                                                                                                |
|                             |                                                                                                                                                                                   |
|                             |   PF_InvalidateRect(                                                                                                                                                              |
|                             |     const PF_ContextH  contextH,                                                                                                                                                  |
|                             |     const PF_Rect*     rectP0);                                                                                                                                                   |
+-----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_ConvertLocalToGlobal`` | Converts from the custom UI coordinate system to global screen coordinates. Use only during custom UI event handling.                                                             |
|                             |                                                                                                                                                                                   |
|                             | ::                                                                                                                                                                                |
|                             |                                                                                                                                                                                   |
|                             |   PF_ConvertLocalToGlobal(                                                                                                                                                        |
|                             |     const PF_Point  *localP,                                                                                                                                                      |
|                             |     PF_Point        *globalP);                                                                                                                                                    |
+-----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

----

Advanced Appsuite: You Can Do That?!
================================================================================

``PF_AdvAppSuite`` was originally designed for some pretty nefarious purposes; an external application was pretending to be an After Effects plug-in, and required ways to notify After Effects of the changes it had made to the project. Our API impurity is your gain.

----

PF_AdvAppSuite2
================================================================================

+-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
|         **Function**          |                                                                    **Purpose**                                                                     |
+===============================+====================================================================================================================================================+
| ``PF_SetProjectDirty``        | Tells After Effects that the project has been changed since it was last saved.                                                                     |
|                               |                                                                                                                                                    |
|                               | ::                                                                                                                                                 |
|                               |                                                                                                                                                    |
|                               |   PF_SetProjectDirty(void);                                                                                                                        |
+-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_SaveProject``            | Saves the project to the current path. To save the project elsewhere, use :ref:`AEGP_SaveProjectToPath() <aegps/aegp-suites.AEGP_ProjSuite>`.      |
|                               |                                                                                                                                                    |
|                               | ::                                                                                                                                                 |
|                               |                                                                                                                                                    |
|                               |   PF_SaveProject(void);                                                                                                                            |
+-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_SaveBackgroundState``    | Stores the background state (After Effects' position in the stacking order of open applications and windows).                                      |
|                               |                                                                                                                                                    |
|                               | ::                                                                                                                                                 |
|                               |                                                                                                                                                    |
|                               |   PF_SaveBackgroundState(void);                                                                                                                    |
+-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_ForceForeground``        | Brings After Effects to the front of all currently open applications and windows.                                                                  |
|                               |                                                                                                                                                    |
|                               | ::                                                                                                                                                 |
|                               |                                                                                                                                                    |
|                               |   PF_ForceForeground(void);                                                                                                                        |
+-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_RestoreBackgroundState`` | Puts After Effects back where it was, in relation to other applications and windows.                                                               |
|                               |                                                                                                                                                    |
|                               | ::                                                                                                                                                 |
|                               |                                                                                                                                                    |
|                               |   PF_RestoreBackgroundState(void);                                                                                                                 |
+-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_RefreshAllWindows``      | Forces all After Effects windows to update.                                                                                                        |
|                               | Note that although the Composition panel will be refreshed, this does not guarantee a new frame will be sent to External Monitor Preview plug-ins. |
|                               |                                                                                                                                                    |
|                               | ::                                                                                                                                                 |
|                               |                                                                                                                                                    |
|                               |   PF_RefreshAllWindows(void);                                                                                                                      |
+-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_InfoDrawText``           | Writes text into the After Effects info palette.                                                                                                   |
|                               |                                                                                                                                                    |
|                               | ::                                                                                                                                                 |
|                               |                                                                                                                                                    |
|                               |   PF_InfoDrawText(                                                                                                                                 |
|                               |     const A_char  *line1Z0,                                                                                                                        |
|                               |     const A_char  *line2Z0);                                                                                                                       |
+-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_InfoDrawColor``          | Draws the specified color in the After Effects info palette (alpha is ignored).                                                                    |
|                               |                                                                                                                                                    |
|                               | ::                                                                                                                                                 |
|                               |                                                                                                                                                    |
|                               |   PF_InfoDrawColor(                                                                                                                                |
|                               |     PF_Pixel  color);                                                                                                                              |
+-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_InfoDrawText3``          | Writes three lines of text into the After Effects info palette.                                                                                    |
|                               |                                                                                                                                                    |
|                               | ::                                                                                                                                                 |
|                               |                                                                                                                                                    |
|                               |   PF_InfoDrawText3(                                                                                                                                |
|                               |     const A_char  *line1Z0,                                                                                                                        |
|                               |     const A_char  *line2Z0,                                                                                                                        |
|                               |     const A_char  *line3Z0);                                                                                                                       |
+-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_InfoDrawText3Plus``      | Writes three lines of text into the After Effects info palette, with portions of the second and third lines left and right justified.              |
|                               |                                                                                                                                                    |
|                               | ::                                                                                                                                                 |
|                               |                                                                                                                                                    |
|                               |   PF_InfoDrawText3Plus(                                                                                                                            |
|                               |     const A_char  *line1Z0,                                                                                                                        |
|                               |     const A_char  *line2_jrZ0,                                                                                                                     |
|                               |     const A_char  *line2_jlZ0,                                                                                                                     |
|                               |     const A_char  *line3_jrZ0,                                                                                                                     |
|                               |     const A_char  *line3_jlZ0);                                                                                                                    |
+-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_AppendInfoText``         | Appends characters to the currently-displayed info text.                                                                                           |
|                               |                                                                                                                                                    |
|                               | ::                                                                                                                                                 |
|                               |                                                                                                                                                    |
|                               |   PF_AppendInfoText(                                                                                                                               |
|                               |     const A_char  *appendZ0);                                                                                                                      |
+-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+

----

.. _effect-details/useful-utility-functions.PF_AdvTimeSuite:

Formatting Time
================================================================================

``PF_AdvTimeSuite`` provides several functions to match how After Effects displays time. In fact, these are the same functions we use internally.

PF_AdvTimeSuite4
********************************************************************************

+-----------------------------+------------------------------------------------------------------------------------------------------------------------------------+
|        **Function**         |                                                            **Purpose**                                                             |
+=============================+====================================================================================================================================+
| ``PF_FormatTimeActiveItem`` | Given a time value and scale, returns a formatted string representing that time.                                                   |
|                             | If durationB is ``TRUE``, appropriate units will be appended.                                                                      |
|                             |                                                                                                                                    |
|                             | ::                                                                                                                                 |
|                             |                                                                                                                                    |
|                             |   PF_FormatTimeActiveItem(                                                                                                         |
|                             |     A_long      time_valueUL,                                                                                                      |
|                             |     A_u_long    time_scaleL,                                                                                                       |
|                             |     PF_Boolean  durationB,                                                                                                         |
|                             |     A_char      *time_buf);                                                                                                        |
+-----------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_FormatTime``           | Contextualizes the formatted time string for the given PF_InData and PF_EffectWorld (i.e., layer time).                            |
|                             |                                                                                                                                    |
|                             | ::                                                                                                                                 |
|                             |                                                                                                                                    |
|                             |   PF_FormatTime(                                                                                                                   |
|                             |     PF_InData       *in_data,                                                                                                      |
|                             |     PF_EffectWorld  *world,                                                                                                        |
|                             |     A_long          time_valueUL,                                                                                                  |
|                             |     A_u_long        time_scaleL,                                                                                                   |
|                             |     PF_Boolean      durationB,                                                                                                     |
|                             |     A_char          *time_buf);                                                                                                    |
+-----------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_FormatTimePlus``       | Allows you to select composition or layer time.                                                                                    |
|                             |                                                                                                                                    |
|                             | ::                                                                                                                                 |
|                             |                                                                                                                                    |
|                             |   PF_FormatTimePlus(                                                                                                               |
|                             |     PF_InData       *in_data,                                                                                                      |
|                             |     PF_EffectWorld  *world,                                                                                                        |
|                             |     A_long          time_valueUL,                                                                                                  |
|                             |     A_u_long        time_scaleL,                                                                                                   |
|                             |     PF_Boolean      comp_timeB,                                                                                                    |
|                             |     PF_Boolean      durationB,                                                                                                     |
|                             |     A_char          *time_buf);                                                                                                    |
+-----------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_GetTimeDisplayPref``   | Returns the starting frame number (specified by the user in composition settings), and the composition's time display preferences. |
|                             | Updated in 14.2 to support higher frame rates.                                                                                     |
|                             |                                                                                                                                    |
|                             | ::                                                                                                                                 |
|                             |                                                                                                                                    |
|                             |   PF_GetTimeDisplayPref(                                                                                                           |
|                             |     PF_TimeDisplayPref2  *tdp,                                                                                                     |
|                             |     A_long               *starting_num);                                                                                           |
|                             |     typedef              struct {                                                                                                  |
|                             |     A_char               display_mode;                                                                                             |
|                             |     A_long               framemax;                                                                                                 |
|                             |     A_long               frames_per_foot;                                                                                          |
|                             |     A_char               frames_start;                                                                                             |
|                             |     A_Boolean            nondrop30B;                                                                                               |
|                             |     A_Boolean            honor_source_timecodeB;                                                                                   |
|                             |     A_Boolean            use_feet_framesB;                                                                                         |
|                             |     } PF_TimeDisplayPrefVersion3;                                                                                                  |
+-----------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| ``PF_TimeCountFrames``      | New in 15.0. Returns the index of the frame in the current comp.                                                                   |
|                             |                                                                                                                                    |
|                             | ::                                                                                                                                 |
|                             |                                                                                                                                    |
|                             |   PF_TimeCountFrames(                                                                                                              |
|                             |     const A_Time  *start_timeTP,                                                                                                   |
|                             |     const A_Time  *time_stepTP,                                                                                                    |
|                             |     A_Boolean     include_partial_frameB,                                                                                          |
|                             |     A_long        *frame_countL);                                                                                                  |
+-----------------------------+------------------------------------------------------------------------------------------------------------------------------------+

----

Affecting The Timeline
================================================================================

Long ago, we helped a developer integrate their stand-alone tracker with After Effects by exposing a set of functions to give them some way to notify us of, and be notified of, changes to the timeline.

With the numerous AEGP API calls available, these aren't used much, but they're still available.

Don't confuse this suite with :ref:`AEGP_ItemSuite <aegps/aegp-suites.AEGP_ItemSuite>`.

----

PF_AdvItemSuite1
********************************************************************************

+--------------------------------+------------------------------------------------------------------------------------------+
|          **Function**          |                                       **Purpose**                                        |
+================================+==========================================================================================+
| ``PF_MoveTimeStep``            | Moves current time num_stepsL in the specified direction.                                |
|                                |                                                                                          |
|                                | ::                                                                                       |
|                                |                                                                                          |
|                                |   PF_MoveTimeStep(                                                                       |
|                                |     PF_InData       *in_data,                                                            |
|                                |     PF_EffectWorld  *world,                                                              |
|                                |     PF_Step         time_dir,                                                            |
|                                |     A_long          num_stepsL);                                                         |
+--------------------------------+------------------------------------------------------------------------------------------+
| ``PF_MoveTimeStepActiveItem``  | Moves num_stepsL in the specified direction, for the active item.                        |
|                                |                                                                                          |
|                                | ::                                                                                       |
|                                |                                                                                          |
|                                |   PF_MoveTimeStepActiveItem(                                                             |
|                                |     PF_Step  time_dir,                                                                   |
|                                |     A_long   num_stepsL);                                                                |
+--------------------------------+------------------------------------------------------------------------------------------+
| ``PF_TouchActiveItem``         | Tells After Effects that the active item must be updated.                                |
|                                |                                                                                          |
|                                | ::                                                                                       |
|                                |                                                                                          |
|                                |   PF_TouchActiveItem (void);                                                             |
+--------------------------------+------------------------------------------------------------------------------------------+
| ``PF_ForceRerender``           | Forces After Effects to rerender the current frame.                                      |
|                                |                                                                                          |
|                                | ::                                                                                       |
|                                |                                                                                          |
|                                |   PF_ForceRerender(                                                                      |
|                                |     PF_InData       *in_data,                                                            |
|                                |     PF_EffectWorld  *world);                                                             |
+--------------------------------+------------------------------------------------------------------------------------------+
| ``PF_EffectIsActiveOrEnabled`` | Returns whether the effect which owns the ``PF_ContextH`` is currently active or enabled |
|                                | (if it isn't, After Effects won't be listening for function calls from it).              |
|                                |                                                                                          |
|                                | ::                                                                                       |
|                                |                                                                                          |
|                                |   PF_EffectIsActiveOrEnabled(                                                            |
|                                |     PF_ContextH  contextH,                                                               |
|                                |     PF_Boolean   *enabledPB);                                                            |
+--------------------------------+------------------------------------------------------------------------------------------+

----

Accessing Auxiliary Channel Data
================================================================================

Some file types contain more than just pixel data; use ``PF_ChannelSuite`` to determine whether such information is present, and the macros in AE_ChannelSuites.h to retrieve it in the format you need.

----

PF_ChannelSuite1
********************************************************************************

+-----------------------------------------+-------------------------------------------------------------------------------------------------------+
|              **Function**               |                                              **Purpose**                                              |
+=========================================+=======================================================================================================+
| ``PF_GetLayerChannelCount``             | Retrieves the number of auxiliary channels associated with the indexed layer.                         |
|                                         |                                                                                                       |
|                                         | ::                                                                                                    |
|                                         |                                                                                                       |
|                                         |   PF_GetLayerChannelCount(                                                                            |
|                                         |     PF_ProgPtr     effect_ref,                                                                        |
|                                         |     PF_ParamIndex  param_index,                                                                       |
|                                         |     A_long         *num_channelsPL);                                                                  |
+-----------------------------------------+-------------------------------------------------------------------------------------------------------+
| ``PF_GetLayerChannelIndexedRefAndDesc`` | Retrieves (by index) a reference to, and description of, the specified channel.                       |
|                                         |                                                                                                       |
|                                         | ::                                                                                                    |
|                                         |                                                                                                       |
|                                         |   PF_GetLayerChannelIndexedRefAndDesc(                                                                |
|                                         |     PF_ProgPtr       effect_ref,                                                                      |
|                                         |     PF_ParamIndex    param_index,                                                                     |
|                                         |     PF_ChannelIndex  channel_index,                                                                   |
|                                         |     PF_Boolean       *foundPB,                                                                        |
|                                         |     PF_ChannelRef    *channel_refP,                                                                   |
|                                         |     PF_ChannelDesc   *channel_descP);                                                                 |
+-----------------------------------------+-------------------------------------------------------------------------------------------------------+
| ``PF_GetLayerChannelTypedRefAndDesc``   | Retrieves an auxiliary channel by type.                                                               |
|                                         | Returned information is valid only if ``foundPB`` returns ``TRUE``.                                   |
|                                         |                                                                                                       |
|                                         | ::                                                                                                    |
|                                         |                                                                                                       |
|                                         |   PF_GetLayerChannelTypedRefAndDesc(                                                                  |
|                                         |     PF_ProgPtr      effect_ref,                                                                       |
|                                         |     PF_ParamIndex   param_index,                                                                      |
|                                         |     PF_ChannelType  channel_type,                                                                     |
|                                         |     PF_Boolean      *foundPB,                                                                         |
|                                         |     PF_ChannelRef   *channel_refP,                                                                    |
|                                         |     PF_ChannelDesc  *channel_descP);                                                                  |
|                                         |                                                                                                       |
|                                         | PF_DataType will be one of the following:                                                             |
|                                         |                                                                                                       |
|                                         |   - ``PF_DataType_FLOAT`` - 34 bytes                                                                  |
|                                         |   - ``PF_DataType_DOUBLE`` - 38 bytes                                                                 |
|                                         |   - ``PF_DataType_LONG`` - 34 bytes                                                                   |
|                                         |   - ``PF_DataType_SHORT`` - 32 bytes                                                                  |
|                                         |   - ``PF_DataType_FIXED_16_16`` - 34 bytes                                                            |
|                                         |   - ``PF_DataType_CHAR`` - 31 byte                                                                    |
|                                         |   - ``PF_DataType_U_BYTE`` - 31 byte                                                                  |
|                                         |   - ``PF_DataType_U_SHORT`` - 32 bytes                                                                |
|                                         |   - ``PF_DataType_U_FIXED_16_16`` - 34 bytes                                                          |
|                                         |   - ``PF_DataType_RGB`` - 3 bytes                                                                     |
|                                         |                                                                                                       |
|                                         | PF_ChannelType will be one of the following:                                                          |
|                                         |                                                                                                       |
|                                         |   - ``PF_ChannelType_DEPTH``                                                                          |
|                                         |   - ``PF_ChannelType_NORMALS``                                                                        |
|                                         |   - ``PF_ChannelType_OBJECTID``                                                                       |
|                                         |   - ``PF_ChannelType_MOTIONVECTOR``                                                                   |
|                                         |   - ``PF_ChannelType_BK_COLOR``                                                                       |
|                                         |   - ``PF_ChannelType_TEXTURE``                                                                        |
|                                         |   - ``PF_ChannelType_COVERAGE``                                                                       |
|                                         |   - ``PF_ChannelType_NODE``                                                                           |
|                                         |   - ``PF_ChannelType_MATERIAL``                                                                       |
|                                         |   - ``PF_ChannelType_UNCLAMPED``                                                                      |
|                                         |   - ``PF_ChannelType_UNKNOWN``                                                                        |
+-----------------------------------------+-------------------------------------------------------------------------------------------------------+
| ``PF_CheckoutLayerChannel``             | Retrieves the ``PF_ChannelChunk`` containing the data associated with the given ``PF_ChannelRefPtr``. |
|                                         |                                                                                                       |
|                                         | ::                                                                                                    |
|                                         |                                                                                                       |
|                                         |   PF_CheckoutLayerChannel(                                                                            |
|                                         |     PF_ProgPtr        effect_ref,                                                                     |
|                                         |     PF_ChannelRefPtr  channel_refP,                                                                   |
|                                         |     long              what_time,                                                                      |
|                                         |     long              duration,                                                                       |
|                                         |     unsigned long     time_scale,                                                                     |
|                                         |     PF_DataType       data_type,                                                                      |
|                                         |     PF_ChannelChunk   *channel_chunkP);                                                               |
+-----------------------------------------+-------------------------------------------------------------------------------------------------------+
| ``PF_CheckinLayerChannel``              | Checks in the ``PF_ChannelChunk``. Always, always, always check the data back in.                     |
|                                         |                                                                                                       |
|                                         | ::                                                                                                    |
|                                         |                                                                                                       |
|                                         |   PF_CheckinLayerChannel(                                                                             |
|                                         |     PF_ProgPtr        effect_ref,                                                                     |
|                                         |     PF_ChannelRefPtr  channel_refP,                                                                   |
|                                         |     PF_ChannelChunk   *channel_chunkP);                                                               |
+-----------------------------------------+-------------------------------------------------------------------------------------------------------+

