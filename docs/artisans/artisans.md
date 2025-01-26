<a id="artisans-artisans"></a>

# Artisans

#### NOTE
If you’re considering developing an Artisan, please talk it over with us first.

The Artisan API exposes function hooks necessary for a plug-in to provide rendered output of 3D layers, taking over completely from After Effects (which still handles all rendering of 2D layers). There can be only one Artisan per composition, chosen from within the *Composition Settings* > *Advanced* dialog. Artisans render the 3D environment, asking After Effects for information about each element in the composition. As you might guess, this is a vast and tedious process. This API is not recommended for anyone without a strong need to override After Effects’ 3D rendering.

Artisans may share information with effects written to communicate with them, but effects may not initiate this communication. Many of the suites used by Artisans require a rendering context which is generated only after all effects have been applied to the layer.

---

## Interactive Artisans

These differ from standard artisans in that they handle all layers in a composition (not just those which the user has made 3D), and they will only ever be called for onscreen display, never for rendered final output (the rendering calls “fall through” to the default artisan).
