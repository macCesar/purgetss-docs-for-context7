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

### v7.12.1

- `purgetss brand --notes` now targets Titanium's launcher Activity instead of only the app theme. Titanium applies `Theme.Titanium` directly to the generated launcher Activity, so adding splash items only to the `<application>` theme could still leave Android 12+ using the SDK's default background. The notes now print a complete `splashscreen.xml` plus a launcher-only `Theme.SplashScreen` derived from `Theme.Titanium`, with the launch color defined in one place.
- Font Awesome Free updated to 7.3.1 — 23 new icon classes (`.fa-lotus`, `.fa-codeberg`, `.fa-copilot`, `.fa-substack`, `.fa-tesla`, …), none removed.
- `sharp` updated to 0.35.3 and `glob` to 13.0.6.

### v7.12.0

- **Android launch background snippets in `purgetss brand --notes`.** The notes covered the iOS launch image and the Android launcher icon, but never the color Android draws before Titanium creates the first Window — so a run that set a brand background still flashed the default theme color at launch. `--notes` now prints `android:windowSplashScreenBackground` (Android 12+ system splash) and `android:windowBackground` (native window) to merge into the existing app theme.
- `--notes` wording no longer names only `tiapp.xml`: the command edits neither `tiapp.xml` nor the Android theme resources, so it now reads "platform launch/theme snippets".
- `completions-v3.json` reports SDK 13.4.0.GA — metadata label only, the properties map is unchanged.

### v7.11.2

- `images.files` sync silently gave up on any config with comments — including the one `purgetss init` generates — because the scanner tracked quotes but not comments. Only the write-back to `config.cjs` was lost; the SVG pipeline still generated the PNGs.
- `parseTssMap()` dropped every property following an escaped quote, and classes carrying a nested object (`'.text-xs': { font: { fontSize: 12 } }`) never entered the TSS map at all — so the SVG pipeline resolved dimensions from an incomplete map.
- Android `theme` values keep their quotes in custom rules: `theme: 'Theme.AppDerived.NoTitleBar'` used to be emitted unquoted, which Alloy cannot compile. Generated `dist/utilities.tss` is byte-identical to the previous release.

→ See the [full changelog](changelog) for older releases (v7.11.1 and earlier).
