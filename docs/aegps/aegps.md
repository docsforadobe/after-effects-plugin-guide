# AEGPs

The After Effects General Plug-in (AEGP) API is powerful and broad, offering functionality beyond what is available to effect plug-ins.

To users, AEGPs appear to be part of After Effects.

They can add, intercept, and trigger menu commands, access the keyframe database, and register functions as part of After Effects’ internal messaging.

AEGPs can add and remove items to projects and compositions, add and remove filters and keyframes.

Once its command is triggered, AEGPs use the numerous PICA function suites (described in this section) to work with every After Effects item.

AEGPs can publish function suites for plug-ins, manipulate all project elements, change interpretations, replace files and determine which external files are used to render a project.

There are several specialized types of AEGP; Keyframers, Artisans, and I/O modules (AEIOs). They are all still AEGPs, but have access to specialized messaging streams, for which they register with After Effects.
