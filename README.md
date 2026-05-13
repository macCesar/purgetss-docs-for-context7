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

- [Installation](./docs/Installation.md)
- [Commands](./docs/Commands.md)
- App Assets
  - [App icons and branding](./docs/app-assets/1-app-icons-and-branding.md)
  - [Multi-density images](./docs/app-assets/2-multi-density-images.md)
- Customization
  - [The Config File](./docs/customization/1-configuring-guide.md)
  - [Custom Rules](./docs/customization/2-custom-rules.md)
  - [The `apply` Directive](./docs/customization/3-the-apply-directive.md)
  - [The `opacity` Modifier](./docs/customization/4-Opacity.md)
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

### v7.10.2

- Configs written before v7.7.0 (the `brand:` regroup) now auto-migrate in memory on every run. The legacy flat layout (`brand.padding: <number>`, `brand.iosPadding`, `brand.bgColor`, `brand.darkBgColor`, top-level `brand.notification`/`brand.splash`) used to crash auto-purge with `TypeError: Cannot create property 'ios' on number '15'`; `getConfigFile()` now normalizes them to the grouped layout (`brand.padding.{ios, androidLegacy, androidAdaptive}`, `brand.android.*`, `brand.ios.darkBackground`, `brand.colors.background`) before applying defaults. When both legacy and new keys coexist, the new key wins. A one-time deprecation notice per session lists the migrated keys. See [Upgrading from pre-7.7.0 configs](./docs/app-assets/1-app-icons-and-branding.md#upgrading-from-pre-770-configs).
- Internal fix: `logger.warning` and `logger.success` are now defined. Across `brand`, `images`, `cleanup-legacy`, and `svg-utils`, ~30 callsites of `logger.warning` and ~10 of `logger.success` referenced methods that did not exist on the logger object — any opt-in command path that hit one of those calls used to throw `TypeError: logger.warning is not a function`. The auto-purge entry point most users hit did not reach those callsites, so the bug stayed latent until commands like `purgetss brand` or `purgetss images` were run.

### v7.10.1

- User-visible references to "Tailwind" in copy that did not document a functional integration were dropped. The Class Syntax Error block now reports `'Square brackets "[ ]" are not supported'` instead of `'Tailwind-style brackets "[ ]" are not supported'`; the promotional `<Label>` injected into new projects by `purgetss create` changed from `"Tailwind-inspired utility classes for Titanium/Alloy"` to `"Utility-first styling for Titanium/Alloy"`. All functional integrations stay: the `tailwindcss@3` dependency installed by `install-dependencies` (drives both the `defaultColors`/`defaultTheme` palette base AND the VSCode IntelliSense extension), the `--tailwind` flag on `purgetss shades`, and the recommended `Tailwind CSS IntelliSense` / `Tailwind Raw Reorder (v4)` VSCode extensions.

### v7.10.0

- `purgetss images` got three CLI-only flags. `--opacity <n>` (integer `0-100`) multiplies the alpha channel of every generated density by `n/100` — useful for placeholder or default ImageView images that render at reduced opacity. `--padding <n>` (integer `0-40`) shrinks the rendered image inside each density canvas by symmetric percentage borders, preserving canvas size with transparent fill — useful for breathing room around an unpadded logo. `--output <relpath>` overrides the basename and subpath relative to each platform's `images/` root, so a logo from `purgetss/brand/` can be written as `images/logos/loading.png` across all densities in a single command. Combines naturally for "transparent placeholder with padding under a custom path". See [Multi-density images — opacity, padding, output](./docs/app-assets/2-multi-density-images.md).
- `purgetss brand` now generates `MarketplaceArtworkFeature.png` (1024×500 Google Play Feature Graphic) alongside the existing iTunesConnect and MarketplaceArtwork submission assets. Auto-discovers `purgetss/brand/logo-feature.{svg,png}` or reuses the master logo if not provided. Default vertical padding is `12%`; override with `--feature-graphic-padding <n>` (range `0-40`), config `brand.padding.featureGraphic`, or CLI `--feature-logo <path>` for a dedicated source. Submission artwork only — written to project root for upload to the Play Console, not bundled into the APK.
- Arbitrary nesting depth in `config.cjs` `theme` objects. Property emission now walks nested values recursively instead of stopping at level 2, so `theme.extend.colors.brand.primary.500` flattens to `brand-primary-500` instead of being silently dropped. Same for `backgroundGradient` and `backgroundSelectedGradient`. Default modifier keys (`default`, `global`, `DEFAULT`) collapse without contributing to the suffix.
- Fix: `apply:` now resolves built-in icon font classes (`fas`, `fab`, `fa-*`, `mi-*`, `ms-*`, `f7-*`) from `dist/` for projects that don't run `build-fonts`. Previously those classes were silently dropped from generated rules — `apply: 'fas fa-times-circle wh-12 ...'` produced everything except the FontAwesome family and the icon glyph.
- Fix: `borderRadius: [...]` arrays no longer get truncated when combined with other utilities in an `apply:` string. The post-merge dedup step (from v7.9.0) tracked depth on `{}` only, so `borderRadius: [0, 0, 0, 16]` (emitted by directional `rounded-{t,b,l,r,tl,tr,bl,br}-*` utilities) was split on its internal commas. The depth tracker now respects `[]` alongside `{}`.
- Fix: `brand --padding <n>` shortcut now applies to BOTH Android paddings as the help text always promised. Previously the shortcut only fed `androidAdaptivePadding` while `androidLegacyPadding` fell through to its own config value, so `purgetss brand --padding 17` actually produced `androidAdaptive=17, androidLegacy=10`.

### v7.9.0

- Opacity modifiers now work on classes that resolve to a semantic color. Writing `bg-surface/65` (or any other opacity modifier on a class mapped to a name in `semantic.colors.json`) produces a working rule with Light/Dark switching preserved. PurgeTSS derives a new `<originalKey>_<alphaPercent>` entry with the original `light`/`dark` hex values and the requested alpha for both modes, writes it back to `semantic.colors.json`, and emits the rule against the derived key. Re-runs are idempotent; manual edits with conflicting values halt the build with a `Conflict` error. New alpha entries require one full Titanium build to be picked up — Liveview hot-reload alone does not refresh `semantic.colors.json`. See [Semantic Colors](./docs/best-practices/2-semantic-colors.md#opacity-modifier-auto-derivation).
- Several fixes around semantic colors, gradients, and Ti Element defaults: the `semantic` tonal palette was inverting Light and Dark; the gradient `from`/`to` color order was position-dependent and could swap after `sort()`; the `bg-gradient-to-X` direction was silently dropped when combined with `from-X to-Y` colors in the same `apply` string; `theme.Window` / `theme.View` / `theme.ImageView` no longer leak the framework presets (white background, `Ti.UI.SIZE`, iOS `hires: true`) when defined at the top level (replace mode), so a Window declared at `theme.Window` with a `backgroundGradient` no longer ghosts on top of a default `backgroundColor: '#FFFFFF'`. `theme.extend.Window` keeps merging with the defaults as before. See [Apply directive — extend mode vs replace mode](./docs/customization/3-the-apply-directive.md#extend-mode-vs-replace-mode).
- **Breaking**: the user-facing glossary output path was renamed from `purgetss/experimental/tailwind-classes/` to `purgetss/glossary/tailwind-classes/`. Any tooling or CI that reads from the old path needs to be updated on upgrade — no transition shim was added on purpose. The `--glossary` flag and command surface are unchanged.

### v7.8.0

- `images` now has a `--width <n>` flag. It pins Android `mdpi` (= iPhone `@1x`) to a specific pixel width, for example `purgetss images logo.svg --width 256`. Larger scales derive at ×1.5, ×2, ×3, and ×4, with height staying proportional to the source's aspect ratio. Use this for SVG sources from vector editors with disproportionate viewBoxes, such as Affinity or Illustrator. Without the flag, every scale derives from the source's viewBox as a 4× master, which can produce unpredictable sizes when the viewBox does not match the intended display size. When you pass an SVG without `--width`, the command prints a one-time hint and then falls back to the legacy 4× behavior. This is CLI-only; there is no matching `images:` config property because the right width is per-asset.
- Class syntax pre-validation now stops `purgetss` with a structured `Class Syntax Error` block (file + line + suggested fix) when it detects known class-name mistakes: inverted negative sign (`top-(-10)` → `-top-(10)`), Tailwind-style brackets (`top-[10px]` → `top-(10px)`), empty parentheses (`wh-()`), whitespace inside parentheses (`wh-( 200 )`), and redundant `px` unit (`top-(10px)` → `top-(10)`). All offenders are reported in one run. Generic unknown classes, such as typos, vendor utilities not enabled, or custom classes not declared yet, are not flagged. They still flow into the `// Unused or unsupported classes` block in `app.tss`.
- The arbitrary-value parser no longer crashes on negative values inside parentheses. Classes like `top-(-10)`, `mt-(-5)`, and `origin-(-10,-20)` used to trigger a `Cannot read properties of null (reading 'pop')` exception. The parser now extracts the `(...)` portion first, so a `-` inside the value does not break the split.

### v7.7.0

- The `brand` config was cleaned up before stabilizing. Branding settings now live under grouped sections: `brand.logos`, `brand.padding`, `brand.android`, `brand.ios`, and `brand.colors`.
- `brand` can now use separate Android inputs: one logo for the general brand set, another for Android launcher icons, and another for Android 12+ splash artwork. Use `brand.logos.androidLauncher` / `--icon-logo` and `brand.logos.androidSplash` / `--splash-logo`, or drop `logo-icon.*` and `logo-splash.*` into `purgetss/brand/`.
- `purgetss brand` now regenerates the legacy Android splash fallback: `app/assets/android/default.png` in Alloy projects and `Resources/android/default.png` in Classic projects.
- `cleanup-legacy` no longer removes `default.png`, because that file can still matter on older Titanium Android splash paths.
- The branding docs now explain what uses `ic_launcher`, what uses `splash_icon.png`, and what still falls back to `default.png`.

### v7.6.2

- The `semantic` command now works in Classic Titanium projects. It writes to `Resources/semantic.colors.json`; Alloy keeps writing to `app/assets/semantic.colors.json`. Existing unrelated entries, such as the default `backgroundColor` / `textColor` values that ship with Classic templates, are preserved in both project types. See [Semantic Colors](./docs/best-practices/2-semantic-colors.md)
- Fixed a UX bug where the "not an Alloy project" error was immediately followed by the palette preview JSON, making it look like the command half-succeeded.

### v7.6.1

- `brand` and `images` now ask before destructive writes (`y` / `N` / `a` for "always"). The prompt is skipped when `stdin` is not a TTY (alloy.jmk hook, CI, pipes), when `-y` / `--yes` is passed, or when `PURGETSS_YES=1` is set. Set `confirmOverwrites: false` on the matching config section to silence it permanently.
- SVG logos and images now get a disproportionate-viewBox warning. The command detects viewBoxes above 4096 pt on any side, common in Affinity and Illustrator exports, and rasterizes with adaptive density to stay within Sharp's pixel budget.
- `init` now creates `purgetss/{fonts,brand,images}/` subfolders.
- Multi-line command output is now grouped under a single `::PurgeTSS::` header with indented continuation lines. This applies across `purge`, `fonts`, `icon-library`, `brand`, `images`, and most warnings.

### v7.6.0

- The new `brand` command generates the Titanium branding set from logos auto-discovered in `./purgetss/brand/`: launcher icons, adaptive icons, iOS 18+ Dark/Tinted variants, marketplace artwork, and optional notification/splash assets. Works on Alloy and Classic projects. See [App icons and branding](./docs/app-assets/1-app-icons-and-branding.md)
- The new `images` command generates multi-density UI images from sources in `./purgetss/images/`: Android `res-*` densities and iPhone `@1x`/`@2x`/`@3x` scales. Subdirectories are preserved, and short paths can target individual files for re-processing. See [Multi-density images](./docs/app-assets/2-multi-density-images.md)
- `brand:` and `images:` config sections were added to `purgetss/config.cjs`. Percentages can be written as `'15%'` strings for clarity; plain numbers are also accepted. Older configs get these sections on first run.
- The new `semantic` command generates Titanium semantic colors (Light/Dark mode) into `app/assets/semantic.colors.json`. `--single` switches between a tonal palette (one base hex → 11 shades with mirror inversion + auto config mapping) and a purpose-based color (explicit per-mode hex + optional alpha). The JSON entry and class mapping in `config.cjs` are written in one shot. Class names are derived by stripping the `Color` suffix, for example `surfaceColor` → `surface`. Smart in-place updates run when a single name matches an existing palette shade. See [Semantic Colors: generating semantic colors with the `semantic` command](./docs/best-practices/2-semantic-colors.md#generating-semantic-colors-with-the-semantic-command)

### v7.5.3

- New `Appearance` export for Light/Dark/System mode switching with persistence. Methods: `init()`, `set(mode)`, `get()`, `toggle()`. See [Appearance Setup](./docs/best-practices/1-appearance-setup.md)
- Default font family classes are generated automatically with platform-appropriate values: `font-sans`, `font-serif`, and `font-mono`.
- XML validation now detects illegal `--` inside XML comments during pre-validation.

### v7.5.0

- `extend` support for Window, View, and ImageView. Component defaults can now be customized from `theme.extend` in `config.cjs`.
- Shorthand `apply`: `{ apply: '...' }` is automatically normalized, so the `default:` wrapper is optional.
- Property deduplication: applied values win over static defaults instead of duplicating.
- Automatic platform resolution: classes inside `ios:` / `android:` blocks find their platform-specific version automatically.
- Font Awesome 7.2.0
- Fixed: `extend.Window` silently ignored, duplicate `font` properties, array-type properties missing `[ ]` notation

### v7.4.0

Animation module expansion: 9 new methods bring the module to 15 total.

- `transition`, `pulse`, `sequence`, `swap`, `shake`, `snapTo`, `reorder`, `undraggable`, `detectCollisions`
- New utility classes: `snap-back`, `snap-center`, `snap-magnet`, `keep-z-index`
- Delta-based drag for transformed views, position normalization, property inheritance from the Animation object

See the [UI Module documentation](./docs/purgetss-ui/1-introduction.md) for full details.

### v7.3.0

- BREAKING: `tailwind.tss` was renamed to `utilities.tss`.
- XML syntax validation adds pre-validation for Alloy XML files with line numbers and fix suggestions.
- Classic Titanium compatibility: `deviceInfo()` works without Alloy dependencies.

### v7.2.7

- Security fixes for command injection in `glob` and prototype pollution in `js-yaml`.
- Dependency cleanup reduced installation size by about 45MB and removed unused packages.
- Titanium SDK 13.1.0.GA: new utility classes for `navBarColor`, `forceBottomPosition`, and `multipleWindows`.

### v7.2.6

- Updated Font Awesome to version 7.1.0
- Simplified flag property names in utilities.tss
