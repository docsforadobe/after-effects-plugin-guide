.. _aegps/whats-new:

What's New?
################################################################################

For what's new in CC releases, see `the heading in the Intro chapter <#_bookmark22>`__.

----

What's New In CS6?
================================================================================

3D is a major theme of AE CS6. A new ``AEGP_LayerFlag_ENVIRONMENT_LAYER`` has been added. Many new `layer streams <#_bookmark627>`__ were added.

Additionally, ``AEGP_LayerStream_SPECULAR_COEFF`` was renamed to ``AEGP_LayerStream_SPECULAR_INTENSITY``, ``AEGP_LayerStream_SHININESS_COEFF`` was renamed to ``AEGP_LayerStream_SPECULAR_SHININESS``, and ``AEGP_LayerStream_METAL_COEFF`` was renamed to just ``AEGP_LayerStream_METAL``.

A new suite, `AEGP_RenderQueueMonitorSuite <#_bookmark709>`__, provides all the info a render queue manager needs to figure out what is happening at any point in a render.

`AEGP Mask Suite <#_bookmark658>`__ is now at version 6, and provides functions to get and set the mask feather falloff type. `AEGP Mask Outline Suite <#_bookmark664>`__ is now at version 3, and provides access to get and set mask outline feather information.

`AEGP Comp Suite <#_bookmark577>`__ is now at version 9. ``AEGP_CreateTextLayerInComp`` and ``AEGP_CreateBoxTextLayerInComp`` now have a new parameter, ``select_new_layerB``.

`AEGP Render Suite <#_bookmark689>`__ is now at version 3, adding a new function to get the GUID for a render receipt.

Finally, we have added two new read-only `Dynamic Stream <#_bookmark642>`__ flags: ``AEGP_DynStreamFlag_SHOWN_WHEN_EMPTY`` and ``AEGP_DynStreamFlag_SKIP_REVEAL_WHEN_UNHIDDEN``.

