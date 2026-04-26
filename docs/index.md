<p align="center">
	<img src="https://codigomovil.mx/images/logotipo-purgetss-gris.svg" height="230" width="230" alt="PurgeCSS logo"/>
</p>

<div align="center">

![npm](https://img.shields.io/npm/dm/purgetss)
![npm](https://img.shields.io/npm/v/purgetss)
![NPM](https://img.shields.io/npm/l/purgetss)

</div>

> ℹ️ **INFO**
>
> **PurgeTSS** is a toolkit for building mobile apps with the [Titanium framework](https://titaniumsdk.com). It adds practical utilities to speed up styling and reduce repeated setup work.
> 
> It includes utility classes, icon font support, an Animation module, a simple grid system, and the `shades` command for generating custom colors.
> 
> If you build UI-heavy screens, PurgeTSS helps you move faster without hand-writing long TSS files.


What it does:

- 23,300+ utility classes for colors, spacing, typography, layout, and more.
- Parses your XML files and writes an `app.tss` with only the classes you actually use.
- Customizable via `config.cjs`. Supports arbitrary values for one-off sizes and colors.
- Icon fonts: Font Awesome, Material Icons, Material Symbols, and Framework7-Icons in Buttons and Labels.
- `build-fonts` command generates `fonts.tss` with class definitions and `fontFamily` selectors for any font you drop in.
- `shades` command generates color palettes from a hex value.
- Animation module: 2D transforms, draggable views with collision detection, sequential animations, and position utilities.
- Grid system for aligning and distributing elements in rows and columns.

## Table of Contents

- [Installation](./Installation.md)
- [Commands](./Commands.md)
- App Assets
  - [App icons and branding](./app-assets/1-app-icons-and-branding.md)
  - [Multi-density images](./app-assets/2-multi-density-images.md)
- Customization
  - [The Config File](./customization/1-configuring-guide.md)
  - [Custom Rules](./customization/2-custom-rules.md)
  - [The `apply` Directive](./customization/3-the-apply-directive.md)
  - [The `opacity` Modifier](./customization/4-Opacity.md)
  - [Arbitrary Values](./customization/5-arbitrary-values.md)
  - [Platform and Device Modifiers](./customization/6-platform-and-device-modifiers.md)
  - [Icon Fonts Libraries](./customization/7-icon-fonts-libraries.md)
- The UI Module
  - [Introduction](./purgetss-ui/1-introduction.md)
  - [The `play` Method](./purgetss-ui/2-the-play-method.md)
  - [The `apply` Method](./purgetss-ui/3-the-apply-method.md)
  - [The `open` and `close` Methods](./purgetss-ui/4-the-open-close-methods.md)
  - [The `draggable` Method](./purgetss-ui/5-the-draggable-method.md)
  - [Complex UI Elements](./purgetss-ui/7-complex-ui-elements.md)
  - [Additional Methods](./purgetss-ui/6-additional-methods.md)
  - [Available Utilities](./purgetss-ui/8-available-utilities.md)
  - [Implementation Rules](./purgetss-ui/9-implementation-rules.md)
  - [Appearance](./purgetss-ui/10-appearance.md)
- Best Practices
  - [Appearance Setup](./best-practices/1-appearance-setup.md)
  - [Semantic Colors](./best-practices/2-semantic-colors.md)
  - [Large Titles on iOS](./best-practices/3-large-titles-on-ios.md)
- [Grid System](./grid-system.md)

---

## Changelog

### v7.7.0

- **`brand` config was cleaned up before stabilizing** — branding settings now live under grouped sections: `brand.logos`, `brand.padding`, `brand.android`, `brand.ios`, and `brand.colors`.
- **Separate Android brand inputs** — `brand` can now use one logo for the general brand set, another for Android launcher icons, and another for Android 12+ splash artwork. Use `brand.logos.androidLauncher` / `--icon-logo` and `brand.logos.androidSplash` / `--splash-logo`, or drop `logo-icon.*` and `logo-splash.*` into `purgetss/brand/`.
- **Legacy Android splash fallback is back** — `purgetss brand` now regenerates `app/assets/android/default.png` in Alloy projects and `Resources/android/default.png` in Classic projects.
- **Safer cleanup** — `cleanup-legacy` no longer removes `default.png`, because that file can still matter on older Titanium Android splash paths.
- **Clearer branding docs** — the docs now explain what uses `ic_launcher`, what uses `splash_icon.png`, and what still falls back to `default.png`.

### v7.6.2

- **`semantic` command now works in Classic Titanium projects** — writes to `Resources/semantic.colors.json` (Alloy keeps writing to `app/assets/semantic.colors.json` as before). Existing unrelated entries (the default `backgroundColor` / `textColor` that ship with Classic templates) are preserved in both project types. See [Semantic Colors](./best-practices/2-semantic-colors.md)
- Fixed a UX bug where the "not an Alloy project" error was immediately followed by the palette preview JSON, making it look like the command half-succeeded.

### v7.6.1

- **Confirmation prompt for destructive writes** in `brand` and `images` (`y` / `N` / `a` for "always"). Auto-skips when `stdin` is not a TTY (alloy.jmk hook, CI, pipes), when `-y` / `--yes` is passed, or when `PURGETSS_YES=1` is set. Pair with `confirmOverwrites: false` on the matching config section to silence permanently.
- **Disproportionate-viewBox warning** for SVG logos and images — detects viewBoxes above 4096 pt on any side (common in Affinity/Illustrator exports) and rasterizes with adaptive density to stay within Sharp's pixel budget.
- **Auto-created `purgetss/{fonts,brand,images}/` subfolders** on init — the directory structure is self-documenting from the first build.
- **Unified `::PurgeTSS::` output** — multi-line command output is now grouped under a single signed header with indented continuation lines (applies across `purge`, `fonts`, `icon-library`, `brand`, `images`, and most warnings).

### v7.6.0

- **`brand` command** — generate the complete Titanium branding set (launcher icons, adaptive icons, iOS 18+ Dark/Tinted, marketplace artwork, optional notification/splash) from logos auto-discovered in `./purgetss/brand/`. Works on Alloy and Classic projects. See [App icons and branding](./app-assets/1-app-icons-and-branding.md)
- **`images` command** — generate multi-density UI images (Android `res-*` densities + iPhone `@1x`/`@2x`/`@3x` scales) from sources in `./purgetss/images/`. Subdirectories preserved, short-path scope targeting for re-processing individual files. See [Multi-density images](./app-assets/2-multi-density-images.md)
- **`brand:` and `images:` config sections** in `purgetss/config.cjs` — percentages can be written as `'15%'` strings for self-documenting clarity; plain numbers also accepted. Auto-injected into older configs on first run.
- **`semantic` command** — generate Titanium semantic colors (Light/Dark mode) into `app/assets/semantic.colors.json`. Two modes dispatched by `--single`: tonal **palette** (one base hex → 11 shades with mirror inversion + auto config mapping) and **single** purpose-based color (explicit per-mode hex + optional alpha; the JSON entry AND a class mapping in `config.cjs` are written in one shot — class name auto-derived by stripping the `Color` suffix, e.g. `surfaceColor` → class `surface`). Smart in-place updates when a single name matches an existing palette shade. See [Semantic Colors — Generating semantic colors with the `semantic` command](./best-practices/2-semantic-colors.md#generating-semantic-colors-with-the-semantic-command)

### v7.5.3

- **Appearance module** — new `Appearance` export for Light/Dark/System mode switching with persistence. Methods: `init()`, `set(mode)`, `get()`, `toggle()`. See [Appearance Setup](./best-practices/1-appearance-setup.md)
- **Default font family classes** — `font-sans`, `font-serif`, and `font-mono` generated automatically with platform-appropriate values
- **XML validation** — detects illegal `--` inside XML comments during pre-validation

### v7.5.0

- **`extend` support for Window, View, and ImageView** — customize component defaults from `theme.extend` in `config.cjs`
- **Shorthand `apply`** — `{ apply: '...' }` is automatically normalized, so the `default:` wrapper is optional
- **Property deduplication** — applied values win over static defaults instead of duplicating
- **Automatic platform resolution** — classes inside `ios:`/`android:` blocks find their platform-specific version automatically
- **Font Awesome 7.2.0**
- Fixed: `extend.Window` silently ignored, duplicate `font` properties, array-type properties missing `[ ]` notation

### v7.4.0

**Animation module expansion.** 9 new methods bring the module to 15 total:

- `transition`, `pulse`, `sequence`, `swap`, `shake`, `snapTo`, `reorder`, `undraggable`, `detectCollisions`
- New utility classes: `snap-back`, `snap-center`, `snap-magnet`, `keep-z-index`
- Delta-based drag for transformed views, position normalization, property inheritance from the Animation object

See the [UI Module documentation](./purgetss-ui/1-introduction.md) for full details.

### v7.3.0

- **BREAKING: `tailwind.tss` → `utilities.tss`** — renamed to reflect PurgeTSS's identity as a standalone toolkit
- **XML syntax validation** — pre-validation for Alloy XML files with line numbers and fix suggestions
- **Classic Titanium compatibility** — `deviceInfo()` works without Alloy dependencies

### v7.2.7

- **Security fixes** — command injection in `glob`, prototype pollution in `js-yaml`
- **Dependency cleanup** — reduces installation size by ~45MB, removed unused packages
- **Titanium SDK 13.1.0.GA** — new utility classes for `navBarColor`, `forceBottomPosition`, `multipleWindows`

### v7.2.6

- Updated Font Awesome to version 7.1.0
- Simplified flag property names in utilities.tss
