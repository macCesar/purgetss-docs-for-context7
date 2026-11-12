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
> PurgeTSS is a toolkit for building mobile apps with the [Titanium framework](https://titaniumsdk.com). It adds practical utilities for styling and setup work.
> 
> It includes utility classes, icon font support, an Animation module, a simple grid system, and the `shades` command for generating custom colors.
> 
> If you build UI-heavy screens, PurgeTSS keeps you from hand-writing long TSS files.


What it does:

- 23,400+ utility classes for colors, spacing, typography, layout, and more.
- Parses XML files and writes an `app.tss` with only the classes you use.
- Customizable through `config.cjs`, with arbitrary values for one-off sizes and colors.
- Icon fonts for Buttons and Labels: Font Awesome, Material Icons, Material Symbols, and Framework7-Icons in Alloy and Classic projects.
- `build-fonts` installs custom fonts in Alloy or Classic; TSS class definitions are generated only for Alloy.
- `shades` command generates color palettes from a hex value.
- Animation module with 2D transforms, draggable views with collision detection, sequential animations, and position utilities.
- Grid system for aligning and distributing elements in rows and columns.

## Table of Contents

- [Installation](./docs/installation.md)
- [Commands](./docs/commands.md)
- [Execution profiles and performance](./docs/performance.md)
- App Assets
  - [App icons and branding](./docs/app-assets/1-app-icons-and-branding.md)
  - [Multi-density images](./docs/app-assets/2-multi-density-images.md)
- Customization
  - [The Config File](./docs/customization/1-configuring-guide.md)
  - [Custom Rules](./docs/customization/2-custom-rules.md)
  - [The `apply` Directive](./docs/customization/3-the-apply-directive.md)
  - [The `opacity` Modifier](./docs/customization/4-opacity.md)
  - [Arbitrary Values](./docs/customization/5-arbitrary-values.md)
  - [Platform and Device Modifiers](./docs/customization/6-platform-and-device-modifiers.md)
  - [Custom Fonts](./docs/customization/7-custom-fonts.md)
  - [Icon Fonts Libraries](./docs/customization/8-icon-fonts-libraries.md)
- The UI Module
  - [Introduction](./docs/purgetss-ui/1-introduction.md)
  - [Using `purgetss.ui` in Titanium Classic](./docs/purgetss-ui/2-titanium-classic.md)
  - [The `play` Method](./docs/purgetss-ui/2-the-play-method.md)
  - [The `apply` Method](./docs/purgetss-ui/3-the-apply-method.md)
  - [The `open` and `close` Methods](./docs/purgetss-ui/4-the-open-close-methods.md)
  - [The `draggable` Method](./docs/purgetss-ui/5-the-draggable-method.md)
  - [Additional Methods](./docs/purgetss-ui/6-additional-methods.md)
  - [Complex UI Elements](./docs/purgetss-ui/7-complex-ui-elements.md)
  - [Available Utilities](./docs/purgetss-ui/8-available-utilities.md)
  - [Implementation Rules](./docs/purgetss-ui/9-implementation-rules.md)
  - [Appearance](./docs/purgetss-ui/10-appearance.md)
- Best Practices
  - [Appearance Setup](./docs/best-practices/1-appearance-setup.md)
  - [Semantic Colors](./docs/best-practices/2-semantic-colors.md)
  - [Large Titles on iOS](./docs/best-practices/3-large-titles-on-ios.md)
  - [Values and Units](./docs/best-practices/4-values-and-units.md)
- [Grid System](./docs/grid-system.md)

---

## Changelog

### Unreleased

### v7.20.2

- Restore arbitrary RGB/RGBA color classes, broken since 7.8.0, with balanced nested parentheses. Malformed values and inverted negatives remain unsupported. Property deduplication in `apply` preserves color commas and quoted strings. See [arbitrary color values](./docs/customization/5-arbitrary-values.md#color-properties) and the [full changelog](./docs/changelog.md#v7202).

### v7.20.1

- Generator corrections exclude read-only properties, emit numeric iOS SplitWindow ratios and bounds, resolve Font Awesome Pro/Beta families from installed font metadata, and reject inverted negatives in the formatter. See the [full changelog](./docs/changelog.md#v7201).

### v7.20.0

- Update Titanium Completions to SDK 14.0.0.GA: 63 new property entries and 356 recovered property descriptions.
- Regenerate utility classes and the glossary, including 18 new property pages for boolean, color, split-view and layout settings.

→ See the [full changelog](./docs/changelog.md) for older releases (v7.19.0 and earlier).
