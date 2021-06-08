.. _effect-details/multi-frame-rendering-in-ae:

===========================
Multi-Frame Rendering in AE
===========================


In order to take advantage of modern hardware with more CPU cores and threads, After Effects (currently in Beta builds) now supports Multi-Frame Rendering. Multi-Frame rendering (MFR) allows multiple frames to be rendered concurrently thereby speeding up rendering and export of AE compositions.

Third-party effects can enable support of Multi-Frame Rendering through the AE Effects SDK by setting the following PF_OutFlag::

  PF_OutFlag2_SUPPORTS_THREADED_RENDERING

This flag indicates the effect supports rendering on multiple threads concurrently. Single or multiple applications of this effect on a layer can be called to render at the same time on multiple threads. Effects must be thread-safe before this flag is set. Please see the :ref:`ts-effect` section below for more information.

.. note::
  When After Effects uses Multi-Frame Rendering, an effect that is not thread-safe and does not set this flag will force each render thread to enter and exit the effect code one thread at a time. This will significantly reduce the performance improvements that MFR provides and as such a warning icon will be shown in the Effects Control Window alongside the effect to warn the user of the performance impact.

----

For effects that require writing to sequence_data during Render, a new flag is now available for backwards compatibility::

  PF_OutFlag2_MUTABLE_RENDER_SEQUENCE_DATA_SLOWER

Each rendering thread will have its own instance of sequence_data that is not shared nor synchronized with other rendering threads. If the data stored in sequence_data is time-consuming to compute, the new :ref:`compute-cache` should be utilized.

.. note::
  Use of the ``PF_OutFlag2_MUTABLE_RENDER_SEQUENCE_DATA_SLOWER`` flag requires compiling against the March 2021 SDK, and the ``AE.NewMultiFrameSequenceDataBehavior`` beta feature flag to be enabled. See the :ref:`sequence-data` section for more information on enabling this flag.

----

Multi-Frame Rendering Effect Updates Required with March 2021 SDK
=================================================================

The March 2021 SDK introduces new ``sequence_data`` behavior that will be enabled as part of the AE beta builds in June 2021, so if you have enabled your effects to support Multi-Frame Rendering using the June 2020 SDK, you will need to recompile the effects against the March 2021 SDK before June 2021 to ensure they continue to support MFR. 

.. note::
  To gain access and test against the new ``sequence_data`` behavior, you will need to enable a beta feature in After Effects. Shift-click the What’s New beaker icon in an After Effects 18.2 beta build and enter ``AE.NewMultiFrameSequenceDataBehavior``. Click Enable and then restart AE.

The table below outlines the changes an effect will need to make:

+-----------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| **MFR & Sequence Data Usage**                                                                                   | **Changes Needed with March 2021 SDK**                                                                                                                                                                                              |
|                                                                                                                 | (with AE.NewMultiFrameSequenceDataBehavior enabled)                                                                                                                                                                                 |
+=================================================================================================================+=====================================================================================================================================================================================================================================+
| Plugin does not set PF_OutFlag2_SUPPORTS_THREADED_RENDERING                                                     | No changes needed. Effect and sequence_data will continue to work as it did in the past.                                                                                                                                            |
+-----------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Plugin sets PF_OutFlag2_SUPPORTS_THREADED_RENDERING but neither reads nor writes to sequence_data during Render | Recompile the plugin with the March 2021 SDK, no other code changes are required.                                                                                                                                                   |
|                                                                                                                 |                                                                                                                                                                                                                                     |
|                                                                                                                 | If the plugin is not compiled with the March 2021 SDK, the plugin will stop utilizing MFR starting in early June 2021.                                                                                                              |
+-----------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Plugin sets PF_OutFlag2_SUPPORTS_THREADED_RENDERING but only reads sequence_data during Render                  | Recompile the plugin with the March 2021 SDK, update reading sequence_data via ``PF_EffectSequenceDataSuite1`` for thread-safe access. See :ref:`effect-details/sequence-data-mfr-suite` for more information.                      |
+-----------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Plugin sets PF_OutFlag2_SUPPORTS_THREADED_RENDERING and reads and writes to sequence_data during Render         | Recompile the plugin with the March 2021 SDK and modify the plugin to:                                                                                                                                                              |
|                                                                                                                 |                                                                                                                                                                                                                                     |
|                                                                                                                 | 1. Utilize the :ref:`effect-details/compute-cache-api` for thread-safe cache access instead of reading/writing to sequence_data directly.  See :ref:`compute-cache` for more information.                                           |
|                                                                                                                 |                                                                                                                                                                                                                                     |
|                                                                                                                 | AND / OR                                                                                                                                                                                                                            |
|                                                                                                                 |                                                                                                                                                                                                                                     |
|                                                                                                                 | 2. Add the ``PF_OutFlag2_MUTABLE_RENDER_SEQUENCE_DATA_SLOWER`` to the effect to restore direct read/write access to sequence_data.                                                                                                  |
|                                                                                                                 |                                                                                                                                                                                                                                     |
+-----------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. note::
  Effects compiled with the March 2021 SDK and using the PF_OutFlag2_SUPPORTS_THREADED_RENDERING flag and, optionally, the PF_OutFlag2_MUTABLE_RENDER_SEQUENCE_DATA_SLOWER flag will work with After Effects beta builds starting with 18.0 when the ``PF_EffectSequeceDataSuite1`` was introduced. Check for the presence of this suite if you need to support both sequence_data behaviors.

----

Some Temporary Implementation Details of Multi-Frame Rendering
==============================================================

1. **Only Render Queue Export is enabled for Multi-Frame Rendering**

  * Preview, Adobe Media Encoder, Motion Graphic Templates and AERender CLI will be fully supported before the full release of MFR to all After Effects users later in 2021.

2. **Multi-Frame Rendering uses a fixed number of render threads for the entire render**

  * The current implementation uses the number of logical CPU cores, available RAM and GPU VRAM to determine the concurrent frames when the render begins. The final implementation of MFR will monitor system utilization and dynamically adjust the concurrent frames during render to optimize rendering for the composition complexity and available hardware resources.

----

Implications to Command Selectors with Multi-Frame Rendering
================================================================================

UI selectors are still sent on the main thread, however ``PF_Cmd_SEQUENCE_SETUP``, ``PF_Cmd_SEQUENCE_RESETUP``, ``PF_Cmd_SEQUENCE_SETDOWN``, ``PF_Cmd_SMART_PRE_RENDER``, ``PF_Cmd_RENDER`` and ``PF_Cmd_SMART_RENDER`` may be sent on multiple threads at the same time as the UI selectors are being handled so all of these selectors must be thread safe.

``PF_Cmd_GLOBAL_SETUP`` and ``PF_Cmd_GLOBAL_SETDOWN`` selectors will only be sent on the main thread and will not be sent at the same time as any other selectors.

----

.. _sequence-data:

Sequence Data in Multi-Frame rendering
================================================================================
The ``sequence_data`` object and related Sequence Selectors have been used over the years to provide a way to store data during the effect’s lifetime. Multi-Frame Rendering introduces some changes to be aware of:

**Changes as of June 2020**

* Multi-Frame rendering requires that After Effects marshal ``sequence_data`` to the render threads. In order to make this efficient for effects with ``sequence_data`` that require flattening with the ``PF_OutFlag_SEQUENCE_DATA_NEEDS_FLATTENING`` flag, these effects must now also set the ``PF_OutFlag2_SUPPORTS_GET_FLATTENED_SEQUENCE_DATA`` flag.


**Changes as of March 2021 when the AE.NewMultiFrameSequenceDataBehavior Beta Feature is Enabled**

* You can enable this new behavior in After Effects beta builds by shift-clicking on the What’s New beaker icon and entering ``AE.NewMultiFrameSequenceDataBehavior``, clicking Enable and then restarting AE.
* The ``sequence_data`` object is now const when read at Render time and should be accessed through the ``PF_EffectSequenceDataSuite`` interface.
* Writing to ``seqeunce_data`` at render time is disabled by default and results will be undefined if ``sequence_data`` is attempted to be written to at render time.
* If an effect must write to sequence_data at render time, it must set the ``PF_OutFlag2_MUTABLE_RENDER_SEQUENCE_DATA_SLOWER`` flag which will tell After Effects to allow writes to ``sequence_data`` but it will be at the expense of performance. The ``sequence_data`` object will be duplicated to each render thread when the render begins, and each render thread will have its own independent copy of ``sequence_data`` to manage for the lifetime of the render. For performance reasons, it is preferred that the :ref:`compute-cache` is utilized for writing any data required by the effect.
* This new behavior will be enabled by default in After Effects beta builds starting in June 2021. 

----

.. _compute-cache:

Compute Cache For Multi-Frame Rendering
================================================================================
The Compute Cache provides a thread-safe cache as a replacement or supplement to Sequence Data where effects can compute, store and read data before or during Render. 

When would you use the Compute Cache?
*********************************************
* You should use the Compute Cache if your effect uses ``sequence_data`` and needs to write to or update ``sequence_data`` during Render, especially if the computation of needed data is time-consuming to calculate. 
* Without the Compute Cache, the effect will need to add the ``PF_OutFlag2_MUTABLE_RENDER_SEQUENCE_DATA_SLOWER`` flag which will create unique copies of sequence_data per render thread. Each render thread may then need to perform the time-consuming calculations independently and won’t be able to share the results between the render threads. 
* By using the Compute Cache, render threads can share the task of computing the data and reap the benefits of already computed data. 
* The Compute Cache API supports both single and multi-checkout computation tasks depending upon the needs of the effect. See the :ref:`effect-details/compute-cache-api` documentation for more information.

How do I enable the Compute Cache?
*********************************************
The Compute Cache API is available starting in the March 2021 SDK and the suite is enabled by default in After Effects beta builds. 

See the :ref:`effect-details/compute-cache-api` documentation for implementation details and sample code.

----

.. _ts-effect:

What does it mean for an effect to be thread-safe?
================================================================================
**An effect is thread-safe when the implementation and shared data is guaranteed to be free of race conditions and is always in a correct state when accessed concurrently.**

To be more specific, the effect:

#.	Has no static or global variables OR, has static or global variables that are free of race conditions.
#.	Does not write to ``in_data->global_data`` at render time. Reading can be done. Write in ``PF_Cmd_GLOBAL_SETUP`` and ``PF_Cmd_GLOBAL_SETDOWN`` only.
#.	Does not write to ``in_data->sequence_data`` at render time or during ``PF_Cmd_UPDATE_PARAMS_UI`` event. Reading can be done via the PF_EffectSequenceDataSuite interface.

.. note::
  If an effect uses any blocking synchronization mechanisms, such as mutexes or gates, these must not be held when calling back into the host. Common calls would be when using a suite or making a checkout call. Failing to do so will very likely result in deadlocks.

----

How to locate the static and global variables in your effects
================================================================================
To help you locate the static and global variables in your effect, we've developed a **Static Analyzer tool** for you use. 
You can find the tool in this Git Repo: https://github.com/adobe/ae-plugin-thread-safety

If you develop on Mac:
  1. Clone/Download the Git Repo at the URL provided above
  2. Find the bash script ``check_symbols_for_thread_safety.sh`` in the **Mac** folder
  3. Navigate inside the package content of a plugin or effect and locate the binary files. (For example, the **Curves.plugin** has its binary file here: ``/Applications/Adobe After Effects [your AE version]/Plug-ins/Effects/Curves.plugin/Contents/MacOS/Curves``)
  4. To analyze the binary, run::

      check_symbols_for_thread_safety.sh [Binary location]
      For example, check_symbols_for_thread_safety.sh /Applications/Adobe After Effects [your AE version]/Plug-ins/Effects/Curves.plugin/Contents/MacOS/Curves)

  5. You will see output from the tool in this format::

      [symbol type]; [symbol name]

  6. ``[symbol type]`` is an one case-sensitive letter that indicates the type of the variable. You can find all the type information here: https://linux.die.net/man/1/nm
  7. Here is an example of the output::

      b; Deform::FindSilEdges()::new_kInfinite

    ``b`` shows this symbol is in the uninitialized data section, which indicates it might be a static variable.
    
    ``Deform::FindSilEdges()::new_kInfinite`` is the symbol name where ``Deform`` is name of the namespace that the variable is located at. 
    
    ``FindSilEdges()`` is the function name the variable is in.
    
    ``new_kInfinite`` is the actual variable name. Namespace and function names might not be shown based on where the variable is.

  8. Search for each symbol in your code, fix it (see :ref:`here <fix-static>` on how) and repeat for every binary file in your solution

|

If you develop on Windows:
  **1. Preparation**
    0. **In order to run this tool, you need a working installation of Visual Studio**
    1. Clone/Download the Git Repo at the URL provided above
    2. Find the ``register_msdia.cmd`` script in the **Win** folder
    3. Search for **"x64 Native Tools Command Prompt for VS...."** from the **Start Menu**
    4. Right click -> Run as an Administrator
    5. In the terminal, ``cd`` to the directory where your ``register_msdia.cmd`` is located at
    6. Run ``.\register_msdia.cmd``
    7. This script will register the **DIA SDK** and some other required dependencies for you
    8. The Static Analyzer should be ready to work

  **2. Using the Windows Static Analyzer**
    1. Find the executable ``CheckThreadSafeSymbols.exe`` in the **Win** folder
    2. Compile your effect in **Debug** mode and find its **.pdb** file
    3. You should also find some **.obj** files in the same build directory if you haven't modified your project build settings
    4. You have **two options** on what to scan through: binaries or source files, using ``-objfile`` or ``-source`` flag.

      * **You can get the same symbols out of either option.** 
      * Use the ``-source`` option if you don’t know exactly what binaries your source code is ending up in, or if you’d like to keep track of thread safety on a per-source-file basis. 
      * Use the ``-objfile`` option if you want more fine-grained control over what parts of your project you’re scanning.

    5. To analyze the symbols in an object file, run::

        CheckThreadSafeSymbols.exe -objfile [absolute path to the binary you want analyzed] [absolute path to .pdb]

    6. To analyze the symbols in a source file, run::

        CheckThreadSafeSymbols.exe -source [absolute path to the source file you want analyzed] [absolute path to .pdb]

    7. Global variables aren't limited to the scope of one file or binary in pdbs, so you'll have to check over the list of all project globals without filtering. Use the -g output to get a list of all of them::
        
        CheckThreadSafeSymbols.exe -g [absolute path to .pdb]

    8. If you're unsure of what binaries your effect is outputting, the tool can also output a **(noisy)** list of binaries, along with the source files each pulls data from. Files you've changed are likely to be near the top. To see the list, run::

        CheckThreadSafeSymbols.exe -sf [absolute path to .pdb]

    9. Output symbols will take the form::

        [Symbol name], [Symbol type], [Datakind], ([Section type of data location], [Binary Address][Binary Address Offset])

    10. Here is an example of the output:

        .. code-block:: c++

          menuBuf, Type: char[0x1000], File Static, (static, [0008FCD0][0003:00001CD0])

      ``menuBuf`` is the actual variable name.

      ``Type: char[0x1000]`` shows what type of the variable it is. The data here is a ``char``.

      ``File Static`` shows what kind of that data it is. The data here is a **File-scoped static variable.** You can find all the data kinds and what they mean on this page https://docs.microsoft.com/en-us/visualstudio/debugger/debug-interface-access/datakind?view=vs-2019

      ``static`` shows that the data is in the static section of the memory.

      ``[0008FCD0][0003:00001CD0]`` shows the Binary Address and the Binary Address offset of the data.


    11. Search for each symbol in your code, fix it (see :ref:`here <fix-static>` on how) and repeat for every binary/source file in your solution


----

.. _fix-static:

What to do if you have static and globals in your effects
================================================================================
When you see a static or global variable, it would be the best to make it a local variable if possible. But what if that variable has to be static or global?

Here are some standard approaches for treating statics or globals:
  **1. Could the data be easily passed between functions instead without a change in behavior?**

    .. code-block:: c++

      // Example of a non Thread-Safe code

      static int should_just_be_local;

      void UseState() {
        DoComputation(should_just_be_local);
      }

      void SetAndUseState() {
        should_just_be_local = DoComputation();
        UseState();
      }

    **Either add it to a struct or expand function arguments to include it**

    .. code-block:: c++

      // We can fix the above code by passing the should_just_be_local variable through function arguments

      void UseState(int should_just_be_local) {
        DoComputation(should_just_be_local);
      }

      void SetAndUseState() {
        int should_just_be_local = DoComputation();
        UseState(should_just_be_local);
      }
    

  **2. Could the data be initialized before you execute your code (e.g. a lookup table, a const variable)?**

    .. code-block:: c++

      // Example of a non Thread-Safe code
      
      // Many places in the code need to read this table but won't be writing to it
      static int state_with_initializer[64]; 

      static bool state_was_initialized = false;

      void InitializeState() {
        for (int i = 0; i < 64; ++i) {
          state_with_initializer[i] = i * i;
        }
        state_was_initialized = true;
      }

      void Main() {
        if (!state_was_initialized) {
          InitializeState();
        }
        DoComputation(state_with_initializer);
      }

    **Make it** ``const`` **or replace it with a macro**

    .. code-block:: c++
      
      std::array<int, 64> InitializeState() {

        std::array<int, 64> temp;
        
        for (int i = 0; i < 64; ++i) {
          temp[i] = i * i;
        }
        return temp;
      }

      // We can fix the above code by making this table a const and initialize it before using it
      static const std::array<int, 64> state_with_initializer = InitializeState();

      void Main() {
        DoComputation(state_with_initializer);
      }

  **3. Is the data initialized once at runtime based on data that doesn't change on subsequent renders?**

    .. code-block:: c++
      
      // Example of a non Thread-Safe code
      static int depends_on_unchanging_runtime_state;

      void UseState() {
        DoComputation(depends_on_unchanging_runtime_state);
      }

      void SetAndUseState() {
        depends_on_unchanging_runtime_state = DoComputationThatNeedsStateOnlyOnce();
        UseState();
      }

    **Double-check that this state isn't known before your code executes (case 2), but if you have to initialize at runtime use a const static local. (Note that thread-safe initialization of static local objects is part of the C++ spec)**

    .. code-block:: c++
      
      void UseState(int depends_on_unchanging_runtime_state) {
		    DoComputation(depends_on_unchanging_runtime_state);
	    }

      void SetAndUseState() {

        // We can fix the above code by making the variable a const static local
        static const int depends_on_unchanging_runtime_state = DoComputationThatNeedsStateOnlyOnce();

        UseState(depends_on_unchanging_runtime_state);
      }

  **4. The data has to stay static/global not being a const. But each render thread can have its own copy of the data.**

    .. code-block:: c++
      
      // This variable has to be static and not a const
      static int this_thread_needs_access;

      void SetState(int new_state) {
        this_thread_needs_access = new_state;
      }

      void UseState() {
        DoComputation(this_thread_needs_access);
      }
    
    **Just make the variable thread_local**

    .. code-block:: c++
      
      // Make this variable a thread_local variable
      thread_local static int this_thread_needs_access;

      void SetState(int new_state) {
        this_thread_needs_access = new_state;
      }

      void UseState() {
        DoComputation(this_thread_needs_access);
      }

  **5. The data has to stay static/global not being a const and each thread needs to read and write from the most up-to-date state. (rare)**

    .. code-block:: c++
      
      // This variable has to be static and not a const
      // It also needs to be shared across several threads
      static int every_thread_needs_latest_state;

      void SetState(int new_state) {
        every_thread_needs_latest_state = new_state;
      }

      void UseState() {
        DoComputation(every_thread_needs_latest_state);
      }

    **In this case, protect access with a mutex.**
    
    .. code-block:: c++
      
      // Add a mutex (lock)
      static std::mutex ex_lock;

      static int every_thread_needs_latest_state;

      void SetState(int new_state) {
        {
          // Protect the access with the mutex (lock)
          std::lock_guard<std::mutex> lock(ex_lock);
          every_thread_needs_latest_state = new_state;
        }
      }

      void UseState() {
        int state_capture;
        {
          // Protect the access with the mutex (lock)
          std::lock_guard<std::mutex> lock(ex_lock);
          state_capture = every_thread_needs_latest_state;
        }
        DoComputation(state_capture);
      }

.. note::
  **The above examples are the common cases we've seen in our effects. You can always come up other methods to treat your statics and globals that best suits your needs.**

----


Setting an Effect as Thread-safe
================================================================================
* Set the ``PF_OutFlag2_SUPPORTS_THREADED_RENDERING`` flag in GlobalSetup to tell After Effects that your effect is Thread-Safe and supports Multi-Frame Rendering. 
* If required, add the ``PF_OutFlag2_MUTABLE_RENDER_SEQUENCE_DATA_SLOWER`` to allow sequence_data to be written at the Render stage.
* Update the ``AE_Effect_Global_OutFlags_2`` magic number. Launch AE with your effect without changing the magic number for the first time, apply your effect and AE will give you the correct number to put in.
* If you are using the ``PF_OutFlag_SEQUENCE_DATA_NEEDS_FLATTENING`` flag, remember to also set the ``PF_OutFlag2_SUPPORTS_GET_FLATTENED_SEQUENCE_DATA`` flag.

----


How to test whether an effect is Thread-Safe
================================================================================

Once you have completed the above steps to make your effect Thread-Safe, you should now be ready to do some testing.

Enable Multi-Frame Rendering in After Effects Beta
********************************************************
* Multi-Frame Rendering is enabled by default in After Effects Beta builds, available via the Creative Cloud Desktop application. 
* To toggle MFR on and off, navigate to Preferences > Memory & Performance > Performance and use the Multi-Frame Rendering (Beta) checkbox.

Test your effect
****************
Once you have completed the above preparation steps, test your effect thoroughly. You should be able to test simple and complex compositions and see performance improvements as the effect utilizes multi-frame rendering.

*	Go through all your existing manual and automated testing plans.
*	Test all the effect parameters and make sure they are working properly.
*	Add in some of the AE effects that have already been made thread-safe as appropriate. See the :ref:`first-party` section.
*	Make sure there are no crashes, hangs, render differences or other unexpected changes when rendering with multi-frame rendering enabled.

----

.. _first-party:

Thread-Safe First Party Effects
================================================================================

Visit https://helpx.adobe.com/after-effects/user-guide.html/after-effects/using/effect-list.ug.html for a full list of MFR supported effects. More are being added every week.
