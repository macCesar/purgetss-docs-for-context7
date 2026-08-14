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

### v7.13.1

- Four vulnerable transitive dependencies patched, all of which shipped inside v7.13.0: `postcss`, `nanoid`, `brace-expansion` and `uuid`. Patch bumps within the same major, so `package.json` is untouched and only the lockfile moves. `npm audit` reports zero vulnerabilities afterwards.

### v7.13.0

- **`purgetss brand` now covers every image the Titanium template ships.** A run on a fresh Alloy project used to leave 28 files wearing the grey Alloy logo: the 16 iPhone launch images, the 11 per-qualifier Android splashes, and `appicon.png`. The rule is now explicit: if the template ships the file, `brand` updates it.
- **The `brand:` config is organized by piece.** Each of the 14 pieces takes the same four keys where they apply (`logo`, `padding`, `background`, `enabled`), and an older block is rewritten to that structure on the next run, carrying over every value you had customized. Unknown keys abort the run instead of being ignored.
- **Breaking: one name per thing** across config, flags, `--only` and the `purgetss/brand/` files. `--splash` → `--splash-icon`, `--notification` → `--notification-icon`, `--feature-logo` → `--feature-graphic-logo`, `--legacy-splash` removed. No aliases were kept.
- **New `--only <pieces>` filter** to regenerate one piece or a group, and **`logo-launch.*`** to put your logotype on the iOS launch screen through `LaunchLogo.png`.
- **New `brand.optimize` / `--optimize`**: quantizes the generated PNGs to a palette. Off by default because it is lossy: 1.6 MB to 476 KB on the reference set, indistinguishable on flat artwork.
- `shades` and `semantic` no longer strip every comment from `config.cjs`; they rewrite only the `theme:` section.

### v7.12.1

- `purgetss brand --notes` now targets Titanium's launcher Activity instead of only the app theme. Titanium applies `Theme.Titanium` directly to the generated launcher Activity, so adding splash items only to the `<application>` theme could still leave Android 12+ using the SDK's default background. The notes now print a complete `splashscreen.xml` plus a launcher-only `Theme.SplashScreen` derived from `Theme.Titanium`, with the launch color defined in one place.
- Font Awesome Free updated to 7.3.1: 23 new icon classes (`.fa-lotus`, `.fa-codeberg`, `.fa-copilot`, `.fa-substack`, `.fa-tesla`, …), none removed.
- `sharp` updated to 0.35.3 and `glob` to 13.0.6.

→ See the [full changelog](./docs/changelog.md) for older releases (v7.12.0 and earlier).
