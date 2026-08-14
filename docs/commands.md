# Commands

> ℹ️ **INFO**
>
> Changelog
> 
> For the latest changes and release notes, see the [CHANGELOG](https://github.com/macCesar/purgetss/blob/main/CHANGELOG.md).


This page lists the commands available in PurgeTSS.

## Setup commands
- `init`: Initializes PurgeTSS on an existing Alloy project.
- `create`: Creates a new Alloy project with PurgeTSS already set up.
- `brand`: Regenerates every image the Titanium template ships, from one main logo: launcher icons, adaptive icons, iOS 18+ Dark/Tinted variants, marketplace artwork, and the iOS and Android splash sets. Per-piece logo overrides and a `--only` filter are available when needed.
- `images`: Generates multi-density UI images (Android `res-*` densities, iPhone `@1x`/`@2x`/`@3x` scales) from sources in `./purgetss/images/`. Works on both Alloy and Classic projects.

## Development commands
- `build`: Generates `utilities.tss` from `config.cjs`.
- `watch`: Runs `purgetss` automatically on each project compile (defaults to `--on`).

## Asset commands
- `icon-library`: Copies the official icon fonts into `./app/assets/fonts`.
- `build-fonts`: Generates `./purgetss/styles/fonts.tss` with class definitions and `fontFamily` selectors for custom fonts.

## Utility commands
- `shades`: Generates shades and tints for a color and writes the palette to `config.cjs`.
- `semantic`: Generates Titanium semantic colors (Light/Dark mode) into `app/assets/semantic.colors.json`. It supports tonal palettes (one base hex -> 11 shades with mirror inversion) and single purpose-based colors (explicit light + dark + optional alpha).
- `color-module`: Creates `./app/lib/purgetss.colors.js` with the colors defined in `config.cjs`.
- `module`: Installs `purgetss.ui.js` in the `lib` folder.

## Maintenance commands
- `update`: Updates PurgeTSS to the latest version.
- `sudo-update`: Updates PurgeTSS using `sudo` to install npm modules if needed.


## `init` command

The `init` command sets up PurgeTSS by creating `./purgetss/config.cjs` at the root of an existing Alloy project.

No arguments or options are needed. The command creates the file inside `./purgetss/`.

```bash
> purgetss init

# alias:
> purgetss i
```

`./purgetss/config.cjs`
```javascript
module.exports = {
  purge: {
    mode: 'all',
    method: 'sync', // How to execute the auto-purging task: sync or async

    // These options are passed directly to PurgeTSS
    options: {
      missing: true, // Reports missing classes
      widgets: false, // Purges widgets too
      safelist: [], // Array of classes to keep
      plugins: [] // Array of properties to ignore
    }
  },
  brand: {
    background: '#FFFFFF',   // inherited by every piece that doesn't set its own
    confirmOverwrites: true, // prompt before overwriting files (set false to skip)
    optimize: false,         // true = quantize the generated PNGs to a palette (lossy, ~71% smaller)

    // One block per piece. Artwork comes from purgetss/brand/logo-<piece>.{svg,png};
    // these keys are for numbers, colors and activation. Padding is never inherited.
    icon:             { padding: '4%' },    // DefaultIcon.png + DefaultIcon-ios.png
    dark:             { background: null }, // DefaultIcon-Dark.png
    tinted:           {},                   // DefaultIcon-Tinted.png
    iosSplash:        { padding: '26%' },   // assets/iphone/Default*.png × 16
    launchLogo:       { padding: '12%' },   // LaunchLogo.png (1024×1024)
    marketplace:      {},                   // iTunesConnect.png + MarketplaceArtwork.png
    featureGraphic:   { padding: '12%' },   // MarketplaceArtworkFeature.png (1024×500)
    adaptive:         { padding: '18%' },   // ic_launcher_{foreground,background,monochrome}.png × 5 + ic_launcher.xml
    legacyIcon:       { padding: '10%' },   // ic_launcher.png × 5
    appicon:          {},                   // appicon.png (128×128)
    androidSplash:    { padding: '26%' },   // assets/android/default.png + images/res-*/default.png × 11

    // Opt-in: inert until you edit the Android theme / FCM meta-data by hand.
    splashIcon:       { enabled: false },   // drawable-*/splash_icon.png × 5
    notificationIcon: { enabled: false },   // drawable-*/ic_stat_notify.png × 5
    ninePatch:        { enabled: false }    // background.9.png (not implemented yet)
  },
  images: {
    quality: 85,             // JPEG/WebP/AVIF quality (0-100)
    format: null,            // null = keep original; 'webp' | 'jpeg' | 'png' to convert every image
    autoSync: true,          // false = SVG pipeline computes dims but doesn't write to images.files
    confirmOverwrites: true, // prompt before overwriting files (set false to skip)
    files: []                // per-file overrides: [{ filename: 'images/<sub>/<name>.<ext>', width, height? }]
  },
  theme: {
    extend: {}
  }
};
```

`init` also creates empty `purgetss/fonts/`, `purgetss/brand/`, and `purgetss/images/` folders so you can see where each kind of asset goes.

> 💡 **TIP**
>
> To learn more
> 
> PurgeTSS looks for `./purgetss/config.cjs`. Each section is optional and can be customized. Missing sections use the default configuration.
> 
> For examples, see the [Configuration section](./customization/1-configuring-guide.md).



## `create` command

The `create` command generates a new Alloy project with PurgeTSS already set up.

### Arguments

- Enclose the project name in single or double quotes. Required.

### Options
- Use `-f, --force` to overwrite an existing project.
- Use `-d, --dependencies` to install ESLint and Tailwind CSS.
- Use `-v, --vendor [fa,mi,ms,f7]` to copy the selected fonts into your project and add the CommonJS module in `./app/lib/`. See the [`icon-library` command](#icon-library-command) for available fonts.

If a project with the same name already exists, the command will prompt you to confirm whether you want to overwrite it.

```bash
> purgetss create 'Name of the Project' [--vendor="fontawesome, materialicons, materialsymbols, framework7"]

# alias:
> purgetss c 'Name of the Project' [-v=fa,mi,ms,f7]
```

### Requirements

Make sure `app.idprefix` and `app.workspace` are configured in Titanium's `config.json`.

```bash
# A name in reverse domain name format.
app.idprefix = "com.yourdomain"

# Path to use as the workspace directory for new projects.
app.workspace = "/<full-path-to>/<workspace>/<folder>"
# ...
```

Use `ti config` to set up both settings:

```bash
ti config app.idprefix 'com.yourdomain'
ti config app.workspace 'the-full-path/to-the-workspace-folder'
```

### Installing dev dependencies

Adds linting and editor support to an existing project.

```bash
> purgetss create 'Name of the Project' [--dependencies]

# alias:
> purgetss c 'Name of the Project' [-d]
```

This option installs ESLint, Tailwind CSS, and setup files for Visual Studio Code (VSCode).

Recommended VSCode extensions:

- [XML Tools](https://marketplace.visualstudio.com/items?itemName=DotJoshJohnson.xml): XML formatting.
- [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint): Linting and coding standards.
- [Tailwind CSS IntelliSense](https://marketplace.visualstudio.com/items?itemName=bradlc.vscode-tailwindcss): PurgeTSS class support.
- [Tailwind Raw Reorder (v4)](https://marketplace.visualstudio.com/items?itemName=KevinYouu.tailwind-raw-reorder-tw4): Class sorting for XML and JS files.
- [Intellisense for CSS class names in HTML](https://marketplace.visualstudio.com/items?itemName=Zignd.html-css-class-completion): Class completion based on your `purgetss/config.cjs`, including `fonts.tss` and `utilities.tss`.

### List of commands used

Running `purgetss create "Name of the Project" [--dependencies --vendor=fa,mi,ms,f7]` executes:

- `ti config app.idprefix && ti config app.workspace` - retrieves the related values.
- `ti create -t app -p all -n "Name of the Project" --no-prompt --id "the-prefix-id-and-the-name-of-the-project"` - creates the app project and id.
- `cd app.workspace/"Name of the Project"` - changes to the new folder.
- `alloy new` - converts it to an Alloy project.
- `purgetss w` - runs PurgeTSS on compile.
- `purgetss b` - builds `./purgetss/styles/utilities.tss`.
- `[--vendor=fa,mi,ms,f7]` - copies the selected fonts and the CommonJS module into `./app/lib/`.
- `[--dependencies]` - installs:
  - `npm i -D tailwindcss && npx tailwindcss init` - Tailwind CSS.
  - `npm i -D eslint eslint-config-axway eslint-plugin-alloy` - ESLint and Titanium plugins.
  - `.editorconfig`, `eslint.config.js`, `tailwind.config.js`, `.vscode/extensions.json`, `.vscode/settings.json` - config files.
- `code .`, `subl .`, or `open .` - opens the project in VS Code, Sublime Text, or Finder.


## `brand` command

Regenerates every image the Titanium template ships, from a main logo image: launcher icons, adaptive icons, iOS 18+ Dark/Tinted variants, marketplace artwork, and both splash sets. The rule is *if the template ships the file, `brand` updates it*. Each piece can take its own artwork, and `--only` narrows a run to the pieces you name. Alloy and Classic projects are detected automatically.

> 💡 **TIP**
>
> Full guide
> This is a quick reference. See [App icons and branding](./app-assets/1-app-icons-and-branding.md) for the full guide: workflow, padding guidance, Android dark mode, iOS 18+ variants, and troubleshooting.
> It also includes the full `brand:` config reference.


```bash
> purgetss brand                                         # uses purgetss/brand/logo.svg + config
```

### The pieces

| Piece               | Config key         | Generates                                                                    | On by default               |
| ------------------- | ------------------ | ---------------------------------------------------------------------------- | --------------------------- |
| `icon`              | `icon`             | `DefaultIcon.png` + `DefaultIcon-ios.png`                                     | yes                         |
| `dark`              | `dark`             | `DefaultIcon-Dark.png`                                                        | yes                         |
| `tinted`            | `tinted`           | `DefaultIcon-Tinted.png`                                                      | yes                         |
| `ios-splash`        | `iosSplash`        | `assets/iphone/Default*.png` × 16                                             | yes                         |
| `launch-logo`       | `launchLogo`       | `LaunchLogo.png` (1024×1024)                                                  | when `logo-launch.*` exists |
| `marketplace`       | `marketplace`      | `iTunesConnect.png` + `MarketplaceArtwork.png`                                | yes                         |
| `feature-graphic`   | `featureGraphic`   | `MarketplaceArtworkFeature.png` (1024×500)                                    | yes                         |
| `adaptive`          | `adaptive`         | `ic_launcher_{foreground,background,monochrome}.png` × 5 + `ic_launcher.xml`   | yes                         |
| `legacy-icon`       | `legacyIcon`       | `ic_launcher.png` × 5                                                         | yes                         |
| `appicon`           | `appicon`          | `appicon.png` (128×128)                                                       | yes                         |
| `android-splash`    | `androidSplash`    | `assets/android/default.png` + `images/res-*/default.png` × 11                | yes                         |
| `splash-icon`       | `splashIcon`       | `drawable-*/splash_icon.png` × 5                                              | `--splash-icon`             |
| `notification-icon` | `notificationIcon` | `drawable-*/ic_stat_notify.png` × 5                                           | `--notification-icon`       |
| `nine-patch`        | `ninePatch`        | `background.9.png` (not implemented yet)                                      | `--nine-patch`              |

Groups for `--only`: `ios` (icon, dark, tinted, ios-splash), `store` (marketplace, feature-graphic), `android` (adaptive, legacy-icon, appicon, android-splash).

### Using custom logo paths

By default, PurgeTSS auto-discovers logos from `purgetss/brand/`: `logo.{svg,png}` for the main artwork, `logo-<piece>.{svg,png}` for a specific piece. To use custom paths, set `logo` on the piece in `config.cjs`:

`./purgetss/config.cjs`
```javascript
module.exports = {
  brand: {
    background: '#FFFFFF',   // inherited by every piece that doesn't set its own
    confirmOverwrites: true, // prompt before overwriting files (set false to skip)
    optimize: false,         // true = quantize the generated PNGs to a palette (lossy, ~71% smaller)

    // One block per piece. Artwork comes from purgetss/brand/logo-<piece>.{svg,png};
    // these keys are for numbers, colors and activation. Padding is never inherited.
    icon:             { padding: '4%' },    // DefaultIcon.png + DefaultIcon-ios.png
    dark:             { background: null }, // DefaultIcon-Dark.png
    tinted:           {},                   // DefaultIcon-Tinted.png
    iosSplash:        { padding: '26%' },   // assets/iphone/Default*.png × 16
    launchLogo:       { padding: '12%' },   // LaunchLogo.png (1024×1024)
    marketplace:      {},                   // iTunesConnect.png + MarketplaceArtwork.png
    featureGraphic:   { padding: '12%' },   // MarketplaceArtworkFeature.png (1024×500)
    adaptive:         { padding: '18%' },   // ic_launcher_{foreground,background,monochrome}.png × 5 + ic_launcher.xml
    legacyIcon:       { padding: '10%' },   // ic_launcher.png × 5
    appicon:          {},                   // appicon.png (128×128)
    androidSplash:    { padding: '26%' },   // assets/android/default.png + images/res-*/default.png × 11

    // Opt-in: inert until you edit the Android theme / FCM meta-data by hand.
    splashIcon:       { enabled: false },   // drawable-*/splash_icon.png × 5
    notificationIcon: { enabled: false },   // drawable-*/ic_stat_notify.png × 5
    ninePatch:        { enabled: false }    // background.9.png (not implemented yet)
  }
}
```

### Options

Project and output

- `--project <path>`: project root (defaults to cwd).
- `--dry-run`: preview what would be generated without writing any files.
- `--output <dir>`: stage into `<dir>` instead of writing in place.
- `-y, --yes`: skip the overwrite confirmation prompt for this invocation.

Selecting what to generate

- `--only <pieces>`: comma-separated pieces or groups. Generates a named piece even when its opt-in flag is absent; an unknown name aborts before writing anything.

Visual customization

- `--bg-color <hex>`: background inherited by every piece that doesn't set its own.
- `--padding <n>`: shortcut that sets both Android launcher paddings to the same value for one run.
- `--android-adaptive-padding <n>`: adaptive icon safe-zone % (default `18`).
- `--android-legacy-padding <n>`: legacy `ic_launcher.png` padding % (default `10`).
- `--ios-padding <n>`: padding % for the four square iOS/marketplace pieces (range `2-8`, default `4`).
- `--feature-graphic-padding <n>`: vertical padding % for `MarketplaceArtworkFeature.png` (default `12`, range `0-40`).
- `--launch-logo-padding <n>`: padding % for `LaunchLogo.png` (default `12`).
- `--splash-padding <n>`: shortcut that sets both splash paddings to the same value for one run.
- `--android-splash-padding <n>`: padding % for `default.png` and the 11 `res-*` splashes (default `26`).
- `--ios-splash-padding <n>`: padding % for the 16 iPhone launch images (default `26`).

Splash padding is a share of the canvas's **shorter** side, so one number keeps the logo at the same visual weight in portrait and in landscape: the `26%` default leaves it at 48% of the shorter side.

Optional asset types

- `--notification-icon`: also emit `ic_stat_notify.png × 5`.
- `--splash-icon`: also emit `splash_icon.png × 5`.
- `--nine-patch`: declared but not implemented yet; prints a warning and writes nothing.

Logo variants and overrides

Every piece has a `--<piece>-logo` flag, each overriding the matching `purgetss/brand/logo-<piece>.{svg,png}`:

- `--icon-logo <path>`: `DefaultIcon.png` / `DefaultIcon-ios.png`.
- `--dark-logo <path>`: iOS 18+ dark variant.
- `--tinted-logo <path>`: iOS 18+ tinted variant.
- `--ios-splash-logo <path>`: the 16 iPhone launch images.
- `--launch-logo <path>`: source for `LaunchLogo.png`; passing it also activates the piece.
- `--marketplace-logo <path>`: `iTunesConnect.png` / `MarketplaceArtwork.png`.
- `--feature-graphic-logo <path>`: the Google Play Feature Graphic (1024×500).
- `--adaptive-logo <path>`: Android adaptive launcher icons.
- `--legacy-icon-logo <path>`: legacy `ic_launcher.png`.
- `--appicon-logo <path>`: `appicon.png`.
- `--android-splash-logo <path>`: Android &lt;12 splash artwork.
- `--splash-icon-logo <path>`: Android 12+ `splash_icon.png`.
- `--notification-icon-logo <path>`: `ic_stat_notify.png`.

Two more sources are not pieces:

- `--monochrome-logo <path>`: the silhouette shared by the adaptive monochrome layer and the notification icons (`purgetss/brand/logo-mono.{svg,png}`).
- the positional `<logo>` argument: the main logo, source for every piece that has no override.

Output size:

- `--optimize`: re-encode every generated PNG with a quantized palette. Lossy, ~71% smaller on a full brand set. Also settable as `brand.optimize` in `config.cjs`.
- `--no-optimize`: skip that pass even when `brand.optimize` is `true`.

Appearance:

- `--dark-bg-color <hex>`: opaque dark bg for `DefaultIcon-Dark.png` (default: transparent per Apple HIG).
- `--no-dark`: skip `DefaultIcon-Dark.png`.
- `--no-tinted`: skip `DefaultIcon-Tinted.png`.

> ⚠️ **CAUTION**
>
> Breaking changes in v7.13.0
> `--splash` → `--splash-icon`, `--notification` → `--notification-icon`, `--splash-logo` → `--splash-icon-logo`, `--feature-logo` → `--feature-graphic-logo`. `--icon-logo` now feeds the `icon` piece; the Android launcher source is `--adaptive-logo`. `--legacy-splash` is gone: the per-qualifier Android splashes are part of `android-splash` and always generated. No flag aliases were kept. The `brand:` config block also changed shape, but that one updates itself. See [Older configs update themselves](./app-assets/1-app-icons-and-branding.md#older-configs-update-themselves).


Legacy cleanup

- `--cleanup-legacy`: remove obsolete branding artifacts (reads `tiapp.xml` for safety rules).
- `--aggressive`: with `--cleanup-legacy`, also remove `ldpi` density folders.

Diagnostics

- `--notes`: print the complete platform launch/theme setup + padding tuning guide.
- `--debug`: print extra diagnostics.

> ℹ️ **INFO**
>
> Confirmation prompt
> `brand` writes in place, so it asks `Continue? [y/N/a]` before overwriting anything. Pick `a` (always) to write `confirmOverwrites: false` into the `brand:` section of `config.cjs` and silence the prompt on future runs. The prompt is skipped automatically when `stdin` is not a TTY (alloy.jmk hook, CI, pipes), when you pass `-y` / `--yes`, or when `PURGETSS_YES=1` is set.


### Examples

```bash
> purgetss brand                                          # uses purgetss/brand/logo.svg + config
> purgetss brand --only icon                              # just the DefaultIcon pair
> purgetss brand --only ios,notification-icon             # a group plus one opt-in piece
> purgetss brand --bg-color "#0B1326"                     # override bg color
> purgetss brand --adaptive-logo ./docs/app-icon.svg      # dedicated square Android launcher mark
> purgetss brand --splash-icon --splash-icon-logo ./docs/splash.svg  # custom Android 12+ splash artwork
> purgetss brand --launch-logo ./docs/wordmark.svg        # iOS launch screen logotype
> purgetss brand --feature-graphic-logo ./docs/feature.svg # custom Google Play Feature Graphic logo
> purgetss brand --feature-graphic-padding 8              # tighter Feature Graphic padding (more impact)
> purgetss brand --notification-icon --splash-icon        # add notification + splash icons
> purgetss brand --no-tinted                              # skip iOS 18+ tinted variant
> purgetss brand --dry-run                                # preview without writing
> purgetss brand --cleanup-legacy --dry-run               # preview legacy cleanup
```

`brand` writes four Android-facing asset groups with different jobs:

- `ic_launcher*` for the app icon and the default Android 12+ system splash path
- `appicon.png` as Titanium's fallback for `tiapp.xml`'s `<icon>` when the manifest declares no `android:icon`
- `default.png` + `images/res-*/default.png` for the Android &lt;12 splash
- `splash_icon.png` when you pass `--splash-icon` and want custom Android 12+ splash artwork

The recommended workflow is convention-first:

- put your files in `purgetss/brand/`
- let auto-discovery pick them up
- use `config.cjs` only when you need a persistent override
- use CLI flags only for one-off runs

`brand.background` is baked into the generated assets, but PurgeTSS does not automatically edit the iOS LaunchScreen or Android configuration. Run `purgetss brand --notes` to print the iOS snippet plus a complete launcher-only Android theme and Activity override with the current color. The Android snippet points its three launch-related theme attributes to one `splashscreen_background` resource, so the launch color is changed in a single line. See [Matching the launch background](./app-assets/1-app-icons-and-branding.md#matching-the-launch-background) for the full setup and merge rules.


## `images` command

Generates multi-density variants of your UI images from one high-resolution source per image. That includes buttons, illustrations, logos, and screen graphics. Alloy and Classic projects are detected automatically.

> 💡 **TIP**
>
> Full guide
> This is a quick reference. See [Multi-density images](./app-assets/2-multi-density-images.md) for the full guide: 4× source convention, single-file regeneration, format conversion, and troubleshooting.
> 
> For SVGs referenced from views or controllers, see [SVG-aware compile-time pipeline](./app-assets/3-svg-pipeline.md). It runs with every `purgetss` command and rasterizes only the SVGs your views use.


```bash
> purgetss images                                        # uses purgetss/images/ + config
```

### Options

Source selection

- `[source]` (positional): optional path to override auto-discovery. Resolves first against `purgetss/images/` (short paths like `buttons/btn.png`), then against cwd.

Platform filter

- `--android`: only Android density variants. Mutually exclusive with `--ios`.
- `--ios`: only iPhone scale variants. Mutually exclusive with `--android`.

Output format

- `--format <ext>`: convert all outputs to `webp`, `jpeg`, `png`, `avif`, `gif`, or `tiff`. Default: keep source format.
- `--quality <n>`: quality `0-100` for lossy formats. Default `85`.

Sizing

- `--width <n>`: pin Android `mdpi` (= iPhone `@1x`) to `<n>` pixels wide. Larger scales derive as ×1.5, ×2, ×3, ×4 from that base; height stays proportional to the source's aspect ratio. Accepts integers in `[1, 8192]`. Use it for SVG sources from vector editors with disproportionate viewBoxes, such as Affinity or Illustrator. Without it, every scale derives from the source's natural pixel size as a 4× master, which can produce unexpected output when the viewBox does not match the intended display size.

Transformations

- `--opacity <n>`: multiply the alpha channel of every generated density by `n/100`. Integer in `[0, 100]`. Useful for placeholder or default ImageView images that render at reduced opacity (loading states, watermarks). Applied so each density inherits the same proportional transparency.
- `--padding <n>`: shrink the rendered image inside each density canvas by `n%` symmetric borders. Integer in `[0, 40]`. The output canvas keeps the size it would have without padding; what changes is the rendered logo inside it, with transparent borders filling the rest. Combines with `--opacity` for placeholders that need reduced opacity AND breathing room around an unpadded source logo.
- `--output <relpath>`: override basename + subfolder relative to the images output root. The full multi-density pattern is preserved. Only the basename and subpath change. Constraints: no extension (decided by `--format` or source ext), no absolute paths, no `..` segments, single-file source only.

Project and output

- `--dry-run`: preview without writing any files.
- `--project <path>`: project root (defaults to cwd).
- `-y, --yes`: skip the overwrite confirmation prompt for this invocation.

Diagnostics

- `--debug`: print extra diagnostics.

### Examples

```bash
> purgetss images                                        # uses purgetss/images/ + config
> purgetss images background/pink-texture.png            # re-process one file (short path)
> purgetss images background/                            # re-process one subfolder
> purgetss images --android                              # only Android densities
> purgetss images --format webp --quality 90             # convert all outputs to WebP
> purgetss images logo.svg --width 256                   # pin SVG output to 256 px @1x/mdpi
> purgetss images logo.svg --opacity 50 --format png     # semi-transparent placeholder, all densities
> purgetss images purgetss/brand/logo.svg --opacity 30 --output 'logos/loading' --format png
> purgetss images purgetss/brand/logo.png --opacity 30 --padding 15 --output 'logos/default' --format png
> purgetss images --dry-run                              # preview
```

> ℹ️ **INFO**
>
> Confirmation prompt
> Like `brand`, `images` writes in place and asks `Continue? [y/N/a]` before overwriting anything. `a` (always) writes `confirmOverwrites: false` into the `images:` section of `config.cjs`. Skipped automatically when `stdin` is not a TTY, when you pass `-y` / `--yes`, or when `PURGETSS_YES=1` is set.



## `install-dependencies` command

This command installs dev dependencies and configuration files in an existing PurgeTSS project. It also sets up Visual Studio Code (VSCode) support.

```bash
> purgetss install-dependencies

# alias:
> purgetss id
```

> ⚠️ **CAUTION**
>
> Important
> 
> This command overwrites any existing `extensions.json` and `settings.json` files. Back them up if you want to keep your current versions.



## `icon-library` command

The `icon-library` command copies the free font files for Font Awesome, Material Icons, Material Symbols, and Framework7 Icons into `./app/assets/fonts`.

```bash
> purgetss icon-library [--vendor=fa,mi,ms,f7] [--module] [--styles]

# alias:
> purgetss il [-v=fa,mi,ms,f7] [-m] [-s]
```

### Options and flags

- `-v, --vendor [fa,mi,ms,f7]`: copy specific font vendors.
- `-m, --module`: copy the corresponding CommonJS module into `./app/lib/`.
- `-s, --styles`: copy the corresponding `tss` files into `./purgetss/styles/` for review.

`./app/assets/fonts/`
```bash
FontAwesome7Brands-Regular.ttf
FontAwesome7Free-Regular.ttf
FontAwesome7Free-Solid.ttf
Framework7-Icons.ttf
MaterialIcons-Regular.ttf
MaterialIconsOutlined-Regular.otf
MaterialIconsRound-Regular.otf
MaterialIconsSharp-Regular.otf
MaterialIconsTwoTone-Regular.otf
MaterialSymbolsOutlined-Regular.ttf
MaterialSymbolsRounded-Regular.ttf
MaterialSymbolsSharp-Regular.ttf
```

After copying the fonts, you can use them in Buttons and Labels. For Font Awesome, set the font family to `fa` (Solid icons) and use a class like `fa-home`.

You do not need to copy any `.tss` file for this to work: PurgeTSS resolves the official icon classes at compile time and writes them to the generated `app/styles/app.tss` (not `purgetss/styles/utilities.tss`). See [Icon font libraries](./customization/8-icon-fonts-libraries.md) for the full reference.

### Available font classes

- [fontawesome.tss](https://github.com/macCesar/purgeTSS/blob/main/dist/fontawesome.tss)
- [materialicons.tss](https://github.com/macCesar/purgeTSS/blob/main/dist/materialicons.tss)
- [materialsymbols.tss](https://github.com/macCesar/purgeTSS/blob/main/dist/materialsymbols.tss)
- [framework7icons.tss](https://github.com/macCesar/purgeTSS/blob/main/dist/framework7icons.tss)

### Copying specific font vendors

To copy specific font vendors, use any of the following arguments:

```bash
> purgetss icon-library --vendor="fontawesome, materialicons, materialsymbols, framework7"

# alias:
> purgetss il -v=fa,mi,ms,f7
```

Available names and aliases:
- fa, fontawesome = Font Awesome Icons
- mi, materialicons = Material Icons
- ms, materialsymbol = Material Symbols
- f7, framework7 = Framework7 Icons

### CommonJS module

Use `--module` to copy the corresponding CommonJS module into `./app/lib/`.

```bash
> purgetss icon-library --module [--vendor="fontawesome, materialicons, materialsymbols, framework7"]

# alias:
> purgetss il -m [-v=fa,mi,ms,f7]
```

Each library includes a CommonJS module that exposes Unicode strings for the icon fonts.

All prefixes are stripped from their class names and camel-cased. For example:

- Font Awesome: `fa-flag` becomes `flag`.
- Material Icons: `mi-flag` becomes `flag`.
- Material Symbols: `ms-flag` becomes `flag`.
- Framework7 Icons: `f7-alarm_fill` becomes `alarmFill` and `f7-clock_fill` becomes `clockFill`.

## `build-fonts` command

The `build-fonts` command generates `./purgetss/styles/fonts.tss` from font files placed in `./purgetss/fonts/`. Use it for brand typography, custom icon fonts, or any community icon library not bundled with PurgeTSS.

```bash
> purgetss build-fonts [--module] [--font-class-from-filename]

# alias:
> purgetss bf [-m] [-f]
```

### Options and flags

- `-m, --module`: generate a CommonJS module in `./app/lib/purgetss.fonts.js`.
- `-f, --font-class-from-filename`: use the font's filename as the font class name and icon prefix instead of the font family name (replaces the old `-p` flag).

For the full workflow with examples (Google Fonts, custom icon libraries, `--module` output, filename-based prefixes), see [Custom fonts](./customization/7-custom-fonts.md).


## `shades` command

The `shades` command generates shades and tints for a given color and writes the palette to `config.cjs`.

> ℹ️ **INFO**
>
> Your config.cjs keeps its comments
> `shades` and `semantic` rewrite only the `theme:` section of `config.cjs`; every other byte of the file, comments included, is left exactly as you wrote it. Before v7.13.0 both commands serialized the whole config object and wrote it back, which reformatted the file and dropped every comment in it, `purge:`, `brand:` and `images:` included.
> 
> Comments *inside* `theme:` are still replaced, since that is the section being rewritten.


```bash
> purgetss shades [hexcode] [name]

# alias:
> purgetss s [hexcode] [name]
```

### Arguments

- `[hexcode]`: the base hexcode value. Omit this to create a random color.
- `[name]`: the color name. Omit this and PurgeTSS chooses a name based on the color's hue.

### Options

- `-n, --name`: Specifies the name of the color.
- `-q, --quotes`: Retains double quotes in the `config.cjs` file.
- `-r, --random`: Generates shades from a random color.
- `-s, --single`: Generates a single color definition.
- `-t, --tailwind`: Logs the generated shades with a `tailwind.config.js` compatible structure.
- `-l, --log`: Logs the generated shades instead of saving them.
- `-j, --json`: Logs a JSON compatible structure, which can be used in `./app/config.json`, for example.

> 💡 **TIP**
>
> Need Titanium semantic colors (Light/Dark mode)?
> Use the dedicated [`semantic` command](#semantic-command). It writes to `app/assets/semantic.colors.json` and generates either an 11-step tonal palette with mirror inversion or a single purpose-based color with explicit per-mode hex and optional alpha.


> ℹ️ **INFO**
>
> More than 66% of `utilities.tss` classes are tied to color properties, so `shades` is a practical way to extend a palette.


Basic usage:

```bash
> purgetss shades 53606b Primary

# alias:
> purgetss s 53606b Primary

::PurgeTSS:: "Primary" (#53606b) saved in config.cjs
```

The shades are added to `config.cjs`. The next time `purgetss` runs, `utilities.tss` picks them up.

`./purgetss/config.cjs`
```js
module.exports = {
  // ...
  theme: {
    extend: {
      colors: {
        primary: {
          '50': '#f4f6f7',
          '100': '#e3e7ea',
          '200': '#cad2d7',
          '300': '#a6b3ba',
          '400': '#7a8b96',
          '500': '#5f707b',
          '600': '#53606b',
          '700': '#464f58',
          '800': '#3e444c',
          '900': '#373c42',
          default: '#53606b'
        }
      }
    }
  },
  // ...
}
```

Use `--log` to print the result to the console instead of saving it to `config.cjs`.

```bash
> purgetss shades 53606b Primary --log

# alias:
> purgetss s 53606b Primary -l

::PurgeTSS:: "Primary" (#53606b)
{
  colors: {
    primary: {
      '50': '#f4f6f7',
      '100': '#e3e7ea',
      '200': '#cad2d7',
      '300': '#a6b3ba',
      '400': '#7a8b96',
      '500': '#5f707b',
      '600': '#53606b',
      '700': '#464f58',
      '800': '#3e444c',
      '900': '#373c42',
      default: '#53606b'
    }
  }
}
```

Use `--tailwind` to print the generated shades in a `tailwind.config.js`-compatible structure.

```bash
> purgetss shades 000f3d --tailwind

# alias:
> purgetss s 000f3d -t

::PurgeTSS:: "Stratos" (#000f3d)
{
  colors: {
    stratos: {
      '50': '#e5f4ff',
      '100': '#cfecff',
      '200': '#a9d8ff',
      '300': '#75bbff',
      '400': '#3f8cff',
      '500': '#145dff',
      '600': '#0047ff',
      '700': '#0048ff',
      '800': '#0040e3',
      '900': '#000f3d'
    }
  }
}
```

To generate a random color value, use `--random`. Here it is paired with `--log`:

```bash
> purgetss shades -rl

::PurgeTSS:: "Harlequin" (#44ed20)
{
  colors: {
    harlequin: {
      '50': '#ecffe6',
      '100': '#d5fec9',
      '200': '#adfd99',
      '300': '#7bf85e',
      '400': '#44ed20',
      '500': '#2ed40e',
      '600': '#1daa06',
      '700': '#19810a',
      '800': '#18660e',
      '900': '#175611',
      default: '#44ed20'
    }
  }
}
```

To print a Titanium `config.json`-compatible structure to the console, use `--json`:

```bash
> purgetss shades '#65e92c' -j

# alias:
> purgetss s '#65e92c' -j

::PurgeTSS:: "Lima" (#65e92c)
{
  global: {
    colors: {
      lima: #65e92c,
      lima-50: #f0fee7,
      lima-100: #dcfdca,
      lima-200: #bbfb9b,
      lima-300: #90f561,
      lima-400: #65e92c,
      lima-500: #48d012,
      lima-600: #34a60a,
      lima-700: #297e0d,
      lima-800: #246410,
      lima-900: #215413
    }
  }
}
```

> ℹ️ **INFO**
>
> `shades` was the first command that wrote to `config.cjs`. Please report any issues you run into.



## `semantic` command

The `semantic` command generates [Titanium semantic colors](./best-practices/2-semantic-colors.md) into `app/assets/semantic.colors.json`, with Light/Dark mode support built in. Use `--single` to switch modes.

```bash
> purgetss semantic [hexcode] [name]
```

### Palette mode (no `--single`)

One base hex becomes an 11-shade tonal palette with mirror-by-index Light/Dark inversion anchored at shade `500`. The command writes both files in one step: the JSON gets 11 entries, and `config.cjs` gets the family mapped to those semantic keys.

```bash
> purgetss semantic '#15803d' amazon

::PurgeTSS:: "amazon" palette (11 shades) saved to app/assets/semantic.colors.json
```

Result:

`./app/assets/semantic.colors.json`
```json
{
  "amazon50":  { "light": "#052e14", "dark": "#f0fdf5" },
  "amazon500": { "light": "#22c55f", "dark": "#22c55f" },
  "amazon950": { "light": "#f0fdf5", "dark": "#052e14" }
}
```

`./purgetss/config.cjs`
```js
theme: {
  extend: {
    colors: {
      amazon: {
        '50':  'amazon50',
        '500': 'amazon500',
        '950': 'amazon950'
      }
    }
  }
}
```

Classes like `bg-amazon-50`, `text-amazon-500`, `border-amazon-950` flip tonal contrast automatically with the system appearance.

### Single mode (`--single`)

This mode uses explicit per-mode hex values for purpose-based semantic colors such as `surfaceColor`, `textColor`, `borderColor`, or `overlayColor`. It writes the JSON entry and maps it to a class in `config.cjs` by stripping the conventional `Color` suffix.

```bash
> purgetss semantic --single '#F9FAFB' surfaceColor       --dark '#0f172a'
> purgetss semantic --single '#FFFFFF' surfaceHighColor   --dark '#1e293b'
> purgetss semantic --single '#111827' textColor          --dark '#f1f5f9'
> purgetss semantic --single '#3B82F6' accentColor        --dark '#60a5fa' --alpha 80
> purgetss semantic --single '#000000' overlayColor       --alpha 50
```

The name is preserved verbatim as the JSON key, including camelCase. When `--dark` is omitted, it defaults to the light hex. That is useful for overlays or glass surfaces where alpha is the only variation. Each command logs the mapping it wrote:

```text
::PurgeTSS:: "surfaceColor" saved to app/assets/semantic.colors.json and mapped to class surface in config.cjs.
::PurgeTSS:: "surfaceHighColor" saved to app/assets/semantic.colors.json and mapped to class surface-high in config.cjs.
```

After the batch above:

`./app/assets/semantic.colors.json`
```json
{
  "surfaceColor":     { "light": "#F9FAFB", "dark": "#0f172a" },
  "surfaceHighColor": { "light": "#FFFFFF", "dark": "#1e293b" },
  "textColor":        { "light": "#111827", "dark": "#f1f5f9" },
  "accentColor":      { "light": { "color": "#3B82F6", "alpha": "80" },
                        "dark":  { "color": "#60a5fa", "alpha": "80" } },
  "overlayColor":     { "light": { "color": "#000000", "alpha": "50" },
                        "dark":  { "color": "#000000", "alpha": "50" } }
}
```

`./purgetss/config.cjs`
```js
theme: {
  extend: {
    colors: {
      surface:         'surfaceColor',
      'surface-high':  'surfaceHighColor',
      text:            'textColor',
      accent:          'accentColor',
      overlay:         'overlayColor'
    }
  }
}
```

You can use the classes immediately: `bg-surface`, `bg-surface-high`, `text-text`, `bg-accent`, `bg-overlay`.

#### Customizing the class name

The auto-mapping uses the most literal Titanium-style transform: strip `Color`, then kebab-case the rest. If your design system prefers different names, for example `on-surface` instead of `text`, or nesting the surface family, edit `config.cjs` after running the command:

`./purgetss/config.cjs`
```js
theme: {
  extend: {
    colors: {
      surface:         { DEFAULT: 'surfaceColor', high: 'surfaceHighColor' },
      'on-surface':    'textColor',
      'on-surface-variant': 'textSecondaryColor',
      muted:           'textMutedColor',
      border:          'borderColor',
      accent:          'accentColor',
      overlay:         'overlayColor'
    }
  }
}
```

The next `purgetss build` will pick up the renamed classes. Editing one line is usually faster than typing the whole mapping from scratch.

### Smart in-place updates

If a `--single` name matches an existing palette shade, PurgeTSS narrows the operation to an in-place JSON value edit. For example, `pt semantic --single '#000' amazon500` edits `amazon500` when the `amazon` palette already exists. The entry stays in its original position, and `config.cjs` is left untouched because the palette already maps to that key.

```bash
> purgetss semantic --single '#ff0000' amazon500 --alpha 80

::PurgeTSS:: amazon500 updated in app/assets/semantic.colors.json - palette amazon already references this key, config.cjs left unchanged.
```

That is the right behavior: you are editing one shade of an existing palette, not creating a new top-level color called `amazon500`.

### Re-running replaces the family

Re-running on the same family replaces it. Before writing, PurgeTSS strips every prior key that belonged to that family, including the bare name and the 11 shade keys, then writes the new entries. Unrelated palettes and manually-defined entries such as `textSecondaryColor` stay untouched. Switching a family between palette and single forms does not leave stale keys behind.

### Options

- `-s, --single`: Generate a single purpose-based semantic color (requires explicit per-mode hex values).
- `-d, --dark <hex>`: With `--single`, the dark-mode hex (defaults to the light value).
- `-a, --alpha <0-100>`: With `--single`, wraps both modes in `{ color, alpha }` per the Titanium spec. Range `0.0–100.0`, integer or float.
- `-n, --name <name>`: Specify the name (alternative to the positional argument).
- `-r, --random`: In palette mode, use a random base color.
- `-o, --override`: Place the mapping in `theme.colors` instead of `theme.extend.colors`.
- `-q, --quotes`: Keep double quotes in `config.cjs`.
- `-l, --log`: Preview the JSON without writing any files.


## `color-module` command

This command creates `purgetss.colors.js` in the `lib` folder with all colors defined in `config.cjs`.

```bash
> purgetss color-module

# alias:
> purgetss cm
```

`./lib/purgetss.colors.js`
```js
module.exports = {
  harlequin: {
    '50': '#ecffe6',
    '100': '#d5fec9',
    '200': '#adfd99',
    '300': '#7bf85e',
    '400': '#44ed20',
    '500': '#2ed40e',
    '600': '#1daa06',
    '700': '#19810a',
    '800': '#18660e',
    '900': '#175611',
    default: '#44ed20'
  },
  primary: {
    '50': '#f4f6f7',
    '100': '#e3e7ea',
    '200': '#cad2d7',
    '300': '#a6b3ba',
    '400': '#7a8b96',
    '500': '#5f707b',
    '600': '#53606b',
    '700': '#464f58',
    '800': '#3e444c',
    '900': '#373c42',
    default: '#53606b'
  },
  lima: {
    '50': '#f0fee7',
    '100': '#dcfdca',
    '200': '#bbfb9b',
    '300': '#90f561',
    '400': '#65e92c',
    '500': '#48d012',
    '600': '#34a60a',
    '700': '#297e0d',
    '800': '#246410',
    '900': '#215413',
    default: '#65e92c'
  }
}
```

Use this when you want to reference configured colors from code instead of hardcoding values in multiple places.


## `build` command

The `build` command generates `utilities.tss` from `config.cjs`. Run it after you change `config.cjs`.

```bash
> purgetss build

# alias:
> purgetss b
```

When `purgetss` runs (manually or via `watch`), it checks for changes in `config.cjs` and regenerates `utilities.tss` when needed.


## `watch` command

The `watch` command runs PurgeTSS on each project compile. You do not need to run `build` manually after each change.

```bash
> purgetss watch

# alias:
> purgetss w
```

This works well with LiveView because it re-runs after changes such as adding or removing styles in views.

The command will install a task in the `alloy.jmk` file to enable this behavior:

```javascript
task('pre:compile', function(event, logger) {
  require('child_process').execSync('purgetss', logger.warn('::PurgeTSS:: Auto-Purging ' + event.dir.project));
});
```

> ℹ️ **INFO**
>
> About `watch`
> 
> This feature works with standard Alloy projects compiled using `ti build`. It has not been tested with project types built using Webpack or Vue.


To deactivate it, use `--off`.
```bash
> purgetss watch --off

# alias:
> purgetss w -o
```


## `module` command

The `module` command installs `purgetss.ui.js` in the `lib` folder.

```bash
> purgetss module

# alias:
> purgetss m
```

The PurgeTSS module includes:

- Animation methods for playing or applying basic animations and transformations to Alloy objects.

> 💡 **TIP**
>
> To learn more
> 
> See the [The UI Module](./purgetss-ui/1-introduction.md) documentation for details.



## `update` command

The `update` command upgrades PurgeTSS to the latest version.

```bash
> purgetss update

# alias:
> purgetss u
```

Runs `npm install -g purgetss@latest`.


## `sudo-update` command

The `sudo-update` command is the same as `update`, but uses `sudo` to install npm modules when needed.

```bash
> purgetss sudo-update

# alias:
> purgetss su
```
