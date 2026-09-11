# Effect Preview Media (Effects Panel)

!!! note
    These features apply to Premiere Pro Beta only, starting with version 27.0. It does not currently apply to After Effects, though After Effects may adopt it in the future.

Third-party effects can ship their own preview media to be shown in the host's Effects panel: a still thumbnail for the effect grid, and a short video that plays on hover. If you don't provide media, the host falls back to a generic category placeholder.

## Where to put the media

Preview media lives in a folder named `Preview`, and each file is named after your plug-in's binary file title (the plug-in filename without its extension). Because the media is keyed on the binary, all effects contained in a single plug-in share the same preview media.

* **Thumbnail (still):** `<pluginFileTitle>.jpg`
* **Hover preview (video):** `<pluginFileTitle>.mp4`

For example, a plug-in whose binary is `AcmeGlow` would provide `AcmeGlow.jpg` and `AcmeGlow.mp4`.

### macOS

Place the `Preview` folder inside your plug-in's `.plugin` bundle, under `Contents/Resources`:

```
<PluginName>.plugin/Contents/Resources/Preview/<pluginFileTitle>.jpg
<PluginName>.plugin/Contents/Resources/Preview/<pluginFileTitle>.mp4
```

### Windows

Place the `Preview` folder next to your plug-in's `.aex` binary:

```
<folder containing the .aex>/Preview/<pluginFileTitle>.jpg
<folder containing the .aex>/Preview/<pluginFileTitle>.mp4
```

## Format and size

**Thumbnail (still image):**

* Format: `.jpg`
* Resolution: 640 x 360

**Hover preview (video):**

* Format: `.mp4`, H.264
* Resolution: 640 x 360
* File size: as small as reasonably possible; typical is 400 KB to 1 MB
* Duration: no upper limit, but typically 4 to 10 seconds

The thumbnail is used for the effect's tile in the grid; the video plays when the user hovers over the tile. If you supply only a `.jpg`, the still is used for the tile and there is no hover video.
