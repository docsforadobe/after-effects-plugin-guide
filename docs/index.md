# Introduction

Welcome to the Adobe® After Effects® Software Development Kit!

This is a living document and is constantly being updated and edited. The latest public version of the SDK is available at: [https://www.adobe.io/after-effects/](https://www.adobe.io/after-effects/)

If you need more information, your question may already be answered on the After Effects SDK forum: [After Effects SDK Forum](https://community.adobe.com/t5/after-effects/bd-p/after-effects?page=1&sort=latest_replies&filter=all&topics=label-sdk)

Use the search box there, and post a new question if your question hasn’t already been answered.

---

## About

This document has changed much over the years. Part encyclopedia, part how-to guide, with multiple sedimentary layers of accreted information from more than two decades of API development and refinement.

Yes, there does need to be one source of information about every last niggling detail of the After Effects APIs. However, since no human in their right mind would ever want to *read* such a document, we've tried to keep it involving and interesting.

As opportunity allows, we'll try to include more diagrams, illustrations, and purdy pickshurs explaining API intricacies.

As always, your input is valued and appreciated.

---

## Organization

The [What Can I Do With This SDK?](intro/what-can-i-do.md) provides an overview of the integration possibilities with After Effects. It explains what plug-ins are, and how they work with After Effects. It describes the sample projects, and how to modify them. It explains where to install plug-ins, and what resources they use.

The basics of effect plug-ins are discussed in [Effect Basics](effect-basics/effect-basics.md). This overview provides information on the function parameters passed to and from an effect plug-in's entry point. It describes capability flags, effect parameters, and image buffers.

[Effect Details](effect-details/effect-details.md) dives into the details of developing a complete effect plug-in using the many provided callback functions. It also provides many testing ideas to ensure the plug-in is stabile.

[SmartFX](smartfx/smartfx.md) is the extension to the effect plug-in API to support 32-bit floating point images.

[Effect UI & Events](effect-ui-events/effect-ui-events.md) covers events sent to effect plug-ins, how to incorporate custom user interface elements, parameter supervision, and the reliance of custom data parameter types on Custom UI messaging.

[Audio](audio/audio.md) effects are covered in... [Audio](audio/audio.md).

[AEGPs](aegps/aegps.md) details the After Effects General Plug-in (AEGP) API. Provided callback functions, hooking into internal messaging, manipulating the current contents of open projects and handling menu commands are all covered at length.

[Artisans](artisans/artisans.md) covers specialized plug-in 3D renderer AEGPs.

[AEIOs](aeios/aeios.md), specialized AEGPs which handle file input and output.

[Premiere Pro & Other Hosts](ppro/ppro.md) discusses issues related to compatibility with Premiere Pro and other applications that support a subset of After Effects plug-ins.

---

## Documentation Conventions

Functions, structure names and general C/C++ code are in Courier; MyStruct and MyFunction();

Text in blue is hyperlinked.

Command selectors are italicized; *PF_Cmd_RENDER*.

---

## A Note About Coding Style

Because we use the public APIs for our own plug-ins, our coding guidelines are apparent throughout the SDK. Here's a description of the pseudo-neo-post-Hungarian notation we use. Of course, you're welcome to code however you like. If you feel strongly that we should change our internal coding standards, please post your requests at comp.sys.programmer.better.things.to.do.with.your.time, and we'll carefully consider them before not making any changes.

### Coding Conventions

|                       Type                       |  Suffix  |     Example      |
| ------------------------------------------------ | -------- | ---------------- |
| Handle                                           | `H`      | `fooH`           |
| pointer (to)                                     | `P`      | `fooP`           |
| Boolean                                          | `B`      | `visibleB`       |
| Float                                            | `F`      | `degreesF`       |
| Long                                             | `L`      | `offsetL`        |
| unsigned long                                    | `Lu`     | `countLu`        |
| short                                            | `S`      | `indexS`         |
| char                                             | `C`      | `digitC`         |
| unsigned char                                    | `Cu`     | `redCu`          |
| function pointer                                 | `\_func` | `sample_func`    |
| time value                                       | `T`      | `durationT`      |
| `char*` (NULL-terminated C string)               | `Z`      | `nameZ`          |
| rectangle                                        | `R`      | `boundsR`        |
| fixed rectangle                                  | `FiR`    | `boundsFiR`      |
| float rectangle                                  | `FR`     | `boundsFR`       |
| ratio                                            | `Rt`     | `scale_factorRt` |
| `void*`                                          | `PV`     | `refconPV`       |
| optional parameter (must be passed, can be NULL) | `0`      | `extra_flags0`   |
