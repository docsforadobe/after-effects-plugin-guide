.. _aegps/aegp-suites:

AEGP Suites
################################################################################

As mentioned earlier, AEGPs do everything through suites. The following suites are used by all types of AEGPs, and may be called from within any hook function (except for the RegisterSuite, which must be used from within the AEGP’s entry point). Following is a description of each function in every suite, and, where appropriate details on using those functions.

+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
|                   **Suite**                   |                                                              **Description**                                                               |
+===============================================+============================================================================================================================================+
| `Memory Suite <#_bookmark550>`__              | Manage memory resources. Use this suite! Whenever memory-related errors are encountered, After Effects can report errors for you.          |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `Command Suite <#_bookmark553>`__             | Manage your AEGP’s menu items. Used in conjunction with the Register Suite.                                                                |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `Register Suite <#_bookmark559>`__            | Used in conjunction with the `Command Suite <#_bookmark553>`__ to add functions to menu commands.                                          |
|                                               | AEIOs and Artisans must use this suite’s functions to indicate to After Effects that they want to receive the appropriate message streams. |
|                                               | You can replace some After Effects’ commands using this suite.                                                                             |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `Project Suite <#_bookmark564>`__             | Reads and modifies project data.                                                                                                           |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `Item Suite <#_bookmark568>`__                | Manages items within a project or composition.                                                                                             |
|                                               | Folders, Compositions, Solids, and Footage are all items.                                                                                  |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `Collection Suite <#_bookmark572>`__          | Query which items are currently selected, and create your own selection sets.                                                              |
|                                               | It’s often a good UI move to select all the items your AEGP has modified, just to give the user some idea what you’ve done.                |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `Composition Suite <#_bookmark577>`__         | Manages (and creates) compositions in a project, and composition-specific items like solids.                                               |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `Footage Suite <#_bookmark584>`__             | Manages footage.                                                                                                                           |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `Layer Suite <#_bookmark593>`__               | Provides information about the layers within a composition, and the relationship(s) between the source and layer times.                    |
|                                               | Solids, text, paint, cameras, lights, images, and image sequences can all become layers.                                                   |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `Effect Suite <#_bookmark611>`__              | Provides access to the effects applied to a layer.                                                                                         |
|                                               | Use Stream suites to obtain effect keyframe information.                                                                                   |
|                                               | Use `AEGP_EffectCallGeneric() <#_bookmark612>`__ to communicate with effects that you setup ahead of time to respond to your AEGP.         |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `Stream Suite <#_bookmark625>`__              | Used to access the values of a layer’s keyframe properties.                                                                                |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `Dynamic Stream Suite <#_bookmark638>`__      | Used to access the characteristics of dynamic streams associated with a layer.                                                             |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `Keyframe_Suite <#_bookmark646>`__            | Used to access and manipulate all keyframe data.                                                                                           |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `Marker Suite <#_bookmark655>`__              | Used to manipulate markers. Use `AEGP_GetCompMarkerStream() <#_bookmark582>`__ to get the composition marker stream.                       |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `Mask Suite <#_bookmark656>`__                | Provides access to retrieve information about a layer’s masks.                                                                             |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `Mask Outline Suite <#_bookmark664>`__        | Used in conjunction with Stream Suite, this suite provides detailed information about the path rendered to make a layer’s mask.            |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `Text Document Suite <#_bookmark667>`__       | Used to access the actual text on a text layer.                                                                                            |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `Text Layer Suite <#_bookmark669>`__          | Used to access the paths that make up the outlines of a text layer.                                                                        |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `Utility Suite <#_bookmark670>`__             | Supplies error message handling, AEGP version checking and access to After Effects’ undo stack.                                            |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `Persistent Data Suite <#_bookmark677>`__     | Query and manage all persistent data (i.e., the preferences file).                                                                         |
|                                               | AEGPs can also add their own data to the prefs.                                                                                            |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `Color Settings Suite <#_bookmark680>`__      | Obtain information on After Effects’ current color management settings.                                                                    |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| Render Suite                                  | Get rendered frames (and audio samples) from within an AEGP.                                                                               |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `World Suite <#_bookmark694>`__               | Allocate, dispose of, and query AEGP_Worlds.                                                                                               |
|                                               | Also provides a way to convert a ``PF_EffectWorld`` into an ``AEGP_World``, for working with effect plug-ins.                              |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| Composite Suite                               | Exposes After Effects’ compositing functionality, including transfer modes, track matting, and good old fashioned bit copying.             |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `Sound Data Suite <#_bookmark700>`__          | Functions for managing and accessing sound data.                                                                                           |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `Render Queue Suite <#_bookmark703>`__        | Add and remove items from the render queue.                                                                                                |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `Render Queue Item Suite <#_bookmark706>`__   | Query and modify items in the render queue.                                                                                                |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `Render Options Suite <#_bookmark686>`__      | Query and manage all items exposed in a render queue item’s options dialog.                                                                |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `Output Module Suite <#_bookmark712>`__       | Query and modify the output modules attached to items in the render queue.                                                                 |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `PF Interface Suite <#_bookmark716>`__        | The functions in this suite, while technically part of the AEGP API, are for use by effects.                                               |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `AEGP Iterate Suite <#_bookmark720>`__        | Gives AEGPs a way to have a function (which has the required signature) to be run on any or all available processors.                      |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
| `File Import Manager Suite <#_bookmark722>`__ | Registers AEGP file and project importers as part of After Effects’ file handling.                                                         |
+-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+

----

Fail Gracefully
================================================================================

If a suite isn’t present, make every attempt to fail gracefully. Show the user a message indicating the nature of the problem. Attempt to acquire and use an earlier version of the same suite.

Since AEGPs are so deeply integrated with After Effects, make sure that users know who or what is encountering a given problem. Identify yourself! Provide support and/or help information to the user whenever possible.

----

Handling Handles
================================================================================

Use the AEGP Memory Suite to manage memory used by the AEGP. Whenever memory related errors are encountered, After Effects can report errors for you to find early on.

``AEGP_MemHandle`` is a structure that contains more than just the referenced memory. So it should not be dereferenced directly. Use ``AEGP_LockMemHandle`` to get a pointer to the memory referenced by the ``AEGP_MemHandle``.

And of course, unlock it when you're done.

AEGP_MemorySuite1
********************************************************************************

+----------------------------+----------------------------------------------------------------------------------------------------------+
|        **Function**        |                                               **Purpose**                                                |
+============================+==========================================================================================================+
| ``AEGP_NewMemHandle``      | Create a new memory handle.                                                                              |
|                            | This memory is guaranteed to be 16-byte aligned.                                                         |
|                            | ``plugin_id`` is the ID passed in through the `main <#_bookmark540>`__\ `entry point <#_bookmark540>`__, |
|                            | or alternatively what you obtained from `AEGP_RegisterWithAEGP() <#_bookmark673>`__.                     |
|                            |                                                                                                          |
|                            | Use ``whatZ`` to identify the memory you are asking for.                                                 |
|                            | After Effects uses the string to display any related error messages.                                     |
|                            |                                                                                                          |
|                            | ::                                                                                                       |
|                            |                                                                                                          |
|                            | AEGP_NewMemHandle(                                                                                       |
|                            | AEGP_PluginID   *plugin_id,                                                                              |
|                            | const A_char    *whatZ,                                                                                  |
|                            | AEGP_MemSize    size,                                                                                    |
|                            | AEGP_MemFlag    flags,                                                                                   |
|                            | AEGP_MemHandle  *memPH);                                                                                 |
+----------------------------+----------------------------------------------------------------------------------------------------------+
| ``AEGP_FreeMemHandle``     | Release a handle you allocated using AEGP_NewMemHandle().                                                |
|                            |                                                                                                          |
|                            | ::                                                                                                       |
|                            |                                                                                                          |
|                            | AEGP_FreeMemHandle(                                                                                      |
|                            | AEGP_MemHandle  memH);                                                                                   |
+----------------------------+----------------------------------------------------------------------------------------------------------+
| ``AEGP_LockMemHandle``     | Locks the handle into memory (cannot be moved by OS).                                                    |
|                            | Use this function prior to using memory allocated by ``AEGP_NewMemHandle``. Can be nested.               |
|                            |                                                                                                          |
|                            | ::                                                                                                       |
|                            |                                                                                                          |
|                            | AEGP_LockMemHandle(                                                                                      |
|                            | AEGP_MemHandle  memH,                                                                                    |
|                            | void            **ptr_to_ptr);                                                                           |
+----------------------------+----------------------------------------------------------------------------------------------------------+
| ``AEGP_UnlockMemHandle``   | Allows OS to move the referenced memory. Always balance lock calls with unlocks.                         |
|                            |                                                                                                          |
|                            | ::                                                                                                       |
|                            |                                                                                                          |
|                            | AEGP_UnlockMemHandle(                                                                                    |
|                            | AEGP_MemHandle  memH);                                                                                   |
+----------------------------+----------------------------------------------------------------------------------------------------------+
| ``AEGP_GetMemHandleSize``  | Returns the allocated size of the handle.                                                                |
|                            |                                                                                                          |
|                            | ::                                                                                                       |
|                            |                                                                                                          |
|                            | AEGP_GetMemHandleSize AEGP_MemHandle memH,                                                               |
|                            | AEGP_MemSize  *sizeP);                                                                                   |
+----------------------------+----------------------------------------------------------------------------------------------------------+
| ``AEGP_ResizeMemHandle``   | Changes the allocated size of the handle.                                                                |
|                            |                                                                                                          |
|                            | ::                                                                                                       |
|                            |                                                                                                          |
|                            | AEGP_ResizeMemHandle(                                                                                    |
|                            | const char      *whatZ,                                                                                  |
|                            | AEGP_MemSize    new_size,                                                                                |
|                            | AEGP_MemHandle  memH);                                                                                   |
+----------------------------+----------------------------------------------------------------------------------------------------------+
| ``AEGP_SetMemReportingOn`` | If After Effects runs into problems with the memory handling, the error should be reported to the user.  |
|                            | Make use of this during development!                                                                     |
|                            |                                                                                                          |
|                            | Only memory allocated and then leaked using this suite is reported using this call,                      |
|                            | so for example memory allocated using `PF_HandleSuite1 <#_bookmark257>`__ will not be reported.          |
|                            |                                                                                                          |
|                            | ::                                                                                                       |
|                            |                                                                                                          |
|                            | AEGP_SetMemReportingOn(                                                                                  |
|                            | A_Boolean  turn_OnB);                                                                                    |
+----------------------------+----------------------------------------------------------------------------------------------------------+
| ``AEGP_GetMemStats``       | Obtain information about the number of currently allocated handles and their total size.                 |
|                            |                                                                                                          |
|                            | Only memory allocated using this suite is tracked and reported using this call,                          |
|                            | so for example memory allocated using `PF_HandleSuite1 <#_bookmark257>`__ will not be reported here.     |
|                            |                                                                                                          |
|                            | ::                                                                                                       |
|                            |                                                                                                          |
|                            | AEGP_GetMemStats(                                                                                        |
|                            | AEGP_MemID mem_id,                                                                                       |
|                            | A_long  *countPL,                                                                                        |
|                            | A_long  *sizePL);                                                                                        |
+----------------------------+----------------------------------------------------------------------------------------------------------+

----

Managing Menu Items
================================================================================

Command Suites allow you to create and handle any menu events.

To add your own menu commands, you must also use `AEGP_RegisterSuite <#_bookmark559>`__ to assign handlers to menu events.

AEGP_CommandSuite1
********************************************************************************

+-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
|         **Function**          |                                                              **Purpose**                                                               |
+===============================+========================================================================================================================================+
| ``AEGP_GetUniqueCommand``     | Obtain a unique command identifier. Use the *Register Suite* to register a handler for the command.                                    |
|                               |                                                                                                                                        |
|                               | ::                                                                                                                                     |
|                               |                                                                                                                                        |
|                               | AEGP_GetUniqueCommand(                                                                                                                 |
|                               | AEGP_Command  *unique_commandP);                                                                                                       |
|                               |                                                                                                                                        |
|                               | Note: On occasion After Effects will send command 0 (zero),                                                                            |
|                               | so don’t use that as part of your command handling logic.                                                                              |
+-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_InsertMenuCommand``    | Add a new menu command. Using nameZ = "-" will insert a separator. menu_ID can be:                                                     |
|                               |                                                                                                                                        |
|                               | - ``AEGP_Menu_NONE``                                                                                                                   |
|                               | - ``AEGP_Menu_APPLE``                                                                                                                  |
|                               | - ``AEGP_Menu_FILE``                                                                                                                   |
|                               | - ``AEGP_Menu_EDIT``                                                                                                                   |
|                               | - ``AEGP_Menu_COMPOSITION``                                                                                                            |
|                               | - ``AEGP_Menu_LAYER``                                                                                                                  |
|                               | - ``AEGP_Menu_EFFECT``                                                                                                                 |
|                               | - ``AEGP_Menu_WINDOW``                                                                                                                 |
|                               | - ``AEGP_Menu_FLOATERS``                                                                                                               |
|                               | - ``AEGP_Menu_KF_ASSIST``                                                                                                              |
|                               | - ``AEGP_Menu_IMPORT``                                                                                                                 |
|                               | - ``AEGP_Menu_SAVE_FRAME_AS``                                                                                                          |
|                               | - ``AEGP_Menu_PREFS``                                                                                                                  |
|                               | - ``AEGP_Menu_EXPORT``                                                                                                                 |
|                               | - ``AEGP_Menu_ANIMATION``                                                                                                              |
|                               | - ``AEGP_Menu_PURGE``                                                                                                                  |
|                               | - ``AEGP_Menu_NEW`` - Supported in CC and later                                                                                        |
|                               |                                                                                                                                        |
|                               | Locations can be set to a specific location in the menu or can be one assigned by After Effects:                                       |
|                               |                                                                                                                                        |
|                               | - ``AEGP_MENU_INSERT_SORTED``                                                                                                          |
|                               | - ``AEGP_MENU_INSERT_AT_BOTTOM``                                                                                                       |
|                               | - ``AEGP_MENU_INSERT_AT_TOP``                                                                                                          |
|                               |                                                                                                                                        |
|                               | For ``AEGP_Menu_WINDOW``, the BOTTOM and TOP options haven’t been supported since CS4 and will return an error.                        |
|                               | We recommend ``SORTED``.                                                                                                               |
|                               |                                                                                                                                        |
|                               | ::                                                                                                                                     |
|                               |                                                                                                                                        |
|                               | AEGP_InsertMenuCommand(                                                                                                                |
|                               | AEGP_Command  command,                                                                                                                 |
|                               | const A_char  *nameZ,                                                                                                                  |
|                               | AEGP_MenuID   menu_id,                                                                                                                 |
|                               | A_long        after_itemL);                                                                                                            |
+-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_RemoveMenuCommand``    | Remove a menu command. If you were so motivated, you could remove ALL of the After Effects menu items.                                 |
|                               |                                                                                                                                        |
|                               | ::                                                                                                                                     |
|                               |                                                                                                                                        |
|                               | AEGP_RemoveMenuCommand(                                                                                                                |
|                               | AEGP_Command  command);                                                                                                                |
+-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetCommandName``       | Set menu name of a command.                                                                                                            |
|                               |                                                                                                                                        |
|                               | ::                                                                                                                                     |
|                               |                                                                                                                                        |
|                               | AEGP_SetCommandName(                                                                                                                   |
|                               | AEGP_Command  command,                                                                                                                 |
|                               | const A_char  *nameZ);                                                                                                                 |
+-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_EnableCommand``        | Enable a menu command.                                                                                                                 |
|                               |                                                                                                                                        |
|                               | ::                                                                                                                                     |
|                               |                                                                                                                                        |
|                               | AEGP_EnableCommand(                                                                                                                    |
|                               | AEGP_Command  command);                                                                                                                |
+-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_DisableCommand``       | Disable a menu command.                                                                                                                |
|                               |                                                                                                                                        |
|                               | ::                                                                                                                                     |
|                               |                                                                                                                                        |
|                               | AEGP_DisableCommand(                                                                                                                   |
|                               | AEGP_Command  command);                                                                                                                |
+-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_CheckMarkMenuCommand`` | After Effects will draw a check mark next to the menu command.                                                                         |
|                               |                                                                                                                                        |
|                               | ::                                                                                                                                     |
|                               |                                                                                                                                        |
|                               | AEGP_CheckMarkMenuCommand(                                                                                                             |
|                               | AEGP_Command  command,                                                                                                                 |
|                               | A_Boolean     checkB);                                                                                                                 |
+-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_DoCommand``            | Call the handler for a specified menu command. Every After Effects menu item has an associated command.                                |
|                               | Note that we make no guarantees that command IDs will be consistent from version to version.                                           |
|                               |                                                                                                                                        |
|                               | ::                                                                                                                                     |
|                               |                                                                                                                                        |
|                               | AEGP_DoCommand(                                                                                                                        |
|                               | AEGP_Command command);                                                                                                                 |
|                               |                                                                                                                                        |
|                               | Having given the disclaimer above, here are a few command numbers that have been supplied to other developers, and may be of interest: |
|                               |                                                                                                                                        |
|                               | - 3061 - Open selection, ignoring any modifier keys.                                                                                   |
|                               | - 10314 - Play/Stop (valid in 13.5 and later)                                                                                          |
|                               | - 2285 - RAM Preview (valid prior to 13.5)                                                                                             |
|                               | - 2415 - Play (spacebar) (valid prior to 13.5)                                                                                         |
|                               | - 2997 - Crop composition to region of interest.                                                                                       |
|                               | - 2372 - Edit > Purge > Image Caches                                                                                                   |
|                               |                                                                                                                                        |
|                               | If your AEGP needs to call some other After Effects menu item,                                                                         |
|                               | there's a fairly easy way to find out most commands you want, using scripting:                                                         |
|                               |                                                                                                                                        |
|                               | ::                                                                                                                                     |
|                               |                                                                                                                                        |
|                               | cmd = app.findMenuCommandId(text); // e.g. text = "Open Project…"                                                                      |
|                               | alert(cmd);                                                                                                                            |
|                               |                                                                                                                                        |
|                               | With AE running, just open up Adobe ExtendScript Toolkit CC, copy the above script in,                                                 |
|                               | and in the app drop-down choose the version of After Effects you have running.                                                         |
|                               | Then hit the Play button to run the script in AE.                                                                                      |
|                               | Otherwise, contact <mailto:zlam@adobe.com> *API Engineering* for the command number.                                                   |
+-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+

----

Registering with After Effects
================================================================================

Register functions for After Effects’ use.

AEGP_RegisterSuites5
*********************************************************************************

+-------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
|               **Function**                |                                                              **Purpose**                                                               |
+===========================================+========================================================================================================================================+
| ``AEGP_RegisterCommandHook``              | Register a hook (command handler) function with After Effects.                                                                         |
|                                           | If you are replacing a function which After Effects also handles, ``AEGP_HookPriority`` determines whether your plug-in gets it first. |
|                                           |                                                                                                                                        |
|                                           | - ``AEGP_HP_BeforeAE``                                                                                                                 |
|                                           | - ``AEGP_HP_AfterAE``                                                                                                                  |
|                                           |                                                                                                                                        |
|                                           | For each menu item you add, obtain your own ``AEGP_Command`` using `AEGP_GetUniqueCommand() <#_bookmark555>`__                         |
|                                           | prior registering a single command_hook_func.                                                                                          |
|                                           | Determine which command was sent within this hook function, and act accordingly.                                                       |
|                                           |                                                                                                                                        |
|                                           | Currently, ``AEGP_HookPriority`` is ignored.                                                                                           |
|                                           |                                                                                                                                        |
|                                           | ::                                                                                                                                     |
|                                           |                                                                                                                                        |
|                                           | AEGP_RegisterCommandHook(                                                                                                              |
|                                           | AEGP_PluginID      aegp_plugin_id,                                                                                                     |
|                                           | AEGP_HookPriority  hook_priority,                                                                                                      |
|                                           | AEGP_Command       command,                                                                                                            |
|                                           | AEGP_CommandHook   command_hook_func                                                                                                   |
|                                           | void               *refconPV);                                                                                                         |
+-------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_RegisterUpdateMenuHook``           | Register your menu update function (which determines whether or not items are active),                                                 |
|                                           | called every time any menu is to be drawn.                                                                                             |
|                                           | This hook function handles updates for all menus.                                                                                      |
|                                           |                                                                                                                                        |
|                                           | ::                                                                                                                                     |
|                                           |                                                                                                                                        |
|                                           | AEGP_RegisterUpdateMenuHook(                                                                                                           |
|                                           | AEGP_PluginID        aegp_plugin_id,                                                                                                   |
|                                           | AEGP_UpdateMenuHook  update_menu_hook_func,                                                                                            |
|                                           | void                 *refconPV);                                                                                                       |
+-------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_RegisterDeathHook``                | Register your termination function. Called when the application quits.                                                                 |
|                                           |                                                                                                                                        |
|                                           | ::                                                                                                                                     |
|                                           |                                                                                                                                        |
|                                           | AEGP_RegisterDeathHook(                                                                                                                |
|                                           | AEGP_PluginID   aegp_plugin_id,                                                                                                        |
|                                           | AEGP_DeathHook  death_hook_func,                                                                                                       |
|                                           | void            *refconPV);                                                                                                            |
+-------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_RegisterVersionHook``              | Currently not called.                                                                                                                  |
+-------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_RegisterAboutStringHook``          | Currently not called.                                                                                                                  |
+-------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_RegisterAboutHook``                | Currently not called.                                                                                                                  |
+-------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_RegisterArtisan``                  | `Register your Artisan. See the <#_bookmark733>`__ Artisan chapter for more details.                                                   |
|                                           |                                                                                                                                        |
|                                           | ::                                                                                                                                     |
|                                           |                                                                                                                                        |
|                                           | AEGP_RegisterArtisan(                                                                                                                  |
|                                           | A_Version              api_version,                                                                                                    |
|                                           | A_Version              Artisan_version,                                                                                                |
|                                           | long                   aegp_plugin_id,                                                                                                 |
|                                           | void                   *aegp_refconPV,                                                                                                 |
|                                           | const A_char           *match_nameZ,                                                                                                   |
|                                           | const A_char           *Artisan_nameZ,                                                                                                 |
|                                           | PR_ArtisanEntryPoints  *entry_funcsP);                                                                                                 |
+-------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_RegisterIO``                       | Register your AEIO plug-in. See the `AEIO <#_bookmark778>`__ section for more details.                                                 |
|                                           |                                                                                                                                        |
|                                           | ::                                                                                                                                     |
|                                           |                                                                                                                                        |
|                                           | AEGP_RegisterIO (                                                                                                                      |
|                                           | AEGP_PluginID              aegp_plugin_id,                                                                                             |
|                                           | AEGP_IORefcon              aegp_refconP,                                                                                               |
|                                           | const AEIO_ModuleInfo      *io_infoP,                                                                                                  |
|                                           | const AEIO_FunctionBlock4  *aeio_fcn_blockP);                                                                                          |
+-------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_RegisterIdleHook``                 | Register your IdleHook function. After Effects will call the function sporadically,                                                    |
|                                           | while the user makes difficult artistic decisions (or while they’re getting more coffee).                                              |
|                                           |                                                                                                                                        |
|                                           | ::                                                                                                                                     |
|                                           |                                                                                                                                        |
|                                           | AEGP_RegisterIdleHook(                                                                                                                 |
|                                           | AEGP_PluginID    aegp_plugin_id,                                                                                                       |
|                                           | AEGP_IdleHook    idle_hook_func,                                                                                                       |
|                                           | AEGP_IdleRefcon  refconP);                                                                                                             |
+-------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_RegisterInteractiveArtisan``       | Registers your AEGP as an interactive artisan, for use in previewing and rendering all layers in a given composition.                  |
|                                           |                                                                                                                                        |
|                                           | ::                                                                                                                                     |
|                                           |                                                                                                                                        |
|                                           | AEGP_RegisterInteractiveArtisan (                                                                                                      |
|                                           | A_Version              api_version,                                                                                                    |
|                                           | A_Version              artisan_version,                                                                                                |
|                                           | AEGP_PluginID          aegp_plugin_id,                                                                                                 |
|                                           | void                   *aegp_refconPV,                                                                                                 |
|                                           | const A_char           *match_nameZ,                                                                                                   |
|                                           | const A_char           *artisan_nameZ,                                                                                                 |
|                                           | PR_ArtisanEntryPoints  *entry_funcsP);                                                                                                 |
+-------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_RegisterPresetLocalizationString`` | Call this to register as many strings as you like for name- replacement when presets are loaded.                                       |
|                                           | Any time a Property name is found, or referred to in an expression,                                                                    |
|                                           | and it starts with an ASCII tab character ('\t'), followed by one of the English names, it will be replaced with the localized name.   |
|                                           | (In English the tab character will simply be removed).                                                                                 |
|                                           |                                                                                                                                        |
|                                           | ::                                                                                                                                     |
|                                           |                                                                                                                                        |
|                                           | AEGP_RegisterPresetLocalizationString(                                                                                                 |
|                                           | const A_char  *english_nameZ,                                                                                                          |
|                                           | const A_char  *localized_nameZ);                                                                                                       |
+-------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+

----

Manage Projects
================================================================================

These functions access and modify project data. Support for multiple projects is included to prepare for future expansion;
After Effects currently adheres to the single project model.

To save project-specific data in After Effects’ preferences (and thus, outside the projects themselves), use the `Persistent Data Suite <#_bookmark677>`__.

Use caution: the functions for opening and creating projects do not save changes to the project currently open when they are called!

AEGP_ProjSuite6
********************************************************************************

+--------------------------------+-----------------------------------------------------------------------------------------------------------------+
|          **Function**          |                                                   **Purpose**                                                   |
+================================+=================================================================================================================+
| ``AEGP_NumProjects``           | Currently will never return more than 1. After Effects can have only one project open at a time.                |
|                                |                                                                                                                 |
|                                | ::                                                                                                              |
|                                |                                                                                                                 |
|                                | AEGP_GetNumProjects(                                                                                            |
|                                | A_long  *num_projPL)                                                                                            |
+--------------------------------+-----------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetIndProject``         | Retrieves a specific project by index.                                                                          |
|                                |                                                                                                                 |
|                                | ::                                                                                                              |
|                                |                                                                                                                 |
|                                | AEGP_GetProjectProjectByIndex(                                                                                  |
|                                | A_long         proj_indexL,                                                                                     |
|                                | AEGP_ProjectH  *projPH);                                                                                        |
+--------------------------------+-----------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetProjectName``        | Get the project name (up to ``AEGP_MAX_PROJ_NAME_LEN + 1``) in length.                                          |
|                                |                                                                                                                 |
|                                | ::                                                                                                              |
|                                |                                                                                                                 |
|                                | AEGP_GetProjectName(                                                                                            |
|                                | AEGP_ProjectH  projH,                                                                                           |
|                                | A_char         *nameZ);                                                                                         |
+--------------------------------+-----------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetProjectPath``        | Get the path of the project (empty string the project hasn’t been saved yet).                                   |
|                                | The path is a handle to a NULL-terminated A_UTF16Char string, and must be disposed with ``AEGP_FreeMemHandle``. |
|                                |                                                                                                                 |
|                                | ::                                                                                                              |
|                                |                                                                                                                 |
|                                | AEGP_GetProjectPath(                                                                                            |
|                                | AEGP_ProjectH   projH,                                                                                          |
|                                | AEGP_MemHandle  *unicode_pathPH)                                                                                |
+--------------------------------+-----------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetProjectRootFolder``  | Get the root of the project, which After Effects also treats as a folder.                                       |
|                                |                                                                                                                 |
|                                | ::                                                                                                              |
|                                |                                                                                                                 |
|                                | AEGP_GetProjectRootFolder(                                                                                      |
|                                | AEGP_ProjectH  projH,                                                                                           |
|                                | AEGP_ItemH     *root_folderPH)                                                                                  |
+--------------------------------+-----------------------------------------------------------------------------------------------------------------+
| ``AEGP_SaveProjectToPath``     | Saves the entire project to the specified full path.                                                            |
|                                | The file path is a NULL-terminated UTF-16 string with platform separators.                                      |
|                                |                                                                                                                 |
|                                | ::                                                                                                              |
|                                |                                                                                                                 |
|                                | AEGP_SaveProjectToPath(                                                                                         |
|                                | AEGP_ProjectH      projH,                                                                                       |
|                                | const A_UTF16Char  *pathZ);                                                                                     |
+--------------------------------+-----------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetProjectTimeDisplay`` | Retrieves the current time display settings.                                                                    |
|                                |                                                                                                                 |
|                                | ::                                                                                                              |
|                                |                                                                                                                 |
|                                | AEGP_GetProjectTimeDisplay(                                                                                     |
|                                | AEGP_ProjectH      projH,                                                                                       |
|                                | AEGP_TimeDisplay3  *time_displayP);                                                                             |
|                                |                                                                                                                 |
|                                | typedef struct {                                                                                                |
|                                | AEGP_TimeDisplayMode            display_mode;                                                                   |
|                                | AEGP_SourceTimecodeDisplayMode  footage_display_mode;                                                           |
|                                | A_Boolean                       display_dropframeB;                                                             |
|                                | A_Boolean                       use_feet_framesB;                                                               |
|                                | A_char                          timebaseC;                                                                      |
|                                | A_char                          frames_per_footC;                                                               |
|                                | AEGP_FramesDisplayMode          frames_display_mode;                                                            |
|                                | } AEGP_TimeDisplay3;                                                                                            |
|                                |                                                                                                                 |
|                                | enum {                                                                                                          |
|                                | AEGP_TimeDisplay_TIMECODE = 0,                                                                                  |
|                                | AEGP_TimeDisplay_FRAMES                                                                                         |
|                                | };                                                                                                              |
|                                |                                                                                                                 |
|                                | typedef char AEGP_TimeDisplayMode;                                                                              |
|                                |                                                                                                                 |
|                                | enum {                                                                                                          |
|                                | AEGP_SourceTimecode_ZERO= 0,                                                                                    |
|                                | AEGP_SourceTimecode_SOURCE_TIMECODE                                                                             |
|                                | };                                                                                                              |
|                                |                                                                                                                 |
|                                | typedef char AEGP_SourceTimecodeDisplayMode;                                                                    |
|                                |                                                                                                                 |
|                                | enum {                                                                                                          |
|                                | AEGP_Frames_ZERO_BASED= 0,                                                                                      |
|                                | AEGP_Frames_ONE_BASED,                                                                                          |
|                                | AEGP_Frames_TIMECODE_CONVERSION                                                                                 |
|                                | };                                                                                                              |
|                                |                                                                                                                 |
|                                | typedef char AEGP_FramesDisplayMode;                                                                            |
+--------------------------------+-----------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetProjectTimeDisplay`` | Specified the settings to be used for displaying time.                                                          |
|                                |                                                                                                                 |
|                                | ::                                                                                                              |
|                                |                                                                                                                 |
|                                | AEGP_SetProjectTimeDisplay(                                                                                     |
|                                | AEGP_ProjectH            projH,                                                                                 |
|                                | const AEGP_TimeDisplay3  *time_displayP);                                                                       |
+--------------------------------+-----------------------------------------------------------------------------------------------------------------+
| ``AEGP_ProjectIsDirty``        | Returns TRUE if the project has been modified since it was opened.                                              |
|                                |                                                                                                                 |
|                                | ::                                                                                                              |
|                                |                                                                                                                 |
|                                | AEGP_ProjectIsDirty(                                                                                            |
|                                | AEGP_ProjectH  projH,                                                                                           |
|                                | A_Boolean      *is_dirtyPB);                                                                                    |
+--------------------------------+-----------------------------------------------------------------------------------------------------------------+
| ``AEGP_SaveProjectAs``         | Saves the project to the specified path.                                                                        |
|                                | The file path is a NULL- terminated UTF-16 string with platform separators.                                     |
|                                |                                                                                                                 |
|                                | NOTE: This will overwrite an existing file.                                                                     |
|                                |                                                                                                                 |
|                                | ::                                                                                                              |
|                                |                                                                                                                 |
|                                | AEGP_SaveProjectAs(                                                                                             |
|                                | AEGP_ProjectH      projH,                                                                                       |
|                                | const A_UTF16Char  *pathZ);                                                                                     |
+--------------------------------+-----------------------------------------------------------------------------------------------------------------+
| ``AEGP_NewProject``            | Creates a new project. NOTE: Will close the current project without saving it first!                            |
|                                |                                                                                                                 |
|                                | ::                                                                                                              |
|                                |                                                                                                                 |
|                                | AEGP_NewProject(                                                                                                |
|                                | AEGP_ProjectH  *new_projectPH);                                                                                 |
+--------------------------------+-----------------------------------------------------------------------------------------------------------------+
| ``AEGP_OpenProjectFromPath``   | Opens a project from the supplied path, and returns its ``AEGP_ProjectH``.                                      |
|                                | The file path is a NULL-terminated UTF- 16 string with platform separators.                                     |
|                                |                                                                                                                 |
|                                | NOTE: Will close the current project without saving it first!                                                   |
|                                |                                                                                                                 |
|                                | ::                                                                                                              |
|                                |                                                                                                                 |
|                                | AEGP_OpenProjectFromPath(                                                                                       |
|                                | const A_UTF16Char  *pathZ,                                                                                      |
|                                | AEGP_ProjectH      *projectPH);                                                                                 |
+--------------------------------+-----------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetProjectBitDepth``    | Retrieves the project bit depth.                                                                                |
|                                |                                                                                                                 |
|                                | ::                                                                                                              |
|                                |                                                                                                                 |
|                                | AEGP_GetProjectBitDepth(                                                                                        |
|                                | AEGP_Projec        tH projectH,                                                                                 |
|                                | AEGP_ProjBitDepth  *bit_depthP);                                                                                |
|                                |                                                                                                                 |
|                                | AEGP_ProjBitDepth will be one of the following:                                                                 |
|                                |                                                                                                                 |
|                                | - ``AEGP_ProjBitDepth_8``                                                                                       |
|                                | - ``AEGP_ProjBitDepth_16``                                                                                      |
|                                | - ``AEGP_ProjBitDepth_32``                                                                                      |
+--------------------------------+-----------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetProjectBitDepth``    | Sets the project bit depth. Undoable.                                                                           |
|                                |                                                                                                                 |
|                                | ::                                                                                                              |
|                                |                                                                                                                 |
|                                | AEGP_SetProjectBitDepth(                                                                                        |
|                                | AEGP_ProjectH      projectH,                                                                                    |
|                                | AEGP_ProjBitDepth  bit_depth);                                                                                  |
+--------------------------------+-----------------------------------------------------------------------------------------------------------------+

AEGP_TimeDisplay2
*****************

.. note::

  Values in unused fields persist when After Effects is using a different display type.

+------------------------------------+-------------------------------------------------------------------------------------------------------------+
|             **Member**             |                                               **Description**                                               |
+====================================+=============================================================================================================+
| ``AEGP_TimeDisplayType type;``     | One of the following:                                                                                       |
|                                    |                                                                                                             |
|                                    | - ``AEGP_TimeDisplayType_TIMECODE``                                                                         |
|                                    | - ``AEGP_TimeDisplayType_FRAMES``                                                                           |
|                                    | - ``AEGP_TimeDisplayType_FEET_AND_FRAMES``                                                                  |
+------------------------------------+-------------------------------------------------------------------------------------------------------------+
| ``A_char timebaseC;``              | 0 - 100. Only used for ``AEGP_TimeDisplayType_TIMECODE``.                                                   |
+------------------------------------+-------------------------------------------------------------------------------------------------------------+
| ``A_Boolean non_drop_30B;``        | When the timebase is 30 and the item’s framerate is 29.97, determines whether to display as non-drop frame. |
+------------------------------------+-------------------------------------------------------------------------------------------------------------+
| ``A_char frames_per_footC;``       | Only used for ``AEGP_TimeDisplayType_FEET_AND_FRAMES``.                                                     |
+------------------------------------+-------------------------------------------------------------------------------------------------------------+
| ``A_long starting_frameL;``        | Usually 0 or 1. Not used when type is usually 0 or 1, not used for ``AEGP_TimeDisplayType_TIMECODE``.       |
+------------------------------------+-------------------------------------------------------------------------------------------------------------+
| ``A_Boolean auto_timecode_baseB;`` | If ``TRUE``, the project timecode display setting is set to auto.                                           |
+------------------------------------+-------------------------------------------------------------------------------------------------------------+

----

Control Items Within Projects
================================================================================

Accesses and modifies items within a project or composition.

Anything in the project bin is an AEGP_Item. Note that cameras have no source, and thus have no ``AEGP_ItemH``.

Unless more specificity is required for the function(s) you’re using, remain as abstract as possible; AEGP_Comps are passed into and returned from most functions as AEGP_Items.

AEGP_ItemSuite9
********************************************************************************

+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
|           **Function**           |                                                        **Purpose**                                                        |
+==================================+===========================================================================================================================+
| ``AEGP_GetFirstProjItem``        | Retrieves the first item in a given project.                                                                              |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_GetFirstProjItem(                                                                                                    |
|                                  | AEGP_ProjectH  projectH,                                                                                                  |
|                                  | AEGP_ItemH     *itemPH);                                                                                                  |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetNextProjItem``         | Retrieves the next project item; ``*next_itemPH`` will be ``NULL`` after the last item.                                   |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_GetNextProjItem(                                                                                                     |
|                                  | AEGP_ProjectH  projectH,                                                                                                  |
|                                  | AEGP_ItemH     itemH,                                                                                                     |
|                                  | AEGP_ItemH     *next_itemPH);                                                                                             |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetActiveItem``           | If the Project window is active, the active item is the selected item (if only one item is selected).                     |
|                                  | If a Composition, Timeline, or Footage window is active,                                                                  |
|                                  | returns the parent of the layer associated with the front- most tab in the window.                                        |
|                                  |                                                                                                                           |
|                                  | Returns NULL if no item is active.                                                                                        |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_GetActiveItem(                                                                                                       |
|                                  | AEGP_ItemH  *itemPH,                                                                                                      |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_IsItemSelected``          | Returns true if the Project window is active and the item is selected.                                                    |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_IsItemSelected(                                                                                                      |
|                                  | AEGP_ItemH  itemH,                                                                                                        |
|                                  | A_Boolean   *selectedPB)                                                                                                  |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SelectItem``              | Toggles the selection state of the item, and (depending on ``deselect_othersB``) can deselect other items.                |
|                                  | This call selects items in the Project panel.                                                                             |
|                                  |                                                                                                                           |
|                                  | To make selections in the Composition panel, use `AEGP_SetSelection <#_bookmark581>`__ in the AEGP Comp Suite.            |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_SelectItem(                                                                                                          |
|                                  | AEGP_ItemH  itemH,                                                                                                        |
|                                  | A_Boolean   selectB,                                                                                                      |
|                                  | A_Boolean   deselect_othersB);                                                                                            |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetItemType``             | Gets type of an item. Note: solids don’t appear in the project, but can be the source to a layer.                         |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_GetItemType(                                                                                                         |
|                                  | AEGP_ItemH     itemH,                                                                                                     |
|                                  | AEGP_ItemType  *item_typeP);                                                                                              |
|                                  |                                                                                                                           |
|                                  | Items are one of the following types:                                                                                     |
|                                  |                                                                                                                           |
|                                  | - ``AEGP_ItemType_NONE``                                                                                                  |
|                                  | - ``AEGP_ItemType_FOLDER``                                                                                                |
|                                  | - ``AEGP_ItemType_COMP``                                                                                                  |
|                                  | - ``AEGP_ItemType_SOLID``                                                                                                 |
|                                  | - ``AEGP_ItemType_FOOTAGE``                                                                                               |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetTypeName``             | Get name of type. (name length up to ``AEGP_MAX_TYPE_NAME_LEN + 1``).                                                     |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_GetTypeName(                                                                                                         |
|                                  | AEGP_ItemType  item_type,                                                                                                 |
|                                  | A_char         *nameZ);                                                                                                   |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetItemName``             | Get item name. (name length has no limit).                                                                                |
|                                  | ``unicode_namePH`` points to ``A_UTF16Char`` (contains null terminated UTF16 string).                                     |
|                                  |                                                                                                                           |
|                                  | It must be disposed with ``AEGP_FreeMemHandle`` .                                                                         |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_GetItemName(                                                                                                         |
|                                  | AEGP_PluginID  pluginID,                                                                                                  |
|                                  | AEGP_ItemH     itemH,                                                                                                     |
|                                  | AEGP_MemHandle *unicode_namePH);                                                                                          |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetItemName``             | Specifies the name of the AEGP_ItemH. (name length has no limit). Undoable.                                               |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_SetItemName(                                                                                                         |
|                                  | AEGP_ItemH         itemH,                                                                                                 |
|                                  | const A_UTF16Char  *nameZ);                                                                                               |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetItemID``               | Returns the item’s unique ID, which persists across saves and loads of the project.                                       |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_GetItemID(                                                                                                           |
|                                  | AEGP_ItemH  itemH,                                                                                                        |
|                                  | A_long      *item_idPL);                                                                                                  |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetItemFlags``            | Get properties of an item.                                                                                                |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_GetItemFlags(                                                                                                        |
|                                  | AEGP_ItemH      itemH,                                                                                                    |
|                                  | AEGP_ItemFlags  *item_flagsP);                                                                                            |
|                                  |                                                                                                                           |
|                                  | Flag values (may be OR’d together):                                                                                       |
|                                  |                                                                                                                           |
|                                  | - ``AEGP_ItemFlag_MISSING``                                                                                               |
|                                  | - ``AEGP_ItemFlag_HAS_PROXY``                                                                                             |
|                                  | - ``AEGP_ItemFlag_USING_PROXY``                                                                                           |
|                                  | - ``AEGP_ItemFlag_MISSING_PROXY``                                                                                         |
|                                  | - ``AEGP_ItemFlag_HAS_VIDEO``                                                                                             |
|                                  | - ``AEGP_ItemFlag_HAS_AUDIO``                                                                                             |
|                                  | - ``AEGP_ItemFlag_STILL``                                                                                                 |
|                                  | - ``AEGP_ItemFlag_HAS_ACTIVE_AUDIO``                                                                                      |
|                                  |                                                                                                                           |
|                                  | Unlike the ``HAS_AUDIO`` flag, this bit flag will set only if the comp has at least one layer where audio is actually on. |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetItemUseProxy``         | Toggle item’s proxy usage. Undoable.                                                                                      |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_SetItemUseProxy(                                                                                                     |
|                                  | AEGP_ItemH  itemH,                                                                                                        |
|                                  | A_Boolean   use_proxyB);                                                                                                  |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetItemParentFolder``     | Get folder containing item.                                                                                               |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_GetItemParentFolder(                                                                                                 |
|                                  | AEGP_ItemH  itemH,                                                                                                        |
|                                  | AEGP_ItemH  *parent_itemPH);                                                                                              |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetItemParentFolder``     | Sets an item’s parent folder. Undoable.                                                                                   |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_SetItemParentFolder(                                                                                                 |
|                                  | AEGP_ItemH  itemH,                                                                                                        |
|                                  | AEGP_ItemH  parent_folderH);                                                                                              |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetItemDuration``         | Get duration of item, in seconds.                                                                                         |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_GetItemDuration(                                                                                                     |
|                                  | AEGP_ItemH  itemH,                                                                                                        |
|                                  | A_Time      *durationPT);                                                                                                 |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetItemCurrentTime``      | Get current time within item. Not updated while rendering.                                                                |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_GetItemCurrentTime(                                                                                                  |
|                                  | AEGP_ItemH  itemH,                                                                                                        |
|                                  | A_long      *curr_timePT);                                                                                                |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetItemDimensions``       | Get width and height of item.                                                                                             |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_GetItemDimensions(                                                                                                   |
|                                  | AEGP_ItemH  itemH,                                                                                                        |
|                                  | A_long      *widthPL)                                                                                                     |
|                                  | A_long      *heightPL);                                                                                                   |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetItemPixelAspectRatio`` | Get the width of a pixel, assuming its height is 1.0, as numerator over denominator.                                      |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_GetItemPixelAspectRatio(                                                                                             |
|                                  | AEGP_ItemH  itemH,                                                                                                        |
|                                  | A_Ratio     *ratioPRt);                                                                                                   |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_DeleteItem``              | Removes item from all compositions. Undo-able.                                                                            |
|                                  | Do not use the ``AEGP_ItemH`` after calling this function.                                                                |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_DeleteItem(                                                                                                          |
|                                  | AEGP_ItemH  itemH);                                                                                                       |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetItemSolidColor``       | Removed in ``AEGP_ItemSuite4``. See `AEGP_GetSolidFootageColor <#_bookmark589>`__                                         |
|                                  |                                                                                                                           |
|                                  | Given a solid item, return its color.                                                                                     |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_GetItemSolidColor(                                                                                                   |
|                                  | AEGP_ItemH  itemH,                                                                                                        |
|                                  | PF_Pixel    *PF_Pixel);                                                                                                   |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetSolidColor``           | Removed in ``AEGP_ItemSuite4``. See `AEGP_SetSolidFootageColor <#_bookmark590>`__.                                        |
|                                  |                                                                                                                           |
|                                  | Sets the color of an existing solid (error if ``itemH`` is not a solid).                                                  |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_SetSolidColor(                                                                                                       |
|                                  | AEGP_ItemH     itemH,                                                                                                     |
|                                  | AEGP_ColorVal  color);                                                                                                    |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetSolidDimensions``      | Removed in ``AEGP_ItemSuite4``. See `AEGP_SetSolidFootageDimensions <#_bookmark591>`__.                                   |
|                                  |                                                                                                                           |
|                                  | Sets the dimensions of an existing solid (error if ``itemH`` is not a solid).                                             |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_SetSolidDimensions(                                                                                                  |
|                                  | AEGP_ItemH  itemH,                                                                                                        |
|                                  | A_short     widthS,                                                                                                       |
|                                  | A_short     heightS);                                                                                                     |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_CreateNewFolder``         | Creates a new folder in the project. The newly created folder is allocated and owned by After Effects.                    |
|                                  |                                                                                                                           |
|                                  | Passing ``NULL`` for ``parent_folderH0`` creates the folder at the project’s root.                                        |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_CreateNewFolder(                                                                                                     |
|                                  | const A_UTF16Char  *nameZ,                                                                                                |
|                                  | AEGP_ProjectH      projH),                                                                                                |
|                                  | AEGP_ItemH         parentH0),                                                                                             |
|                                  | AEGP_ItemH         *new_folderPH);                                                                                        |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetItemCurrentTime``      | Sets the current time within a given ``itemH``.                                                                           |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_SetItemCurrentTime(                                                                                                  |
|                                  | AEGP_ItemH    itemH,                                                                                                      |
|                                  | const A_Time  *new_timePT);                                                                                               |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetItemCommentLength``    | Removed in ItemSuite9. Retrieves the length (in characters) of the ``itemH’s`` comment.                                   |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_GetItemCommentLength(                                                                                                |
|                                  | AEGP_ItemH  itemH,                                                                                                        |
|                                  | A_u_long    *buf_sizePLu);                                                                                                |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetItemComment``          | Updated to support Unicode in ItemSuite9, available in                                                                    |
|                                  |                                                                                                                           |
|                                  | 14.1. Retrieves the itemH’s comment.                                                                                      |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_GetItemComment(                                                                                                      |
|                                  | AEGP_ItemH      itemH,                                                                                                    |
|                                  | AEGP_MemHandle  *unicode_namePH);                                                                                         |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetItemComment``          | Updated to support Unicode in ItemSuite9, available in                                                                    |
|                                  |                                                                                                                           |
|                                  | 14.1. Sets the itemH’s comment.                                                                                           |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_SetItemComment(                                                                                                      |
|                                  | AEGP_ItemH         itemH,                                                                                                 |
|                                  | const A_UTF16Char  *commentZ);                                                                                            |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetItemLabel``            | Retrieves an item’s label.                                                                                                |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_GetItemLabel(                                                                                                        |
|                                  | AEGP_ItemH    itemH,                                                                                                      |
|                                  | AEGP_LabelID  *labelP);                                                                                                   |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetItemLabel``            | Sets an item’s label.                                                                                                     |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_SetItemLabel(                                                                                                        |
|                                  | AEGP_ItemH    itemH,                                                                                                      |
|                                  | AEGP_LabelID  label);                                                                                                     |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetItemMRUView``          | Gets an item’s most recently used view.                                                                                   |
|                                  | The view can be used with two calls in the ``AEGP_ColorSettingsSuite``,                                                   |
|                                  | to perform a color transform on a pixel buffer from working to view color space.                                          |
|                                  |                                                                                                                           |
|                                  | ::                                                                                                                        |
|                                  |                                                                                                                           |
|                                  | AEGP_GetItemMRUView(                                                                                                      |
|                                  | AEGP_ItemH      itemH,                                                                                                    |
|                                  | AEGP_ItemViewP  *mru_viewP);                                                                                              |
+----------------------------------+---------------------------------------------------------------------------------------------------------------------------+

.. note::

  *`AEGP_RenderNewItemSoundData() <#_bookmark692>`__\ used to be here, but is now part of AEGP_RenderSuite.*

----

Managing Selections
================================================================================

This suite manages selection states, mirroring the functionality supplied by vectors in the C++ Standard Template Library.

Many types of items may be simultaneously selected in After Effects; ``AEGP_CollectionItems`` are unions of layer, mask, effect, stream, mask vertex, and keyframe items.

First acquire the current collection, then iterate across its members to ensure that whatever your AEGP does is applicable to each.

We’ve added ``AEGP_Collection2H`` and ``AEGP_CollectionItemV2`` so that selected dynamic streams can be handled with the ``AEGP_CollectionSuite``.

AEGP_CollectionSuite2
********************************************************************************

+-----------------------------------+----------------------------------------------------------------------------------------------------------------+
|           **Function**            |                                                  **Purpose**                                                   |
+===================================+================================================================================================================+
| ``AEGP_NewCollection``            | Creates and returns a new, empty collection.                                                                   |
|                                   | To obtain the current composition’s selection as a collection, use ``AEGP_GetNewCollectionFromCompSelection``. |
|                                   |                                                                                                                |
|                                   | ::                                                                                                             |
|                                   |                                                                                                                |
|                                   | AEGP_NewCollection(                                                                                            |
|                                   | AEGP_PluginID      plugin_id,                                                                                  |
|                                   | AEGP_Collection2H  *collectionPH);                                                                             |
+-----------------------------------+----------------------------------------------------------------------------------------------------------------+
| ``AEGP_DisposeCollection``        | Disposes of a collection.                                                                                      |
|                                   |                                                                                                                |
|                                   | ::                                                                                                             |
|                                   |                                                                                                                |
|                                   | AEGP_DisposeCollection(                                                                                        |
|                                   | AEGP_Collection2H  collectionH);                                                                               |
+-----------------------------------+----------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetCollectionNumItems``    | Returns the number of items contained in the given collection.                                                 |
|                                   |                                                                                                                |
|                                   | ::                                                                                                             |
|                                   |                                                                                                                |
|                                   | AEGP_GetCollectionNumItems(                                                                                    |
|                                   | AEGP_Collection2H  collectionH,                                                                                |
|                                   | A_u_long           *num_itemsPL);                                                                              |
+-----------------------------------+----------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetCollectionItemByIndex`` | Retrieves (creates and populates) the index’d collection item.                                                 |
|                                   |                                                                                                                |
|                                   | ::                                                                                                             |
|                                   |                                                                                                                |
|                                   | AEGP_GetCollectionItemByIndex(                                                                                 |
|                                   | AEGP_Collection2H      collectionH,                                                                            |
|                                   | A_u_long               indexL,                                                                                 |
|                                   | AEGP_CollectionItemV2  *itemP);                                                                                |
+-----------------------------------+----------------------------------------------------------------------------------------------------------------+
| ``AEGP_CollectionPushBack``       | Adds an item to the given collection.                                                                          |
|                                   |                                                                                                                |
|                                   | ::                                                                                                             |
|                                   |                                                                                                                |
|                                   | AEGP_CollectionPushBack(                                                                                       |
|                                   | AEGP_Collection2H            collectionH,                                                                      |
|                                   | const AEGP_CollectionItemV2  *itemP);                                                                          |
+-----------------------------------+----------------------------------------------------------------------------------------------------------------+
| ``AEGP_CollectionErase``          | Removes an index’d item (or items) from a given collection. NOTE: this range is exclusive,                     |
|                                   | like STL iterators. To erase the first item, you would pass 0 and 1, respectively.                             |
|                                   |                                                                                                                |
|                                   | ::                                                                                                             |
|                                   |                                                                                                                |
|                                   | AEGP_CollectionErase(                                                                                          |
|                                   | AEGP_Collection2H  collectionH,                                                                                |
|                                   | A_u_long           index_firstL,                                                                               |
|                                   | A_u_long           index_lastL);                                                                               |
+-----------------------------------+----------------------------------------------------------------------------------------------------------------+

Ownership Of Collection Items
********************************************************************************

When ``AEGP_StreamRefHs`` are inserted into a collection, they are adopted by the collection; do not free them.

``AEGP_EffectRefHs``, on the other hand, are not adopted, and must be freed by the calling AEGP.

----

Manipulate Compositions
================================================================================

Provide information about the compositions in a project, and create cameras, lights, and solids.

AEGP_CompSuite11
********************************************************************************

+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
|                 **Function**                  |                                                      **Purpose**                                                      |
+===============================================+=======================================================================================================================+
| ``AEGP_GetCompFromItem``                      | Retrieves the handle to the composition, given an item handle.                                                        |
|                                               | Returns ``NULL`` if ``itemH`` is not an ``AEGP_CompH``.                                                               |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_GetCompFromItem(                                                                                                 |
|                                               | AEGP_ItemH  itemH,                                                                                                    |
|                                               | AEGP_CompH  *compPH);                                                                                                 |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetItemFromComp``                      | Used to get the item handle, given a composition handle.                                                              |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_GetItemFromComp(                                                                                                 |
|                                               | AEGP_CompH  compH,                                                                                                    |
|                                               | AEGP_ItemH  *itemPH);                                                                                                 |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetCompDownsampleFactor``              | Returns current downsample factor. Measured in pixels X by Y.                                                         |
|                                               | Users can choose a custom downsample factor with independent X and Y.                                                 |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_GetCompDownsampleFactor(                                                                                         |
|                                               | AEGP_CompH             compH,                                                                                         |
|                                               | AEGP_DownsampleFactor  *dsfP);                                                                                        |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetCompDownsampleFactor``              | Sets the composition’s downsample factor.                                                                             |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_SetCompDownsampleFactor(                                                                                         |
|                                               | AEGP_CompH             compH,                                                                                         |
|                                               | AEGP_DownsampleFactor  *dsfP);                                                                                        |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetCompBGColor``                       | Returns the composition background color.                                                                             |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_GetCompBGColor(                                                                                                  |
|                                               | AEGP_CompH     compH,                                                                                                 |
|                                               | AEGP_ColorVal  *bg_colorP);                                                                                           |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetCompBGColor``                       | Sets a composition’s background color.                                                                                |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_SetCompBGColor(                                                                                                  |
|                                               | AEGP_CompH           compH,                                                                                           |
|                                               | const AEGP_ColorVal  *bg_colorP);                                                                                     |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetCompFlags``                         | Returns composition flags, or’d together.                                                                             |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_GetCompFlags(                                                                                                    |
|                                               | AEGP_CompH      compH,                                                                                                |
|                                               | AEGP_CompFlags  *AEGP_CompFlags);                                                                                     |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | - ``AEGP_CompFlag_SHOW_ALL_SHY``                                                                                      |
|                                               | - ``AEGP_CompFlag_ENABLE_MOTION_BLUR``                                                                                |
|                                               | - ``AEGP_CompFlag_ENABLE_TIME_FILTER``                                                                                |
|                                               | - ``AEGP_CompFlag_GRID_TO_FRAME``                                                                                     |
|                                               | - ``AEGP_CompFlag_GRID_TO_FIELDS``                                                                                    |
|                                               | - ``AEGP_CompFlag_USE_LOCAL_DSF``                                                                                     |
|                                               | - ``AEGP_CompFlag_DRAFT_3D``                                                                                          |
|                                               | - ``AEGP_CompFlag_SHOW_GRAPH``                                                                                        |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetShowLayerNameOrSourceName``         | New in CC. Passes back true if the Comp’s timeline shows layer names, false if source names.                          |
|                                               | This will open the comp as a side effect.                                                                             |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_GetShowLayerNameOrSourceName(                                                                                    |
|                                               | AEGP_CompH  compH,                                                                                                    |
|                                               | A_Boolean   *layer_names_shownPB);                                                                                    |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetShowLayerNameOrSourceName``         | New in CC. Pass in true to have the Comp’s timeline show layer names, false for source names.                         |
|                                               | This will open the comp as a side effect.                                                                             |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_SetShowLayerNameOrSourceName(                                                                                    |
|                                               | AEGP_CompH  compH,                                                                                                    |
|                                               | A_Boolean   *layer_names_shownPB);                                                                                    |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetShowBlendModes``                    | New in CC. Passes back true if the Comp’s timeline shows blend modes column, false if hidden.                         |
|                                               | This will open the comp as a side effect.                                                                             |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_GetShowBlendModes(                                                                                               |
|                                               | AEGP_CompH  compH,                                                                                                    |
|                                               | A_Boolean   *blend_modes_shownPB);                                                                                    |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetShowBlendModes``                    | New in CC. Pass in true to have the Comp’s timeline show the blend modes column, false to hide it.                    |
|                                               | This will open the comp as a side effect.                                                                             |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_GetCompFlags(                                                                                                    |
|                                               | AEGP_CompH  compH,                                                                                                    |
|                                               | A_Boolean   show_blend_modesB);                                                                                       |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetCompFramerate``                     | Returns the composition’s frames per second.                                                                          |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_GetCompFramerate(                                                                                                |
|                                               | AEGP_CompH  compH,                                                                                                    |
|                                               | A_FpLong    *fpsPF);                                                                                                  |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetCompFramerate``                     | Sets the composition’s frames per second.                                                                             |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_SetCompFramerate(                                                                                                |
|                                               | AEGP_CompH  compH,                                                                                                    |
|                                               | A_FpLong    *fpsPF);                                                                                                  |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetCompShutterAnglePhase``             | The composition shutter angle and phase.                                                                              |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_GetCompShutterAnglePhase(                                                                                        |
|                                               | AEGP_CompH  compH,                                                                                                    |
|                                               | A_Ratio     *angle,                                                                                                   |
|                                               | A_Ratio     *phase);                                                                                                  |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetCompShutterFrameRange``             | The duration of the shutter frame, in seconds.                                                                        |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_GetCompShutterFrameRange(                                                                                        |
|                                               | AEGP_CompH    compH,                                                                                                  |
|                                               | const A_Time  *comp_timeP);                                                                                           |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetCompSuggestedMotionBlurSamples``    | Retrieves the number of motion blur samples After Effects will perform in the given composition.                      |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_GetCompSuggestedMotionBlurSamples(                                                                               |
|                                               | AEGP_CompH  compH,                                                                                                    |
|                                               | A_long      *samplesPL)                                                                                               |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetCompSuggestedMotionBlurSamples``    | Specifies the number of motion blur samples After Effects will perform in the given composition. Undoable.            |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_SetCompSuggestedMotionBlurSamples(                                                                               |
|                                               | AEGP_CompH  compH,                                                                                                    |
|                                               | A_long      samplesL);                                                                                                |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetCompMotionBlurAdaptiveSampleLimit`` | New in CC. Retrieves the motion blur adaptive sample limit for the given composition.                                 |
|                                               | As of CC, a new comp defaults to 128.                                                                                 |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_GetCompMotionBlurAdaptiveSampleLimit(                                                                            |
|                                               | AEGP_CompH  compH,                                                                                                    |
|                                               | A_long      *samplesPL)                                                                                               |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetCompMotionBlurAdaptiveSampleLimit`` | New in CC. Specifies the motion blur adaptive sample limit for the given composition.                                 |
|                                               | As of CC, both the limit and the suggested values are clamped to [2,256] range                                        |
|                                               | and the limit value will not be allowed less than the suggested value.                                                |
|                                               |                                                                                                                       |
|                                               | Undoable.                                                                                                             |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_SetCompMotionBlurAdaptiveSampleLimit(                                                                            |
|                                               | AEGP_CompH  compH,                                                                                                    |
|                                               | A_long      samplesL);                                                                                                |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetCompWorkAreaStart``                 | Get the time where the current work area starts.                                                                      |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_GetCompWorkAreaStart(                                                                                            |
|                                               | AEGP_CompH  compH,                                                                                                    |
|                                               | A_Time      *startPT);                                                                                                |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetCompWorkAreaDuration``              | Get the duration of a composition’s current work area, in seconds.                                                    |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_GetCompWorkAreaDuration(                                                                                         |
|                                               | AEGP_CompH  compH,                                                                                                    |
|                                               | A_Time      *durationPT);                                                                                             |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetCompWorkAreaStartAndDuration``      | Set the work area start and duration, in seconds. Undo-able.                                                          |
|                                               | One call to this function is sufficient to set the layer’s in point and duration;                                     |
|                                               | it’s not necessary to call it twice, once for each timespace.                                                         |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_SetCompWorkAreaStartAndDuration(                                                                                 |
|                                               | AEGP_CompH    compH,                                                                                                  |
|                                               | const A_Time  *startPT)                                                                                               |
|                                               | const A_Time  *durationPT);                                                                                           |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_CreateSolidInComp``                    | Creates a new solid with a specified width, height, color, and duration in the composition. Undo-able.                |
|                                               |                                                                                                                       |
|                                               | If you pass ``NULL`` for the duration, After Effects uses its preference for the duration of a new still.             |
|                                               | If you pass NULL, or an invalid time scale, duration is set to the length of the composition.                         |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_CreateSolidInComp(                                                                                               |
|                                               | const A_UTF16Char  *utf_nameZ,                                                                                        |
|                                               | A_Long             widthL,                                                                                            |
|                                               | A_Long             heightL,                                                                                           |
|                                               | const PF_Pixel     *color,                                                                                            |
|                                               | AEGP_CompH         parent_compH,                                                                                      |
|                                               | const A_Time       *durationPT0,                                                                                      |
|                                               | AEGP_LayerH        *new_solidPH);                                                                                     |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_CreateCameraInComp``                   | Creates and adds a camera to the specified composition.                                                               |
|                                               | Once created, you can manipulate the camera’s parameter streams using the `AEGP_StreamSuite <#_bookmark625>`__.       |
|                                               |                                                                                                                       |
|                                               | To specify a two-node camera, use `AEGP_SetLayerFlag <#_bookmark599>`__ to set ``AEGP_LayerFlag_LOOK_AT_POI``.        |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_CreateCameraInComp(                                                                                              |
|                                               | const A_UTF16Char  *utf_nameZ,                                                                                        |
|                                               | A_FloatPoint       center_point,                                                                                      |
|                                               | AEGP_CompH         parent_compH,                                                                                      |
|                                               | AEGP_LayerH        *new_cameraPH);                                                                                    |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_CreateLightInComp``                    | Creates and adds a light to the specified composition.                                                                |
|                                               | Once created, you can manipulate the light’s parameter streams using the `AEGP_StreamSuite <#_bookmark625>`__.        |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_CreateLightInComp(                                                                                               |
|                                               | const A_UTF16Char  *utf_nameZ,                                                                                        |
|                                               | A_FloatPoint       center_point,                                                                                      |
|                                               | AEGP_CompH         parent_compH,                                                                                      |
|                                               | AEGP_LayerH        *new_lightPH);                                                                                     |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_CreateComp``                           | Creates a new composition for the project.                                                                            |
|                                               | If you don’t provide a parent folder, the composition will be at the root level of the project. Undo-able.            |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_CreateComp(                                                                                                      |
|                                               | AEGP_ItemH         parent_folderHO,                                                                                   |
|                                               | const A_UTF16Char  *utf_nameZ,                                                                                        |
|                                               | A_Long             widthL,                                                                                            |
|                                               | A_Long             heightL,                                                                                           |
|                                               | const A_Ratio      *pixel_aspect_ratioPRt,                                                                            |
|                                               | const A_Time       *durationPT,                                                                                       |
|                                               | const A_Ratio      *frameratePRt,                                                                                     |
|                                               | AEGP_CompH         *new_compPH);                                                                                      |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetNewCollectionFromCompSelection``    | Creates a new AEGP_Collection2H from the items selected in the given composition.                                     |
|                                               | The plug-in is responsible for disposing of the ``AEGP_Collection2H``.                                                |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_GetNewCollectionFromCompSelection(                                                                               |
|                                               | AEGP_PluginID      plugin_id,                                                                                         |
|                                               | AEGP_CompH         compH,                                                                                             |
|                                               | AEGP_Collection2H  *collectionPH);                                                                                    |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetSelection``                         | Sets the selection within the given composition to the given AEGP_Collection2H.                                       |
|                                               | Will return an error if members of the AEGP_Collection2H are not available.                                           |
|                                               | Don’t assume that a composition hasn’t changed between operations; always use a fresh ``AEGP_Collection2H``.          |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_SetSelection(                                                                                                    |
|                                               | AEGP_CompH         compH,                                                                                             |
|                                               | AEGP_Collection2H  collectionH);                                                                                      |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetCompDisplayStartTime``              | Gets the displayed start time of a composition.                                                                       |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_GetCompDisplayStartTime(                                                                                         |
|                                               | AEGP_CompH    compH,                                                                                                  |
|                                               | const A_Time  *start_timePT);                                                                                         |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetCompDisplayStartTime``              | Not undo-able. Sets the displayed start time of a composition (has no effect on the duration of the composition).     |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_SetCompDisplayStartTime(                                                                                         |
|                                               | AEGP_CompH    compH,                                                                                                  |
|                                               | const A_Time  *start_timePT);                                                                                         |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetCompDuration``                      | Undoable. Sets the duration of the given composition.                                                                 |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_SetCompDuration(                                                                                                 |
|                                               | AEGP_CompH    compH,                                                                                                  |
|                                               | const A_Time  *durationPT);                                                                                           |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_CreateNullInComp``                     | Creates a "null object" in the composition (useful for translating projects from 3D applications into After Effects). |
|                                               |                                                                                                                       |
|                                               | If you pass ``NULL`` for the duration, After Effects uses its preference for the duration of a new still.             |
|                                               | If you pass 0, or an invalid time scale, duration is set to the length of the composition.                            |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_CreateNullInComp(                                                                                                |
|                                               | const A_UTF16Char  *utf_nameZ,                                                                                        |
|                                               | AEGP_CompH         parent_compH,                                                                                      |
|                                               | const A_Time       *durationPT0,                                                                                      |
|                                               | AEGP_LayerH        *new_null_solidPH);                                                                                |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetCompPixelAspectRatio``              | Sets the pixel aspect ratio of a composition.                                                                         |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_SetCompPixelAspectRatio(                                                                                         |
|                                               | AEGP_CompH     compH,                                                                                                 |
|                                               | const A_Ratio  *parPRt);                                                                                              |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_CreateTextLayerInComp``                | Updated in CS6. Creates a text layer in the composition, and returns its AEGP_LayerH.                                 |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_CreateTextLayerInComp(                                                                                           |
|                                               | AEGP_CompH   parent_compH,                                                                                            |
|                                               | A_Boolean    select_new_layerB,                                                                                       |
|                                               | AEGP_LayerH  *new_text_lyrPH);                                                                                        |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_CreateBoxTextLayerInComp``             | Updated in CS6. Creates a new box text layer, and returns its ``AEGP_LayerH``.                                        |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_CreateBoxTextLayerInComp(                                                                                        |
|                                               | AEGP_CompH    parent_compH,                                                                                           |
|                                               | A_Boolean     select_new_layerB,                                                                                      |
|                                               | A_FloatPoint  box_dimensions,                                                                                         |
|                                               | AEGP_LayerH   *new_text_layerPH);                                                                                     |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetCompDimensions``                    | Sets the dimensions of the composition. Undoable.                                                                     |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_SetCompDimensions(                                                                                               |
|                                               | AEGP_CompH  compH,                                                                                                    |
|                                               | A_long      widthL,                                                                                                   |
|                                               | A_long      heightL);                                                                                                 |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_DuplicateComp``                        | Duplicates the composition. Undoable.                                                                                 |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_DuplicateComp(                                                                                                   |
|                                               | AEGP_CompH  compH,                                                                                                    |
|                                               | AEGP_CompH  *new_compPH);                                                                                             |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetCompFrameDuration``                 | Retrieves the duration of a frame in a composition.                                                                   |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_GetCompFrameDuration(                                                                                            |
|                                               | AEGP_CompH  compH,                                                                                                    |
|                                               | A_Time      *timeP);                                                                                                  |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetMostRecentlyUsedComp``              | Returns the most-recently-used composition.                                                                           |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_GetMostRecentlyUsedComp(                                                                                         |
|                                               | AEGP_CompH  *compPH);                                                                                                 |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_CreateVectorLayerInComp``              | Creates and returns a handle to a new vector layer.                                                                   |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_CreateVectorLayerInComp(                                                                                         |
|                                               | AEGP_CompH   parent_compH,                                                                                            |
|                                               | AEGP_LayerH  *new_vec_layerPH);                                                                                       |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetNewCompMarkerStream``               | Returns an AEGP_StreamRefH to the composition’s marker stream.                                                        |
|                                               | Must be disposed by caller.                                                                                           |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_GetNewCompMarkerStream(                                                                                          |
|                                               | AEGP_PluginID    aegp_plugin_id,                                                                                      |
|                                               | AEGP_CompH       parent_compH,                                                                                        |
|                                               | AEGP_StreamRefH  *streamPH);                                                                                          |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetCompDisplayDropFrame``              | Passes back a boolean that indicates whether the specified comp uses drop-frame timecode or not.                      |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_GetCompDisplayDropFrame(                                                                                         |
|                                               | AEGP_CompH  compH,                                                                                                    |
|                                               | A_Boolean   *dropFramePB);                                                                                            |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetCompDisplayDropFrame``              | Sets the dropness of the timecode in the specified composition.                                                       |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_SetCompDisplayDropFrame(                                                                                         |
|                                               | AEGP_CompH  compH,                                                                                                    |
|                                               | A_Boolean   dropFrameB);                                                                                              |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+
| ``AEGP_ReorderCompSelection``                 | Move the selection to a certain layer index. Use along with ``AEGP_SetSelection().``                                  |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |
|                                               | AEGP_SetCompDisplayDropFrame(                                                                                         |
|                                               | AEGP_CompH  compH,                                                                                                    |
|                                               | A_long      index);                                                                                                   |
+-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------+

----

Work with Footage
================================================================================

Provides information about footage, or items in a project or composition. When getting and setting footage’s interpretation, it is possible to specify incompatible options.

If you encounter warnings and errors during development, be sure to make all related changes atomically, and reassess the logic of the operation you’re performing.

For example, changing the pull-down interpretation of footage won’t work unless there’s a difference between it’s native and conformed frame rate.

Depending on what you’re trying to accomplish, it may make sense to abort all of your operations at that point, inform the user of the problem encountered.

AEGP_FootageSuite5
********************************************************************************

+------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|               **Function**               |                                                                               **Purpose**                                                                               |
+==========================================+=========================================================================================================================================================================+
| ``AEGP_GetMainFootageFromItem``          | Returns an error if item isn’t a footage item.                                                                                                                          |
|                                          | Used to convert an item handle to a footage handle.                                                                                                                     |
|                                               |                                                                                                                       |
|                                               | ::                                                                                                                    |
|                                               |                                                                                                                       |

|                                          |                                                                      AEGP_GetMainFootageFromItem(                                                                       |
|                                          |                                                                          AEGP_ItemH     itemH,                                                                          |
|                                          |                                                                       AEGP_FootageH  *footagePH);                                                                       |
+------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetProxyFootageFromItem``         | Returns an error if item has no proxy. Returns the proxy footage handle. Note: a composition can have a proxy.                                                          |
|                                          |                                                                                                                                                                         |
|                                          | ::                                                                                                                                                                      |
|                                          |                                                                                                                                                                         |
|                                          | AEGP_GetProxyFootageFromItem(                                                                                                                                           |
|                                          | AEGP_ItemH     itemH,                                                                                                                                                   |
|                                          | AEGP_FootageH  *proxy_ftgPH);                                                                                                                                           |
+------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetFootageNumFiles``              | Returns the number of data (RGBA or audio) files, and the number of files per frame (may be greater than one if the footage has auxiliary channels).                    |
|                                          |                                                                                                                                                                         |
|                                          | ::                                                                                                                                                                      |
|                                          |                                                                                                                                                                         |
|                                          | AEGP_GetFootageNumFiles(                                                                                                                                                |
|                                          | AEGP_FootageH  footageH,                                                                                                                                                |
|                                          | A_long         *num_filesPL0,                                                                                                                                           |
|                                          | A_long         *files_per_frmPL0);                                                                                                                                      |
+------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetFootagePath``                  | Get fully realized path to footage source file. Retrieves the footage path for a piece of footage (or for the specified frame of a footage sequence).                   |
|                                          | ``frame_numL`` ranges from ``0 to num_main_files``, as obtained using `AEGP_GetFootageNumFiles <#_bookmark586>`__.                                                      |
|                                          |                                                                                                                                                                         |
|                                          | ``AEGP_FOOTAGE_MAIN_FILE_INDEX`` is the main file.                                                                                                                      |
|                                          | The path is a handle to a NULL-terminated ``A_UTF16Char`` string, and must be disposed with `AEGP_FreeMemHandle`.                                                       |
|                                          |                                                                                                                                                                         |
|                                          | ::                                                                                                                                                                      |
|                                          |                                                                                                                                                                         |
|                                          | AEGP_GetFootagePath(                                                                                                                                                    |
|                                          | AEGP_FootageH   footageH,                                                                                                                                               |
|                                          | A_long          frame_numL,                                                                                                                                             |
|                                          | A_long          file_indexL,                                                                                                                                            |
|                                          | AEGP_MemHandle  *unicode_pathPH);                                                                                                                                       |
+------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetFootageSignature``             | Retrieves the footage signature of specified footage.                                                                                                                   |
|                                          |                                                                                                                                                                         |
|                                          | ::                                                                                                                                                                      |
|                                          |                                                                                                                                                                         |
|                                          | AEGP_GetFootageSignature(                                                                                                                                               |
|                                          | AEGP_FootageH          footageH,                                                                                                                                        |
|                                          | AEGP_FootageSignature  *sigP);                                                                                                                                          |
|                                          |                                                                                                                                                                         |
|                                          | The signature will be one of the following:                                                                                                                             |
|                                          |                                                                                                                                                                         |
|                                          | - ``AEGP_FootageSignature_NONE``                                                                                                                                        |
|                                          | - ``AEGP_FootageSignature_MISSING``                                                                                                                                     |
|                                          | - ``AEGP_FootageSignature_SOLID``                                                                                                                                       |
+------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_NewFootage``                      | Creates a new footage item. The file path is a NULL- terminated UTF-16 string with platform separators.                                                                 |
|                                          | Note that footage filenames with colons are not allowed, since colons are used as path separators in the HFS+ file system.                                              |
|                                          |                                                                                                                                                                         |
|                                          | ::                                                                                                                                                                      |
|                                          |                                                                                                                                                                         |
|                                          | AEGP_NewFootage(                                                                                                                                                        |
|                                          | AEGP_PluginID                         aegp_plugin_id,                                                                                                                   |
|                                          | const A_UTF16Char                     *pathZ,                                                                                                                           |
|                                          | const AEGP_FootageLayerKey            *layer_infoP0,                                                                                                                    |
|                                          | const AEGP_FileSequenceImportOptions  *sequence_optionsP0,                                                                                                              |
|                                          | AEGP_InterpretationStyle              interp_style,                                                                                                                     |
|                                          | void                                  *reserved,                                                                                                                        |
|                                          | AEGP_FootageH                         *footagePH);                                                                                                                      |
|                                          |                                                                                                                                                                         |
|                                          | Note the optional params. If ``allow_interpretation_dialogB`` is `FALSE`, After Effects will guess the alpha interpretation.                                            |
|                                          |                                                                                                                                                                         |
|                                          | ::                                                                                                                                                                      |
|                                          |                                                                                                                                                                         |
|                                          | typedef struct {                                                                                                                                                        |
|                                          | A_long               layer_idL;                                                                                                                                         |
|                                          | A_long               layer_indexL                                                                                                                                       |
|                                          | char                 *nameAC;                                                                                                                                           |
|                                          | AEGP_LayerDrawStyle  draw_style;                                                                                                                                        |
|                                          | } AEGP_FootageLayerKey;                                                                                                                                                 |
|                                          |                                                                                                                                                                         |
|                                          | ``AEGP_LayerDrawStyle`` can be:                                                                                                                                         |
|                                          |                                                                                                                                                                         |
|                                          | - ``AEGP_LayerDrawStyle_LAYER_BOUNDS``                                                                                                                                  |
|                                          | - ``AEGP_LayerDrawStyle_DOCUMENT_BOUNDS``                                                                                                                               |
|                                          |                                                                                                                                                                         |
|                                          | ``AEGP_InterpretationStyle`` can be:                                                                                                                                    |
|                                          |                                                                                                                                                                         |
|                                          | - ``AEGP_InterpretationStyle_NO_DIALOG_GUESS`` Will guess alpha interpretation even if file contains unknown alpha interpretation and user pref says to ask user.       |
|                                          | - ``AEGP_InterpretationStyle_DIALOG_OK`` Optionally can show a dialog.                                                                                                  |
|                                          | - ``AEGP_InterpretationStyle_NO_DIALOG_NO_GUESS`` Used for replace footage implementation.                                                                              |
+------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_AddFootageToProject``             | Adds a footage item to a project. Footage will be adopted by the project, and may be added only once.                                                                   |
|                                          | This is Undo-able; do not dispose of the returned added item if it’s undone.                                                                                            |
|                                          |                                                                                                                                                                         |
|                                          | ::                                                                                                                                                                      |
|                                          |                                                                                                                                                                         |
|                                          | AEGP_AddFootageToProject(                                                                                                                                               |
|                                          | AEGP_FootageH  footageH,                                                                                                                                                |
|                                          | AEGP_ItemH     folderH,                                                                                                                                                 |
|                                          | AEGP_ItemH     *add_itemPH0);                                                                                                                                           |
+------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetItemProxyFootage``             | Sets footage as the proxy for an item. Will be adopted by the project.                                                                                                  |
|                                          | This is Undo-able; do not dispose of the returned added item if it’s undone.                                                                                            |
|                                          |                                                                                                                                                                         |
|                                          | ::                                                                                                                                                                      |
|                                          |                                                                                                                                                                         |
|                                          | AEGP_SetItemProxyFootage(                                                                                                                                               |
|                                          | AEGP_FootageH  footageH,                                                                                                                                                |
|                                          | AEGP_ItemH     itemH);                                                                                                                                                  |
+------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_ReplaceItemMainFootage``          | Replaces footage for an item. The item will replace the main footage for this item.                                                                                     |
|                                          | This is Undo-able; do not dispose of the returned added item if it’s undone.                                                                                            |
|                                          |                                                                                                                                                                         |
|                                          | ::                                                                                                                                                                      |
|                                          |                                                                                                                                                                         |
|                                          | AEGP_ReplaceItemMainFootage(                                                                                                                                            |
|                                          | AEGP_FootageH  footageH,                                                                                                                                                |
|                                          | AEGP_ItemH     itemH);                                                                                                                                                  |
+------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_DisposeFootage``                  | Deletes a footage item. Do not dispose of footage you did not create, or that has been added to the project.                                                            |
|                                          |                                                                                                                                                                         |
|                                          | ::                                                                                                                                                                      |
|                                          |                                                                                                                                                                         |
|                                          | AEGP_DisposeFootage(                                                                                                                                                    |
|                                          | AEGP_FootageH  footageH);                                                                                                                                               |
+------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetFootageInterpretation``        | Populates an AEGP_FootageInterp describing the settings of the ``AEGP_FootageH``.                                                                                       |
|                                          | There is no way to create a valid ``AEGP_FootageInterp`` other than by using this function.                                                                             |
|                                          |                                                                                                                                                                         |
|                                          | ::                                                                                                                                                                      |
|                                          |                                                                                                                                                                         |
|                                          | AEGP_GetFootageInterpretation(                                                                                                                                          |
|                                          | const AEGP_ItemH    itemH,                                                                                                                                              |
|                                          | A_Boolean           proxyB,                                                                                                                                             |
|                                          | AEGP_FootageInterp  *interpP);                                                                                                                                          |
|                                          |                                                                                                                                                                         |
|                                          | If proxyB is ``TRUE``, the proxy footage’s settings are retrieved.                                                                                                      |
+------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetFootageInterpretation``        | Apply the settings in the ``AEGP_FootageInterp`` to the ``AEGP_FootageH``. Undo-able.                                                                                   |
|                                          |                                                                                                                                                                         |
|                                          | ::                                                                                                                                                                      |
|                                          |                                                                                                                                                                         |
|                                          | AEGP_SetFootageInterpreta tion(                                                                                                                                         |
|                                          | const AEGP_ItemH          itemH,                                                                                                                                        |
|                                          | A_Boolean                 proxyB,                                                                                                                                       |
|                                          | const AEGP_FootageInterp  *interpP);                                                                                                                                    |
|                                          |                                                                                                                                                                         |
|                                          | If ``proxyB`` is ``TRUE``, the proxy footage’s settings are modified.                                                                                                   |
+------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetFootageLayerKey``              | Populates an ``AEGP_FootageLayerKey`` describing the footage.                                                                                                           |
|                                          |                                                                                                                                                                         |
|                                          | ::                                                                                                                                                                      |
|                                          |                                                                                                                                                                         |
|                                          | AEGP_GetFootageLayerKey(                                                                                                                                                |
|                                          | AEGP_FootageH         footageH,                                                                                                                                         |
|                                          | AEGP_FootageLayerKey* layerKeyP);                                                                                                                                       |
+------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_NewPlaceholderFootage``           | Deprecated. Adds a new placeholder footage item to the project.                                                                                                         |
|                                          | Using this function for missing footage will cause the user to search for each individual missing file, regardless of whether or not they’re all in the same directory. |
|                                          | Undo-able.                                                                                                                                                              |
|                                          |                                                                                                                                                                         |
|                                          | ::                                                                                                                                                                      |
|                                          |                                                                                                                                                                         |
|                                          | AEGP_NewPlaceholderFootage(                                                                                                                                             |
|                                          | AEGP_PluginID  plugin_id,                                                                                                                                               |
|                                          | const A_char   *nameZ,                                                                                                                                                  |
|                                          | A_long         width,                                                                                                                                                   |
|                                          | A_long         height,                                                                                                                                                  |
|                                          | const A_Time   *durationPT,                                                                                                                                             |
|                                          | AEGP_FootageH  *footagePH);                                                                                                                                             |
+------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_NewPlaceholderFootageWithPath``   | This is the hip new way to add references to footage that can’t be found right this moment.                                                                             |
|                                          | The file path is a NULL-terminated UTF-16 string with platform separators.                                                                                              |
|                                          |                                                                                                                                                                         |
|                                          | In CS6 and earlier, file_type was ignored and we previously recommendedsetting it to ``AEIO_FileType_NONE``.                                                            |
|                                          | Starting in CC, ``AEIO_FileType_NONE`` is now a warning condition.                                                                                                      |
|                                          | If you pass ``AEIO_FileType_ANY``, then path MUST exist.                                                                                                                |
|                                          |                                                                                                                                                                         |
|                                          | If the path may not exist, pass ``AEIO_FileType_DIR`` for folder, or ``AEIO_FileType_GENERIC`` for a file.                                                              |
|                                          |                                                                                                                                                                         |
|                                          | ::                                                                                                                                                                      |
|                                          |                                                                                                                                                                         |
|                                          | AEGP_NewPlaceholderFootageWithPath(                                                                                                                                     |
|                                          | AEGP_PluginID      plugin_id,                                                                                                                                           |
|                                          | const A_UTF16Char  *pathZ,                                                                                                                                              |
|                                          | AEGP_Platform      path_platform,                                                                                                                                       |
|                                          | AEIO_FileType      file_type,                                                                                                                                           |
|                                          | A_long             widthL,                                                                                                                                              |
|                                          | A_long             heightL,                                                                                                                                             |
|                                          | const A_Time       *durationPT,                                                                                                                                         |
|                                          | AEGP_FootageH      *footagePH);                                                                                                                                         |
+------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_NewSolidFootage``                 | This is the way to add a solid.                                                                                                                                         |
|                                          | Until the footage is added to the project, the caller owns the ``AEGP_FootageH``                                                                                        |
|                                          | (and must dispose of it if, and only if, it isn’t added to the project).                                                                                                |
|                                          |                                                                                                                                                                         |
|                                          | ::                                                                                                                                                                      |
|                                          |                                                                                                                                                                         |
|                                          | AEGP_NewSolidFootage(                                                                                                                                                   |
|                                          | const A_char         *nameZ,                                                                                                                                            |
|                                          | A_long               width,                                                                                                                                             |
|                                          | A_long               height,                                                                                                                                            |
|                                          | const AEGP_ColorVal  *colorP,                                                                                                                                           |
|                                          | AEGP_FootageH        *footagePH);                                                                                                                                       |
+------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetSolidFootageColor``            | Returns the color of a given solid. Returns an error if the ``AEGP_ItemH`` is not a solid.                                                                              |
|                                          |                                                                                                                                                                         |
|                                          | ::                                                                                                                                                                      |
|                                          |                                                                                                                                                                         |
|                                          | AEGP_GetSolidFootageColor(                                                                                                                                              |
|                                          | AEGP_ItemH     itemH,                                                                                                                                                   |
|                                          | A_Boolean      proxyB,                                                                                                                                                  |
|                                          | AEGP_ColorVal  *colorP);                                                                                                                                                |
|                                          |                                                                                                                                                                         |
|                                          | If ``proxyB`` is ``TRUE``, the proxy solid’s color is retrieved.                                                                                                        |
+------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetSolidFootageColor``            | Sets the color of a solid. Undo-able.                                                                                                                                   |
|                                          |                                                                                                                                                                         |
|                                          | ::                                                                                                                                                                      |
|                                          |                                                                                                                                                                         |
|                                          | AEGP_SetSolidFootageColor(                                                                                                                                              |
|                                          | AEGP_ItemH     itemH,                                                                                                                                                   |
|                                          | A_Boolean      proxyB,                                                                                                                                                  |
|                                          | AEGP_ColorVal  *colorP);                                                                                                                                                |
|                                          |                                                                                                                                                                         |
|                                          | If ``proxyB`` is ``TRUE``, the proxy solid’s color is set.                                                                                                              |
+------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_SetSolidFootageDimensions``       | Sets the dimensions of a solid. Undo-able.                                                                                                                              |
|                                          |                                                                                                                                                                         |
|                                          | ::                                                                                                                                                                      |
|                                          |                                                                                                                                                                         |
|                                          | AEGP_SetSolidFootageDimensions(                                                                                                                                         |
|                                          | AEGP_ItemH  itemH,                                                                                                                                                      |
|                                          | A_Boolean   proxyB,                                                                                                                                                     |
|                                          | A_long      widthL,                                                                                                                                                     |
|                                          | A_long      heightL);                                                                                                                                                   |
|                                          |                                                                                                                                                                         |
|                                          | If ``proxyB`` is ``TRUE``, the proxy solid’s dimensions are modified. Returns an error if the item isn’t a solid.                                                       |
+------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetFootageSoundDataFormat``       | Retrieves information about the audio data in the footage item (by populating the ``AEGP_SoundDataFormat`` you passed in).                                              |
|                                          |                                                                                                                                                                         |
|                                          | ::                                                                                                                                                                      |
|                                          |                                                                                                                                                                         |
|                                          | AEGP_GetFootageSoundDataFormat(                                                                                                                                         |
|                                          | AEGP_FootageH footageH,                                                                                                                                                 |
|                                          | AEGP_SoundDataFormat *formatP);                                                                                                                                         |
+------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``AEGP_GetFootageSequenceImportOptions`` | Populates and returns a ``AEGP_FileSequenceImportOptions`` describing the given ``AEGP_FootageH``.                                                                      |
|                                          |                                                                                                                                                                         |
|                                          | ::                                                                                                                                                                      |
|                                          |                                                                                                                                                                         |
|                                          | AEGP_GetFootageSequenceImportOptions(                                                                                                                                   |
|                                          | AEGP_FootageH footageH,                                                                                                                                                 |
|                                          | AEGP_FileSequenceImportOptions *optionsP);                                                                                                                              |
+------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

AEGP_FootageInterp
********************************************************************************

