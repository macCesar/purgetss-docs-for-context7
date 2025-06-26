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

- **🎨 Tailwind-like Utility Classes**: Offers over 21,000 utility classes similar to Tailwind CSS, providing a vast array of styling options for your projects.

- **⚡ Efficient Style Management**: Parses all your XML files to create a clean `app.tss` file, containing only the classes actually used in your project. This helps in reducing file size and improving performance.

- **🔧 Customization and JIT Classes**: Developers can customize default classes via a simple configuration file. It also supports just-in-time (JIT) classes, allowing the creation of arbitrary values within views for tailored styling.

- **🎯 Icon Fonts Integration**: Facilitates the use of popular icon fonts such as *Font Awesome*, *Material Icons*, *Material Symbols*, and *Framework7-Icons* in Buttons and Labels.

- **📝 fonts.tss Generation**: The `build-fonts` command creates a `fonts.tss` file with class definitions and fontFamily selectors for various font types. It supports both regular fonts and icon fonts, with simplified options for using filenames as class names and icon prefixes.

- **🌈 Shades Command**: Includes a `shades` command that enables developers to generate custom color shades from a specified hex color, eliminating the need for external tools.

- **🎬 Animation Module**: Comes with an Animation module to apply basic 2D Matrix animations or transformations to elements or arrays of elements.

- **📐 Grid System**: Includes a simple yet effective two-dimensional grid system to align and distribute elements within views.

## What's New in v7.1.x

**Major Refactoring & ESM Migration**: **PurgeTSS v7.1** has been completely refactored with improved code organization, better ESM compatibility, enhanced error handling, and a more intuitive CLI experience.

### ⚠️ Breaking Changes

- **Node.js 16+** required (ESM support)
- **Configuration file**: `config.js` → `config.cjs` (same content, different extension for CommonJS compatibility)
- **Removed deprecated commands**:
  - `copy-fonts` (use `icon-library` instead)
  - `build-legacy` (legacy Tailwind build removed)
- **Complete legacy mode removal**:
  - All legacy-related code and conditional checks eliminated
  - Legacy mode no longer supported anywhere in the codebase
  - `purge.options.legacy` configuration option completely removed
- **Simplified font generation**:
  - `build-fonts` `-p` flag removed (now handled by `-f` flag)
  - `build-fonts` command options simplified for better consistency
- **Updated dependencies** to latest ESM versions (chalk v5+, etc.)

### ✅ What's Maintained

- **Same CLI interface** - All commands and options preserved
- **100% API compatibility** - All existing commands work the same
- **Same configuration structure** - Your existing config content works unchanged

### 🔄 Command Improvements

- **Enhanced CLI error handling**: Improved error messages with command suggestions when unknown commands are entered
- **CLI reorganization**: Commands are now organized in logical categories (Setup, Development, Assets, Utilities, Maintenance) for better discoverability
- **`build-fonts` simplified**:
  - Removed `-p` (--icon-prefix-from-filename) flag
  - The `-f` flag now controls both font class names AND icon prefixes using filenames
  - More consistent and intuitive behavior
- **Internal code modularization**: Refactored monolithic helper files into specialized modules for better maintainability
- **Complete legacy mode removal**: Removed `build-legacy` command and all legacy-related code for cleaner, modern codebase

### 🔧 Migration Guide

For most users, upgrading is seamless:
```bash
npm install -g purgetss@latest
```

**Key changes to note:**
- Only requirement: **Node.js 16 or higher**
- If you used the `build-legacy` command, use the regular `build` command instead
- **Enhanced CLI**: Unknown commands now provide helpful suggestions instead of generic errors
- If you had `legacy: true` in your config, remove this option (legacy mode completely discontinued)
- If you used `build-fonts` with the `-p` flag, now use `-f` instead (handles both font classes and icon prefixes)

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