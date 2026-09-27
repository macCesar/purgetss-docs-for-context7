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

### v7.18.0

- **Platform and device modifiers stack.** `ios:tablet:bg-red-500` (in either order) now generates `[platform=ios formFactor=tablet]`. Alloy reads only the last bracket of a selector, so `tablet:` on an iOS-only class used to lose its platform condition. A modifier that contradicts the class, such as `android:status-bar-dark`, leaves a comment in `app.tss` instead of a selector that never applies. See [Combining a platform and a device](./docs/customization/6-platform-and-device-modifiers.md#combining-a-platform-and-a-device).
- **Font Awesome Pro and Beta work again.** `purgetss build` no longer stops with `ENOENT`, and `icon-library --module` / `--styles` generate `fontawesome.js` and `fontawesome.tss` instead of printing a placeholder.
- **Stricter flags.** `icon-library --vendor` accepts `materialsymbols` and rejects unknown values before copying anything. `images --width` accepts 1 to 1024, the largest value whose `xxxhdpi` output fits the 4096px cap. See [the `icon-library` command](./docs/commands.md#icon-library-command).
- **Removed:** the `*-keyboard-type-appearance*` classes (they assigned an appearance constant to `keyboardType`; use `keyboard-appearance-*`), `snap-magnet` (never read by the animation module), and `init --all` (never implemented). `(Npx)` [arbitrary values](./docs/customization/5-arbitrary-values.md) are accepted again: `w-(100px)` means explicit pixels, not a redundant unit.

### v7.17.1

- **The notification icon is now named `notificationicon.png`.** `firebase.cloudmessaging` resolves that exact name, so data messages find the icon without any manifest wiring; under the old `ic_stat_notify` name they fell back to the opaque launcher icon and the status bar showed a white blob. Projects that wired `@drawable/ic_stat_notify` by hand update one `meta-data` line and delete the five stale files.

### v7.17.0

- **`--dependencies` scaffolds an ESLint setup that runs.** The template is `eslint.config.mjs`, a flat config for ESLint 9 that declares the Titanium and Alloy globals and ignores generated code. `eslint-config-axway` and `eslint-plugin-alloy` are no longer installed: neither works under ESLint 9.
- **The `images:` section rejects unknown keys.** A typo like `qualty: 95` is now an error naming the offending key and the entry index, instead of silently falling back to the default.
- **The generated `images:` block documents the 4× convention.** The comments state where the output sizes come from, why there is no `width` key, and which formats `quality` actually reaches.

→ See the [full changelog](./docs/changelog.md) for older releases (v7.16.2 and earlier).
