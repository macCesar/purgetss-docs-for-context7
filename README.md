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

- [Installation](./docs/installation.md)
- [Commands](./docs/commands.md)
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
  - [The `play` Method](./docs/purgetss-ui/2-the-play-method.md)
  - [The `apply` Method](./docs/purgetss-ui/3-the-apply-method.md)
  - [The `open` and `close` Methods](./docs/purgetss-ui/4-the-open-close-methods.md)
  - [The `draggable` Method](./docs/purgetss-ui/5-the-draggable-method.md)
  - [Complex UI Elements](./docs/purgetss-ui/7-complex-ui-elements.md)
  - [Additional Methods](./docs/purgetss-ui/6-additional-methods.md)
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

- **Round splash artwork without rounding the app icon.** A shared `brand.splashCornerRadius` setting, per-platform config overrides, and three one-run flags apply only to the 28 legacy iOS/Android splash PNGs. Existing projects stay square at the `0%` default.
- **`appicon.padding` now has a matching CLI flag.** The canonical config exposes its `10%` default and `--appicon-padding` handles temporary changes.

### v7.15.0

- **Standalone resource commands now work cleanly in Classic Titanium projects.** `images`, `semantic`, `shades`, `color-module`, `module`, `icon-library`, and `build-fonts` detect Alloy or Classic and write to native `Resources/` paths where appropriate. Classic does not receive the PurgeTSS utility-class lifecycle.
- **Official icon modules expose font-family aliases.** For example, use `fontAwesome.icons.home` with `fontAwesome.solid`; `families` exposes the complete stable mapping for every bundled library.
- **`images` follows `tiapp.xml` deployment targets by default.** Explicit `--android` and `--ios` flags remain available when you need an override.

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

→ See the [full changelog](./docs/changelog.md) for older releases (v7.13.0 and earlier).
