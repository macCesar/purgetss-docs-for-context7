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

- **Utility-First Classes**: PurgeTSS ships with 21,000+ utility classes, so you get a lot of styling options out of the box.

- **Efficient style management**: It parses all XML files to create a clean `app.tss` containing only the classes used in your project, reducing size and improving performance.

- **Customization and JIT classes**: You can customize default classes via a config file and use JIT classes for arbitrary values inside views.

- **Icon fonts integration**: Use icon fonts such as Font Awesome, Material Icons, Material Symbols, and Framework7-Icons in Buttons and Labels.

- **`fonts.tss` generation**: The `build-fonts` command creates a `fonts.tss` file with class definitions and fontFamily selectors for regular and icon fonts, with simplified options for filenames and icon prefixes.

- **`shades` command**: Generate custom color shades from a hex color without external tools.

- **Animation module**: Apply basic 2D matrix animations or transformations to elements or arrays of elements.

- **Grid system**: A two-dimensional grid system to align and distribute elements within views.

In short, PurgeTSS keeps styling consistent and removes a lot of repetitive UI setup work.

## What's New in v7.4.0

**Bug fix and documentation improvements.** PurgeTSS v7.4.0 fixes a serialization bug in custom rules and improves the Animation module documentation.

### Fixed

- **`backgroundGradient.colors` serialization**: Custom classes using arrays of objects (e.g. gradient color stops) now serialize correctly in `utilities.tss`.

  Previously, defining `colors` as an array of `{ color, offset }` objects in `purgetss/config.cjs` produced broken output:
  ```
  colors: { 0: '[object Object]', 1: '[object Object]' }
  ```
  It now outputs the correct format:
  ```
  colors: [ { color: '#132C50', offset: 0 }, { color: '#0A1529', offset: 1 } ]
  ```
  This fix applies to any array of objects at any depth in your custom rules.

### Documentation

- Added full Animation Module reference to `README.md`: method table, callback event object, array animation patterns, and utility classes.
- Animation module docs updated with the enriched callback event object (`index`, `total`, `getTarget()`).

---

## What's New in v7.3.x

**File rename and improved error handling.** PurgeTSS v7.3 renames `tailwind.tss` to `utilities.tss` to reflect the project's standalone identity, and adds XML syntax validation to catch errors early.

### Breaking changes

- **File rename**: Output file is now `utilities.tss` instead of `tailwind.tss`
  - Generated file: `purgetss/styles/utilities.tss` (was `purgetss/styles/tailwind.tss`)
  - Distribution file: `dist/utilities.tss` (was `dist/tailwind.tss`)

### Major improvements

- **XML syntax validation**: Catches common Alloy XML malformations before processing
  - Detects missing opening `<` brackets (e.g., `Label id=` instead of `<Label id=`)
  - Shows detailed error messages with line numbers, context preview, and fix suggestions
  - Saves debugging time by catching errors early in the build process
- **Classic Titanium compatibility**: `deviceInfo()` function now works in both Alloy and Classic projects
  - Removed dependency on `Alloy.isTablet`/`Alloy.isHandheld`
  - Uses platform-based detection instead

### Migration guide

If you have references to `tailwind.tss` in your project, update them to `utilities.tss`:

```bash
# Update any custom scripts or paths
# From: purgetss/styles/tailwind.tss
# To:   purgetss/styles/utilities.tss
```

For most users, upgrading is straightforward:
```bash
npm install -g purgetss@latest
```

Key changes to note:
- Node.js 20 or higher is now required.
- FontAwesome 7: If you use FA7, PurgeTSS will automatically handle the new `--fa:` properties.
- VS Code extension: We recommend `KevinYouu.tailwind-raw-reorder-tw4` for better compatibility with modern Tailwind versions and XML reordering.
- Clean reinstall: If you run into issues, try `npm uninstall -g purgetss && npm install -g purgetss`.

## Documentation Structure

This repository contains the complete documentation for PurgeTSS, organized as follows:

### 📖 Core Documentation

- **[Installation](docs/installation.md)** - Getting started with PurgeTSS
- **[Commands](docs/commands.md)** - Complete CLI reference

### 🎨 Customization

- **[The Config File](docs/customization/1-configuring-guide.md)** - Configuration options and setup
- **[Custom Rules](docs/customization/2-custom-rules.md)** - Creating your own utility classes
- **[The `apply` Directive](docs/customization/3-the-apply-directive.md)** - Applying multiple classes at once
- **[The `opacity` Modifier](docs/customization/4-opacity.md)** - Working with opacity values
- **[Arbitrary Values](docs/customization/5-arbitrary-values.md)** - Just-in-time custom values
- **[Platform and Device Modifiers](docs/customization/6-platform-and-device-modifiers.md)** - Platform-specific styling
- **[Icon Fonts Libraries](docs/customization/7-icon-fonts-libraries.md)** - Working with icon fonts

### 🎬 Animation Module

- **[Introduction](docs/animation-module/1-introduction.md)** - Getting started with animations
- **[The `play` Method](docs/animation-module/2-the-play-method.md)** - Basic animation playback
- **[The `apply` Method](docs/animation-module/3-the-apply-method.md)** - Applying transformations
- **[The `open` and `close` Methods](docs/animation-module/4-the-open-close-methods.md)** - Show/hide animations
- **[The `draggable` Method](docs/animation-module/5-the-draggable-method.md)** - Drag and drop functionality
- **[Complex UI Elements](docs/animation-module/6-complex-ui-elements.md)** - Advanced animation techniques
- **[Available Utilities](docs/animation-module/7-available-utilities.md)** - Animation helper functions

### 📐 Layout System

- **[Grid System](docs/grid-system.md)** - Two-dimensional layout system

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
- **🔄 Up-to-date**: Reflects the latest PurgeTSS v7.1.x features and changes

## Purpose

This repository serves as a standalone documentation source that can be processed by AI systems, documentation tools, and search platforms like Context7 to make PurgeTSS knowledge more accessible to developers and AI assistants.

---

**PurgeTSS** aims to simplify the mobile app development process, offering tools and features that enhance productivity and creativity in designing user interfaces for Titanium applications.

*Built with ❤️ by [Código Móvil](https://codigomovil.mx)*
