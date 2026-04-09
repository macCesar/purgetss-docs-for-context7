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
> **PurgeTSS** is a toolkit for building mobile apps with the [Titanium framework](https://titaniumsdk.com). It adds practical utilities to speed up styling and reduce repeated setup work.
> 
> It includes utility classes, icon font support, an Animation module, a simple grid system, and the `shades` command for generating custom colors.
> 
> If you build UI-heavy screens, PurgeTSS helps you move faster without hand-writing long TSS files.


## What's New in v7.5.0

### Added
- **`extend` support for Window, View, and ImageView** — customize component defaults from `theme.extend` in `config.cjs`
- **Shorthand `apply`** — `{ apply: '...' }` is automatically normalized to `{ default: { apply: '...' } }`
- **Apply directive property deduplication** — applied values win over static defaults instead of duplicating
- **Automatic platform resolution in apply directives** — classes inside `ios:`/`android:` blocks find their platform-specific version automatically
- **Updated Font Awesome to 7.2.0**

### Fixed
- `extend.Window` was silently ignored — both `theme.Window` and `theme.extend.Window` work now
- Duplicate `font` properties in apply directives
- Array-type properties (`extendEdges`, `mediaTypes`, etc.) now correctly wrapped in `[ ]` notation

---

## What's New in v7.4.0

**Animation module expansion.** 9 new methods bring the module to 15 total:

### New methods
- **`transition(views, layouts)`** — multi-view layout transitions using GPU-accelerated `Matrix2D.translate().rotate().scale()`. Compatible with TiDesigner presets
- **`pulse(view, count)`** — scale-up-and-back pulse using native `autoreverse` + `repeat`. Good for notification badges
- **`sequence(views, cb)`** — animate views one after another (not parallel like `play`)
- **`swap(view1, view2)`** — animate two views exchanging positions
- **`shake(view, intensity)`** — bidirectional horizontal shake for error/validation feedback
- **`snapTo(view, targets)`** — snap a view to the nearest target by center distance
- **`reorder(views, newOrder)`** — animate views to new positions by index mapping
- **`undraggable(views)`** — remove draggable behavior and clean up all listeners
- **`detectCollisions(views, dragCB, dropCB)`** — collision detection with hover and drop callbacks

### New utility classes
- `snap-back`, `snap-center`, `snap-magnet` — control drop behavior on draggable views
- `keep-z-index` — preserve z-order during drag (for `transition` presets)

### Improvements
- Delta-based drag for views with `rotate`/`scale` transforms
- Position normalization — `swap`, `reorder`, and `snapTo` work without explicit `top`/`left`
- Property inheritance — `swap`, `reorder`, `snapTo`, and `shake` inherit `duration`, `delay`, and `curve` from the `<Animation>` object

See the [UI Module documentation](./purgetss-ui/1-introduction.md) for full details.

---

What it does:

- 23,300+ utility classes for colors, spacing, typography, layout, and more.
- Parses your XML files and writes an `app.tss` with only the classes you actually use.
- Customizable via `config.cjs`. Supports arbitrary values for one-off sizes and colors.
- Icon fonts: Font Awesome, Material Icons, Material Symbols, and Framework7-Icons in Buttons and Labels.
- `build-fonts` command generates `fonts.tss` with class definitions and `fontFamily` selectors for any font you drop in.
- `shades` command generates color palettes from a hex value.
- Animation module: 2D transforms, draggable views with collision detection, sequential animations, and position utilities.
- Grid system for aligning and distributing elements in rows and columns.

## Table of Contents

- [Installation](./Installation.md)
- [Commands](./Commands.md)
- Customization
  - [The Config File](./customization/1-configuring-guide.md)
  - [Custom Rules](./customization/2-custom-rules.md)
  - [The `apply` Directive](./customization/3-the-apply-directive.md)
  - [The `opacity` Modifier](./customization/4-Opacity.md)
  - [Arbitrary Values](./customization/5-arbitrary-values.md)
  - [Platform and Device Modifiers](./customization/6-platform-and-device-modifiers.md)
  - [Icon Fonts Libraries](./customization/7-icon-fonts-libraries.md)
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
- Recommendations
  - [Window Defaults](./recommendations/3-window-defaults.md)
  - [Semantic Colors](./recommendations/2-semantic-colors.md)
  - [Appearance Setup](./recommendations/1-appearance-setup.md)
- [Grid System](./grid-system.md)
