# Memory Allocation

Use After Effects for any memory allocations of significant size. For small allocations, you can use new and delete, but this is the exception, not the rule. In low-memory conditions (such as during RAM preview), it's very important that plug-ins deal gracefully with out-of-memory conditions, and not compete with After Effects for OS memory. By using our memory allocation functions, After Effects can know when to free cached images, to avoid memory swapping. Failing to use our functions for sizable allocations can cause lock-ups, crashes, and tech support calls. Don't do that.

If you're wrapping existing C++ classes, create a base class that implements new and delete for that class and derive from it. To overload the STL, we don't recommend you overload global new and delete. Instead provide an allocator as part of the template definition.

Handles passed to you by After Effects are locked for you before you're called, and unlocked once you return.

---

## PF_HandleSuite1

+------------------------+----------------------------------------------------------------------------------------------------------+----------------------+
|        Function        |                                                 Purpose                                                  |       Replaces       |
+========================+==========================================================================================================+======================+
| `host_new_handle`      | Allocates a new handle.                                                                                  | `PF_NEW_HANDLE`      |
|                        |                                                                                                          |                      |
|                        | <pre lang="cpp">PF_Handle (*host_new_handle)(<br/>  A_HandleSize size);</pre>                            |                      |
+------------------------+----------------------------------------------------------------------------------------------------------+----------------------+
| `host_lock_handle`     | Locks a handle.                                                                                          | `PF_LOCK_HANDLE`     |
|                        |                                                                                                          |                      |
|                        | <pre lang="cpp">void (*host_lock_handle)(<br/>  PF_Handle pf_handle);</pre>                              |                      |
+------------------------+----------------------------------------------------------------------------------------------------------+----------------------+
| `host_unlock_handle`   | Unlocks a handle.                                                                                        | `PF_UNLOCK_HANDLE`   |
|                        |                                                                                                          |                      |
|                        | <pre lang="cpp">void (*host_unlock_handle)(<br/>  PF_Handle pf_handle);</pre>                            |                      |
+------------------------+----------------------------------------------------------------------------------------------------------+----------------------+
| `host_dispose_handle`  | Frees a handle.                                                                                          | `PF_DISPOSE_HANDLE`  |
|                        |                                                                                                          |                      |
|                        | <pre lang="cpp">void (*host_dispose_handle)(<br/>  PF_Handle pf_handle);</pre>                           |                      |
+------------------------+----------------------------------------------------------------------------------------------------------+----------------------+
| `host_get_handle_size` | Returns the size, in bytes, of the reallocatable block whose handle is passed in.                        | `PF_GET_HANDLE_SIZE` |
|                        |                                                                                                          |                      |
|                        | <pre lang="cpp">A_HandleSize (*host_get_handle_size)(<br/>  PF_Handle pf_handle);</pre>                  |                      |
+------------------------+----------------------------------------------------------------------------------------------------------+----------------------+
| `host_resize_handle`   | Resizes a handle.                                                                                        | `PF_RESIZE_HANDLE`   |
|                        |                                                                                                          |                      |
|                        | <pre lang="cpp">PF_Err (*host_resize_handle)(<br/>  A_HandleSize new_sizeL, PF_Handle \*handlePH);</pre> |                      |
+------------------------+----------------------------------------------------------------------------------------------------------+----------------------+
