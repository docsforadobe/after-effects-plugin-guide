.. _effect-details/memory-allocation:

Memory Allocation
################################################################################

Use After Effects for any memory allocations of significant size. For small allocations, you can use new and delete, but this is the exception, not the rule. In low-memory conditions (such as during RAM preview), it's very important that plug-ins deal gracefully with out-of- memory conditions, and not compete with After Effects for OS memory. By using our memory allocation functions, After Effects can know when to free cached images, to avoid memory swapping. Failing to use our functions for sizable allocations can cause lock-ups, crashes, and tech support calls. Don't do that.

If you're wrapping existing C++ classes, create a base class that implements new and delete for that class and derive from it. To overload the STL, we don't recommend you overload global new and delete. Instead provide an allocator as part of the template definition.

Handles passed to you by After Effects are locked for you before you're called, and unlocked once you return.

----

.. _effect-details/memory-allocation.PF_HandleSuite:

PF_HandleSuite1
================================================================================

+--------------------------+--------------------------------------------------------------------------------------------------------------------+
|       **Function**       |                                                    **Purpose**                                                     |
+==========================+====================================================================================================================+
| ``host_new_handle``      | Allocates a new handle. Replaces ``PF_NEW_HANDLE``.                                                                |
|                          |                                                                                                                    |
|                          | ::                                                                                                                 |
|                          |                                                                                                                    |
|                          |   PF_Handle (*host_new_handle)(                                                                                    |
|                          |     A_HandleSize size);                                                                                            |
+--------------------------+--------------------------------------------------------------------------------------------------------------------+
| ``host_lock_handle``     | Locks a handle. Replaces ``PF_LOCK_HANDLE``.                                                                       |
|                          |                                                                                                                    |
|                          | ::                                                                                                                 |
|                          |                                                                                                                    |
|                          |   void (*host_lock_handle)(                                                                                        |
|                          |     PF_Handle pf_handle);                                                                                          |
+--------------------------+--------------------------------------------------------------------------------------------------------------------+
| ``host_unlock_handle``   | Unlocks a handle. Replaces ``PF_UNLOCK_HANDLE``.                                                                   |
|                          |                                                                                                                    |
|                          | ::                                                                                                                 |
|                          |                                                                                                                    |
|                          |   void (*host_unlock_handle)(                                                                                      |
|                          |     PF_Handle pf_handle);                                                                                          |
+--------------------------+--------------------------------------------------------------------------------------------------------------------+
| ``host_dispose_handle``  | Frees a handle. Replaces ``PF_DISPOSE_HANDLE``.                                                                    |
|                          |                                                                                                                    |
|                          | ::                                                                                                                 |
|                          |                                                                                                                    |
|                          |   void (*host_dispose_handle)(                                                                                     |
|                          |     PF_Handle pf_handle);                                                                                          |
+--------------------------+--------------------------------------------------------------------------------------------------------------------+
| ``host_get_handle_size`` | Returns the size, in bytes, of the reallocatable block whose handle is passed in. Replaces ``PF_GET_HANDLE_SIZE``. |
|                          |                                                                                                                    |
|                          | ::                                                                                                                 |
|                          |                                                                                                                    |
|                          |   A_HandleSize (*host_get_handle_size)(                                                                            |
|                          |     PF_Handle pf_handle);                                                                                          |
+--------------------------+--------------------------------------------------------------------------------------------------------------------+
| ``host_resize_handle``   | Resizes a handle. Replaces ``PF_RESIZE_HANDLE``.                                                                   |
|                          |                                                                                                                    |
|                          | ::                                                                                                                 |
|                          |                                                                                                                    |
|                          |   PF_Err (*host_resize_handle)(                                                                                    |
|                          |     A_HandleSize new_sizeL, PF_Handle *handlePH);                                                                  |
+--------------------------+--------------------------------------------------------------------------------------------------------------------+
