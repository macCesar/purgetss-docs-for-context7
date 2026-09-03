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

- 23,300+ utility classes for colors, spacing, typography, layout, and more.
- Parses XML files and writes an `app.tss` with only the classes you use.
- Customizable through `config.cjs`, with arbitrary values for one-off sizes and colors.
- Icon fonts for Buttons and Labels: Font Awesome, Material Icons, Material Symbols, and Framework7-Icons in Alloy and Classic projects.
- `build-fonts` installs custom fonts in Alloy or Classic; TSS class definitions are generated only for Alloy.
- `shades` command generates color palettes from a hex value.
- Animation module with 2D transforms, draggable views with collision detection, sequential animations, and position utilities.
- Grid system for aligning and distributing elements in rows and columns.

## Table of Contents

- [Installation](./installation.md)
- [Commands](./commands.md)
- App Assets
  - [App icons and branding](./app-assets/1-app-icons-and-branding.md)
  - [Multi-density images](./app-assets/2-multi-density-images.md)
- Customization
  - [The Config File](./customization/1-configuring-guide.md)
  - [Custom Rules](./customization/2-custom-rules.md)
  - [The `apply` Directive](./customization/3-the-apply-directive.md)
  - [The `opacity` Modifier](./customization/4-opacity.md)
  - [Arbitrary Values](./customization/5-arbitrary-values.md)
  - [Platform and Device Modifiers](./customization/6-platform-and-device-modifiers.md)
  - [Custom Fonts](./customization/7-custom-fonts.md)
  - [Icon Fonts Libraries](./customization/8-icon-fonts-libraries.md)
- The UI Module
  - [Introduction](./purgetss-ui/1-introduction.md)
  - [Using `purgetss.ui` in Titanium Classic](./purgetss-ui/2-titanium-classic.md)
  - [The `play` Method](./purgetss-ui/2-the-play-method.md)
  - [The `apply` Method](./purgetss-ui/3-the-apply-method.md)
  - [The `open` and `close` Methods](./purgetss-ui/4-the-open-close-methods.md)
  - [The `draggable` Method](./purgetss-ui/5-the-draggable-method.md)
  - [Additional Methods](./purgetss-ui/6-additional-methods.md)
  - [Complex UI Elements](./purgetss-ui/7-complex-ui-elements.md)
  - [Available Utilities](./purgetss-ui/8-available-utilities.md)
  - [Implementation Rules](./purgetss-ui/9-implementation-rules.md)
  - [Appearance](./purgetss-ui/10-appearance.md)
- Best Practices
  - [Appearance Setup](./best-practices/1-appearance-setup.md)
  - [Semantic Colors](./best-practices/2-semantic-colors.md)
  - [Large Titles on iOS](./best-practices/3-large-titles-on-ios.md)
  - [Values and Units](./best-practices/4-values-and-units.md)
- [Grid System](./grid-system.md)

---

## Changelog

### Unreleased

### v7.16.2

- **Custom-font modules expose every processed family.** `build-fonts --module` maps each TTF/OTF to the exact PostScript name Titanium expects, even when the project contains no icon CSS.
- **Classic color commands avoid unrelated empty source folders.** `shades` updates only `purgetss/config.cjs`, while `color-module` writes the CommonJS file under `Resources/lib/` without initializing brand, font, image, or Alloy scaffolding.

### v7.16.1

- **Titanium Classic video production assets were added**, together with clearer PurgeTSS diagnostics when an automatic Alloy purge fails.

### v7.16.0

- **Round non-icon artwork without pre-masking app icons.** `brand.artworkCornerRadius`, piece overrides, and six one-run flags cover the 28 legacy splash PNGs, Feature Graphic, and LaunchLogo. Store/launcher icons stay square for platform masking, and existing projects retain byte-compatible output at the `0%` default.
- **`appicon.padding` now has a matching CLI flag.** The canonical config exposes its `10%` default and `--appicon-padding` handles temporary changes.

→ See the [full changelog](./changelog.md) for older releases (v7.15.0 and earlier).
