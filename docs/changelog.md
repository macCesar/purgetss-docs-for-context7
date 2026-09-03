# Changelog

All notable changes to PurgeTSS. For the canonical, full-detail log see [the project CHANGELOG on GitHub](https://github.com/macCesar/purgetss/blob/main/CHANGELOG.md).

## Unreleased

## v7.16.2

- **Custom-font modules now include every processed font family.** `build-fonts --module` exports each TTF/OTF PostScript name through `families`, including projects that contain ordinary text fonts without an icon CSS map.
- **Classic color commands no longer create unrelated empty source folders.** Saving a palette with `shades` creates or updates only `purgetss/config.cjs`; `color-module` adds only `Resources/lib/purgetss.colors.js`. The empty `purgetss/brand/`, `purgetss/fonts/`, and `purgetss/images/` conventions remain part of Alloy initialization.

## v7.16.1

- **Titanium Classic video voice samples.** Added the reference script and Zoe Premium recordings used by the demo production kit.
- **Alloy build failures now preserve PurgeTSS diagnostics.** The synchronous hook exposes the CLI output before Alloy's generic failure message and suggests running `purgetss` directly when more context is needed.

## v7.16.0

- **Non-icon artwork now has a configurable corner radius.** `brand.artworkCornerRadius` defaults to `0%` for byte-compatible output and covers the 16 iPhone launch images, 12 Android pre-12 splashes, Feature Graphic, and LaunchLogo. Piece overrides and six matching flags support temporary values from 0 through 50. Store and launcher icon files remain square for Apple/Google masking; `brand.splashCornerRadius` remains available as a splash-only shared override.
- **`appicon.padding` is complete and visible.** New configs show its existing `10%` default, and `--appicon-padding` overrides it for one run.

## v7.15.0

- **Standalone resource commands now work cleanly in Classic Titanium projects.** `images`, `semantic`, `shades`, `color-module`, `module`, `icon-library`, and `build-fonts` detect Alloy or Classic and write to the appropriate native location. Classic never receives `app/`, `app/alloy.jmk`, TSS, or a PurgeTSS compilation dependency.
- **`images` follows `tiapp.xml` deployment targets by default.** Android-only projects get Android density files; iOS-only projects get iPhone scales. `--android` and `--ios` remain intentional overrides, and an external source does not create an empty `purgetss/` folder.
- **Official icon modules now include font-family aliases.** Use `fontAwesome.icons.home` with `fontAwesome.solid`; the other bundled modules expose their installed variants through `families` and concise aliases. Existing icon APIs remain unchanged.

## v7.14.0

- **Square iOS/store artwork is now full-bleed by default.** `icon`, `dark`, `tinted` and `marketplace` use `0%` instead of the former `4%` inset. `--ios-padding` still moves the family together when the source is a logo that needs breathing room.
- **`DefaultIcon.png` and `DefaultIcon-ios.png` now both obey `brand.icon.padding`.** The root fallback had incorrectly inherited Android adaptive padding, creating a much larger border than configured. Both outputs are opaque and use the same inset.
- **Standalone Classic projects get a complete first-run setup.** If `purgetss/config.cjs` is missing, `brand` creates the canonical config. A positional source such as `sample-icon.png` is adopted as `purgetss/brand/logo.png` when no canonical logo exists, and the move is reported.
- **Generation follows `tiapp.xml` deployment targets in Alloy and Classic.** Normal runs omit disabled platforms; explicit `--only` remains an override. Classic Android retains the 11 `Resources/android/images/res-*` splash variants Titanium consumes even though `ti create` does not seed those folders.
- **Visible icon frames are diagnosed before they surprise you.** Opaque edge-to-edge artwork combined with padding and a contrasting inherited background produces a warning naming the affected pieces. White remains a configurable fallback, not a platform requirement.

## v7.13.2

- **`purgetss brand --help` advertised padding defaults the command does not use.** It printed `default: 19` for `--android-adaptive-padding` and `default: 20` for `--android-splash-padding` and `--ios-splash-padding`, while the pipeline applies `18`, `26` and `26`. The numbers live as data in `src/core/branding/pieces.js`, and `bin/purgetss` carried a second hand-typed copy inside each option description with nothing linking the two. The adaptive string dated to April 2026 and was never revisited when 7.13.0 moved the value; the two splash strings were introduced by 7.13.0 already carrying the wrong figure. All seven padding descriptions are now interpolated from the piece table, so the drift is no longer expressible, and a new unit test parses the real `--help` output and compares every advertised default against the table. The values documented on this site were already the correct ones. See [Padding guidance](./app-assets/1-app-icons-and-branding.md#padding-guidance).
- The 7.13.0 entry in the project CHANGELOG said the splash paddings default to `20%`, "leaving the logo at 60%". Corrected to `26%` and 48%, matching the code and the rest of that entry.

## v7.13.1

- Four vulnerable transitive dependencies patched. They shipped inside v7.13.0: `postcss` ≤8.5.22 → 8.5.26 (high: XSS via an unescaped `</style>` in the stringify output, plus three path-traversal advisories around `sourceMappingURL`), `nanoid` 3.3.11 → 3.3.18 (high: non-secure generators can loop indefinitely on a negative size), `brace-expansion` 2.1.0 → 2.1.4 and 5.0.5 → 5.0.9 (high: a large numeric range defeats the documented `max` DoS protection), and `uuid` → 11.1.1 (moderate: missing buffer bounds check in v3/v5/v6 when `buf` is provided). All patch bumps within the same major, so `package.json` is untouched and only the lockfile moves. `npm audit` reports zero vulnerabilities afterwards.

## v7.13.0

- **`purgetss brand` now covers every image the Titanium template ships.** A run on a fresh Alloy project used to leave 28 files still wearing the grey Alloy logo: the 16 `assets/iphone/Default*.png` launch images, the 11 `assets/android/images/res-*/default.png` splashes, and `assets/android/appicon.png`. Three new generators close that gap, and the per-qualifier Android splashes moved from the opt-in `--legacy-splash` flag, now removed, into the default set. The rule is explicit: if the template ships the file, `brand` updates it. See [App icons and branding](./app-assets/1-app-icons-and-branding.md).
- **The `brand:` config section is organized by piece, not by kind of setting.** Each of the 14 pieces gets one block accepting the same four keys where they apply (`logo`, `padding`, `background`, `enabled`), instead of a single piece being split across `brand.logos`, `brand.padding` and `brand.android`. `background` cascades from the top-level `brand.background`; `padding` deliberately does not, because `18%` answers to Android's launcher mask while `4%` is an iOS aesthetic choice, and one inherited number would break the mask silently. See [the `brand:` config section](./app-assets/1-app-icons-and-branding.md#the-brand-config-section).
- **Breaking: one name per thing across config, flags, `--only` and the `purgetss/brand/` files.** `--splash` → `--splash-icon`, `--notification` → `--notification-icon`, `--splash-logo` → `--splash-icon-logo`, `--feature-logo` → `--feature-graphic-logo`. `--icon-logo` now feeds the `icon` piece; the Android launcher source is `--adaptive-logo`. Logo basenames follow `logo-<piece>` with no exceptions, so `logo-splash` and `logo-feature` become `logo-splash-icon` and `logo-feature-graphic`. `--legacy-splash` is gone. No aliases were kept. Every piece also gained its `--<piece>-logo` flag: `--ios-splash-logo`, `--marketplace-logo`, `--legacy-icon-logo`, `--appicon-logo`, `--android-splash-logo` and `--notification-icon-logo` were missing before.
- **New `--only <pieces>` filter.** `purgetss brand --only icon` regenerates just the `DefaultIcon` pair; `--only ios,notification-icon` mixes a group and a piece. Groups: `ios` (icon, dark, tinted, ios-splash), `store` (marketplace, feature-graphic), `android` (adaptive, legacy-icon, appicon, android-splash). Naming a piece generates it even when its opt-in flag is absent, an unknown name aborts before writing anything, and `--dry-run` honors the same filter. See [Regenerating a single piece](./app-assets/1-app-icons-and-branding.md#regenerating-a-single-piece-with---only).
- **`LaunchLogo.png` (1024×1024) as the iOS launch screen source.** Titanium already resizes `LaunchLogo.imageset` on every build, preferring `LaunchLogo.png` over `DefaultIcon.png`. Dropping `purgetss/brand/logo-launch.{svg,png}` into the project makes the launch screen show the logotype instead of the app icon with its safe-zone padding. Activated by the file's presence or with `--only launch-logo`; `--launch-logo <path>` and `--launch-logo-padding <n>` set the source and the framing. See [The iOS launch screen and LaunchLogo.png](./app-assets/1-app-icons-and-branding.md#the-ios-launch-screen-and-launchlogopng).
- **`config.cjs` is brought up to date on disk, not translated on every read.** A `brand:` block written for an earlier version is rewritten to the per-piece structure on the next run, carrying over every value that had been customized (paddings, colors, logo paths, enabled flags) and listing each one it moved. It happens on `purgetss brand` and on any command that goes through the config. The in-memory `normalizeLegacyBrand()` of v7.10.2 is gone, so every command now reads exactly one shape and dropping the migration later touches nothing else. See [Older configs update themselves](./app-assets/1-app-icons-and-branding.md#older-configs-update-themselves).
- **New `brand.optimize` / `--optimize`: quantize the generated PNGs to a palette.** Off by default because it is lossy. On the reference project the full set of 56 files goes from 1.6 MB to 476 KB, 71% smaller. Measured on visible pixels the difference against truecolor averages 0.08–0.19 out of 255 with no channel over 16/255, and transparency survives intact, so flat marks are safe and wide gradients are the case to check. It runs as a post-pass over what the pipeline wrote, so every piece is covered from one place, and a file is left alone when the palette version is not actually smaller. `--no-optimize` skips it for one run. See [Shrinking the generated files](./app-assets/1-app-icons-and-branding.md#shrinking-the-generated-files).
- **`shades` and `semantic` no longer strip every comment from `config.cjs`.** Both serialized the whole config object and wrote it back, which reformatted the file and dropped all of its comments, including the ones `init` generates for `purge:`, `brand:` and `images:`, sections the color commands never look at. They now rewrite only the `theme:` section, leaving every other byte untouched. Comments inside `theme:` still go, since that is the section being rewritten. Pre-existing bug, unrelated to the brand work.
- **Unknown keys inside `brand:` are rejected instead of ignored**, at both levels: `brand.foo` and `brand.adaptive.paddig` both abort before a single file is written, listing the valid keys. A typo is deliberately not treated as an old structure: rewriting the block would drop it silently, and ignoring a misspelled padding key would render the whole icon set at the wrong size and still look plausible.
- **The intermediate masters are sized to the run instead of a fixed 1024 px.** Every piece scales down from those masters, so their resolution was a hard ceiling on sharpness: a splash at `--splash-padding 14` or lower asked for more than 1024 px and got an upscaled raster. `brand` now measures the largest request across the selected pieces and rasterizes to exactly that (a default run reports `Masters at 942 px`), so every destination is a reduction. Runs also got faster, 6.8 s → 2.2 s on the reference project, because the master is no longer larger than needed.
- **The 28 splash images share one sizing rule, and it is configurable.** `default.png` used a hardcoded box of 72% × 26% of its own canvas while the 11 `res-*` used a separate hardcoded 60%: two rules for the same piece, neither adjustable. All of them, plus the 16 iPhone launch images, now fit the logo into a square sized as a share of the canvas's **shorter** side, so one number holds in portrait and landscape alike. Exposed as `brand.androidSplash.padding` / `brand.iosSplash.padding` with `--android-splash-padding`, `--ios-splash-padding` and the `--splash-padding` shortcut. The `26%` default is calibrated against the template itself: the Alloy logo in the stock `default.png` measures 665×488 px on a 1440×2560 canvas, and `26%` lands within 4% of it.
- Adaptive icon padding default is now `18%` (was `19%`). Android's adaptive canvas is 108 dp with a 66 dp guaranteed safe circle inside a ~72 dp visible area; `18%` puts the logo at 69.1 dp, past the circle's corners but inside what the mask shows, which suits marks that carry their own margin.
- `--nine-patch` is declared and documented, but the generator is not implemented yet. Selecting it prints a warning and writes nothing.

## v7.12.1

- `purgetss brand --notes` now targets Titanium's launcher Activity instead of only the app theme. Titanium applies `Theme.Titanium` directly to the generated launcher Activity, so adding splash items only to the `<application>` theme could still leave Android 12+ using the SDK's default background. The notes now print a complete `splashscreen.xml` at the correct Alloy or Classic resource path, define a launcher-only `Theme.SplashScreen` derived from `Theme.Titanium`, and show how to merge that theme into the existing launcher Activity declaration without changing the app's current theme. `windowSplashScreenBackground`, `windowBackground` and `colorBackground` all reference one `splashscreen_background` resource, so the launch color is changed in one place. With `--splash` enabled, the same copy-ready style also includes `windowSplashScreenAnimatedIcon`.
- Font Awesome Free updated to 7.3.1: 23 new icon classes (`.fa-lotus`, `.fa-codeberg`, `.fa-copilot`, `.fa-substack`, `.fa-tesla`, `.fa-storybook`, `.fa-matrix`, `.fa-nextcloud`, `.fa-visual-studio`, …), none removed.
- `sharp` updated to 0.35.3 and `glob` to 13.0.6.

## v7.12.0

- **Android launch background snippets in `purgetss brand --notes`.** The full notes covered the iOS launch image and the Android launcher icon, but never the color Android draws before Titanium creates the first Window, so a run that set a brand background still flashed the default theme color at launch. `--notes` now prints a step with both items to merge into the existing app theme: `android:windowSplashScreenBackground` (Android 12+ system splash) and `android:windowBackground` (native window), plus the reminder that `tiapp.xml` `<application>` must reference that theme with `android:theme="@style/YourExistingTheme"`.
- `--notes` wording no longer names only `tiapp.xml`. The command edits neither `tiapp.xml` nor the Android theme resources, so the help text and the compact summary now read "platform launch/theme snippets".
- `completions-v3.json` reports SDK 13.4.0.GA. Metadata label only; the properties map is unchanged.

## v7.11.2

- `images.files` sync silently gave up on any config with comments, including the one `purgetss init` generates. `matchBracket()` tracked quotes but not comments, so the apostrophe in the template's own comment opened a string that never closed, and every run printed `Could not insert <file> into images.files (section missing or unreadable)` while `files` stayed `[]`. The SVG pipeline still generated the PNGs, so only the write-back to `config.cjs` was lost.
- `parseTssMap()` dropped every property following an escaped quote. A single `\'` in a value flipped the scanner into "inside a string" permanently, so `'.card': { title: 'it\'s here', width: 200 }` yielded no `width` at all and the SVG pipeline resolved dimensions from an incomplete map.
- Classes carrying a nested object never entered the TSS map. The class body was delimited by `[^}]*`, which cannot see past an inner `}`, so `'.text-xs': { font: { fontSize: 12 } }`, and any custom class combining `font: { ... }` with `width`/`height`, was skipped entirely by the SVG pipeline. The body is now delimited by brace balancing.
- Android `theme` values keep their quotes in custom rules. `'.welcome-window': { android: { theme: 'Theme.AppDerived.NoTitleBar' } }` used to emit an unquoted value that Alloy cannot compile, because any value containing `Theme` or `Titanium` was treated as a JavaScript expression. Detection is now anchored to the start of the value (`Alloy.`, `Ti.`, `Titanium.`, `L(`, plus constant array literals), so theme names stay strings while real expressions are still emitted raw. Generated `dist/utilities.tss` is byte-identical to the previous release.
- E2E suites run against a disposable copy of `test-project/`. They used to execute the real CLI inside the versioned project, leaving the working tree dirty and, via a `rm -f purgetss/config.cjs` cleanup, dropping the `images.files` entries the SVG pipeline had synced.

## v7.11.1

- Fixes for the SVG image pipeline from v7.11.0: width/height now cascade symmetrically (the unpinned side derives from the `viewBox` on every run instead of getting cemented in `config.cjs`), `syncConfigImages` mirrors the current run so shrinking a class actually shrinks the entry, and SVGs listed in `images.files` always emit PNG to match Titanium's runtime fallback.
- `purgetss images` respects `--yes` for overwrite confirmations, and `syncConfigImages` no longer bumps `config.cjs` mtime on untouched runs (which was triggering needless `utilities.tss` rebuilds).
- Restored `src/dev/builders/tailwind-builder.js`. It was the entry point for `npm run build:tailwind`, deleted as "orphan" by mistake in 7.11.0.

## v7.11.0

- **SVG-aware compile-time image pipeline as a post-step of `purgetss`.** When views or controllers reference `image="/images/<sub>/<name>.svg"` alongside utility classes that resolve to numeric width/height (`w-32`, `w-(300)`, `h-auto`, …), purge now compiles those SVGs into the 8 Titanium density variants (5 Android + 3 iPhone PNGs) using dimensions resolved from `app.tss`. Titanium loads the generated `.png` automatically at runtime; the SVG attribute in your source is never rewritten. See [SVG-aware image pipeline](./app-assets/3-svg-pipeline.md).
- New `images.files` array in `config.cjs` to pin width/height per file, plus `images.autoSync` (default `true`) for devs who want to manage `images.files` by hand.
- `config.cjs` syntax validator: type mismatches in known fields (`theme.fontFamily.*`, `theme.extend.fontFamily.*`) print a formatted `Config Syntax Error` block with file, JSON path, and a fix snippet, instead of cryptic downstream crashes like `rule.startsWith is not a function`.

## v7.10.2

- Configs written before v7.7.0 (the `brand:` regroup) now auto-migrate in memory on every run. The legacy flat layout (`brand.padding: <number>`, `brand.iosPadding`, `brand.bgColor`, `brand.darkBgColor`, top-level `brand.notification`/`brand.splash`) used to crash auto-purge with `TypeError: Cannot create property 'ios' on number '15'`; `getConfigFile()` now normalizes them to the grouped layout (`brand.padding.{ios, androidLegacy, androidAdaptive}`, `brand.android.*`, `brand.ios.darkBackground`, `brand.colors.background`) before applying defaults. When both legacy and new keys coexist, the new key wins. A one-time deprecation notice per session lists the migrated keys. (In v7.13.0 this moved out of the read path: the `config.cjs` file itself is rewritten to the current structure instead.)
- Internal fix: `logger.warning` and `logger.success` are now defined. Across `brand`, `images`, `cleanup-legacy`, and `svg-utils`, ~30 callsites of `logger.warning` and ~10 of `logger.success` referenced methods that did not exist on the logger object, so any opt-in command path that hit one of those calls used to throw `TypeError: logger.warning is not a function`. The auto-purge entry point most users hit did not reach those callsites, so the bug stayed latent until commands like `purgetss brand` or `purgetss images` were run.

## v7.10.1

- User-visible references to "Tailwind" in copy that did not document a functional integration were dropped. The Class Syntax Error block now reports `'Square brackets "[ ]" are not supported'` instead of `'Tailwind-style brackets "[ ]" are not supported'`; the promotional `<Label>` injected into new projects by `purgetss create` changed from `"Tailwind-inspired utility classes for Titanium/Alloy"` to `"Utility-first styling for Titanium/Alloy"`. All functional integrations stay: the `tailwindcss@3` dependency installed by `install-dependencies` (drives both the `defaultColors`/`defaultTheme` palette base AND the VSCode IntelliSense extension), the `--tailwind` flag on `purgetss shades`, and the recommended `Tailwind CSS IntelliSense` / `Tailwind Raw Reorder (v4)` VSCode extensions.

## v7.10.0

- `purgetss images` got three CLI-only flags. `--opacity <n>` (integer `0-100`) multiplies the alpha channel of every generated density by `n/100`, useful for placeholder or default ImageView images that render at reduced opacity. `--padding <n>` (integer `0-40`) shrinks the rendered image inside each density canvas by symmetric percentage borders, preserving canvas size with transparent fill, for breathing room around an unpadded logo. `--output <relpath>` overrides the basename and subpath relative to each platform's `images/` root, so a logo from `purgetss/brand/` can be written as `images/logos/loading.png` across all densities in a single command. Combines naturally for "transparent placeholder with padding under a custom path". See [Multi-density images: opacity, padding, output](./app-assets/2-multi-density-images.md).
- `purgetss brand` now generates `MarketplaceArtworkFeature.png` (1024×500 Google Play Feature Graphic) alongside the existing iTunesConnect and MarketplaceArtwork submission assets. Auto-discovers `purgetss/brand/logo-feature.{svg,png}` or reuses the master logo if not provided. Default vertical padding is `12%`; override with `--feature-graphic-padding <n>` (range `0-40`), config `brand.padding.featureGraphic`, or CLI `--feature-logo <path>` for a dedicated source. Submission artwork only: written to project root for upload to the Play Console, not bundled into the APK.
- Arbitrary nesting depth in `config.cjs` `theme` objects. Property emission now walks nested values recursively instead of stopping at level 2, so `theme.extend.colors.brand.primary.500` flattens to `brand-primary-500` instead of being silently dropped. Same for `backgroundGradient` and `backgroundSelectedGradient`. Default modifier keys (`default`, `global`, `DEFAULT`) collapse without contributing to the suffix.
- Fix: `apply:` now resolves built-in icon font classes (`fas`, `fab`, `fa-*`, `mi-*`, `ms-*`, `f7-*`) from `dist/` for projects that don't run `build-fonts`. Previously those classes were silently dropped from generated rules: `apply: 'fas fa-times-circle wh-12 ...'` produced everything except the FontAwesome family and the icon glyph.
- Fix: `borderRadius: [...]` arrays no longer get truncated when combined with other utilities in an `apply:` string. The post-merge dedup step (from v7.9.0) tracked depth on `{}` only, so `borderRadius: [0, 0, 0, 16]` (emitted by directional `rounded-{t,b,l,r,tl,tr,bl,br}-*` utilities) was split on its internal commas. The depth tracker now respects `[]` alongside `{}`.
- Fix: `brand --padding <n>` shortcut now applies to BOTH Android paddings as the help text always promised. Previously the shortcut only fed `androidAdaptivePadding` while `androidLegacyPadding` fell through to its own config value, so `purgetss brand --padding 17` actually produced `androidAdaptive=17, androidLegacy=10`.

## v7.9.0

- Opacity modifiers now work on classes that resolve to a semantic color. Writing `bg-surface/65` (or any other opacity modifier on a class mapped to a name in `semantic.colors.json`) produces a working rule with Light/Dark switching preserved. PurgeTSS derives a new `<originalKey>_<alphaPercent>` entry with the original `light`/`dark` hex values and the requested alpha for both modes, writes it back to `semantic.colors.json`, and emits the rule against the derived key. Re-runs are idempotent; manual edits with conflicting values halt the build with a `Conflict` error. New alpha entries require one full Titanium build to be picked up. Liveview hot-reload alone does not refresh `semantic.colors.json`. See [Semantic Colors](./best-practices/2-semantic-colors.md#opacity-modifier-auto-derivation).
- Several fixes around semantic colors, gradients, and Ti Element defaults: the `semantic` tonal palette was inverting Light and Dark; the gradient `from`/`to` color order was position-dependent and could swap after `sort()`; the `bg-gradient-to-X` direction was silently dropped when combined with `from-X to-Y` colors in the same `apply` string; `theme.Window` / `theme.View` / `theme.ImageView` no longer leak the framework presets (white background, `Ti.UI.SIZE`, iOS `hires: true`) when defined at the top level (replace mode), so a Window declared at `theme.Window` with a `backgroundGradient` no longer ghosts on top of a default `backgroundColor: '#FFFFFF'`. `theme.extend.Window` keeps merging with the defaults as before. See [Apply directive: extend mode vs replace mode](./customization/3-the-apply-directive.md#extend-mode-vs-replace-mode).
- **Breaking**: the user-facing glossary output path was renamed from `purgetss/experimental/tailwind-classes/` to `purgetss/glossary/tailwind-classes/`. Any tooling or CI that reads from the old path needs to be updated on upgrade. No transition shim was added on purpose. The `--glossary` flag and command surface are unchanged.

## v7.8.0

- `images` now has a `--width <n>` flag. It pins Android `mdpi` (= iPhone `@1x`) to a specific pixel width, for example `purgetss images logo.svg --width 256`. Larger scales derive at ×1.5, ×2, ×3, and ×4, with height staying proportional to the source's aspect ratio. Use this for SVG sources from vector editors with disproportionate viewBoxes, such as Affinity or Illustrator. Without the flag, every scale derives from the source's viewBox as a 4× master, which can produce unpredictable sizes when the viewBox does not match the intended display size. When you pass an SVG without `--width`, the command prints a one-time hint and then falls back to the legacy 4× behavior. This is CLI-only; there is no matching `images:` config property because the right width is per-asset.
- Class syntax pre-validation now stops `purgetss` with a structured `Class Syntax Error` block (file + line + suggested fix) when it detects known class-name mistakes: inverted negative sign (`top-(-10)` → `-top-(10)`), Tailwind-style brackets (`top-[10px]` → `top-(10px)`), empty parentheses (`wh-()`), whitespace inside parentheses (`wh-( 200 )`), and redundant `px` unit (`top-(10px)` → `top-(10)`). All offenders are reported in one run. Generic unknown classes, such as typos, vendor utilities not enabled, or custom classes not declared yet, are not flagged. They still flow into the `// Unused or unsupported classes` block in `app.tss`.
- The arbitrary-value parser no longer crashes on negative values inside parentheses. Classes like `top-(-10)`, `mt-(-5)`, and `origin-(-10,-20)` used to trigger a `Cannot read properties of null (reading 'pop')` exception. The parser now extracts the `(...)` portion first, so a `-` inside the value does not break the split.

## v7.7.0

- The `brand` config was cleaned up before stabilizing. Branding settings now live under grouped sections: `brand.logos`, `brand.padding`, `brand.android`, `brand.ios`, and `brand.colors`.
- `brand` can now use separate Android inputs: one logo for the general brand set, another for Android launcher icons, and another for Android 12+ splash artwork. Use `brand.logos.androidLauncher` / `--icon-logo` and `brand.logos.androidSplash` / `--splash-logo`, or drop `logo-icon.*` and `logo-splash.*` into `purgetss/brand/`.
- `purgetss brand` now regenerates the legacy Android splash fallback: `app/assets/android/default.png` in Alloy projects and `Resources/android/default.png` in Classic projects.
- `cleanup-legacy` no longer removes `default.png`, because that file can still matter on older Titanium Android splash paths.
- The branding docs now explain what uses `ic_launcher`, what uses `splash_icon.png`, and what still falls back to `default.png`.

## v7.6.2

- The `semantic` command now works in Classic Titanium projects. It writes to `Resources/semantic.colors.json`; Alloy keeps writing to `app/assets/semantic.colors.json`. Existing unrelated entries, such as the default `backgroundColor` / `textColor` values that ship with Classic templates, are preserved in both project types. See [Semantic Colors](./best-practices/2-semantic-colors.md)
- Fixed a UX bug where the "not an Alloy project" error was immediately followed by the palette preview JSON, making it look like the command half-succeeded.

## v7.6.1

- `brand` and `images` now ask before destructive writes (`y` / `N` / `a` for "always"). The prompt is skipped when `stdin` is not a TTY (alloy.jmk hook, CI, pipes), when `-y` / `--yes` is passed, or when `PURGETSS_YES=1` is set. Set `confirmOverwrites: false` on the matching config section to silence it permanently.
- SVG logos and images now get a disproportionate-viewBox warning. The command detects viewBoxes above 4096 pt on any side, common in Affinity and Illustrator exports, and rasterizes with adaptive density to stay within Sharp's pixel budget.
- `init` now creates `purgetss/{fonts,brand,images}/` subfolders.
- Multi-line command output is now grouped under a single `::PurgeTSS::` header with indented continuation lines. This applies across `purge`, `fonts`, `icon-library`, `brand`, `images`, and most warnings.

## v7.6.0

- The new `brand` command generates the Titanium branding set from logos auto-discovered in `./purgetss/brand/`: launcher icons, adaptive icons, iOS 18+ Dark/Tinted variants, marketplace artwork, and optional notification/splash assets. Works on Alloy and Classic projects. See [App icons and branding](./app-assets/1-app-icons-and-branding.md)
- The new `images` command generates multi-density UI images from sources in `./purgetss/images/`: Android `res-*` densities and iPhone `@1x`/`@2x`/`@3x` scales. Subdirectories are preserved, and short paths can target individual files for re-processing. See [Multi-density images](./app-assets/2-multi-density-images.md)
- `brand:` and `images:` config sections were added to `purgetss/config.cjs`. Percentages can be written as `'15%'` strings for clarity; plain numbers are also accepted. Older configs get these sections on first run.
- The new `semantic` command generates Titanium semantic colors (Light/Dark mode) into `app/assets/semantic.colors.json`. `--single` switches between a tonal palette (one base hex → 11 shades with mirror inversion + auto config mapping) and a purpose-based color (explicit per-mode hex + optional alpha). The JSON entry and class mapping in `config.cjs` are written in one shot. Class names are derived by stripping the `Color` suffix, for example `surfaceColor` → `surface`. Smart in-place updates run when a single name matches an existing palette shade. See [Semantic Colors: generating semantic colors with the `semantic` command](./best-practices/2-semantic-colors.md#generating-semantic-colors-with-the-semantic-command)

## v7.5.3

- New `Appearance` export for Light/Dark/System mode switching with persistence. Methods: `init()`, `set(mode)`, `get()`, `toggle()`. See [Appearance Setup](./best-practices/1-appearance-setup.md)
- Default font family classes are generated automatically with platform-appropriate values: `font-sans`, `font-serif`, and `font-mono`.
- XML validation now detects illegal `--` inside XML comments during pre-validation.

## v7.5.0

- `extend` support for Window, View, and ImageView. Component defaults can now be customized from `theme.extend` in `config.cjs`.
- Shorthand `apply`: `{ apply: '...' }` is automatically normalized, so the `default:` wrapper is optional.
- Property deduplication: applied values win over static defaults instead of duplicating.
- Automatic platform resolution: classes inside `ios:` / `android:` blocks find their platform-specific version automatically.
- Font Awesome 7.2.0
- Fixed: `extend.Window` silently ignored, duplicate `font` properties, array-type properties missing `[ ]` notation

## v7.4.0

Animation module expansion: 9 new methods bring the module to 15 total.

- `transition`, `pulse`, `sequence`, `swap`, `shake`, `snapTo`, `reorder`, `undraggable`, `detectCollisions`
- New utility classes: `snap-back`, `snap-center`, `snap-magnet`, `keep-z-index`
- Delta-based drag for transformed views, position normalization, property inheritance from the Animation object

See the [UI Module documentation](./purgetss-ui/1-introduction.md) for full details.

## v7.3.0

- BREAKING: `tailwind.tss` was renamed to `utilities.tss`.
- XML syntax validation adds pre-validation for Alloy XML files with line numbers and fix suggestions.
- Classic Titanium compatibility: `deviceInfo()` works without Alloy dependencies.

## v7.2.7

- Security fixes for command injection in `glob` and prototype pollution in `js-yaml`.
- Dependency cleanup reduced installation size by about 45MB and removed unused packages.
- Titanium SDK 13.1.0.GA: new utility classes for `navBarColor`, `forceBottomPosition`, and `multipleWindows`.

## v7.2.6

- Updated Font Awesome to version 7.1.0
- Simplified flag property names in utilities.tss
