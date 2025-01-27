# Compatibility Across Multiple Versions?

Generally, you should compile your plug-ins with the latest After Effects SDK headers. This makes the latest suites and API functionality available to your plug-ins. When a new version of After Effects is released, you generally will not need to provide a new version unless you wish to take advantage of new functionality exposed through the new SDK. However, you should always test your plug-in in new versions of After Effects before claiming compatibility with new versions.

You should test your plug-in thoroughly in each version of After Effects supported by your plug-in. If you need to add a conditional block of code to be run only in specific versions of After Effects, you can always check the API version in [PF_InData](../effect-basics/PF_InData.md).version for effects, or in the major and minor_versionL passed into your AEGP in the [Entry Point](../aegps/implementation.md#entry-point).

For even more precise version checking, a plug-in can run a script using `AEGP_ExecuteScript` ([AEGP_UtilitySuite6](../aegps/aegp-suites.md#aegp_utilitysuite6)), querying one of the following attributes:

```cpp
app.version - e.g. 11.0.1x12
app.buildNumber - e.g. 12.
```

---

## API Versions

|        Release         |                                              Effect API Version                                              | AEGP API Version |
| ---------------------- | ------------------------------------------------------------------------------------------------------------ | ---------------- |
| 22.0                   | 13.27                                                                                                        |                  |
| 18.2                   | 13.25                                                                                                        |                  |
| 18.0                   | 13.24                                                                                                        |                  |
| 17.7                   | 13.23                                                                                                        |                  |
| 17.6                   | 13.22                                                                                                        |                  |
| 17.5                   | 13.21                                                                                                        |                  |
| 17.3                   | 13.20                                                                                                        |                  |
| 17.1                   | 13.19                                                                                                        |                  |
| 17.0                   | 13.18                                                                                                        |                  |
| 16.1                   | 13.17                                                                                                        |                  |
| 16.0                   | 13.16                                                                                                        |                  |
| 15.0                   | 13.15                                                                                                        |                  |
| CC 2017.1 (14.2)       | 13.14                                                                                                        |                  |
| CC 2017 (14.0)         | 13.13                                                                                                        | 114.0            |
| CC 2015.3 (13.8)       | 13.11                                                                                                        | 113.8            |
| CC 2015 (13.7)         | 13.10                                                                                                        | 113.7            |
| CC 2015 (13.6)         | 13.10                                                                                                        |                  |
| CC 2015 (13.5, 13.5.1) | 13.9                                                                                                         | 113.5            |
| CC 2014 (13.0-13.2)    | 13.7                                                                                                         | 113              |
| CC (12.2)              | 13.6                                                                                                         | 112.2            |
| CC (12.1)              | 13.5                                                                                                         | 112.1            |
| CC (12.0)              | 13.4                                                                                                         | 112.0            |
| CS6.0.1 (11.0.1)       | 13.3                                                                                                         | 111.0            |
| CS6 (11.0)             | 13.2                                                                                                         | 111.0            |
| CS5.5 (10.5)           | 13.1                                                                                                         | 17.0             |
| CS5 (10.0)             | 13.0                                                                                                         | 17.0             |
| CS4 (9.0)              | 12.14                                                                                                        | 16.24            |
| CS3 (8.0)              | 12.13                                                                                                        | 16.24            |
| 7.0                    | 12.12                                                                                                        |                  |
| 6.5, 6.0               | 12.10 (Check for the presence of updated AEGP suites, should you need to differentiate between 6.0 and 6.5.) |                  |
| 5.0                    | 12.5                                                                                                         |                  |
| 4.1                    | 12.2                                                                                                         |                  |
| 3.1                    | 11.6                                                                                                         |                  |
