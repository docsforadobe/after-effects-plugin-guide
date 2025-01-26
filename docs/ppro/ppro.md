# Premiere Pro & Other Hosts

Adobe Premiere Pro and Adobe Premiere Elements both support the After Effects effect API as described in chapters 2, 3, and 5.

They offer a thorough host implementation, some the key omissions being 3D-related calls (auxiliary channel information, cameras and lights), 16-bit and SmartFX support, and other utility functions provided by After Effects' AEGP API.

Both Premiere Pro and Premiere Elements set `PF_InData>appl_id` to 'PrMr'.

In this chapter, we will describe the AE API support in Premiere Pro, but generally the same support exists in corresponding versions of Premiere Elements.

If you need to distinguish between Premiere Pro and Premiere Elements, you may use the Premiere-specific App Info Suite, available from the [Premiere Pro SDK](http://ppro-plugin-sdk.aenhancers.com) headers.

| Application Versions                     |   PF_InData> version.major |   PF_InData> version.minor |
|----------------------------------------------|--------------------------------|--------------------------------|
| Premiere Pro CC through Premiere Pro CC 2019 |                             13 |                              4 |
| Premiere Pro CS6                             |                             13 |                              2 |
| Premiere Pro CS5.5                           |                             13 |                              1 |
| Premiere Pro CS5, Premiere Elements 9        |                             13 |                              0 |
| Premiere Pro CS4, Premiere Elements 8        |                             12 |                              5 |
| Premiere Pro CS3, Premiere Elements 4 and 7  |                             12 |                              4 |
| Premiere Pro 2.0, Premiere Elements 3        |                             12 |                              3 |
| Premiere Pro 1.5, Premiere Elements 2        |                             12 |                              2 |
| Premiere Pro 1.0, Premiere Elements 1        |                             12 |                              1 |

Note that the versioning used by Premiere Pro and Premiere Elements does not mean that they support the same API features After Effects did at the same version. It is simply meant to distinguish from one version to the next.
