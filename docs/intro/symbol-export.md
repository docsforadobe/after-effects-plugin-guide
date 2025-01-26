# Exporting Symbols in Effects

The After Effects team recently became aware of an issue with conflicting symbols that violate the C++ language One Definition Rule (ODR).

In early 2021, the version of the Boost library used by After Effects was upgraded to 1.74. Over the last few months we've identified a number of plugins that are also using Boost but are exporting symbols in such a way that After Effects or the plugin may end up calling the incorrect version of Boost leading to hangs and crashes for users. We also identified a number of the AE SDK samples were setup to export all symbols by default which may have been contributing to the problem, assuming they were used as the starting point for other plugins. These have been fixed as part of the March 2021 SDK.

**The only symbol that After Effects requires to be exported is the entry point of the plugin.**

An example can be found in the SDK samples in entry.h:

```cpp
#ifdef AE_OS_WIN
        #define DllExport   __declspec( dllexport )
#elif defined AE_OS_MAC
        #define DllExport   __attribute__ ((visibility ("default")))
#endif
```

and then this is applied to the entry point function, for example:

```cpp
extern "C" DllExport
PF_Err PluginDataEntryFunction(
  PF_PluginDataPtr inPtr,
  PF_PluginDataCB inPluginDataCallBackPtr,
  SPBasicSuite* inSPBasicSuitePtr,
  const char* inHostName,
  const char* inHostVersion)
{
  PF_Err result = PF_Err_INVALID_CALLBACK;

  result = PF_REGISTER_EFFECT(
    inPtr,
    inPluginDataCallBackPtr,
    "ColorGrid", // Name
    "ADBE ColorGrid", // Match Name
    "Sample Plug-ins", // Category
    AE_RESERVED_INFO); // Reserved Info

  return result;
}
```

---

## Disabling Xcode Symbol Export

To disable symbol export in Xcode:

1. Find the **Apple Clang - Code Generation** section in the **Build** settings for your project.
2. Set the **Symbols Hidden By Default** to **YES**

![Apple Clang Symbols](../_static/appleclang-symbols.png "Apple Clang Symbols")
*Apple Clang Symbols*

For any specific symbols that must be made public, use the `__attribute__((visibility("default")))` in code.

More information can be found in Apple's Xcode documentation [https://help.apple.com/xcode/mac/11.4/#/itcaec37c2a6](https://help.apple.com/xcode/mac/11.4/#/itcaec37c2a6) (excerpt below):

```none
Symbols Hidden by Default (GCC_SYMBOLS_PRIVATE_EXTERN)

When enabled, all symbols are declared private extern unless explicitly marked to be exported using __attribute__((visibility("default"))) in code. If not enabled, all symbols are exported unless explicitly marked as private extern.
```

---

## Disabling Visual Studio Export

By default, builds from Visual Studio automatically disable symbol exports. To export symbols, you must either supply a module definition file or set the \_\_declspec(dllexport) keyword in the functions definition.

More information can be found in Microsoft's Visual Studio documentation [https://docs.microsoft.com/en-us/cpp/build/exporting-from-a-dll?view=msvc-160](https://docs.microsoft.com/en-us/cpp/build/exporting-from-a-dll?view=msvc-160) (excerpt below):

```none
You can export functions from a DLL using two methods:
  1. Create a module definition (.def) file and use the .def file when building the DLL. Use this approach if you want to export functions from your DLL by ordinal rather than by name.
  2. Use the keyword __declspec(dllexport) in the function's definition.

When exporting functions with either method, make sure to use the __stdcall calling convention.
```
