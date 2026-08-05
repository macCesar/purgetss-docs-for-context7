# App icons and branding

> ℹ️ **INFO**
>
> The `brand` command at a glance
> `purgetss brand` generates launcher icons, adaptive icons, iOS 18+ Dark/Tinted variants, marketplace artwork, and optional notification/splash icons from one main SVG or PNG logo, with optional Android-specific overrides when you need them.
> 
> Works on Alloy and Classic projects. The layout is detected automatically.


This guide covers the `brand` workflow: setting up `purgetss/brand/`, tuning padding for your logo, handling dark mode on iOS 18+ and Android 13+, and fixing common rebuild issues.

For a terse reference of every flag, see the [`brand` command reference](../commands#brand-command).

## Quick start

Drop a logo file into `purgetss/brand/`, then run the command. That is the core workflow.

```bash
> mkdir -p purgetss/brand # if the folder doesn't exist yet
> cp docs/my-logo.svg purgetss/brand/logo.svg

> purgetss brand
```

On the first run, the command:

1. Creates the `brand:` section in `purgetss/config.cjs` with sensible defaults (if missing).
2. Generates every branding file directly into the project (in-place).
3. Prints a compact summary of what was written.

Pass `--dry-run` to preview without writing any files:

```bash
> purgetss brand --dry-run
```

## The `purgetss/brand/` convention

`init` creates `purgetss/brand/` (alongside `fonts/` and `images/`) so the folder is already there the first time you look for it, even before you've dropped in a logo.

PurgeTSS auto-discovers logo files under this folder, the same way `purgetss/fonts/` works for fonts. Drop files with these names and you're done:

`./purgetss/brand/`
```text
purgetss/brand/
├── logo.svg              required - main logo (or logo.png)
├── logo-icon.svg         optional - square Android launcher mark
├── logo-mono.svg         optional - monochrome layer + notifications
├── logo-dark.svg         optional - iOS 18+ dark variant
├── logo-splash.svg       optional - Android 12+ splash icon override
├── logo-tinted.svg       optional - iOS 18+ tinted variant
└── logo-feature.svg      optional - Google Play Feature Graphic override
```

Only `logo.svg` (or `logo.png`) is required. Everything else is optional:

- `logo-icon`: a separate square mark for Android launcher icons. Use this when your main logo is a horizontal wordmark, a vertical lockup, or anything else that looks fine in a 1024×1024 branding canvas but feels cramped inside an Android launcher mask.
- `logo-splash`: alternate artwork for Android 12+ `splash_icon.png`. Use this when the splash should use a different composition than the launcher icon. PurgeTSS generates the file, but Titanium still needs a custom Android splash theme if you want the system splash to use it instead of `ic_launcher`.

- `logo-mono`: silhouette used for the Android adaptive monochrome layer (themed icons on Android 13+) and for notification icons. When omitted, `brand` whitens the main logo automatically. Provide your own when the colored logo has detail that would collapse under automatic whitening. A painter's palette with colored dots is a good example: the monochrome version should have cutouts instead.
- `logo-dark`: alternate logo for iOS 18+ dark mode. When omitted, the dark variant comes from the main logo with a transparent background (Apple's recommended approach). Provide your own when dark-mode brand guidelines use a different lockup or color treatment.
- `logo-tinted`: alternate logo for iOS 18+ tinted mode. When omitted, the tinted variant comes from a grayscale of the main logo. Provide your own when you want a simpler silhouette that tints better than a grayscale of the colored version.
- `logo-feature`: alternate logo for the Google Play Feature Graphic (1024×500 banner). When omitted, the main logo is centered inside the banner with the configured vertical padding. Provide your own when you want a different composition for the Play Store listing — for example, a logo-plus-tagline lockup or a wider artwork that takes advantage of the rectangular canvas instead of being constrained to the centered square.

> 💡 **TIP**
>
> Prefer SVG for the master
> SVG scales cleanly to every density Sharp needs to emit. A single `logo.svg` can be rasterized at every `res-*dpi` output. PNG masters should be at least 1024×1024 to avoid upscaling artifacts.


### Overriding auto-discovery

You can also pass a path directly or point to a logo from the config. Useful when your masters live in `docs/` or another workflow folder:

```bash
> purgetss brand ./docs/snap-logo.svg
```

Or in `purgetss/config.cjs`:

`./purgetss/config.cjs`
```javascript
brand: {
  logos: {
    primary: './docs/snap-logo.svg',
    androidLauncher: './docs/snap-app-icon.svg',
    monochrome: './docs/snap-logo-mono.svg'
  }
}
```

CLI flags override config values, and config values override auto-discovery.

## The `brand:` config section

On the first run, `purgetss brand` adds a `brand:` block to your existing `purgetss/config.cjs` between `purge:` and `theme:`:

`./purgetss/config.cjs`
```javascript
brand: {
  logos: {},  // empty = auto-discovers from purgetss/brand/
  padding: {
    ios: '4%',
    androidLegacy: '10%',
    androidAdaptive: '19%',
    featureGraphic: '12%'
  },
  android: {
    splash: false,
    notification: false
  },
  ios: {
    dark: true,
    tinted: true,
    darkBackground: null  // null = transparent per Apple HIG
  },
  colors: {
    background: '#FFFFFF'
  },
  confirmOverwrites: true  // prompt before overwriting files (set false to skip)
}
```

Change only what you want to keep as a project default. CLI flags still win for one-off runs.

### Overriding logo paths

By default, PurgeTSS auto-discovers logo files from `purgetss/brand/`. If you want to use custom paths, add them to `brand.logos`:

`Example: Custom logo paths`
```javascript
brand: {
  logos: {
    primary: './my-logos/main.svg',         // overrides auto-discovered logo.svg
    androidLauncher: './my-logos/icon.svg', // overrides auto-discovered logo-icon.svg
    androidSplash: './my-logos/splash.svg', // overrides auto-discovered logo-splash.svg
    monochrome: './my-logos/mono.svg',      // overrides auto-discovered logo-mono.svg
    iosDark: './my-logos/dark.svg',         // overrides auto-discovered logo-dark.svg
    iosTinted: './my-logos/tinted.svg',     // overrides auto-discovered logo-tinted.svg
    featureGraphic: './my-logos/feature.svg' // overrides auto-discovered logo-feature.svg
  }
}
```

You only need to override the ones you're using. Missing overrides still auto-discover from `purgetss/brand/`.

If `logo.svg` is a wide wordmark, leave it as the main brand source and set `logos.androidLauncher` to a square mark for Android. That gives you cleaner launcher results without changing the iOS or marketplace outputs.

## Brand config reference

This is the reference for the `brand:` section in `purgetss/config.cjs`.

### `brand.logos`

All `logos.*` keys are optional path overrides. If you omit them, PurgeTSS auto-discovers files from `purgetss/brand/`.

- `logos.primary`
  Main brand source. Override for `purgetss/brand/logo.svg`.
- `logos.androidLauncher`
  Android launcher source. Override for `purgetss/brand/logo-icon.svg`.
  Use this when the main logo is a wordmark or any non-square lockup.
- `logos.androidSplash`
  Android 12+ splash source. Override for `purgetss/brand/logo-splash.svg`.
  Useful when the splash should use a different composition than the launcher icon.
- `logos.monochrome`
  Monochrome silhouette source. Override for `purgetss/brand/logo-mono.svg`.
  Used for Android themed icons and notification icons.
- `logos.iosDark`
  iOS dark variant source. Override for `purgetss/brand/logo-dark.svg`.
- `logos.iosTinted`
  iOS tinted variant source. Override for `purgetss/brand/logo-tinted.svg`.
- `logos.featureGraphic`
  Google Play Feature Graphic source. Override for `purgetss/brand/logo-feature.svg`.
  Useful when the Play Store banner should use a different composition than the main app icon (e.g. a logo + tagline lockup that fills the wider 1024×500 canvas).

### `brand.padding`

All padding values accept either numbers or percentage strings like `'19%'`.

- `padding.ios`
  Default: `4%`.
  Controls the visual inset for `DefaultIcon-ios.png`, `DefaultIcon-Dark.png`, `DefaultIcon-Tinted.png`, and marketplace artwork.
- `padding.androidLegacy`
  Default: `10%`.
  Controls the visual inset for legacy `ic_launcher.png`.
- `padding.androidAdaptive`
  Default: `19%`.
  Controls the visual inset for adaptive Android foreground assets such as `ic_launcher_foreground.png`.
  This is the setting to adjust first when the installed icon looks cropped inside launcher masks.
- `padding.featureGraphic`
  Default: `12%`.
  Vertical padding (top + bottom) for `MarketplaceArtworkFeature.png` (1024×500 Google Play banner). The logo is rendered as a square block of side `500 - 2 × pad` centered horizontally and vertically. Lower this for a more impactful banner; raise it if the logo looks cramped against the top or bottom edge on smaller Play Store crops.

### `brand.android`

- `android.splash`
  Default: `false`.
  When `true`, also generates `drawable-*/splash_icon.png`.
- `android.notification`
  Default: `false`.
  When `true`, also generates `drawable-*/ic_stat_notify.png`.

### `brand.ios`

This section is optional. If you leave it out, PurgeTSS behaves as if:

- `ios.dark = true`
- `ios.tinted = true`
- `ios.darkBackground = null`

Properties:

- `ios.dark`
  Default: `true`.
  Set to `false` to skip `DefaultIcon-Dark.png`.
- `ios.tinted`
  Default: `true`.
  Set to `false` to skip `DefaultIcon-Tinted.png`.
- `ios.darkBackground`
  Default: `null`.
  When `null`, `DefaultIcon-Dark.png` stays transparent per Apple HIG.
  Set a hex color if you want an opaque dark background baked into that asset.

### `brand.colors`

- `colors.background`
  Default: `#FFFFFF`.
  Shared background color used for the Android adaptive background layer, `DefaultIcon-ios.png`, and marketplace flattening when a background color is explicitly applied.

### `brand.confirmOverwrites`

- `confirmOverwrites`
  Default: `true`.
  Controls whether `brand` asks before overwriting project files in place.

### Upgrading from pre-7.7.0 configs

In v7.7.0, the `brand:` block was reorganized into grouped subsections (`logos`, `padding`, `android`, `ios`, `colors`). If your `config.cjs` still uses the original flat layout, PurgeTSS v7.10.2+ auto-migrates it in memory on every run — your old config keeps working without any change on disk.

The mapping is:

| Pre-7.7.0 flat key                              | Current grouped key                                                                              |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| `brand.padding: <number\|string>` (single value)| `brand.padding.androidLegacy` **and** `brand.padding.androidAdaptive` (same value applied to both)|
| `brand.iosPadding`                              | `brand.padding.ios`                                                                              |
| `brand.bgColor`                                 | `brand.colors.background`                                                                        |
| `brand.darkBgColor`                             | `brand.ios.darkBackground`                                                                       |
| `brand.notification`                            | `brand.android.notification`                                                                     |
| `brand.splash`                                  | `brand.android.splash`                                                                           |

If both legacy and grouped keys are present for the same property, the grouped key wins.

On first encounter per session, PurgeTSS prints a one-time deprecation notice listing the legacy keys it migrated:

```text
::PurgeTSS:: Legacy brand: schema detected in purgetss/config.cjs — auto-migrated in memory:
     • brand.padding: 15 → brand.padding.androidLegacy + brand.padding.androidAdaptive
     • brand.iosPadding → brand.padding.ios
     • brand.bgColor → brand.colors.background
     Update purgetss/config.cjs to the new grouped schema to silence this warning.
```

The notice is suppressed once you update the file to the grouped layout. Auto-migration is purely transitional and may be removed in a future major version, so a one-time update to your `config.cjs` is recommended.

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
├── DefaultIcon.png                 <- 1024×1024, universal fallback (Android-safe padding)
├── DefaultIcon-ios.png             <- 1024×1024, iOS flattened on brand.colors.background
├── DefaultIcon-Dark.png            <- 1024×1024, iOS 18+ dark (transparent per Apple HIG)
├── DefaultIcon-Tinted.png          <- 1024×1024, iOS 18+ tinted (grayscale on black)
├── iTunesConnect.png               <- 1024×1024, App Store submission
├── MarketplaceArtwork.png          <- 512×512, Google Play submission
├── MarketplaceArtworkFeature.png   <- 1024×500, Google Play Feature Graphic
└── app/
    └── assets/android/
        ├── default.png             <- legacy Titanium Android splash fallback
        └── res/
            ├── mipmap-mdpi/        <- 108×108 foreground + background + monochrome + legacy
            ├── mipmap-hdpi/        <- 162×162
            ├── mipmap-xhdpi/       <- 216×216
            ├── mipmap-xxhdpi/      <- 324×324
            ├── mipmap-xxxhdpi/     <- 432×432
            ├── drawable-*/         <- optional splash_icon.png when --splash is enabled
            └── mipmap-anydpi-v26/
                └── ic_launcher.xml <- adaptive-icon binder
```

`Classic layout`
```text
<project>/
├── DefaultIcon.png  DefaultIcon-ios.png  ...     <- same root-level files as Alloy
├── MarketplaceArtworkFeature.png   <- 1024×500, Google Play Feature Graphic
├── Resources/
│   └── android/default.png         <- legacy Titanium Android splash fallback
└── platform/
    └── android/res/
        ├── mipmap-*/               <- same 5 densities as Alloy
        ├── drawable-*/             <- optional splash_icon.png when --splash is enabled
        └── mipmap-anydpi-v26/ic_launcher.xml
```

The Android outputs are related, but they are not interchangeable:

- `ic_launcher*` drives the app icon, and by default it also feeds the Android 12+ system splash
- `splash_icon.png` is only generated when you ask for it with `--splash`
- `default.png` is the older Titanium Android splash fallback

`brand` is aimed at the modern Titanium path: adaptive launcher icons, current iOS icon variants, and optional Android 12+ splash artwork. Older Android splash theme assets such as `background.png` or `background.9.png` are intentionally left out of the normal `brand` flow.

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

If you pass `--splash`, PurgeTSS generates `drawable-*/splash_icon.png` across Android densities.

```bash
> purgetss brand --splash
```

If you want that artwork to differ from the launcher icon, provide `logo-splash.svg` or set `brand.logos.androidSplash`.

`./purgetss/config.cjs`
```javascript
brand: {
  android: {
    splash: true
  },
  logos: {
    androidSplash: './docs/snap-splash-mark.svg'
  }
}
```

Important detail: generating `splash_icon.png` does not automatically switch Titanium to use it for the Android 12+ system splash. Titanium still needs a custom splash theme that points `android:windowSplashScreenAnimatedIcon` to `@drawable/splash_icon`. If you do nothing, Android will keep using `ic_launcher`.

Keep the theme already assigned to `<application>`. Define a launcher-only theme that inherits from `Theme.Titanium`, then assign it to Titanium's generated launcher Activity. The complete setup is in [Matching the launch background](#matching-the-launch-background).

Also, if you still see a brief flash during splash exit even with correct assets, do not assume the PNGs are wrong. That artifact can come from Titanium's splash theme or the system splash transition itself.

## Android legacy splash fallback

PurgeTSS now regenerates `app/assets/android/default.png` in Alloy projects and `Resources/android/default.png` in Classic projects.

That file still matters as a compatibility fallback on older Titanium Android splash paths, which is why `cleanup-legacy` no longer removes it.

That said, it is not the main modern splash strategy. If a project still depends on `background.png`, `background.9.png`, or a custom Android splash theme, keep managing that part manually.

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

The `--bg-color` flag (or `brand.colors.background` in config) controls the background across the generated branding assets:

1. The Android adaptive background layer: a solid color that fills the full 108dp canvas behind your logo.
2. The iOS alpha flatten for `DefaultIcon-ios.png`. Apple rejects transparent App Store icons, so the logo is flattened on this color.
3. The marketplace flatten for `iTunesConnect.png`, `MarketplaceArtwork.png`, and `MarketplaceArtworkFeature.png`.
4. The Android legacy `default.png` splash fallback.

```bash
> purgetss brand --bg-color "#0B1326"
```

If you never pass the flag, background stays `#FFFFFF`. `iTunesConnect.png` and `MarketplaceArtwork.png` keep their alpha channel to match Titanium's default; `MarketplaceArtworkFeature.png` is always flattened for Google Play.

### Matching the launch background

`brand.colors.background` changes the generated image pixels. It does not edit `tiapp.xml` or an Android theme. To carry the same color into the native launch surfaces, copy the configured value into the platform settings below.

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

If `brand.android.splash` is enabled and you want the generated `splash_icon.png` instead of the launcher icon, add this item to the same style:

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

A solid `android:windowBackground` takes precedence over `default.png`, `background.png`, or `background.9.png` during the legacy launch path. If the older splash must retain artwork, use a drawable or layer-list for `windowBackground` instead of a plain color.
:::

Run `purgetss brand --notes` after generating the assets to print the complete resource file and manifest override with the project's current `brand.colors.background` value. PurgeTSS only prints the guidance and never edits either platform configuration automatically.

## Padding guidance

PurgeTSS now treats Android adaptive and Android legacy launcher icons as two related but different cases:

- `brand.padding.androidAdaptive` or `--android-adaptive-padding` for the adaptive foreground
- `brand.padding.androidLegacy` or `--android-legacy-padding` for `ic_launcher.png`
- `--padding` as a shortcut when you want both Android paddings to match for a single run

The adaptive default is `19%`, which stays close to the Android safe-zone. The legacy default is `10%`, so the flat `ic_launcher.png` can keep a little more visual weight.

### Adaptive icon padding

| Padding | Logo fill | When to use                                                                       |
| ------- | --------- | --------------------------------------------------------------------------------- |
| `15%`   | 70%       | Aggressive. Better for square symbols with lots of built-in breathing room.       |
| `18%`   | 64%       | Defensive: for intricate logos, fine serifs, multi-element designs.               |
| `19%`   | 62%       | Default. Close to the Android safe-zone and safer for adaptive masks.            |
| `20%`   | 60%       | Conservative, spec-compliant. Safe on every launcher, including aggressive masks. |

A useful visual check is the "corners" heuristic: imagine a circle inscribed in your 1024×1024 canvas with the given padding. If your logo's outermost corners fit inside that circle, you're safe on circular launchers (Pixel default, Oppo Android 15). If they poke out, they'll be clipped.

The official Android spec floor is `19.44%` (108dp canvas, 66dp inscribed safe-zone circle). That is the theoretical worst-case for aggressive adaptive masks, which is why the adaptive default now sits close to it.

### Legacy icon padding

Legacy `ic_launcher.png` does not go through the same adaptive mask, so it can usually run tighter. That is why the default for `brand.padding.androidLegacy` is `10%`.

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

Those files are still valid legacy splash fallbacks.

## Troubleshooting

### The icon looks cropped or cramped on my phone

Your adaptive foreground is probably landing too close to the launcher mask. Increase `--android-adaptive-padding`:

```bash
> purgetss brand --android-adaptive-padding 20
```

Or set it in the config:

```javascript
brand: {
  padding: {
    androidAdaptive: '20%'
  }
}
```

### The icon looks tiny / lost in the middle

Adaptive padding is probably too generous. Lower it:

```bash
> purgetss brand --android-adaptive-padding 17
```

### The monochrome version looks like a white blob

Your colored logo likely has multi-color detail that does not survive automatic whitening. Provide a dedicated silhouette:

```bash
> cp docs/my-logo-mono.svg purgetss/brand/logo-mono.svg
> purgetss brand
```

### iOS rejects the app icon upload ("contains transparency")

Apple requires App Store icons to have no alpha channel. `DefaultIcon-ios.png` is always flattened on `brand.colors.background` for that reason. If you edited the file manually and reintroduced alpha, re-run `purgetss brand`.

### The dark variant doesn't show on my iPhone

Dark variants require iOS 18+ and Titanium SDK automatic wiring (tracked upstream in [titanium-sdk#14122](https://github.com/tidev/titanium-sdk/issues/14122)). Until that PR merges, you may need to add `DefaultIcon-Dark.png` and `DefaultIcon-Tinted.png` manually into the Xcode appiconset after the first iOS build.

### I get "Input image exceeds pixel limit" on an SVG from Affinity / Illustrator

Affinity Designer and Adobe Illustrator often bake transforms into the exported SVG's `viewBox`, so the intrinsic dimensions can end up at something like `29559×13542 pt`. Rasterized at 1× density, that exceeds Sharp's pixel limit and the command crashes.

PurgeTSS checks the `viewBox` on every SVG. When either side is over 4096 pt, it prints a warning with the actual dimensions and switches to an adaptive density that caps the output pixel count regardless of input size. The warning tells you the source is oversized; the command still finishes.

If you want to clean up the source, re-export from the vector editor with a canvas-sized viewBox (`0 0 1024 1024`, for example). The rasterized output is identical either way, but a normalized viewBox keeps the SVG portable for other tools.

### I changed my bg color. Do I need to regenerate the Android densities too?

Yes. `brand.colors.background` bakes into every Android background layer and the iOS flatten. Re-run:

```bash
> purgetss brand --bg-color "#NEW_COLOR"
```

All 5 Android densities, marketplace artwork, and iOS variants regenerate in one pass.
