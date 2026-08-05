# Multi-density images

> ℹ️ **INFO**
>
> The `images` command at a glance
> `purgetss images` generates every Titanium density variant of your UI images from one source file per image.
> 
> - Android: `res-mdpi`, `res-hdpi`, `res-xhdpi`, `res-xxhdpi`, `res-xxxhdpi` (5 densities)
> - iPhone: `@1x`, `@2x`, `@3x` (3 scales via filename suffix)
> 
> Works on Alloy and Classic projects. The layout is auto-detected.


This page covers the `purgetss/images/` folder, the 4× master convention, single-file regeneration, and where the command fits in a normal build.

For a terse reference of every flag, see the [`images` command reference](../commands#images-command).

## Why multi-density?

Android's UI toolkit resolves images by density: a Pixel 7 (xxhdpi ≈ 3×) picks files from `res-xxhdpi/`, a low-end Moto (hdpi ≈ 1.5×) picks from `res-hdpi/`. If the right density isn't available, Android upscales the closest one, which looks noticeably blurry on high-DPI screens.

iOS uses the same idea with filename suffixes: `icon.png`, `icon@2x.png`, `icon@3x.png`. iPhone 15 Pro picks `@3x`, older iPads pick `@2x`.

Ship every variant and the UI stays sharp on each device. `purgetss images` creates those files in one pass.

## Quick start

Drop source images into `purgetss/images/`, then run the command:

```bash
> cp my-hero-illustration.png purgetss/images/
> cp my-button.svg purgetss/images/buttons/primary.svg
> purgetss images
```

Output in an Alloy project:

```text
app/assets/
├── android/images/
│   ├── res-mdpi/
│   │   ├── my-hero-illustration.png
│   │   └── buttons/primary.svg
│   ├── res-hdpi/…
│   ├── res-xhdpi/…
│   ├── res-xxhdpi/…
│   └── res-xxxhdpi/…
└── iphone/images/
    ├── my-hero-illustration.png        (@1x)
    ├── my-hero-illustration@2x.png
    ├── my-hero-illustration@3x.png
    └── buttons/
        ├── primary.svg                 (@1x)
        ├── primary@2x.svg
        └── primary@3x.svg
```

## The `purgetss/images/` convention

`init` creates `purgetss/images/` alongside `fonts/` and `brand/`, so the folder is already there before you drop in any source images.

`./purgetss/images/`
```text
purgetss/images/
├── logo-screen.svg
├── hero.png
├── buttons/
│   ├── primary.png
│   └── secondary.png
└── icons/
    └── home.svg
```

Supported input formats: `.svg`, `.png`, `.jpg`, `.jpeg`, `.webp`, `.gif`.

Subdirectories are preserved in the output. A file at `purgetss/images/buttons/primary.png` ends up at `app/assets/android/images/res-*/buttons/primary.png` and `app/assets/iphone/images/buttons/primary.png`. Use whatever folder layout makes sense for your project.

> 💡 **TIP**
>
> Use SVG whenever you can
> SVG scales cleanly to every density. A single `icon.svg` can be rasterized into every `res-*dpi` folder without pixel loss. PNG and JPG sources are downscaled, so smaller densities can lose a little sharpness.


## Source sizes and the 4× convention

PurgeTSS, like Titanium Alloy in practice, treats every raster source you drop in `purgetss/images/` (`.png`, `.jpg`, `.jpeg`, `.webp`, `.gif`) as a 4× master. That single file is the largest density: `res-xxxhdpi` on Android, equivalent to `@4x` on iPhone if iOS supported it. Every smaller density is downscaled from it. Nothing is upscaled when the source is already the master size you want.

For example, drop `logos/hero.webp` at 1024×512 px into `purgetss/images/`. One `purgetss images` run produces eight files:

| Density / scale        | Multiplier | Width × Height | Note              |
| ---------------------- | ---------- | -------------- | ----------------- |
| Android `res-xxxhdpi`  | ×1   (4×)  | 1024×512       | master, untouched |
| Android `res-xxhdpi`   | ×0.75 (3×) | 768×384        | downscaled        |
| Android `res-xhdpi`    | ×0.5  (2×) | 512×256        | downscaled        |
| Android `res-hdpi`     | ×0.375     | 384×192        | downscaled        |
| Android `res-mdpi`     | ×0.25 (1×) | 256×128        | downscaled        |
| iPhone `@3x`           | ×0.75      | 768×384        | downscaled        |
| iPhone `@2x`           | ×0.5       | 512×256        | downscaled        |
| iPhone `@1x` (`.webp`) | ×0.25      | 256×128        | downscaled        |

The 4× labels (`xxxhdpi`, `@4x` if iOS had one) line up: the file you provide is the 4× master. The intended `@1x` / `mdpi` display size is your source ÷ 4.

That is why a 1024 px-wide source produces a 256 px `mdpi`/@1x output. The tool is not arbitrarily shrinking your image; it is deriving smaller files from the master you supplied.

Pick your source size by multiplying the intended `@1x` size by 4:

| Source size (master 4×) | Use for                               | Resulting `@1x` |
| ----------------------- | ------------------------------------- | --------------- |
| 1920×1080 or larger     | Full-screen illustration / hero image | 480×270         |
| 800×800                 | Card, list item, large icon           | 200×200         |
| 200×200                 | Button, inline icon                   | 50×50           |
| 96×96                   | Small inline icon                     | 24×24           |

If your source is smaller than the intended 4× master size, every density still gets generated, but `xxxhdpi`/`@3x` will be upscaled beyond the source and look soft on high-DPI devices. PurgeTSS warns you in that case (`source is Xpx wide but xxxhdpi needs Ypx`). Use the largest size as the source. Sharp downscales cleanly, but no rasterizer can invent detail when it has to upscale.

Recommended sources for common UI elements (in source pixels, assumed 4×):

- Full-screen illustration: at least `1920×1080`
- Tab bar icon: at least `192×192` (source for 48 px at xxxhdpi)
- List-row thumbnail: at least `320×320`
- Button background: match the intended display size × 4

> 💡 **TIP**
>
> SVG is the exception
> SVGs are vector. Resolution comes from their `viewBox`, not pixel dimensions, so there is no "master size" to worry about. Sharp rasterizes each density at the right pixel size. Use SVG whenever you can; use raster images when the source is photographic, has effects that do not translate to SVG, or comes from a non-vector tool.


## Pinning the output width with `--width`

The 4× convention works well for raster sources (`png`, `jpg`, `webp`) because the file's pixel size usually tells you the intended 4× size. SVGs are different. Their source size comes from the `viewBox`, and vector editors can write out strange values. Affinity Designer, for example, can export logos with viewBoxes like `29559×13542 pt`. If PurgeTSS scales from that, the generated files will be far too large for the UI.

`--width <n>` is the escape hatch:

```bash
> purgetss images logo-don-nacho.svg --width 256
```

This pins Android `mdpi` and iPhone `@1x` to 256 px wide, then derives every other scale from that base:

| Scale            | Multiplier | Width if `--width 256` |
| ---------------- | ---------- | ---------------------- |
| `mdpi` / `@1x`   | ×1         | 256                    |
| `hdpi`           | ×1.5       | 384                    |
| `xhdpi` / `@2x`  | ×2         | 512                    |
| `xxhdpi` / `@3x` | ×3         | 768                    |
| `xxxhdpi`        | ×4         | 1024                   |

Height stays proportional to the source's aspect ratio. You only specify the width.

The flag is CLI-only. There is no matching `images:` config property because width is a per-asset decision. A hero illustration, a logo, and an icon usually need different widths.

### When the command suggests `--width`

When you run `purgetss images` against an SVG without `--width`, you'll see a one-time hint:

```text
⚠  SVG source detected without --width. Output sizes will be derived from
   each SVG's viewBox (treated as a 4× master).
   For SVGs from vector editors with disproportionate viewBoxes, pass
   --width <n> (e.g. --width 256) to pin the @1x/mdpi width.
```

This is a hint, not an error. The legacy 4× behavior still runs in the same invocation. If your SVG has a sensible viewBox, such as `300×150` for a 300px-wide logo at 1×, the default behavior is fine. If the viewBox is in pt or is much larger than expected, re-run with `--width <n>`.

### Validation

`--width` accepts integers in `[1, 8192]`. Anything else exits immediately:

```bash
> purgetss images logo.svg --width 0
Invalid --width '0'. Must be an integer between 1 and 8192.

> purgetss images logo.svg --width 9000
Invalid --width '9000'. Must be an integer between 1 and 8192.

> purgetss images logo.svg --width abc
Invalid --width 'NaN'. Must be an integer between 1 and 8192.
```

The upper bound exists because `--width 8192` already produces an `xxxhdpi` output of 32 768 px wide. That is Sharp's render ceiling and far beyond what a normal Titanium app needs.

## The `images:` config section

On the first run, `purgetss images` adds an `images:` block to your existing `purgetss/config.cjs` between `brand:` and `theme:`:

`./purgetss/config.cjs`
```javascript
images: {
  autoSync: true,          // false = SVG pipeline computes dims but doesn't write to images.files
  quality: 85,             // JPEG/WebP/AVIF quality (0-100)
  format: null,            // null = keep original; 'webp' | 'jpeg' | 'png' to convert every image
  confirmOverwrites: true, // prompt before overwriting files (set false to skip)
  files: []                // per-file overrides: [{ filename: 'images/<sub>/<name>.<ext>', width, height? }]
}
```

Change only what should become a project default. CLI flags still win for one-off runs. `autoSync` and `files` are covered in the [Per-file overrides](#per-file-overrides-with-imagesfiles) and [SVG-aware compile-time pipeline](svg-pipeline) sections.

## Per-file overrides with `images.files`

`images.files` is an array of per-file dimension pins. It does not filter which images get processed. `purgetss images` always walks every supported file under `purgetss/images/`. Entries in `files` only override the source and derived sizing for files whose `filename` matches.

`./purgetss/config.cjs`
```javascript
images: {
  // ...
  files: [
    { filename: 'images/logos/logo.svg',     width: 256 },         // SVG: width pins @1x/mdpi
    { filename: 'images/buttons/hero.svg',   width: 512, height: 128 }, // both dims pinned
    { filename: 'images/photos/banner.jpg',  width: 800 }          // raster: behaves like --width 800
  ]
}
```

Precedence is the same for every file processed:

1. CLI `--width <n>` wins.
2. Otherwise, the matching entry in `images.files` (if any).
3. Otherwise, the source's natural size: `viewBox` for SVG, intrinsic pixel size for rasters (treated as 4× master).

With the example above:

| Source                                | In `files`? | Width source              | Output format              |
| ------------------------------------- | ----------- | ------------------------- | -------------------------- |
| `logos/logo.svg`                      | yes         | 256 px @1x (from `files`) | `png` (forced; see below)  |
| `buttons/hero.svg`                    | yes         | 512×128 pinned            | `png` (forced)             |
| `photos/banner.jpg`                   | yes         | 800 px @1x                | jpg (or `format` if set)   |
| `icons/cart.svg` (not listed)         | no          | from SVG viewBox          | follows `format`           |
| `backgrounds/sunset.png` (not listed) | no          | source as 4× master       | png (or `format`)          |

SVGs in `files` always emit `.png`. PurgeTSS detects that the file is a vector source whose entry exists because the developer pinned it from a view/controller reference (`image="/.../foo.svg"`). Titanium's runtime only falls back from `.svg` references to `.png`, never `.webp`, `.jpeg`, or another format. Emitting anything else here would silently produce files Titanium cannot load. See the [Format conversion](#format-conversion) section for the matching warning on `images.format`.

Set `autoSync: false` if you do not want the [SVG pipeline](svg-pipeline) writing into this section automatically. Your `images.files` entries are then yours to maintain.

## Overwrite confirmation

`images` writes directly into the project, so it asks before overwriting anything:

```text
Continue? [y/N/a]
```

- `y` / `yes`: write this time
- `N` / `no` / `Enter`: abort without writing
- `a` / `always`: write, then add `confirmOverwrites: false` to the `images:` section of `config.cjs`

The prompt is skipped automatically when:

- `stdin` is not a TTY, such as the `alloy.jmk` hook, CI, or a pipe
- you pass `-y` / `--yes`
- `PURGETSS_YES=1` is set in the environment
- `confirmOverwrites: false` is already in the `images:` config

```bash
> purgetss images -y                             # skip prompt once
> PURGETSS_YES=1 purgetss images                 # skip for the whole session
```

## Re-processing a single file or subfolder

A common workflow is tweaking one image in Affinity or Figma and regenerating only that file.

Pass its path directly:

```bash
> purgetss images buttons/primary.png
```

Short paths resolve against `purgetss/images/`, so you do not need to type `purgetss/images/buttons/primary.png`. The command tries two paths:

1. `purgetss/images/buttons/primary.png` (matches the convention folder).
2. `./buttons/primary.png` (fallback, relative to the project root).

Subdirectory structure is preserved in the output whenever the source lives inside `purgetss/images/`, whether you pass the full folder, a subfolder, or a single file. Re-processing one file produces the same output path it had in a full run.

### Re-process a whole subfolder

```bash
> purgetss images buttons/
```

This walks `purgetss/images/buttons/` recursively and regenerates every image inside. Everything outside stays untouched.

### Pointing to sources outside the convention

If your source images live elsewhere (e.g. next to your design files in `docs/screenshots/`), pass an absolute or cwd-relative path:

```bash
> purgetss images ./docs/screenshots/home-hero.png
> purgetss images /Users/cesar/Design/banner.svg
```

When the source is outside `purgetss/images/`, the source file's directory becomes the root for output paths.

## Platform filter

By default, every run generates both Android densities and iPhone scales. Scope to one platform for targeted runs:

```bash
> purgetss images --android                # Android only (skip iPhone)
> purgetss images --ios                    # iPhone only (skip Android)
```

This is useful when:

- You are iterating on an iOS-only screen and do not need to regenerate Android assets every time.
- You want to tune JPEG quality differently for the two platforms by running the command twice with different flags.

The two flags are mutually exclusive. Pass neither to get both.

## Format conversion

The default preserves each source's format: drop in `.png`, get out `.png`; drop in `.jpg`, get out `.jpg`. Add `--format <ext>` to convert every output to a single target format:

```bash
> purgetss images --format webp            # convert every output to WebP
> purgetss images --format jpeg --quality 90
```

Valid targets: `webp`, `jpeg`, `png`, `avif`, `gif`, `tiff`.

### Why WebP?

WebP usually produces files about 25-35% smaller than JPEG at similar visual quality, and Titanium supports it natively on both platforms. On a large UI asset library, that can trim several MB from your APK or IPA.

```bash
> purgetss images --format webp --quality 85
```

Keep `--format null` (the default) when you need to stay in the same format as the source, for example PNG with alpha that shouldn't be flattened.

> 🚨 **WARNING**
>
> WebP and SVG references don't mix
> Titanium's runtime fallback for `image="/.../foo.svg"` resolves to `.png` only (verified empirically). Other formats, such as `.webp`, `.jpeg`, and `.avif`, are not picked up through the SVG reference.
> 
> If both `.png` and `.webp` of the same basename live next to each other on disk, the `.svg` fallback breaks entirely. Titanium then shows nothing for that view.
> 
> PurgeTSS protects you in two places:
> 
> - SVGs listed in `images.files` always emit `.png`, even with `format: 'webp'`. The output line says `png (forced; ignores format: webp)`.
> - The post-purge [SVG pipeline](svg-pipeline) always emits `.png` regardless of `format`.
> 
> `format` only applies to raster sources and SVGs not listed in `files`, which are the ones you reference directly as `.webp` in XML instead of through `.svg`.


## Applying opacity to all densities

`--opacity <n>` multiplies the alpha channel of every generated density by `n/100`. Use it for placeholder or default images that render at reduced opacity, such as loading overlays or watermarks behind content.

The transformation is applied before resize, so each density variant inherits the same proportional transparency:

```bash
> purgetss images logo.svg --opacity 50 --format png
```

Generates 8 PNGs (5 Android densities + 3 iPhone scales). Each pixel's alpha equals `original_alpha × 0.5`. Fully opaque source pixels become 50% transparent; already-transparent areas, such as backgrounds and antialiased edges, stay transparent.

### Validation

`--opacity` accepts integers in `[0, 100]`:

```bash
> purgetss images logo.svg --opacity 150
Invalid --opacity '150'. Must be an integer between 0 and 100.
```

`--opacity 100` is a no-op, so the compositing pass is skipped entirely. `--opacity 0` produces a fully transparent output. It is technically valid, but visually empty.

### Format compatibility

Opacity requires an output format that supports an alpha channel:

- `png`, `webp`, `avif`, `tiff`, `gif`: alpha preserved.
- `jpeg`: JPEG does not support alpha. The existing flatten-on-white step composites the semi-transparent image onto white before writing, so `--opacity 50 --format jpeg` produces a 50%-faded version on white.

For placeholders meant to layer over arbitrary backgrounds, prefer `--format png` or `--format webp`.

## Adding breathing room with `--padding`

`--padding <n>` shrinks the rendered image inside each density canvas by `n%` symmetric borders. Use it when the source logo has no built-in padding and you want a placeholder with breathing room without editing the source asset.

```bash
> purgetss images purgetss/brand/logo.png --padding 15 --format png
```

Each density's output canvas keeps the same dimensions it would have without `--padding`, but the rendered image takes only `(1 − 2 × 0.15) = 70%` of that canvas (centered). The remaining 30% of the canvas (15% on each side) is transparent.

For a `mdpi` output of 256×256, the rendered image becomes a 179×179 block centered inside the 256×256 transparent canvas. For `xxxhdpi` at 1024×1024, it becomes 716×716 inside 1024×1024. The visual ratio is identical at every density.

### Validation

`--padding` accepts integers in `[0, 40]`:

```bash
> purgetss images logo.png --padding 50
Invalid --padding '50'. Must be an integer between 0 and 40.

> purgetss images logo.png --padding -5
Invalid --padding '-5'. Must be an integer between 0 and 40.
```

`--padding 0` is a no-op, so the extend pass is skipped.

### Combining with `--opacity`

The two flags compose naturally for placeholder images:

```bash
> purgetss images purgetss/brand/logo.png \
    --opacity 30 \
    --padding 15 \
    --output 'logos/default-image' \
    --format png
```

Generates the 8 multi-density variants. Each output is the source logo at 70% × 70% of the canvas, semi-transparent at 30% alpha, written under `images/logos/default-image.{png,@2x.png,@3x.png}` (iPhone) and `images/res-*/logos/default-image.png` (Android).

### Padding vs source-baked padding

If your source logo already has the breathing room you want baked in, you do not need `--padding`. It would compound the existing padding. Use `--padding` when:

- The source is edge-to-edge (no built-in margin)
- You're sourcing from `purgetss/brand/logo.svg`, which is meant to fill icon canvases tightly through `brand`'s own padding controls, and need a different visual ratio for an ImageView placeholder
- You want different placeholders from the same source at different paddings

## Renaming the output with `--output`

By default, the output basename comes from the source filename, and the subfolder mirrors the source's location inside `purgetss/images/`. When you want a different output path, for example when sourcing a logo from `purgetss/brand/` and writing it as a placeholder under `images/logos/`, `--output <relpath>` overrides both:

```bash
> purgetss images purgetss/brand/logo.svg --opacity 50 --output 'logos/loading-placeholder' --format png
```

Generates:

```text
app/assets/iphone/images/logos/loading-placeholder.png         (@1x)
app/assets/iphone/images/logos/loading-placeholder@2x.png
app/assets/iphone/images/logos/loading-placeholder@3x.png
app/assets/android/images/res-mdpi/logos/loading-placeholder.png
app/assets/android/images/res-hdpi/logos/loading-placeholder.png
app/assets/android/images/res-xhdpi/logos/loading-placeholder.png
app/assets/android/images/res-xxhdpi/logos/loading-placeholder.png
app/assets/android/images/res-xxxhdpi/logos/loading-placeholder.png
```

The full multi-density pattern is preserved. `--output` only renames the basename and rewrites the subpath. The Android density folder (`res-*`) still comes first; your subpath sits inside it.

### Constraints

- No extension: pass the basename only. The extension comes from `--format` if set, otherwise from the source extension, or PNG when the source is SVG because Sharp cannot write SVG. Any extension passed in `--output` is stripped.
- Relative path only: it must stay inside the project images folder. Absolute paths (`/tmp/foo`) and paths containing `..` are rejected.
- Single source only: `--output` cannot apply when the source is a directory because one basename cannot apply to multiple files. Pass a single file as the source, or drop `--output`.

### Common use case: ImageView placeholder

For a default ImageView placeholder while remote images load:

```bash
> purgetss images purgetss/brand/logo.svg --opacity 30 --output 'logos/default-image' --format png
```

Then in your XML:

```xml
<ImageView defaultImage="/images/logos/default-image.png" image="{remoteUrl}" />
```

Titanium auto-picks the correct density at runtime based on the device.

## Full pipeline alongside `build`

A normal app workflow looks like this:

```bash
# 1. Edit your source images in Affinity/Figma, drop into purgetss/images/
# 2. Regenerate the variants
> purgetss images

# 3. Run purgetss build to regenerate utilities.tss if you changed classes
> purgetss build

# 4. Build the app
> ti build -p android -T emulator
```

If you only changed CSS classes, you do not need to re-run `purgetss images`.

## Cleaning up

`purgetss images` never deletes files. It only creates them. If you remove an image from `purgetss/images/`, the generated copies in `app/assets/android/images/res-*/` and `app/assets/iphone/images/` stay in place. Remove them manually or with git when you clean up.

## Troubleshooting

### The output is blurry on high-DPI devices

Your source is probably smaller than the 4× master size. Use a larger source, at least 4× the intended display size, or switch to SVG when possible.

### JPG output has a white background instead of transparency

JPEG doesn't support alpha channels. If your source is PNG with transparency and you convert to JPEG (via `--format jpeg`), the alpha gets flattened on white. To preserve transparency, keep the format as PNG or WebP:

```bash
> purgetss images --format webp            # supports alpha
> purgetss images --format png             # keeps alpha
```

### My `<ImageView image="/.../foo.svg" />` shows nothing

Two common causes:

1. There is no `.png` next to the `.svg`. Titanium falls back from `.svg` references to `.png` siblings. If `purgetss images` (or the post-purge SVG pipeline) hasn't generated them yet, the fallback can't fire. Run `purgetss images` (or `purgetss` for the automatic pipeline) and verify the `.png` exists under `app/assets/iphone/images/` and `app/assets/android/images/res-*/`.
2. Both `.png` and `.webp` (or another non-png) sit next to each other for the same basename. That breaks the fallback entirely, and Titanium shows nothing. Delete the non-`.png` siblings, or use the post-purge SVG pipeline / pin the SVG in `images.files` to force-PNG behavior.

### My subdirectories aren't preserved in the output

Verify that your source path is inside `purgetss/images/`. When you pass sources from outside the convention, such as `./docs/screenshots`, the directory of the source file is used as the root. A file at `./docs/screenshots/hero.png` therefore outputs to `images/hero.png`, not `images/screenshots/hero.png`.

Move the file into `purgetss/images/screenshots/` if you want subdirectory preservation.

### I want to preview what would happen before writing files

```bash
> purgetss images --dry-run
```

Shows every file that would be written without writing anything.

### Can I use this for app icons?

No. App icons need a different pipeline: adaptive icons, mask safe zones, marketplace flattening, and iOS 18+ Dark/Tinted variants. Use [`purgetss brand`](./app-icons-and-branding) for the launcher icon.

`purgetss images` is for the UI assets inside your screens: buttons, backgrounds, illustrations, inline icons.
