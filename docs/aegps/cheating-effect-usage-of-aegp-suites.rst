.. _aegps/cheating-effect-usage-of-aegp-suites:

Cheating Effect Usage of AEGP Suites
################################################################################

As soon as we showed developers the initial implementation of AEGP suites, they wanted to "cheat" and use them from within effects. This is certainly possible, but please keep in mind that depending on factors outside the effect API (i.e., any information you get from the AEGP APIs) can lead to trouble. If After Effects thinks an effect has all the information it needs to render, it won't (for example) update its parameters based on changes made through an AEGP function. We're actively working on this dependency issue for future versions, but bear it in mind as you write effects which "masquerade" as AEGPs.

Effects can use some AEGP suites to take advantage of camera and lighting information, as well as the ``AEGP_GetLayerParentComp`` and ``AEGP_GetCompBGColor`` functions, from :ref:`aegps/aegp-suites.AEGP_CompSuite`. This should not be interpreted to mean that effects can use *any* AEGP suite calls. Also, see :ref:`effect-ui-events/effect-ui-events` for more information on effects adding keyframes.

:ref:`AEGP_PFInterfaceSuite <aegps/aegp-suites.pf-interface-suite>` is the starting point. The functions in this suite allow you to retrieve the AEGP_LayerH for the layer to which the effect is applied, and the AEGP_EffectRefH for the instance of your effect. ``AEGP_RegisterWithAEGP`` from :ref:`aegps/aegp-suites.AEGP_UtilitySuite` allows you to get an AEGP_PluginID, which is needed for many AEGP calls.

----

Depending on AEGP Queries
================================================================================

One word: Don't. Effects cannot allow the results of AEGP queries to control what is rendered, without appropriately storing those query results (usually in sequence data), cancelling their own render, and forcing a re-render using the queried information.

This is tricky.

Failure to do so will result in nasty, subtle caching bugs guaranteed to cause hair loss and weight gain.
