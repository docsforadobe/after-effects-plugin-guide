.. _effect-details/accessing-function-suites:

Accessing the After Effects Function Suites
################################################################################

If you are writing C++ code, accessing functions in our PICA function suites is a breeze, using the AEGP_SuiteHandler, which automatically acquires the suite when needed, and disposes of it when done. Just instantiate the handler like so::

  AEGP_SuiteHandler suites(in_data->pica_basicP);

After that, you may make calls to any function in any suite, like so::

  PF_Handle infoH = suites.HandleSuite1()->host_new_handle(sizeof(MyStruct));

If you must use C code, then acquire and release the suites manually using the ``PF_Suite_Helper`` utility files, as demonstrated in the Checkout sample project.

Behind the scenes, these both of these methods acquire PICA function suites using ``AcquireSuite``, a member function of the SPBasicSuite pointed to in `PF_InData <#_bookmark116>`__.

----

Suite Versions
################################################################################

WhizBangSuite1 may provide a Foobar() function which takes two arguments, and WhizBangSuite2>Foobar() may take three. Though each new version of a suite supercedes the old one, feel free to acquire multiple versions of the same suite; we never remove or alter previously shipped suites.

When unsure of the capabilities of the plug-in host (no third party host besides Premiere supports PICA), attempt to acquire the latest version, and "fall back" to previous versions. If functionality you require isn’t available, warn the user, and return an error (or fall back on other behavior when running in more "primitive" plug-in hosts). Note that support for these suites in other hosts of After Effects plug-ins is a maze of twisty caves and passages, all alike.

----

Threading
################################################################################

Unless documented otherwise, assume that any function provided by our suites is not thread-safe. For example, only your plug-in’s main thread should do anything that modifies the user interface.
