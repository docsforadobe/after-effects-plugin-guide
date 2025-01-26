<a id="effect-ui-events-tips-and-tricks"></a>

# Tips & Tricks

## UI Performance

Experiment with `PF_EO_ALWAYS_UPDATE` and `PF_EO_NEVER_UPDATE` from [PF_EventExtra](PF_EventExtra.md#effect-ui-events-pf-eventextra), to find a happy medium between responsiveness and accuracy.

On macOS, the foreground and background colors are not set to white and black when custom UI draw events are sent.

This is by design; you don’t have to change the background color when you’re drawing directly into our context.

---

## How Deep Are My Pixels?

There is no way to determine the bit depth of the layer(s) being processed during events.

However, you can cache the last-known pixel depth in your sequence data.

Better still, you can have your fixed and float slider parameters rely on the `PF_ValueDisplayFlags` in their parameter definitions; if you use this, it will have your parameters’ UI respond to the user’s preferences for pixel display values.

You can also check the depth of your input world during `PF_Cmd_RENDER`.

---

## Arbitrary Data

An arbitrary data parameter is an excellent way to manage your custom UI.

Store state, preference, and last-item-used information in an arb, and you’ll always be able to recover it.

After Effects manages parameters with a much richer message stream than custom UIs.

---

## Custom UI Implementation for Color Sampling, Using Keyframes

A plug-in may want to get a color from a layer within a composition. The user would use the eyedropper associated with a color parameter, or the plug-in’s custom composition panel UI, to select the point.

During the click event, the plug-in converts the coordinates of the click into layer space, and stores that information in sequence data. It then forces a re-render, during which it has access to the color of the layer point corresponding to the stored coordinates.

The plug-in stores the color value in sequence data, and cancels the render, requesting a redraw of the affected parameter(s).

Finally, during the draw, the plug-in adds appropriate keyframes to its color parameter stream using the [AEGP_KeyframeSuite](../aegps/aegp-suites.md#aegps-aegp-suites-keyframe-suite).

Yes, this means the effect needs to [Cheating Effect Usage of AEGP Suites](../aegps/cheating-effect-usage-of-aegp-suites.md#aegps-cheating-effect-usage-of-aegp-suites) and use the AEGP API.
