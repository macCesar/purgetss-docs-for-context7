# PurgeTSS Documentation

<p align="center">
	<img src="https://codigomovil.mx/images/logotipo-purgetss-gris.svg" height="230" width="230" alt="PurgeTSS logo"/>
</p>

<div align="center">

![npm](https://img.shields.io/npm/dm/purgetss)
![npm](https://img.shields.io/npm/v/purgetss)
![NPM](https://img.shields.io/npm/l/purgetss)

</div>

## About PurgeTSS

PurgeTSS is a toolkit for building mobile apps with the [Titanium framework](https://titaniumsdk.com). It gives you utility classes, icon font support, an Animation module, a grid system, and a handful of commands (`shades`, `semantic`, `brand`, `images`) that take care of the repetitive parts of setting up an app.

The point: write less TSS by hand. If you spend a lot of time wiring up screens, PurgeTSS removes most of the boring parts.

## What it does

- 23,300+ utility classes for colors, spacing, typography, layout, and more.
- Parses your XML files and writes an `app.tss` containing only the classes you actually use.
- Configurable through `config.cjs`. You can also use arbitrary values inside views for one-off sizes and colors.
- `extend` for `Window`, `View`, and `ImageView`: set component defaults from `theme.extend` in `config.cjs`. Platform resolution and property deduplication are handled for you.
- Icon fonts work out of the box. Font Awesome, Material Icons, Material Symbols, and Framework7-Icons are usable directly in Buttons and Labels.
- `build-fonts` generates a `fonts.tss` with class definitions and `fontFamily` selectors for any font you drop into the fonts folder.
- Default font family classes (`font-sans`, `font-serif`, `font-mono`) are generated with platform-appropriate values.
- `shades` generates color palettes from a single hex value.
- `semantic` writes Titanium semantic colors (Light/Dark) to `app/assets/semantic.colors.json`. You can generate a tonal palette from one base color (11 shades with mirror inversion), or a single purpose-based color with explicit per-mode hex and optional alpha.
- `brand` generates the full Titanium branding set from logos in `./purgetss/brand/`: launcher icons, adaptive icons, iOS 18+ Dark/Tinted variants, marketplace artwork, and optional notification/splash. Use one logo for everything, or separate logos for the Android launcher and the Android 12+ splash. The legacy `default.png` splash is regenerated for older Android paths.
- `images` generates multi-density UI images (Android `res-*` densities and iPhone `@1x`/`@2x`/`@3x`) from sources in `./purgetss/images/`.
- Appearance module: switch between Light, Dark, and System modes with automatic persistence. Methods: `Appearance.init()`, `set()`, `get()`, `toggle()`.
- Animation module: 2D transforms, draggable views with collision detection, sequential animations, and position helpers.
- Grid system for arranging elements in rows and columns.


## Documentation Structure

This repository contains the complete documentation for PurgeTSS, organized as follows:

### 📖 Core Documentation

- **[Installation](docs/installation.md)** - Getting started with PurgeTSS
- **[Commands](docs/commands.md)** - Complete CLI reference

### 🖼️ App Assets

- **[App icons and branding](docs/app-assets/1-app-icons-and-branding.md)** - Generate the complete Titanium branding set with the `brand` command
- **[Multi-density images](docs/app-assets/2-multi-density-images.md)** - Generate UI images for all Android densities and iPhone scales with the `images` command

### 🎨 Customization

- **[The Config File](docs/customization/1-configuring-guide.md)** - Configuration options and setup
- **[Custom Rules](docs/customization/2-custom-rules.md)** - Creating your own utility classes
- **[The `apply` Directive](docs/customization/3-the-apply-directive.md)** - Applying multiple classes at once
- **[The `opacity` Modifier](docs/customization/4-opacity.md)** - Working with opacity values
- **[Arbitrary Values](docs/customization/5-arbitrary-values.md)** - Just-in-time custom values
- **[Platform and Device Modifiers](docs/customization/6-platform-and-device-modifiers.md)** - Platform-specific styling
- **[Icon Fonts Libraries](docs/customization/7-icon-fonts-libraries.md)** - Working with icon fonts

### 🎬 The UI Module

- **[Introduction](docs/purgetss-ui/1-introduction.md)** - Getting started with the UI Module
- **[The `play` Method](docs/purgetss-ui/2-the-play-method.md)** - Basic animation playback
- **[The `apply` Method](docs/purgetss-ui/3-the-apply-method.md)** - Applying transformations instantly
- **[The `open` and `close` Methods](docs/purgetss-ui/4-the-open-close-methods.md)** - Show/hide animations
- **[The `draggable` Method](docs/purgetss-ui/5-the-draggable-method.md)** - Drag and drop functionality
- **[Additional Methods](docs/purgetss-ui/6-additional-methods.md)** - `swap`, `sequence`, `shake`, `snapTo`, `reorder`, and more
- **[Complex UI Elements](docs/purgetss-ui/7-complex-ui-elements.md)** - Advanced animation techniques
- **[Available Utilities](docs/purgetss-ui/8-available-utilities.md)** - Animation helper utilities
- **[Implementation Rules](docs/purgetss-ui/9-implementation-rules.md)** - Patterns and conventions
- **[Appearance](docs/purgetss-ui/10-appearance.md)** - Light/Dark/System mode management

### ✨ Best Practices

- **[Appearance Setup](docs/best-practices/1-appearance-setup.md)** - Configure Light/Dark/System mode in your app
- **[Semantic Colors](docs/best-practices/2-semantic-colors.md)** - Generate and use Titanium semantic colors
- **[Large Titles on iOS](docs/best-practices/3-large-titles-on-ios.md)** - iOS-native large title patterns
- **[Values and Units](docs/best-practices/4-values-and-units.md)** - How PurgeTSS handles numeric values and units

### 📐 Layout System

- **[Grid System](docs/grid-system.md)** - Two-dimensional layout system


## Changelog

### v7.7.0

- **`brand` config was cleaned up before stabilizing** — branding settings now live under grouped sections: `brand.logos`, `brand.padding`, `brand.android`, `brand.ios`, and `brand.colors`.
- **Separate Android brand inputs** — `brand` can now use one logo for the general brand set, another for Android launcher icons, and another for Android 12+ splash artwork. Use `brand.logos.androidLauncher` / `--icon-logo` and `brand.logos.androidSplash` / `--splash-logo`, or drop `logo-icon.*` and `logo-splash.*` into `purgetss/brand/`.
- **Legacy Android splash fallback is back** — `purgetss brand` now regenerates `app/assets/android/default.png` in Alloy projects and `Resources/android/default.png` in Classic projects.
- **Safer cleanup** — `cleanup-legacy` no longer removes `default.png`, because that file can still matter on older Titanium Android splash paths.
- **Clearer branding docs** — the docs now explain what uses `ic_launcher`, what uses `splash_icon.png`, and what still falls back to `default.png`.

### v7.6.2

- **`semantic` command now works in Classic Titanium projects.** Previously aborted in Classic with a "not an Alloy project" error. The command now detects the layout and writes `semantic.colors.json` to the correct location per TiDev convention (`app/assets/` for Alloy, `Resources/` for Classic). Covers palette mode, fresh single mode, and the in-place shade-conflict update. Existing unrelated entries (default `backgroundColor` / `textColor` shipped with Classic templates) are preserved.
- Fixed a UX bug where Classic printed the error message followed by a palette preview, making it look like the command half-succeeded.

### v7.6.1

- **Confirmation prompt for destructive writes** in `brand` and `images` (`y` / `N` / `a` for "always"). Auto-skips when `stdin` is not a TTY (alloy.jmk hook, CI, pipes), when `-y` / `--yes` is passed, or when `PURGETSS_YES=1` is set. Pair with `confirmOverwrites: false` on the matching config section to silence permanently.
- **Disproportionate-viewBox warning** for SVG logos and images — detects viewBoxes above 4096 pt on any side (common in Affinity/Illustrator exports) and rasterizes with adaptive density to stay within Sharp's pixel budget.
- **Auto-created `purgetss/{fonts,brand,images}/` subfolders** on init — the directory structure is self-documenting from the first build.
- **Unified `::PurgeTSS::` output** — multi-line command output is now grouped under a single signed header with indented continuation lines (applies across `purge`, `fonts`, `icon-library`, `brand`, `images`, and most warnings).

### v7.6.0

- **`brand` command** — generate the complete Titanium branding set (launcher icons, adaptive icons, iOS 18+ Dark/Tinted, marketplace artwork, optional notification/splash) from logos auto-discovered in `./purgetss/brand/`. Works on Alloy and Classic projects.
- **`images` command** — generate multi-density UI images (Android `res-*` densities + iPhone `@1x`/`@2x`/`@3x` scales) from sources in `./purgetss/images/`. Subdirectories preserved, short-path scope targeting for re-processing individual files.
- **`brand:` and `images:` config sections** in `purgetss/config.cjs` — percentages can be written as `'15%'` strings for self-documenting clarity; plain numbers also accepted. Auto-injected into older configs on first run.
- **`semantic` command** — generate Titanium semantic colors (Light/Dark mode) into `app/assets/semantic.colors.json`. Two modes dispatched by `--single`: tonal **palette** (one base hex → 11 shades with mirror inversion + auto config mapping) and **single** purpose-based color (explicit per-mode hex + optional alpha; the JSON entry AND a class mapping in `config.cjs` are written in one shot). Smart in-place updates when a single name matches an existing palette shade.

### v7.5.3

- **Appearance module** — new `Appearance` export for Light/Dark/System mode switching with persistence. Methods: `init()`, `set(mode)`, `get()`, `toggle()`.
- **Default font family classes** — `font-sans`, `font-serif`, and `font-mono` generated automatically with platform-appropriate values.
- **XML validation** — detects illegal `--` inside XML comments during pre-validation.

### v7.5.0

- **`extend` support for Window, View, and ImageView** — customize component defaults from `theme.extend` in `config.cjs`.
- **Shorthand `apply`** — `{ apply: '...' }` is automatically normalized, so the `default:` wrapper is optional.
- **Property deduplication** — applied values win over static defaults instead of duplicating.
- **Automatic platform resolution** — classes inside `ios:`/`android:` blocks find their platform-specific version automatically.
- **Font Awesome 7.2.0.**
- Fixed: `extend.Window` silently ignored, duplicate `font` properties, array-type properties missing `[ ]` notation.

### v7.4.0

**Animation module expansion.** 9 new methods bring the module to 15 total: `transition`, `pulse`, `sequence`, `swap`, `shake`, `snapTo`, `reorder`, `undraggable`, `detectCollisions`.

- New utility classes: `snap-back`, `snap-center`, `snap-magnet`, `keep-z-index`.
- Delta-based drag for transformed views, position normalization, property inheritance from the Animation object.
- Fixed `backgroundGradient.colors` serialization for arrays of objects in custom rules.

### v7.3.0

- **BREAKING: `tailwind.tss` → `utilities.tss`** — renamed to reflect PurgeTSS's identity as a standalone toolkit.
- **XML syntax validation** — pre-validation for Alloy XML files with line numbers and fix suggestions.
- **Classic Titanium compatibility** — `deviceInfo()` works without Alloy dependencies.

### v7.2.7

- **Security fixes** — command injection in `glob`, prototype pollution in `js-yaml`.
- **Dependency cleanup** — reduces installation size by ~45MB, removed unused packages.
- **Titanium SDK 13.1.0.GA** — new utility classes for `navBarColor`, `forceBottomPosition`, `multipleWindows`.

### v7.2.6

- Updated Font Awesome to version 7.1.0.
- Simplified flag property names in utilities.tss.


## Links and Resources

- **Main Repository**: [https://github.com/macCesar/purgetss](https://github.com/macCesar/purgetss)
- **NPM Package**: [https://www.npmjs.com/package/purgetss](https://www.npmjs.com/package/purgetss)
- **Titanium SDK**: [https://titaniumsdk.com](https://titaniumsdk.com)
- **Example App**: [https://github.com/macCesar/tailwind.tss-sample-app](https://github.com/macCesar/tailwind.tss-sample-app)

## About This Repository

This documentation repository is designed to be:

- **📚 Comprehensive**: Complete coverage of all PurgeTSS features and capabilities
- **🔍 Searchable**: Optimized for AI/LLM processing and search systems
- **🎯 Self-contained**: All images and references are included within this repository
- **🔄 Up-to-date**: Reflects the latest PurgeTSS v7.7.x features and changes

## Purpose

This repository serves as a standalone documentation source that can be processed by AI systems, documentation tools, and search platforms like Context7 to make PurgeTSS knowledge more accessible to developers and AI assistants.

---

**PurgeTSS** aims to simplify the mobile app development process, offering tools and features that enhance productivity and creativity in designing user interfaces for Titanium applications.

*Built with ❤️ by [Código Móvil](https://codigomovil.mx)*
