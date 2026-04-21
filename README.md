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

**PurgeTSS** is a powerful toolkit designed to enhance the development of mobile applications using the **[Titanium framework](https://titaniumsdk.com)**. It introduces several key features to streamline the app development process, making it simpler and more efficient for developers.

It offers a range of tools such as customizable utility classes, support for icon fonts, a user-friendly Animation module, a straightforward grid system, and the added convenience of the `shades` command for crafting personalized colors.

With **PurgeTSS**, creating visually appealing and dynamic mobile apps becomes more accessible and efficient.

## Key Features

- **Utility-First Classes**: PurgeTSS ships with 23,300+ utility classes, so you get a lot of styling options out of the box.

- **Efficient style management**: It parses all XML files to create a clean `app.tss` containing only the classes used in your project, reducing size and improving performance.

- **Customization and JIT classes**: You can customize default classes via a config file and use JIT classes for arbitrary values inside views.

- **`extend` for Window, View, and ImageView**: Customize component defaults from `theme.extend` in `config.cjs`, with automatic platform resolution and property deduplication.

- **Icon fonts integration**: Use icon fonts such as Font Awesome, Material Icons, Material Symbols, and Framework7-Icons in Buttons and Labels.

- **`fonts.tss` generation**: The `build-fonts` command creates a `fonts.tss` file with class definitions and fontFamily selectors for regular and icon fonts, with simplified options for filenames and icon prefixes.

- **Default font families**: `font-sans`, `font-serif`, and `font-mono` classes generated automatically with platform-appropriate values.

- **`shades` command**: Generate custom color shades from a hex color without external tools.

- **`semantic` command**: Generate Titanium semantic colors (Light/Dark mode) into `app/assets/semantic.colors.json` — tonal palettes (11 shades with mirror inversion) or purpose-based single colors with optional alpha.

- **`brand` command**: Generate the complete Titanium branding set (launcher icons, adaptive icons, iOS 18+ Dark/Tinted, marketplace artwork, optional notification/splash) from logos auto-discovered in `./purgetss/brand/`.

- **`images` command**: Generate multi-density UI images (Android `res-*` densities + iPhone `@1x`/`@2x`/`@3x` scales) from sources in `./purgetss/images/`.

- **Appearance module**: Switch between Light/Dark/System modes with automatic persistence via `Appearance.init()`, `set()`, `get()`, and `toggle()`.

- **Animation module**: 2D matrix animations, draggable views with collision detection, sequential animations, position utilities, and declarative property inheritance.

- **Grid system**: A two-dimensional grid system to align and distribute elements within views.

In short, PurgeTSS keeps styling consistent and removes a lot of repetitive UI setup work.


## Documentation Structure

This repository contains the complete documentation for PurgeTSS, organized as follows:

### 📖 Core Documentation

- **[Installation](docs/Installation.md)** - Getting started with PurgeTSS
- **[Commands](docs/Commands.md)** - Complete CLI reference

### 🖼️ App Assets

- **[App icons and branding](docs/app-assets/1-app-icons-and-branding.md)** - Generate the complete Titanium branding set with the `brand` command
- **[Multi-density images](docs/app-assets/2-multi-density-images.md)** - Generate UI images for all Android densities and iPhone scales with the `images` command

### 🎨 Customization

- **[The Config File](docs/customization/1-configuring-guide.md)** - Configuration options and setup
- **[Custom Rules](docs/customization/2-custom-rules.md)** - Creating your own utility classes
- **[The `apply` Directive](docs/customization/3-the-apply-directive.md)** - Applying multiple classes at once
- **[The `opacity` Modifier](docs/customization/4-Opacity.md)** - Working with opacity values
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

### 📐 Layout System

- **[Grid System](docs/grid-system.md)** - Two-dimensional layout system


## Changelog

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
- **🔄 Up-to-date**: Reflects the latest PurgeTSS v7.6.x features and changes

## Purpose

This repository serves as a standalone documentation source that can be processed by AI systems, documentation tools, and search platforms like Context7 to make PurgeTSS knowledge more accessible to developers and AI assistants.

---

**PurgeTSS** aims to simplify the mobile app development process, offering tools and features that enhance productivity and creativity in designing user interfaces for Titanium applications.

*Built with ❤️ by [Código Móvil](https://codigomovil.mx)*
