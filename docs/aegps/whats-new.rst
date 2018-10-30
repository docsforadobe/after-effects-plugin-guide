.. _aegps/whats-new:

What's New?
################################################################################

For what's new in CC releases, see :ref:`intro/whats-new`.

----

What's New In CS6?
================================================================================

3D is a major theme of AE CS6. A new ``AEGP_LayerFlag_ENVIRONMENT_LAYER`` has been added. Many new :ref:`layer streams <aegps/aegp-suites.AEGP_StreamSuite5>` were added.

Additionally, ``AEGP_LayerStream_SPECULAR_COEFF`` was renamed to ``AEGP_LayerStream_SPECULAR_INTENSITY``, ``AEGP_LayerStream_SHININESS_COEFF`` was renamed to ``AEGP_LayerStream_SPECULAR_SHININESS``, and ``AEGP_LayerStream_METAL_COEFF`` was renamed to just ``AEGP_LayerStream_METAL``.

A new suite, :ref:`aegps/aegp-suites.AEGP_RenderQueueMonitorSuite1`, provides all the info a render queue manager needs to figure out what is happening at any point in a render.

:ref:`Mask Suite <aegps/aegp-suites.mask-suite>` is now at version 6, and provides functions to get and set the mask feather falloff type. :ref:`Mask Outline Suite <aegps/aegp-suites.mask-outline-suite>` is now at version 3, and provides access to get and set mask outline feather information.

:ref:`Composition Suite <aegps/aegp-suites.composition-suite>` is now at version 9. ``AEGP_CreateTextLayerInComp`` and ``AEGP_CreateBoxTextLayerInComp`` now have a new parameter, ``select_new_layerB``.

:ref:`Render Suite <aegps/aegp-suites.render-suite>` is now at version 3, adding a new function to get the GUID for a render receipt.

Finally, we have added two new read-only :ref:`Dynamic Stream Suite <aegps/aegp-suites.dynamic-stream-suite>` flags: ``AEGP_DynStreamFlag_SHOWN_WHEN_EMPTY`` and ``AEGP_DynStreamFlag_SKIP_REVEAL_WHEN_UNHIDDEN``.

