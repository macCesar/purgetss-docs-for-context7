# App icons and branding

> ℹ️ **INFO**
>
> The `brand` command at a glance
> From one main SVG or PNG logo, `purgetss brand` generates the complete branding set for the platforms enabled in `tiapp.xml`: launcher icons, adaptive icons, iOS 18+ Dark/Tinted variants, marketplace artwork, and the iOS and Android splash sets. Per-piece overrides are there when you need them.
> 
> The rule is simple: **if an enabled platform can consume the file, `brand` generates or updates it.** You should never have to know whether `ti create` happened to seed that useful file in Alloy or Classic.
> 
> Works on Alloy and Classic projects. The layout is detected automatically.


This guide covers the `brand` workflow: setting up `purgetss/brand/`, tuning padding for your logo, handling dark mode on iOS 18+ and Android 13+, and fixing common rebuild issues.

For a terse reference of every flag, see the [`brand` command reference](../commands.md#brand-command).

## Quick start

Drop a logo file into `purgetss/brand/`, then run the command.

```bash
> mkdir -p purgetss/brand # if the folder doesn't exist yet
> cp docs/my-logo.svg purgetss/brand/logo.svg

> purgetss brand
```

On the first run, the command:

1. Creates `purgetss/config.cjs` from the canonical template when it does not exist, or adds the `brand:` section when only that block is missing.
2. Generates every branding file directly into the project (in-place).
3. Prints a compact summary of what was written.

In a standalone Classic project you may pass the source directly:

```bash
> purgetss brand sample-icon.png
```

When no canonical `purgetss/brand/logo.{svg,png}` exists, PurgeTSS moves that positional source to `purgetss/brand/logo.png` (or `.svg`) and reports the new path. It never replaces an existing canonical logo without confirmation.

A normal run reads `<deployment-targets>` from `tiapp.xml` and omits iOS or Android output when that platform is disabled. `--only` is an intentional override, so `--only ios` can prepare iOS assets before enabling the target.

Pass `--dry-run` to preview without writing any files:

```bash
> purgetss brand --dry-run
```

## The `purgetss/brand/` convention

`init` creates `purgetss/brand/` (alongside `fonts/` and `images/`) so the folder is already there the first time you look for it, even before you've dropped in a logo.

PurgeTSS auto-discovers logo files under this folder, the same way `purgetss/fonts/` works for fonts. The naming rule is `logo-<piece>`, with the same piece names the CLI and the config use. Drop a file in and you're done:

`./purgetss/brand/`
```text
purgetss/brand/
├── logo.svg                   required - main logo (or logo.png), source for every piece
├── logo-mono.svg              optional - monochrome layer + notification icons
├── logo-icon.svg              optional - DefaultIcon.png + DefaultIcon-ios.png
├── logo-adaptive.svg          optional - square Android launcher mark
├── logo-dark.svg              optional - iOS 18+ dark variant
├── logo-tinted.svg            optional - iOS 18+ tinted variant
├── logo-launch.svg            optional - iOS launch screen logotype (LaunchLogo.png)
├── logo-splash-icon.svg       optional - Android 12+ splash icon override
├── logo-android-splash.svg    optional - Android <12 splash artwork
├── logo-ios-splash.svg        optional - iPhone launch images
├── logo-marketplace.svg       optional - App Store / Play Store artwork
├── logo-feature-graphic.svg   optional - Google Play Feature Graphic override
├── logo-legacy-icon.svg       optional - legacy ic_launcher.png
├── logo-appicon.svg           optional - appicon.png
└── logo-notification-icon.svg optional - notification icons
```

Only `logo.svg` (or `logo.png`) is required. Every other file is an override for one piece, and dropping it in is all it takes. There is no syntax to remember, and opening the folder shows what has been customized.

Every one of them has a CLI equivalent, `--<piece>-logo <path>`, and a config equivalent, `brand.<piece>.logo`, for artwork that lives outside `purgetss/brand/`.

The ones worth knowing about:

- `logo-icon`: alternate artwork for `DefaultIcon.png` / `DefaultIcon-ios.png`, when the universal icon should differ from the main logo.
- `logo-adaptive`: a separate square mark for Android launcher icons. Use this when your main logo is a horizontal wordmark, a vertical lockup, or anything else that looks fine in a 1024×1024 branding canvas but feels cramped inside an Android launcher mask.
- `logo-mono`: silhouette used for the Android adaptive monochrome layer (themed icons on Android 13+) and for notification icons. When omitted, `brand` whitens the main logo automatically. Provide your own when the colored logo has detail that would collapse under automatic whitening. A painter's palette with colored dots is a good example: the monochrome version should have cutouts instead.
- `logo-dark`: alternate logo for iOS 18+ dark mode. When omitted, the dark variant comes from the main logo with a transparent background (Apple's recommended approach). Provide your own when dark-mode brand guidelines use a different lockup or color treatment.
- `logo-tinted`: alternate logo for iOS 18+ tinted mode. When omitted, the tinted variant comes from a grayscale of the main logo. Provide your own when you want a simpler silhouette that tints better than a grayscale of the colored version.
- `logo-launch`: the only file that also *activates* a piece. Drop it in and `brand` writes `LaunchLogo.png`, so the iOS launch screen shows your logotype instead of the app icon. See [The iOS launch screen and LaunchLogo.png](#the-ios-launch-screen-and-launchlogopng).
- `logo-splash-icon`: alternate artwork for Android 12+ `splash_icon.png`. Use this when the splash should use a different composition than the launcher icon. PurgeTSS generates the file, but Titanium still needs a custom Android splash theme if you want the system splash to use it instead of `ic_launcher`.
- `logo-feature-graphic`: alternate logo for the Google Play Feature Graphic (1024×500 banner). When omitted, the main logo is centered inside the banner with the configured vertical padding. Provide your own when you want a different composition for the Play Store listing: a logo-plus-tagline lockup, say, or wider artwork that uses the rectangular canvas instead of staying inside the centered square.

> 💡 **TIP**
>
> Prefer SVG for the master
> SVG scales cleanly to every density Sharp needs to emit. A single `logo.svg` can be rasterized at every `res-*dpi` output. PNG masters should be at least 1024×1024 to avoid upscaling artifacts.


### Overriding auto-discovery

You can also pass a path directly or point to a logo from the config. Useful when your masters live in `docs/` or another workflow folder:

```bash
> purgetss brand ./docs/snap-logo.svg
```

Or in `purgetss/config.cjs`, where each piece takes its own `logo`:

`./purgetss/config.cjs`
```javascript
brand: {
  background: '#FFFFFF',      // inherited by pieces that use an opaque background canvas
  splashCornerRadius: '0%',   // rounded artwork on legacy iOS/Android splash screens (0-50)
  confirmOverwrites: true,    // prompt before overwriting files (set false to skip)
  optimize: false,            // true = quantize the generated PNGs to a palette (lossy, ~71% smaller)

  // One block per piece. Artwork comes from purgetss/brand/logo-<piece>.{svg,png};
  // these keys are for numbers, colors and activation. Padding is never inherited.
  // Only iosSplash/androidSplash accept cornerRadius; it overrides splashCornerRadius.
  // iOS/store icons are full-bleed by default; increase padding only for logo artwork.
  icon:             { padding: '0%' },    // DefaultIcon.png + DefaultIcon-ios.png
  dark:             { background: null }, // DefaultIcon-Dark.png
  tinted:           {},                   // DefaultIcon-Tinted.png
  iosSplash:        { padding: '26%' },   // assets/iphone/Default*.png × 16
  launchLogo:       { padding: '12%' },   // LaunchLogo.png (1024×1024)
  marketplace:      {},                   // iTunesConnect.png + MarketplaceArtwork.png
  featureGraphic:   { padding: '12%' },   // MarketplaceArtworkFeature.png (1024×500)
  adaptive:         { padding: '18%' },   // ic_launcher_{foreground,background,monochrome}.png × 5 + ic_launcher.xml
  legacyIcon:       { padding: '10%' },   // ic_launcher.png × 5
  appicon:          { padding: '10%' },   // appicon.png (128×128)
  androidSplash:    { padding: '26%' },   // assets/android/default.png + images/res-*/default.png × 11

  // Opt-in: inert until you edit the Android theme / FCM meta-data by hand.
  splashIcon:       { enabled: false },   // drawable-*/splash_icon.png × 5
  notificationIcon: { enabled: false },   // drawable-*/ic_stat_notify.png × 5
  ninePatch:        { enabled: false }    // background.9.png (not implemented yet)
}
```

CLI flags override config values, and config values override auto-discovery.

## The `brand:` config section

On the first run, `purgetss brand` adds a `brand:` block to your existing `purgetss/config.cjs` between `purge:` and `theme:`. It has one block per piece:

`./purgetss/config.cjs`
```javascript
brand: {
  background: '#FFFFFF',      // inherited by pieces that use an opaque background canvas
  splashCornerRadius: '0%',   // rounded artwork on legacy iOS/Android splash screens (0-50)
  confirmOverwrites: true,    // prompt before overwriting files (set false to skip)
  optimize: false,            // true = quantize the generated PNGs to a palette (lossy, ~71% smaller)

  // One block per piece. Artwork comes from purgetss/brand/logo-<piece>.{svg,png};
  // these keys are for numbers, colors and activation. Padding is never inherited.
  // Only iosSplash/androidSplash accept cornerRadius; it overrides splashCornerRadius.
  // iOS/store icons are full-bleed by default; increase padding only for logo artwork.
  icon:             { padding: '0%' },    // DefaultIcon.png + DefaultIcon-ios.png
  dark:             { background: null }, // DefaultIcon-Dark.png
  tinted:           {},                   // DefaultIcon-Tinted.png
  iosSplash:        { padding: '26%' },   // assets/iphone/Default*.png × 16
  launchLogo:       { padding: '12%' },   // LaunchLogo.png (1024×1024)
  marketplace:      {},                   // iTunesConnect.png + MarketplaceArtwork.png
  featureGraphic:   { padding: '12%' },   // MarketplaceArtworkFeature.png (1024×500)
  adaptive:         { padding: '18%' },   // ic_launcher_{foreground,background,monochrome}.png × 5 + ic_launcher.xml
  legacyIcon:       { padding: '10%' },   // ic_launcher.png × 5
  appicon:          { padding: '10%' },   // appicon.png (128×128)
  androidSplash:    { padding: '26%' },   // assets/android/default.png + images/res-*/default.png × 11

  // Opt-in: inert until you edit the Android theme / FCM meta-data by hand.
  splashIcon:       { enabled: false },   // drawable-*/splash_icon.png × 5
  notificationIcon: { enabled: false },   // drawable-*/ic_stat_notify.png × 5
  ninePatch:        { enabled: false }    // background.9.png (not implemented yet)
}
```

Change only what you want to keep as a project default. CLI flags still win for one-off runs.

### How the work is divided between files and config

- **Files decide the artwork.** Dropping `logo-dark.svg` next to `logo.svg` is enough; open the folder and you can see what has been customized. This is the main path.
- **Config decides numbers, colors and activation**, none of which can be expressed with a filename.
- `logo:` inside a piece is there for artwork that lives outside `purgetss/brand/`.

Use CLI flags for temporary choices: artwork sources, visual geometry (padding and splash corner radius), the shared background, one-run selection or activation, and optimization. Keep persistent preferences in `config.cjs`, including `confirmOverwrites`, permanent `enabled` values, and exceptional backgrounds for individual pieces.

## Brand config reference

Every piece accepts the same five keys, where they apply:

| Key          | What it does                                                                     |
| ------------ | -------------------------------------------------------------------------------- |
| `logo`       | Path to this piece's artwork, when it lives outside `purgetss/brand/`.            |
| `padding`    | Inset per side, as a number or a percentage string like `'19%'`. Never inherited. |
| `cornerRadius` | Rounded artwork corners, only for `iosSplash` and `androidSplash`; range `0–50`. |
| `background` | Hex color, or `null` for transparent. Inherited from `brand.background`.          |
| `enabled`    | `false` turns a default piece off; `true` turns an opt-in piece on.               |

And these live at the top level:

- `brand.background` — default `#FFFFFF`. This is the inherited opaque fallback for icon canvases, splash canvases, adaptive background layers and store artwork. White is only a configurable fallback; neither platform requires PurgeTSS to add a white frame.
- `brand.splashCornerRadius` — default `'0%'`. Shared corner radius for the artwork placed inside legacy iOS and Android splash canvases. It never changes the square master icon.
- `brand.confirmOverwrites` — default `true`. Whether `brand` asks before overwriting project files in place.
- `brand.optimize` — default `false`. Whether generated PNGs are quantized after rendering.
- `brand.logo` / `brand.monochromeLogo` — path overrides for the two sources that are not pieces: the main logo, and the monochrome silhouette shared by the adaptive monochrome layer and the notification icons.

### The pieces

| Piece              | Config key         | Generates                                                                  | Default padding | On by default            |
| ------------------ | ------------------ | -------------------------------------------------------------------------- | --------------- | ------------------------ |
| `icon`             | `icon`             | `DefaultIcon.png` + `DefaultIcon-ios.png`                                   | `0%`            | yes                      |
| `dark`             | `dark`             | `DefaultIcon-Dark.png`                                                      | `0%`            | yes                      |
| `tinted`           | `tinted`           | `DefaultIcon-Tinted.png`                                                    | `0%`            | yes                      |
| `ios-splash`       | `iosSplash`        | `assets/iphone/Default*.png` × 16                                           | `26%`           | yes                      |
| `launch-logo`      | `launchLogo`       | `LaunchLogo.png` (1024×1024)                                                | `12%`           | when `logo-launch.*` exists |
| `marketplace`      | `marketplace`      | `iTunesConnect.png` + `MarketplaceArtwork.png`                              | `0%`            | yes                      |
| `feature-graphic`  | `featureGraphic`   | `MarketplaceArtworkFeature.png` (1024×500)                                  | `12%`           | yes                      |
| `adaptive`         | `adaptive`         | `ic_launcher_{foreground,background,monochrome}.png` × 5 + `ic_launcher.xml` | `18%`           | yes                      |
| `legacy-icon`      | `legacyIcon`       | `ic_launcher.png` × 5                                                       | `10%`           | yes                      |
| `appicon`          | `appicon`          | `appicon.png` (128×128)                                                     | `10%`           | yes                      |
| `android-splash`   | `androidSplash`    | `assets/android/default.png` + `images/res-*/default.png` × 11              | `26%`           | yes                      |
| `splash-icon`      | `splashIcon`       | `drawable-*/splash_icon.png` × 5                                            | —               | `--splash-icon`          |
| `notification-icon`| `notificationIcon` | `drawable-*/ic_stat_notify.png` × 5                                         | —               | `--notification-icon`    |
| `nine-patch`       | `ninePatch`        | `background.9.png`                                                          | —               | `--nine-patch` (not implemented yet) |

`ic_launcher.xml` always travels inside `adaptive`; it is never generated on its own.

Only three pieces are opt-in, and for one reason: they produce nothing useful until you edit XML by hand. `splash_icon.png` is inert without `windowSplashScreenAnimatedIcon` in the theme, and `ic_stat_notify.png` is inert without the FCM `meta-data` entry.

### `background` is inherited, `padding` is not

Set `brand.background` once and every piece that supports a canvas background picks it up. Padding works the other way on purpose: the Android launcher pieces need safe-zone padding, while finished square iOS/store artwork is full-bleed at `0%`. A single inherited number would let an iOS-oriented value quietly break the Android launcher mask, so padding is set per piece or not at all.

### Older configs update themselves

PurgeTSS keeps `config.cjs` current the same way it renamed `config.js` to `config.cjs`: on the file, once. When the `brand:` block uses a shape from an earlier version, the next run rewrites it to the per-piece structure and carries over everything that had been customized (paddings, colors, logo paths, enabled flags), then lists each value it moved:

```text
::PurgeTSS:: Updated the brand: structure in ./purgetss/config.cjs.
  Your values were carried over:
    • brand.logos.androidLauncher → brand.adaptive.logo
    • brand.padding.androidAdaptive → brand.adaptive.padding
    • brand.android.splash → brand.splashIcon.enabled
    • brand.colors.background → brand.background
```

This happens on `purgetss brand`, and on any command that goes through the config: `build`, `watch`, `purge`, `shades`. Values that already matched a default are not written, so a block that was never customized comes out clean rather than cluttered with redundant keys.

Both earlier shapes are recognized: the original flat keys (`brand.padding` as a number, `brand.iosPadding`, `brand.bgColor`, `brand.darkBgColor`, top-level `brand.notification` / `brand.splash`) and the grouped sections from v7.7.0 (`logos` / `padding` / `android` / `ios` / `colors`). One key is dropped rather than moved: `brand.android.legacySplash`, because the per-qualifier splashes belong to `androidSplash` now and are always generated. The run says so when it happens.

The command itself understands exactly one structure. Nothing translates on the fly, so the day this migration is removed, nothing else changes.

### Unknown keys are an error

A key `brand:` does not define stops the run before a single file is written, at both levels:

```text
Unknown key(s) in the brand: section of purgetss/config.cjs:
  • brand.adaptive.paddig

  Top-level keys: background, confirmOverwrites, logo, monochromeLogo
  Piece blocks:   icon, dark, tinted, iosSplash, launchLogo, marketplace, featureGraphic,
                  adaptive, legacyIcon, appicon, androidSplash, splashIcon,
                  notificationIcon, ninePatch
  Inside a piece: logo, padding, background, enabled

  Check the spelling. Nothing was written.
```

A typo is deliberately not treated as an old structure: rewriting the block would drop it silently. And ignoring a misspelled `paddig` would be worse than stopping: the whole icon set would render at the wrong size and look perfectly plausible.

## Overwrite confirmation

`brand` writes directly into the project, so it asks before overwriting anything:

```text
⚠  In-place mode will OVERWRITE files in <project>. Commit first if you want a rollback.
Continue? [y/N/a]
```

- `y` / `yes`: write this time
- `N` / `no` / `Enter`: abort without writing
- `a` / `always`: write, then add `confirmOverwrites: false` to the `brand:` section of `config.cjs`

The prompt is skipped automatically when:

- `stdin` is not a TTY, such as the `alloy.jmk` hook, CI, or a pipe
- you pass `-y` / `--yes`
- `PURGETSS_YES=1` is set in the environment
- `confirmOverwrites: false` is already in the `brand:` config

```bash
> purgetss brand -y                              # skip prompt once
> PURGETSS_YES=1 purgetss brand                  # skip for the whole session
```

## What gets generated

The output is automatically routed to the right directory for your project layout:

`Alloy layout`
```text
<project>/
├── DefaultIcon.png                 <- 1024×1024, opaque and full-bleed by default
├── DefaultIcon-ios.png             <- 1024×1024, iOS flattened on brand.background
├── DefaultIcon-Dark.png            <- 1024×1024, iOS 18+ dark (transparent per Apple HIG)
├── DefaultIcon-Tinted.png          <- 1024×1024, iOS 18+ tinted (grayscale on black)
├── iTunesConnect.png               <- 1024×1024, App Store submission
├── MarketplaceArtwork.png          <- 512×512, Google Play submission
├── MarketplaceArtworkFeature.png   <- 1024×500, Google Play Feature Graphic
└── app/
    ├── assets/iphone/
    │   ├── Default*.png            <- the 16 launch images the template ships
    │   └── LaunchLogo.png          <- 1024×1024, only when logo-launch.* exists
    ├── assets/android/
    │   ├── appicon.png             <- 128×128
    │   ├── default.png             <- Android <12 splash
    │   └── images/res-*/default.png <- the 11 per-qualifier splashes
    └── platform/android/res/
        ├── mipmap-mdpi/            <- 108×108 foreground + background + monochrome + legacy
        ├── mipmap-hdpi/            <- 162×162
        ├── mipmap-xhdpi/           <- 216×216
        ├── mipmap-xxhdpi/          <- 324×324
        ├── mipmap-xxxhdpi/         <- 432×432
        ├── drawable-*/             <- optional splash_icon.png with --splash-icon
        └── mipmap-anydpi-v26/
            └── ic_launcher.xml     <- adaptive-icon binder
```

`Classic layout`
```text
<project>/
├── DefaultIcon.png  DefaultIcon-ios.png  ...     <- same root-level files as Alloy
├── MarketplaceArtworkFeature.png   <- 1024×500, Google Play Feature Graphic
├── Resources/
│   ├── iphone/Default*.png         <- the launch images the template ships
│   └── android/
│       ├── appicon.png             <- 128×128
│       ├── default.png             <- Android <12 splash
│       └── images/res-*/default.png <- the 11 per-qualifier splashes
└── platform/
    └── android/res/
        ├── mipmap-*/               <- same 5 densities as Alloy
        ├── drawable-*/             <- optional splash_icon.png with --splash-icon
        └── mipmap-anydpi-v26/ic_launcher.xml
```

A fresh Classic project does not include `images/res-*`, but `brand` creates the 11 qualifier folders under `Resources/android/images/`. Titanium consumes those files for the density/orientation-specific Android &lt;12 splash path; they are useful platform inputs, not Alloy-only leftovers.

The Android outputs are related, but they are not interchangeable:

- `ic_launcher*` drives the app icon, and by default it also feeds the Android 12+ system splash
- `splash_icon.png` is only generated when you ask for it with `--splash-icon`
- `default.png` and `images/res-*/default.png` are the Android &lt;12 splash path
- `appicon.png` is the fallback Titanium uses for `tiapp.xml`'s `<icon>` when the manifest declares no `android:icon`

### Why files that "no longer matter" are regenerated

Several of these are read by nothing in a modern build. With `<enable-launch-screen-storyboard>` enabled, the default since Titanium 8, iOS draws the storyboard and never opens the 16 `Default*.png`. `appicon.png` only comes into play when the Android manifest declares no `android:icon`.

They are regenerated anyway, because the alternative is worse: the template ships them with the grey Alloy logo, and deciding which ones to skip would mean asking you to keep track of which Titanium and OS version reads which file. If it is in the project tree, it carries your logo.

To skip the ones you know you don't need, use [`--only`](#regenerating-a-single-piece-with---only). To delete them outright, see [Cleanup legacy branding artifacts](#cleanup-legacy-branding-artifacts).

## Shrinking the generated files

`brand` writes truecolor PNGs. Logos are flat artwork with few distinct colors, which is exactly the case where a 256-color palette is indistinguishable from truecolor at a fraction of the size. It is the same trick TinyPNG and pngquant use.

It is off by default because it is lossy. Turn it on per run or for the project:

```bash
> purgetss brand --optimize
```

`./purgetss/config.cjs`
```javascript
brand: {
  background: '#FFFFFF',      // inherited by pieces that use an opaque background canvas
  splashCornerRadius: '0%',   // rounded artwork on legacy iOS/Android splash screens (0-50)
  confirmOverwrites: true,    // prompt before overwriting files (set false to skip)
  optimize: false,            // true = quantize the generated PNGs to a palette (lossy, ~71% smaller)

  // One block per piece. Artwork comes from purgetss/brand/logo-<piece>.{svg,png};
  // these keys are for numbers, colors and activation. Padding is never inherited.
  // Only iosSplash/androidSplash accept cornerRadius; it overrides splashCornerRadius.
  // iOS/store icons are full-bleed by default; increase padding only for logo artwork.
  icon:             { padding: '0%' },    // DefaultIcon.png + DefaultIcon-ios.png
  dark:             { background: null }, // DefaultIcon-Dark.png
  tinted:           {},                   // DefaultIcon-Tinted.png
  iosSplash:        { padding: '26%' },   // assets/iphone/Default*.png × 16
  launchLogo:       { padding: '12%' },   // LaunchLogo.png (1024×1024)
  marketplace:      {},                   // iTunesConnect.png + MarketplaceArtwork.png
  featureGraphic:   { padding: '12%' },   // MarketplaceArtworkFeature.png (1024×500)
  adaptive:         { padding: '18%' },   // ic_launcher_{foreground,background,monochrome}.png × 5 + ic_launcher.xml
  legacyIcon:       { padding: '10%' },   // ic_launcher.png × 5
  appicon:          { padding: '10%' },   // appicon.png (128×128)
  androidSplash:    { padding: '26%' },   // assets/android/default.png + images/res-*/default.png × 11

  // Opt-in: inert until you edit the Android theme / FCM meta-data by hand.
  splashIcon:       { enabled: false },   // drawable-*/splash_icon.png × 5
  notificationIcon: { enabled: false },   // drawable-*/ic_stat_notify.png × 5
  ninePatch:        { enabled: false }    // background.9.png (not implemented yet)
}
```

`--no-optimize` skips the pass on a single run even when the config asks for it.

On the reference project, the full set of 56 PNGs goes from **1.6 MB to 476 KB, 71% smaller**. Measured on the visible pixels of the generated icons, the difference against the truecolor version averages 0.08–0.19 out of 255, with no channel exceeding 16/255: indistinguishable in practice on flat artwork. Transparency survives too. `DefaultIcon-Dark.png` keeps its alpha channel and the same 67% transparent pixels.

### What "lossy" means here, in numbers

The loss is real: the generated icons carry between 950 and 4,300 distinct visible colors, not because the logo has that many, but because every curved edge and every letter of small text produces hundreds of intermediate tones through antialiasing. Quantization reduces all of that to at most 256.

Those intermediate tones are gradations between two or three base colors, which is exactly what a palette approximates well, and sharp dithers the result. Measured on the visible pixels:

| Source | Colors | Average difference | Channels over 16/255 |
| --- | --- | --- | --- |
| Flat logo (the usual case) | 950–4,300 | 0.08–0.19 / 255 | 0% |
| Three-stop gradient (stress test) | 1,215 | 0.74 / 255 | 0.01% |

Even a full-circle gradient does not band measurably. Still, quantization is lossy by definition, so if your mark leans on wide, smooth gradients, compare a generated file before turning it on for the whole project. A file is only ever rewritten when the palette version comes out smaller.

> ⚠️ **CAUTION**
>
> This is tuned for logo artwork. It is not meant for photographic sources.


What the platforms already do, for context: iOS re-encodes every PNG in the bundle with `pngcrush -iphone` when it packages the app, but that is lossless, so this saving is not something the SDK would have done for you. On Android, nothing in Titanium touches these files.

## Regenerating a single piece with `--only`

A full run rewrites every branding file. That is what you want the first time, and it is exactly what you don't want when only one piece changed and the others were tweaked by hand.

`--only` takes a comma-separated list of pieces, groups, or both:

```bash
> purgetss brand                          # everything
> purgetss brand --only icon              # just the DefaultIcon pair
> purgetss brand --only ios               # icon, dark, tinted, ios-splash
> purgetss brand --only ios,notification-icon
```

Groups are shorthand for the obvious sets:

| Group     | Expands to                                        |
| --------- | ------------------------------------------------- |
| `ios`     | `icon`, `dark`, `tinted`, `ios-splash`            |
| `store`   | `marketplace`, `feature-graphic`                  |
| `android` | `adaptive`, `legacy-icon`, `appicon`, `android-splash` |

Details worth knowing:

- Naming a piece generates it even when its opt-in flag is absent: `--only notification-icon` is enough on its own.
- A name that doesn't exist aborts the run before anything is written, printing the valid pieces and groups.
- `--dry-run` honors the same filter, so you can check the plan first.
- The order you type doesn't matter; generation always follows the pipeline order.

## Android dark mode

> ℹ️ **INFO**
>
> No separate "dark icon" file on Android
> Unlike iOS 18+, Android has no dedicated dark-mode icon file. Instead, Android 13+ uses the monochrome layer of the adaptive icon and tints it based on the user's wallpaper + theme.
> 
> The `brand` command generates `ic_launcher_monochrome.png` at every density by default. You do not need extra flags for themed icon support.


If you want to provide a dedicated silhouette (recommended for detailed logos):

```bash
> cp docs/my-logo-mono.svg purgetss/brand/logo-mono.svg
> purgetss brand
```

The monochrome layer is pure white (`RGB 255,255,255`) with alpha preserved. Android applies the user's tint on top at render time.

## Android 12+ splash artwork

If you pass `--splash-icon`, PurgeTSS generates `drawable-*/splash_icon.png` across Android densities.

```bash
> purgetss brand --splash-icon
```

If you want that artwork to differ from the launcher icon, provide `logo-splash-icon.svg` or set `brand.splashIcon.logo`.

`./purgetss/config.cjs`
```javascript
brand: {
  background: '#FFFFFF',      // inherited by pieces that use an opaque background canvas
  splashCornerRadius: '0%',   // rounded artwork on legacy iOS/Android splash screens (0-50)
  confirmOverwrites: true,    // prompt before overwriting files (set false to skip)
  optimize: false,            // true = quantize the generated PNGs to a palette (lossy, ~71% smaller)

  // One block per piece. Artwork comes from purgetss/brand/logo-<piece>.{svg,png};
  // these keys are for numbers, colors and activation. Padding is never inherited.
  // Only iosSplash/androidSplash accept cornerRadius; it overrides splashCornerRadius.
  // iOS/store icons are full-bleed by default; increase padding only for logo artwork.
  icon:             { padding: '0%' },    // DefaultIcon.png + DefaultIcon-ios.png
  dark:             { background: null }, // DefaultIcon-Dark.png
  tinted:           {},                   // DefaultIcon-Tinted.png
  iosSplash:        { padding: '26%' },   // assets/iphone/Default*.png × 16
  launchLogo:       { padding: '12%' },   // LaunchLogo.png (1024×1024)
  marketplace:      {},                   // iTunesConnect.png + MarketplaceArtwork.png
  featureGraphic:   { padding: '12%' },   // MarketplaceArtworkFeature.png (1024×500)
  adaptive:         { padding: '18%' },   // ic_launcher_{foreground,background,monochrome}.png × 5 + ic_launcher.xml
  legacyIcon:       { padding: '10%' },   // ic_launcher.png × 5
  appicon:          { padding: '10%' },   // appicon.png (128×128)
  androidSplash:    { padding: '26%' },   // assets/android/default.png + images/res-*/default.png × 11

  // Opt-in: inert until you edit the Android theme / FCM meta-data by hand.
  splashIcon:       { enabled: false },   // drawable-*/splash_icon.png × 5
  notificationIcon: { enabled: false },   // drawable-*/ic_stat_notify.png × 5
  ninePatch:        { enabled: false }    // background.9.png (not implemented yet)
}
```

Important detail: generating `splash_icon.png` does not automatically switch Titanium to use it for the Android 12+ system splash. Titanium still needs a custom splash theme that points `android:windowSplashScreenAnimatedIcon` to `@drawable/splash_icon`. If you do nothing, Android will keep using `ic_launcher`. That inertness is exactly why the piece is opt-in.

Keep the theme already assigned to `<application>`. Define a launcher-only theme that inherits from `Theme.Titanium`, then assign it to Titanium's generated launcher Activity. The complete setup is in [Matching the launch background](#matching-the-launch-background).

> ⚠️ **CAUTION**
>
> Android masks this icon into a circle
> The Android 12+ splash icon is drawn inside a circular mask. A wide wordmark that fills the canvas loses its corners. Use a square mark for `logo-splash-icon`, the same advice that applies to launcher icons.


Also, if you still see a brief flash during splash exit even with correct assets, do not assume the PNGs are wrong. That artifact can come from Titanium's splash theme or the system splash transition itself.

## Android &lt;12 splash

Below Android 12 there is no system splash: the launch screen comes from the image Titanium maps into `drawable-*/background.png`, and that image comes from the project's own splash artwork.

`brand` regenerates the whole set on every run. It is the `android-splash` piece, on by default:

- `app/assets/android/default.png` (`Resources/android/default.png` in Classic)
- `app/assets/android/images/res-*/default.png` (`Resources/android/images/res-*/default.png` in Classic) — the 11 per-qualifier images Titanium consumes

Earlier versions regenerated only the first file and hid the other 11 behind a `--legacy-splash` flag, which is why a freshly branded project could still flash the grey Alloy logo on an older phone. That flag is gone: its output is part of `android-splash` and always generated.

:::note A solid windowBackground wins
If the launch theme sets `android:windowBackground` to a plain color, which is what [Matching the launch background](#matching-the-launch-background) recommends, that color takes precedence over this artwork on Android &lt;12. Drop the `windowBackground` item if you want the image to show instead.
:::

## The iOS launch screen and LaunchLogo.png

Titanium builds `LaunchLogo.imageset` itself on every iOS build, resizing one source into the five sizes it needs. It looks for `LaunchLogo.png` first and falls back to `DefaultIcon.png`.

So there is nothing else for PurgeTSS to generate there, but there is something to choose. With only `DefaultIcon.png` around, the launch screen shows your app icon with its configured icon inset (`0%` by default). Dropping a `logo-launch.svg` (or `.png`) into `purgetss/brand/` makes `brand` write a `LaunchLogo.png`, and the launch screen shows the full logotype instead:

```bash
> cp docs/my-wordmark.svg purgetss/brand/logo-launch.svg
> purgetss brand
```

The file is written at exactly 1024×1024. That is not a style choice: the SDK validates the size and discards the file with a warning when it does not match.

The piece activates by convention, the presence of `logo-launch.*`, rather than through a flag, because `--<piece>-logo` already means "the source for this piece" everywhere else in the command. To generate it from a path without adding the file:

```bash
> purgetss brand --launch-logo docs/my-wordmark.svg
> purgetss brand --only launch-logo
> purgetss brand --launch-logo-padding 18   # more breathing room around the logotype
```

The output keeps its alpha, so the storyboard's `<default-background-color>` shows through.

## iPhone launch images

The Alloy and Classic templates ship 16 `Default*.png` launch images under `assets/iphone/`, from the 320×480 original iPhone size up to 2688×1242. `brand` regenerates all of them, scaling the logo against the shorter side of each canvas so portrait and landscape carry the same visual weight.

With `<enable-launch-screen-storyboard>` enabled, the default, iOS never reads these files. They are regenerated because they are in your project, they ship with the Alloy logo, and no one should have to audit their `tiapp.xml` to know whether that matters. If you are sure your project doesn't need them, [`--cleanup-legacy`](#cleanup-legacy-branding-artifacts) deletes them instead.

## iOS 18+ Dark and Tinted variants

iOS 18 added two appearance variants on top of the standard app icon: Dark (for the dark appearance of the Home Screen) and Tinted (for the user-accent-colored mode).

The `brand` command generates both by default:

- `DefaultIcon-Dark.png`: 1024×1024, transparent by default per Apple HIG. The system paints its own dark gradient behind the icon at render time. Override with `--dark-bg-color <hex>` to bake in an opaque dark tint instead.
- `DefaultIcon-Tinted.png`: 1024×1024, grayscale on black (`#000000`) per Apple HIG. iOS composites its own gradient background and multiplies the luminance by the user-selected accent color at render time.

### Skipping Dark or Tinted

```bash
> purgetss brand --no-dark
> purgetss brand --no-tinted
> purgetss brand --no-dark --no-tinted
```

### Titanium SDK wiring status

As of April 2026, Titanium SDK picks up `DefaultIcon-ios.png` automatically but does not yet wire `DefaultIcon-Dark.png` / `DefaultIcon-Tinted.png` into the generated iOS appiconset. Upstream tracking: [tidev/titanium-sdk#14122](https://github.com/tidev/titanium-sdk/issues/14122).

Until that PR lands, after your first iOS build you may need to add the two PNGs manually into `build/iphone/Assets.xcassets/AppIcon.appiconset/` in Xcode (via the "Appearance" column in the asset catalog editor). Once #14122 merges, the command becomes fully end-to-end.

## Brand color

The `--bg-color` flag (or `brand.background` in config) controls the background across the generated branding assets:

1. The Android adaptive background layer: a solid color that fills the full 108dp canvas behind your logo.
2. The iOS alpha flatten for `DefaultIcon-ios.png`. Apple rejects transparent App Store icons, so the logo is flattened on this color.
3. The marketplace flatten for `iTunesConnect.png`, `MarketplaceArtwork.png`, and `MarketplaceArtworkFeature.png`.
4. Every splash canvas: `default.png`, the 11 `res-*/default.png`, and the 16 iPhone launch images.
5. `appicon.png`.

```bash
> purgetss brand --bg-color "#0B1326"
```

Any piece can opt out with its own `background`. That is what `dark: { background: null }` does in the default config, which keeps `DefaultIcon-Dark.png` transparent per Apple HIG.

If you never pass the flag, background stays `#FFFFFF`. `iTunesConnect.png` and `MarketplaceArtwork.png` keep their alpha channel to match Titanium's default; `MarketplaceArtworkFeature.png` is always flattened for Google Play.

### Matching the launch background

`brand.background` changes the generated image pixels. It does not edit `tiapp.xml` or an Android theme. To carry the same color into the native launch surfaces, copy the configured value into the platform settings below.

On iOS, set the background of the LaunchScreen generated by Titanium under `<ios>` in `tiapp.xml`:

`./tiapp.xml`
```xml
<ios>
  <enable-launch-screen-storyboard>true</enable-launch-screen-storyboard>
  <default-background-color>#0B1326</default-background-color>
</ios>
```

On Android, keep the app's existing theme unchanged. Titanium assigns `Theme.Titanium` directly to the generated launcher Activity, so values added only to the `<application>` theme can be superseded by Titanium's API-specific splash theme. Put the launch colors in a dedicated child theme instead.

For an Alloy project, create this file:

`app/platform/android/res/values/splashscreen.xml`
```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <!-- Change only this line when the brand background changes. -->
  <color name="splashscreen_background">#0B1326</color>

  <style name="Theme.SplashScreen" parent="@style/Theme.Titanium">
    <!-- Android 12+ system splash -->
    <item name="android:windowSplashScreenBackground">@color/splashscreen_background</item>

    <!-- Native window before Titanium draws the first Window -->
    <item name="android:windowBackground">@color/splashscreen_background</item>

    <!-- Background attribute referenced by Titanium's base splash theme -->
    <item name="android:colorBackground">@color/splashscreen_background</item>
  </style>
</resources>
```

Classic projects use `platform/android/res/values/splashscreen.xml` instead. The filename is only organizational. `Theme.SplashScreen` and `splashscreen_background` are fixed, generic resource names in this example, not placeholders derived from the app name. You can paste them unchanged; when the brand color changes, edit only the `<color>` line.

There is one color resource but three theme attributes because each attribute has a different consumer:

- `android:windowSplashScreenBackground` directly colors the Android 12+ system splash.
- `android:windowBackground` colors the native launch window before Titanium renders its first Window and covers the legacy launch path.
- `android:colorBackground` supplies the general background attribute used by Titanium 13.4's base splash theme. `Base.Theme.Titanium.Splash` uses it as the inherited Android 12+ splash color when no direct override exists, and for the launch status and navigation bars.

These are not three different colors or three separate splash screens. Pointing all three attributes to `@color/splashscreen_background` keeps the launch surfaces synchronized while leaving one line to maintain.

`Theme.Titanium` is not the universal base theme for every Titanium Window. It is the theme Titanium assigns to the launcher Activity. During the Android build, Titanium connects it back to the theme already selected on `<application>` through this generated inheritance chain:

```text
Theme.SplashScreen
└── Theme.Titanium
    └── Base.Theme.Titanium.Splash
        └── Theme.AppDerived
            └── existing <application> theme
```

That means an application theme such as `Theme.Titanium.DayNight.Solid.Fullscreen` remains in effect; the dedicated child only overrides the launch backgrounds. Assign `Theme.SplashScreen` to the launcher Activity, not to `<application>` or regular Titanium Windows.

If `brand.splashIcon.enabled` is `true` and you want the generated `splash_icon.png` instead of the launcher icon, add this item to the same style:

```xml
<item name="android:windowSplashScreenAnimatedIcon">@drawable/splash_icon</item>
```

Then assign the dedicated theme only to Titanium's launcher Activity in `tiapp.xml`:

`./tiapp.xml`
```xml
<android xmlns:android="http://schemas.android.com/apk/res/android">
  <manifest>
    <application android:theme="@style/YourExistingTheme">
      <activity
          android:name=".YourAppActivity"
          android:theme="@style/Theme.SplashScreen"/>
    </application>
  </manifest>
</android>
```

Keep every existing `<application>` attribute, including its `android:theme`. If `<application/>` is self-closing, expand it before adding the Activity child. Replace `.YourAppActivity` with the real launcher Activity name; for example, an app named `My App` normally generates `.MyAppActivity`.

This does not create another Java Activity. The entry is a manifest override for the Activity Titanium already generates. If that Activity is already declared in `tiapp.xml` for orientation, deep links, or another setting, add `android:theme` to the existing element instead of pasting a duplicate declaration.

> 🚨 **WARNING**
>
> Existing launcher Activity theme
> 
> If the launcher Activity already has its own custom `android:theme`, do not replace it blindly. Either make `Theme.SplashScreen` inherit from that Activity theme, or merge the splash items into that launcher-only theme. This exception is different from modifying the general `<application>` theme.


`<resources>` cannot live inside `tiapp.xml`: Titanium merges the `<android><manifest>` subtree into `AndroidManifest.xml`, while Android compiles files under `res/values/` into a separate resource table.

:::note Android versions below 12

A solid `android:windowBackground` takes precedence over `default.png`, the `res-*/default.png` set, `background.png`, or `background.9.png` during the legacy launch path. `brand` regenerates that artwork with your logo on every run, so if you want it to show, use a drawable or layer-list for `windowBackground` instead of a plain color, or drop the `windowBackground` item altogether.
:::

Run `purgetss brand --notes` after generating the assets to print the complete resource file and manifest override with the project's current `brand.background` value. PurgeTSS only prints the guidance and never edits either platform configuration automatically.

## Padding guidance

Padding belongs to the piece, not to the project. Each piece has its own key and its own flag:

| Piece             | Config                     | Flag                          | Default |
| ----------------- | -------------------------- | ----------------------------- | ------- |
| `adaptive`        | `brand.adaptive.padding`   | `--android-adaptive-padding`  | `18%`   |
| `legacy-icon`     | `brand.legacyIcon.padding` | `--android-legacy-padding`    | `10%`   |
| `appicon`         | `brand.appicon.padding`    | `--appicon-padding`           | `10%`   |
| `icon`            | `brand.icon.padding`       | `--ios-padding`               | `0%`    |
| `feature-graphic` | `brand.featureGraphic.padding` | `--feature-graphic-padding` | `12%` |
| `launch-logo`     | `brand.launchLogo.padding` | `--launch-logo-padding`       | `12%`   |
| `android-splash`  | `brand.androidSplash.padding` | `--android-splash-padding` | `26%`   |
| `ios-splash`      | `brand.iosSplash.padding`  | `--ios-splash-padding`        | `26%`   |

`--padding` is a shortcut for the two Android launcher paddings in a single run, and `--splash-padding` for the two splash paddings. `--ios-padding` moves the four square iOS/marketplace pieces together (`icon`, `dark`, `tinted`, `marketplace`); in config each of them has its own key.

There is deliberately **no global padding value that cascades down**. The defaults answer to different constraints: `18%` answers to the Android launcher mask, while finished iOS/store icon artwork stays full-bleed at `0%`. One inherited number would let an iOS-oriented value silently break the launcher mask. `background`, which has no such trap, is inherited from `brand.background`.

### How the source is read, and how sharp the output is

Two things are worth knowing about what happens to your `logo.svg` or `logo.png` before any padding is applied.

**The container is what counts, not the artwork's bounding box.** An SVG is read at its `viewBox`, a raster at its full canvas, and neither is trimmed to where the pixels actually are. So whatever margin a designer baked into the file **adds** to the padding configured per piece. A round logo exported inside a 2048×2048 PNG with 25% of its own air, generated at `adaptive: { padding: '18%' }`, ends up covering about 32% of the icon canvas, not 64%. If a mark comes out smaller than the numbers suggest, that is almost always why: crop the source or lower the padding.

**The masters are sized to the run.** The source is rasterized once into two intermediate masters, and every piece scales down from them, so their resolution is the ceiling on output sharpness. Rather than a fixed size, `brand` measures the largest number of pixels any selected piece will ask for and builds the masters at exactly that. A default run reports it:

```text
  • Masters at 1024 px — the largest any selected piece asks for
```

Lower a padding and the figure rises with it (`--splash-padding 4` needs 1413 px), so output never goes soft against a fixed ceiling. Every destination is a reduction or the same size, never an upscale.

The one case this cannot fix is a raster source that is simply too small: a 512-px PNG cannot produce a sharp 1024-px icon. Prefer SVG, or a PNG of at least 1024×1024.

### Splash padding

The 28 splash images (`default.png`, the 11 `res-*`, and the 16 iPhone launch images) share one rule: the logo is fitted into a square whose side is a share of the canvas's **shorter** side.

Measuring against the shorter side is what lets a single number work across canvases as different as 1440×2560 and 800×480: at 800×480 the limit comes from the height, at 240×400 from the width, and the logo keeps the same visual weight in portrait and in landscape.

| `androidSplash.padding` / `iosSplash.padding` | Logo | `default.png` (1440×2560) | `res-notlong-port-mdpi` (320×480) |
| --- | --- | --- | --- |
| `20%` | 60% of the shorter side | 864 px | 192 px |
| `26%` (default) | 48% | 691 px | 153 px |
| `30%` | 40% | 576 px | 128 px |
| `35%` | 30% | 432 px | 96 px |

The `26%` default is calibrated against the Titanium template itself: the Alloy logo in the stock `default.png` measures 665×488 px on a 1440×2560 canvas, so `26%` lands within 4% of the size Titanium ships.

Before v7.13.0 none of this was configurable: `default.png` used a hardcoded box of 72% × 26% of its own canvas, and the `res-*` set a separate hardcoded 60%. Two rules for the same piece, neither adjustable.

### Rounded splash artwork

The master logo stays square so iOS and Android can apply their own launcher-icon masks. Rounding is applied only after the splash artwork has been resized, immediately before it is composited onto the splash background.

`./purgetss/config.cjs`
```javascript
brand: {
  splashCornerRadius: '22%',
  iosSplash: {
    padding: '26%',
    // cornerRadius: '18%'
  },
  androidSplash: {
    padding: '26%',
    // cornerRadius: '26%'
  }
}
```

Values accept integers or percentage strings from `0` through `50`. The percentage is measured against the shorter side of the already-resized artwork: `0%` is a no-op, `50%` produces a circle for square art or a capsule for a wordmark. Existing configs without the property therefore keep their previous square output exactly.

Precedence is specific platform flag, shared flag, piece config, global config, then `0%`:

```text
--ios-splash-corner-radius / --android-splash-corner-radius
→ --splash-corner-radius
→ brand.iosSplash.cornerRadius / brand.androidSplash.cornerRadius
→ brand.splashCornerRadius
→ 0%
```

The mask covers the 16 iPhone `Default*.png` files, Android `default.png`, and its 11 `images/res-*/default.png` variants. It does not touch `DefaultIcon*`, marketplace artwork, `LaunchLogo.png`, adaptive or legacy launcher icons, Android 12+ `splash_icon.png`, or notification icons. Putting `cornerRadius` in any other piece is an error.

### Adaptive icon padding

Android's adaptive canvas is 108 dp. The mask leaves roughly 72 dp visible, and the **guaranteed** safe area is a 66 dp circle inscribed in it. What each padding means in those terms:

| Padding | Logo | vs. the 66 dp safe circle | vs. the ~72 dp the mask shows |
| ------- | ---- | ------------------------- | ----------------------------- |
| `15%`   | 75.6 dp | outside | **outside** — clipped on any launcher |
| `16%`   | 73.4 dp | outside | **outside** |
| `18%`   | 69.1 dp | corners outside | inside — **the default** |
| `19.44%` | 66.0 dp | exactly on it | inside |
| `20%`   | 64.8 dp | inside | inside — most conservative |

`18%` sits between the guaranteed circle and the mask edge: a logo that carries its own margin never reaches those corners, which is why it is a safe default in practice. Raise it to `20%` if your mark runs edge to edge and you see clipping on a circular launcher.

A useful visual check is the "corners" heuristic: imagine a circle inscribed in your 1024×1024 canvas with the given padding. If your logo's outermost corners fit inside that circle, you're safe on circular launchers (Pixel default, Oppo Android 15). If they poke out, they'll be clipped.

The official Android spec floor is `19.44%` (108dp canvas, 66dp inscribed safe-zone circle). That is the theoretical worst-case for aggressive adaptive masks, which is why the adaptive default now sits close to it.

### Legacy icon padding

Legacy `ic_launcher.png` does not go through the same adaptive mask, so it can usually run tighter. That is why the default for `brand.legacyIcon.padding` is `10%`.

## Cleanup legacy branding artifacts

Projects that predate Android adaptive icons (API 26+) or modern iOS launch storyboards often accumulate obsolete assets: `res-long-*/res-notlong-*` qualifiers dead since Android 3.0, legacy `Default-*.png` launch images ignored when the storyboard is enabled, pre-adaptive `appicon.png`, and similar files.

The `--cleanup-legacy` flag removes them with context-aware safety rules: it reads `tiapp.xml` to decide what is actually safe to delete for your project. Always preview first:

```bash
> purgetss brand --cleanup-legacy --dry-run
```

Review the output, then remove `--dry-run` to apply:

```bash
> purgetss brand --cleanup-legacy
```

Add `--aggressive` to also remove `ldpi` density folders (less than 1% of active Android devices globally in 2026):

```bash
> purgetss brand --cleanup-legacy --aggressive
```

> 🚨 **WARNING**
>
> Commit first
> `--cleanup-legacy` deletes files permanently. Commit your project to git before running without `--dry-run` so `git restore` is available as a rollback.


Files kept on purpose:

- `app/assets/android/default.png` in Alloy projects
- `Resources/android/default.png` in Classic projects

Those files are still valid Android &lt;12 splash artwork.

> ℹ️ **INFO**
>
> `--cleanup-legacy` never deletes what the same run just generated
> Three cleanup rules target files `brand` now regenerates by default: the `res-long-*`/`res-notlong-*` folders (`android-splash`), the iPhone `Default*.png` images (`ios-splash`) and `appicon.png` (`appicon`). When `--cleanup-legacy` runs as part of a generating pass, those paths are skipped and the run reports how many it spared:
> 
> ```text
> Keeping 28 path(s) this run just regenerated with your artwork.
> ```
> 
> To actually delete them, exclude the piece that writes them. For example, `purgetss brand --only android --cleanup-legacy` regenerates the Android icons while letting cleanup remove the iPhone launch images.


## Troubleshooting

### The icon looks cropped or cramped on my phone

Your adaptive foreground is probably landing too close to the launcher mask. Increase `--android-adaptive-padding`:

```bash
> purgetss brand --android-adaptive-padding 20
```

Or set it in the config:

```javascript
brand: {
  background: '#FFFFFF',      // inherited by pieces that use an opaque background canvas
  splashCornerRadius: '0%',   // rounded artwork on legacy iOS/Android splash screens (0-50)
  confirmOverwrites: true,    // prompt before overwriting files (set false to skip)
  optimize: false,            // true = quantize the generated PNGs to a palette (lossy, ~71% smaller)

  // One block per piece. Artwork comes from purgetss/brand/logo-<piece>.{svg,png};
  // these keys are for numbers, colors and activation. Padding is never inherited.
  // Only iosSplash/androidSplash accept cornerRadius; it overrides splashCornerRadius.
  // iOS/store icons are full-bleed by default; increase padding only for logo artwork.
  icon:             { padding: '0%' },    // DefaultIcon.png + DefaultIcon-ios.png
  dark:             { background: null }, // DefaultIcon-Dark.png
  tinted:           {},                   // DefaultIcon-Tinted.png
  iosSplash:        { padding: '26%' },   // assets/iphone/Default*.png × 16
  launchLogo:       { padding: '12%' },   // LaunchLogo.png (1024×1024)
  marketplace:      {},                   // iTunesConnect.png + MarketplaceArtwork.png
  featureGraphic:   { padding: '12%' },   // MarketplaceArtworkFeature.png (1024×500)
  adaptive:         { padding: '18%' },   // ic_launcher_{foreground,background,monochrome}.png × 5 + ic_launcher.xml
  legacyIcon:       { padding: '10%' },   // ic_launcher.png × 5
  appicon:          { padding: '10%' },   // appicon.png (128×128)
  androidSplash:    { padding: '26%' },   // assets/android/default.png + images/res-*/default.png × 11

  // Opt-in: inert until you edit the Android theme / FCM meta-data by hand.
  splashIcon:       { enabled: false },   // drawable-*/splash_icon.png × 5
  notificationIcon: { enabled: false },   // drawable-*/ic_stat_notify.png × 5
  ninePatch:        { enabled: false }    // background.9.png (not implemented yet)
}
```

### The icon looks tiny / lost in the middle

Adaptive padding is probably too generous. Lower it:

```bash
> purgetss brand --android-adaptive-padding 17
```

### A white frame appears around a dark icon

PurgeTSS does not require a white frame. Square iOS/store pieces now default to `0%`, so finished edge-to-edge icon artwork stays full-bleed. If an older or customized config still has `brand.icon.padding: '4%'`, change it to `0%` and regenerate.

Android launcher pieces intentionally retain safe-zone padding (`18%` adaptive, `10%` legacy/appicon). If the source PNG is already a finished opaque icon, that inset exposes `brand.background`. Set the inherited background once to a color that matches the artwork's perimeter, or use a transparent logo/mark as the Android source instead of the finished icon canvas:

```javascript
brand: {
  background: '#020109',
  icon: { padding: '0%' },
  adaptive: { logo: './purgetss/brand/logo-adaptive.png', padding: '18%' },
  legacyIcon: { padding: '10%' },
  appicon: { padding: '10%' }
}
```

When an opaque edge and a contrasting background would make this frame visible, `brand` prints a warning naming the affected pieces.

### The monochrome version looks like a white blob

Your colored logo likely has multi-color detail that does not survive automatic whitening. Provide a dedicated silhouette:

```bash
> cp docs/my-logo-mono.svg purgetss/brand/logo-mono.svg
> purgetss brand
```

### iOS rejects the app icon upload ("contains transparency")

Apple requires App Store icons to have no alpha channel. `DefaultIcon-ios.png` is always flattened on `brand.background` for that reason. If you edited the file manually and reintroduced alpha, re-run `purgetss brand`.

### The dark variant doesn't show on my iPhone

Dark variants require iOS 18+ and Titanium SDK automatic wiring (tracked upstream in [titanium-sdk#14122](https://github.com/tidev/titanium-sdk/issues/14122)). Until that PR merges, you may need to add `DefaultIcon-Dark.png` and `DefaultIcon-Tinted.png` manually into the Xcode appiconset after the first iOS build.

### I get "Input image exceeds pixel limit" on an SVG from Affinity / Illustrator

Affinity Designer and Adobe Illustrator often bake transforms into the exported SVG's `viewBox`, so the intrinsic dimensions can end up at something like `29559×13542 pt`. Rasterized at 1× density, that exceeds Sharp's pixel limit and the command crashes.

PurgeTSS checks the `viewBox` on every SVG. When either side is over 4096 pt, it prints a warning with the actual dimensions and switches to an adaptive density that caps the output pixel count regardless of input size. The warning tells you the source is oversized; the command still finishes.

If you want to clean up the source, re-export from the vector editor with a canvas-sized viewBox (`0 0 1024 1024`, for example). The rasterized output is identical either way, but a normalized viewBox keeps the SVG portable for other tools.

### I changed my bg color. Do I need to regenerate the Android densities too?

Yes. `brand.background` bakes into every Android background layer, every splash canvas, and the iOS flatten. Re-run:

```bash
> purgetss brand --bg-color "#NEW_COLOR"
```

All 5 Android densities, marketplace artwork, and iOS variants regenerate in one pass.
