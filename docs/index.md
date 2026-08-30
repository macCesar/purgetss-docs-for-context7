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
- Icon fonts for Buttons and Labels: Font Awesome, Material Icons, Material Symbols, and Framework7-Icons.
- `build-fonts` command generates `fonts.tss` with class definitions and `fontFamily` selectors for any font you drop in.
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
  - [Values and Units](./best-practices/4-values-and-units.md)
- [Grid System](./grid-system.md)

---

## Changelog

### v7.14.0

- **Square iOS/store artwork is now full-bleed by default.** `icon`, `dark`, `tinted` and `marketplace` use `0%` instead of the former `4%` inset. `--ios-padding` still moves the family together when the source is a logo that needs breathing room.
- **`DefaultIcon.png` and `DefaultIcon-ios.png` now both obey `brand.icon.padding`.** The root fallback had incorrectly inherited Android adaptive padding, creating a much larger border than configured. Both outputs are opaque and use the same inset.
- **Standalone Classic projects get a complete first-run setup.** If `purgetss/config.cjs` is missing, `brand` creates the canonical config. A positional source such as `sample-icon.png` is adopted as `purgetss/brand/logo.png` when no canonical logo exists, and the move is reported.
- **Generation follows `tiapp.xml` deployment targets in Alloy and Classic.** Normal runs omit disabled platforms; explicit `--only` remains an override. Classic Android retains the 11 `Resources/android/images/res-*` splash variants Titanium consumes even though `ti create` does not seed those folders.
- **Visible icon frames are diagnosed before they surprise you.** Opaque edge-to-edge artwork combined with padding and a contrasting inherited background produces a warning naming the affected pieces. White remains a configurable fallback, not a platform requirement.

### v7.13.2

- `purgetss brand --help` advertised padding defaults the command does not use: `19` for the adaptive icon and `20` for both splash sets, where the pipeline applies `18`, `26` and `26`. The seven padding descriptions are now interpolated from the piece table instead of being hand-typed in a second place, with a new test that compares the real `--help` output against that table. The values documented on this site were already the correct ones.

### v7.13.1

- Four vulnerable transitive dependencies patched, all of which shipped inside v7.13.0: `postcss`, `nanoid`, `brace-expansion` and `uuid`. Patch bumps within the same major, so `package.json` is untouched and only the lockfile moves. `npm audit` reports zero vulnerabilities afterwards.

→ See the [full changelog](./changelog.md) for older releases (v7.13.0 and earlier).
