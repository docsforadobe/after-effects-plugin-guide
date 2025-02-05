# Tips & Tricks

## Best Practices

If your prototypes are anything like ours, the first version of your plug-in that runs without crashing differs radically from the version that actually ships.

How your plug-in responds to things like downsampling, errors and exceptions, pixel aspect ratio, out-of-memory situations, and being interrupted while processing determines how usable it is (and how many support requests you'll have to handle).

---

## Responsiveness

Make your plug-ins as responsive as possible using `PF_ABORT()` and `PF_PROGRESS()` from [Interaction Callbacks](interaction-callback-functions.md#interaction-callbacks).

We actually test all our effects for interrupt-ability; you'd be surprised how cranky users can get waiting for your pokey effect to finish processing a film resolution sequence!

After Effects' iteration functions inherently provide this functionality; you don't need to worry about calling the above functions from within your pixel processing functions.

---

## Make Your Effect Easy To Find

It's possible to have your effect show up in the "Effects & Presets" palette when users search for something other than the plug-in's name.

Apply your effect (leaving the settings at default, unless you're very certain the user will want something different when they search for the given term), and select "Save selection as animation preset" from the effect controls palette.

Save it to the name by which you want users to find the plug-in.

Have your plug-in's installer put the resultant .ffx file into the \\Presets directory, next to the After Effects executable.

Your preset will show up when users search for the name to which it was saved.

---

## Sampling Pixels At (x,y)

Sometimes, instead of just processing every pixel, you'll want to get to a specific offset within the input frame. Below is one way to sample the pixel at a given (x,y) location; similar code could be used to write to the given location.

```cpp
PF_Pixel *sampleIntegral32(PF_EffectWorld &def, int x, int y){
    return (PF_Pixel*)((char*)def.data +
        (y * def.rowbytes) +
        (x * sizeof(PF_Pixel)));
}

PF_Pixel16 *sampleIntegral64(PF_EffectWorld &def, int x, int y){
    assert(PF_WORLD_IS_DEEP(&def));
    return (PF_Pixel16*)((char*)def.data +
        (y * def.rowbytes) +
        (x * sizeof(PF_Pixel16)));
}
```

Special thanks to Paul Miller for answering this question.

---

## Where's The Center Of A Pixel?

Deeeeeep, man. After Effects rotates around the upper left corner of the upper left pixel when the anchor point (see User Documentation) is (0,0).

However, the subpixel sample and area sample callbacks actually treat (.0, .0) as a direct hit. To compensate for this, subtract 0.5 from x and y values before calling those functions.

The matrix functions (`transform_world` from [PF_WorldTransformSuite1](graphics-utility-suites.md#pf_worldtransformsuite1)) don't have this problem.

When translating an image by a subpixel amount, make the output layer one pixel wider than its input, and leave the origin at (0,0).

---

## Text Layer Origin

Almost all layer types have their origin in the upper-left corner. Not so with text layers!

A text layer origin by default is at the bottom-left baseline position of the first character. You can see this if you create a text item and then pick the layer so the anchor point shows up.

Look at where the default anchor point location is. The transform is not at the corner of the layer rectangle.

---

## Clean Slate

You don't necessarily begin effect processing with a clean output slate. Our Gaussian blur filter, in an effort to do so, performs the following before rendering:

```cpp
src_rect.left   = in_data>output_origin_x;
src_rect.right  = src_rect.left + input>width;
src_rect.top    = in_data>output_origin_y;
src_rect.bottom = src_rect.top + input>height;

err = PF_FILL(NULL, NULL, output);

if (!err) {
    err = PF_COPY(&params[0]>u.ld, output, NULL, &src_rect);
}
```

---

## Caching Behavior

After Effects provides numerous ways to specify caching behavior. `PF_OutFlag_NON_PARAM_VARY`, `PF_OutFlag_WIDE_TIME_INPUT`, `PF_OutFlag_I_USE_SHUTTER_ANGLE`, `PF_OutFlag_I_SYNTHESIZE_AUDIO`, `PF_OutFlag2_I_USE_3D_CAMERA`, and `PF_OutFlag2_I_USE_3D_LIGHTS` (all from [PF_OutFlags](../effect-basics/PF_OutData.md#pf_outflags)) all influence caching decisions.

Supporting [dynamic outflags](../effect-basics/PF_OutData.md#pf_outflags) can greatly improve performance, preventing After Effects from invalidating your effect's cache as aggressively as it otherwise would.

Confirm that your plug-in performs well with different After Effects cache settings. Does your plug-in get called to update as often as expected, or does After Effects think it has valid pixels when you think it doesn't?

---

## Global Performance Cache Consideratons

With the new caching in CS6, you may need to clear cached frames after changing your effect's rendering, so that frames rendered and stored in the cache prior to the change will not be reused. To do so manually during development:

1. In Preferences > Media & Disk Cache, disable the Disk Cache
2. Click "Empty Disk Cache" just to be sure (disabling the Disk Cache in step 1 only disables the *writing* of disk cache, not necessarily the usage)
3. Relaunch

If you ever encounter a glitch, it likely a legitimate bug in your effect, such as improper rectangle handling in SmartFX.

On the other hand, if you fix a rendering bug in your plug-in and ship an update, you can't expect all users will empty their disk caches. A user may have a disk cache of the buggy frame and it needs to be invalidated. What to do? Update your plug-in's effect version. This value (and the AE build number) is part of the cache key, so if you update it any frames cached containing content from your plug-in will no longer match.

---

## Some Thoughts On Time From A Long-Time Developer

Stoney Ballard put together the following summary of how time works with effects; you may find it helpful.

There are five `in_data` parameters that describe time to a filter:

- `current_time`
- `time_step`
- `local_time_step`
- `total_time`
- `time_scale`

Their values are dependent on:

- The frame being rendered
- The duration of the layer and composition The frame rate of the comp
- Any Time Stretch Any Time Remapping
- The time behavior of an outer composition (one enclosing the composition with the layer being filtered)
- The setting of the "Preserve frame rate when nested or in render queue" (PFR) switch

The frame being rendered affects current_time. It is expressed in the local (layer) time system. If the PFR switch is off, current_time may be any non-negative value. If on, it will be restricted to a multiple of time_step and local_time_step. Layer duration affects only total_time. Comp duration is a factor only when Time Remapping (TR) is on. In that case, total_time is the larger of layer duration and composition duration. Composition frame rate affects only the time_scale. Time Stretch affects only time_step and local_time_step. If the time stretch is negative, these values are negative. Even if the layer's duration (as seen in

the comp) changes, total_time remains unaffected. This works as if Time Stretch was *above* a filter, but *below* an outer comp. PFR does not alter the effect of Time Stretch. Time Stretch is different than an outer comp, since it affects both step params equally, while an outer comp affects only time_step.

Time Remapping happens *below* the filter, so that it does not affect the time params other than the total_time. When TR is on, the layer is lengthened to the same as the comp (but never shortened), regardless of how much time it actually takes, or where in the comp the layer is. This may cause total_time to be larger. It has nothing to do with the actual time map, just whether or not it's enabled.

The biggest variation comes from being nested in an outer comp, unless PFR is on. When PFR is on, a filter is completely isolated from time variations in an outer comp. Of course, current_time will not necessarily move in increments of time_step in that case. It may skip frames or go backwards.

When PFR is off, local_time_step, total_time, and time_scale remain set to what they were for the inner comp, but time_step contains the time to the next frame in the outer comp, expressed in the local time system. This may be any value, including 0. This can be interpreted as an instantaneous time rate, rather than a duration. A 0 value can last for an arbitrary number of rendered frames, but the current_time won't change on the local layer.

Looked at from the other direction:

current_time is quantized to time_step intervals unless rendering an outer comp with PFR off for the inner comp. This is the current time in the layer, not in any comp.

The value of local_time_step is affected only by Time Stretch. It can never be zero, but it can be negative.

time_step and local_time_step are always the same value unless rendering an outer comp with PFR off. time_step is also affected by the time behavior of an outer comp (with PFR off). It can have any value, positive, negative, or zero, and can be different for every frame (of the outer comp). time_step can be used to determine the duration of the current frame (with PFR off).

total_time is the duration of the layer, unless Time Remapping is on, which makes it the larger of the layer duration and the duration of the comp.

time_scale is the scale such that total_time / time_scale is the layer duration in seconds in its comp. It is affected only by the comp frame rate, although presumably all the time values could be scaled proportionately for any reason.

A layer's intrinsic frame rate (if it has one) is not visible anywhere, although it's usually the same as the comp frame rate. If a filter needs to access the actual frames of a clip, it can do so

only by being in a comp of the same frame rate, and with no Time Stretch or Time Remapping applied to its layer. It should use local_time_step to determine where the frames are.

---

## Rate x Time == Pain!

Be careful if one of your parameters is a speed or velocity parameter. Consider the ripple effect. It assumes a constant and uses the current time to determine how far along the ripple has gone (d = v \* t). If the user interpolates the speed over time, you should integrate the velocity function from time zero to the current time. Ripple does *not* do this, but provides a "phase" parameter that the user can interpolate as they wish, providing correct results as long as the speed is set to zero. If you want to provide the correct behavior, you can sample (and integrate) the speed parameter from the beginning of time until the current time using PF_CHECKOUT_PARAM(), or you can provide a "phase" or "distance" parameter and warn the user about interpolating the speed. The cost of checking out many parameter values is negligible compared to rendering, and is the recommended approach.

If you check out parameter values at other times, or use layer parameters at all, you *must* check in those parameters when finished, even if an error has occurred. Remember, checked-out parameters are read-only.

---

## Testing

Try using your plug-in in RAM previews to ensure you handle out-of-memory conditions gracefully. Does your plug-in handle running out of memory gracefully?

If you receive `PF_Err_OUT_OF_MEMORY` (from [Error Codes](../effect-basics/errors.md#error-codes)) when requesting memory, do you pass it back to After Effects?

What happens when your video effect is applied to an audio-only layer? Test with projects created using older versions of your plug-in.
