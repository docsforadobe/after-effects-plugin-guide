.. _effect-details/global-sequence-frame-data:

Global, Sequence, & Frame Data
################################################################################

After Effects allows plug-ins to store data at three scopes; global, sequence, and frame. Consider carefully where you store information; choosing poorly can impact performance, or make your plug-in confusing to the user.

Use global data for information common to all instances of the effect: static variables and data, bitmaps, pointers to other DLLs or external applications.

Store anything specific to this instance of your plug-in (UI settings, text strings, and any custom data not stored in parameters) in sequence data, Use After Effects’ memory allocation functions.

Frame data is used for information specific to rendering a given frame. This has fallen into disuse, as most machines are capable of loading an entire frame into memory at a time. Of course, your IMAX-generating users will still appreciate any optimizations you can make.

----

Persistence
================================================================================

After Effects saves sequence data in the project file, but not global or frame data. Pointers within sequence data which point to external data are, in all likelihood, invalid upon re- opening the project, and must be re-connected. We call this process "flattening" and "un- flattening" the sequence data.

----

Validating Sequence Data
================================================================================

Careful sequence data validation is important for effects that do simulation across time, where frame N is dependent on frame N-1, and you use a cache of calculated data in your sequence data. If a parameter is changed, certain calculated data may no longer be valid, but it would also be wasteful to blindly recalculate everything after every change.

When asked to render frame N, assuming you have your cached data calculated up to frame N-1, call `PF_GetCurrentState() <#_bookmark322>`__ / `PF_AreStatesIdentical() <#_bookmark324>`__ to see if the cache of calculated data is still valid given the current parameter settings. The state of all parameters (except those with `PF_ParamFlag_EXCLUDE_FROM_HAVE_INPUTS_CHANGED <#_bookmark228>`__ set), including layer parameters (including `param[0] <#_bookmark214>`__) are checked over the passed time span.

This is done efficiently, as the change tracking is done with timestamps.

If the inputs have not changed, you can safely use your cache, AND the internal caching system will assume that you have a temporal dependency on the passed range. So if something changes upstream, the host’s caches will be properly invalidated automatically.

To test that it is working, apply your effect with one parameter keyframed on every frame. RAM Preview to fill the cache, then change one of the keyframes. The related frame and all dependent frames (e.g. later frames, in the case of a simulation) should lose their cache marks and require re-rendering. Similarly, upstream changes to sources of layer parameters should cause time-selective invalidation of the cache.

----

Flattened And Unflattened Sequence Data
================================================================================

If your sequence data references external memory (in pointers or handles), you must flatten and unflatten your data for disk-safe storage. This is analogous to creating your own miniature file format.

Upon receiving `PF_Cmd_SEQUENCE_FLATTEN <#_bookmark90>`__\ *,* put data referenced by pointers into one contiguous block from which you can later recover the old structure. If your sequence data contains a pointer to a long, allocate 4 bytes in which to store the flattened data. You must handle platform-specific byte ordering.


Remember, your users (the ones who bought two copies of your plug-in, anyway) may want the same project to work on macOS and Windows.

After Effects sends `PF_Cmd_SEQUENCE_RESETUP <#_bookmark88>`__ when the data is reloaded, for either flat or unflat data.

Use a flag at a common offset within both structures to indicate the data’s state.

::

  typedef struct {
    A_char*    messageZ;
    PF_FpLong  big_numF;
    void*      temp_storage;
  } non_flat_data;

  typedef struct {
    char       message[256];
    PF_FpLong  big_numF;
    A_Boolean  big_endianB;
  } flat_data;

----

Resizing Sequence Data
================================================================================

During `PF_Cmd_SEQUENCE_SETUP <#_bookmark86>`__, allocate a handle for data specific to this instance of your effect.

You may modify the contents, but not the size, of the sequence data during any selector.

You may resize the sequence data handle only during the following selectors:

  - ``PF_Cmd_AUDIO_SETUP``
  - ``PF_Cmd_AUDIO_SETDOWN``
  - ``PF_Cmd_FRAME_SETUP``
  - ``PF_Cmd_FRAME_SETDOWN``
  - ``PF_Cmd_AUDIO_RENDER``
  - ``PF_Cmd_RENDER``
  - ``PF_Cmd_SEQUENCE_SETUP`` (duh)
  - ``PF_Cmd_SEQUENCE_SETDOWN``
  - ``PF_Cmd_SEQUENCE_FLATTEN``
  - ``PF_Cmd_SEQUENCE_RESETUP``
  - ``PF_Cmd_DO_DIALOG``
