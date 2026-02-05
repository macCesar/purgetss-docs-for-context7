---
sidebar_position: 2
slug: commands
---

# Commands

:::info What's new in v7.2.x

**PurgeTSS** v7.2 adds full support for **Font Awesome 7**, including the CSS custom properties format. It also reduces install size and refactors internals for performance and maintainability.

**Key changes**:
- **Node.js 20+** required (due to the "inquirer" v13 upgrade).
- **Font Awesome 7** support, including the CSS custom properties format.
- **Titanium SDK 13.1.x** support, with new properties from 13.1.0.GA.
- **Removed deprecated commands**: `copy-fonts` and `build-legacy`.
- **Install size reduced** by about 45MB by moving non-essential assets to dev dependencies.
- **Improved Unicode extraction** for more formats and direct character mappings.

:::

This page lists the commands available in PurgeTSS.

## Setup commands
- `init`: Initializes PurgeTSS on an existing Alloy project.
- `create`: Creates a new Alloy project with PurgeTSS already set up.

## Development commands
- `build`: Generates `utilities.tss` from `config.cjs`.
- `watch`: Runs `purgetss` automatically on each project compile (defaults to `--on`).

## Asset commands
- `icon-library`: Copies the official icon fonts into `./app/assets/fonts`.
- `build-fonts`: Generates `./purgetss/styles/fonts.tss` with class definitions and `fontFamily` selectors for custom fonts.

## Utility commands
- `shades`: Generates shades and tints for a color and writes the palette to `config.cjs`.
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

```javascript title="./purgetss/config.cjs"
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
  theme: {
    extend: {}
  }
};
```

:::tip To learn more

PurgeTSS looks for `./purgetss/config.cjs`. Each section is optional and can be customized. Missing sections use the default configuration.

For examples, see the [Configuration section](customization/the-config-file).

:::


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

Ensure that `app.idprefix` and `app.workspace` are configured in Titanium's `config.json`.

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

Installing these dependencies adds linting and editor support for projects using PurgeTSS.

```bash
> purgetss create 'Name of the Project' [--dependencies]

# alias:
> purgetss c 'Name of the Project' [-d]
```

This option installs ESLint for code quality, Tailwind CSS for utility classes, and setup files for Visual Studio Code (VSCode).

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


## `install-dependencies` command

This command installs dev dependencies and configuration files in existing PurgeTSS projects, and sets up Visual Studio Code (VSCode) support.

```bash
> purgetss install-dependencies

# alias:
> purgetss id
```

:::caution Important

This command overwrites any existing `extensions.json` and `settings.json` files. Back them up if you want to keep your current versions.

:::


## `icon-library` command

The `icon-library` command copies the free font files for Font Awesome, Material Icons, Material Symbols, and/or Framework7 Icons into `./app/assets/fonts`. It avoids manual downloading and placement.

```bash
> purgetss icon-library [--vendor=fa,mi,ms,f7] [--module] [--styles]

# alias:
> purgetss il [-v=fa,mi,ms,f7] [-m] [-s]
```

### Options and Flags

  - `-v, --vendor [fa,mi,ms,f7]` to copy specific font vendors
  - `-m, --module` to copy the corresponding CommonJS module into the `./app/lib/` folder
  - `-s, --styles` to copy the corresponding `tss` files into the `./purgetss/styles/` folder for your review

```bash title="./app/assets/fonts/"
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

After copying the fonts, you can use them in Buttons and Labels. For example, for Font Awesome, set the font family to `fa` (Solid icons) and use a class like `fa-home`.

### Available font classes

- [fontawesome.tss](https://github.com/macCesar/purgeTSS/blob/master/dist/fontawesome.tss)
- [materialicons.tss](https://github.com/macCesar/purgeTSS/blob/master/dist/materialicons.tss)
- [materialsymbols.tss](https://github.com/macCesar/purgeTSS/blob/master/dist/materialsymbols.tss)
- [framework7icons.tss](https://github.com/macCesar/purgeTSS/blob/master/dist/framework7icons.tss)

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

You can use the `--module` option to copy the corresponding CommonJS module into the `./app/lib/` folder.

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

### Font Awesome Pro

If you have a [Font Awesome Pro account](https://fontawesome.com/pro), you can generate a custom `./purgetss/styles/fontawesome.tss` file with the Pro-only classes (except duotone icons; see note below).

After setting the [@fortawesome scope](https://fontawesome.com/how-to-use/on-the-web/setup/using-package-managers#installing-pro) with your token, install it in your project's root folder using `npm init` and `npm install --save-dev @fortawesome/fontawesome-pro` (current version 7.1.0).

To generate a new `purgetss/styles/fontawesome.tss`, run `purgetss build`. It also copies the Pro font files into `./app/assets/fonts` if needed.

Note: Titanium cannot use Font Awesome duotone icons because each icon uses two glyphs.

### Font Awesome 7 Beta

To generate a custom `fontawesome.tss` file from [Font Awesome 7 Beta](https://fontawesome.com/download):

Move the "css" and "webfonts" folders from "fontawesome-pro-7.0.0-beta3-web/":

```bash
fontawesome-pro-7.0.0-beta3-web
└─ css
└─ webfonts
```

Into `./purgetss/fontawesome-beta`:

```bash
purgetss
└─ fontawesome-beta
   ├─ css
   └─ webfonts
```

Then run `purgetss build` to generate your custom `fontawesome.tss` file and test the new icons.

### Font example file

To use this file, follow these steps:

- Copy the content of `index.xml` into a new Alloy project.
- Install the official icon font files using `purgetss icon-library`.
  - Without `--vendor`, PurgeTSS copies all official icon fonts.
- Run `purgetss` once to generate the required files.
- Compile your app as usual.
- Use `liveview` if you want faster testing.

```xml title="index.xml"
<Alloy>
  <Window>
    <View class="grid">
      <View class="vertical mx-auto grid-cols-2 gap-y-2">
        <!-- FontAwesome -->
        <Label class="mt-2 text-gray-700" text="FontAwesome" />
        <Button class="fa fa-home my-1 h-10 w-10 text-xl text-blue-500" />
        <Button class="fa fa-home my-1 h-10 w-10 rounded bg-blue-500 text-xl text-white" />
      </View>

      <View class="vertical mx-auto grid-cols-2 gap-y-2">
        <!-- Material Icons -->
        <Label class="mt-2 text-gray-700" text="Material Icons" />
        <Button class="mi mi-home my-1 h-10 w-10 text-xl text-blue-500" />
        <Button class="mi mi-home my-1 h-10 w-10 rounded bg-blue-500 text-xl text-white" />
      </View>

      <View class="vertical mx-auto grid-cols-2 gap-y-2">
        <!-- Material Symbol -->
        <Label class="mt-2 text-gray-700" text="Material Symbol" />
        <Button class="ms ms-home my-1 h-10 w-10 text-xl text-blue-500" />
        <Button class="ms ms-home my-1 h-10 w-10 rounded bg-blue-500 text-xl text-white" />
      </View>

      <View class="vertical mx-auto grid-cols-2 gap-y-2">
        <!-- Framework7-Icons -->
        <Label class="mt-2 text-gray-700" text="Framework7-Icons" />
        <Button class="f7 f7-house my-1 h-10 w-10 text-xl text-blue-500" />
        <Button class="f7 f7-house my-1 h-10 w-10 rounded bg-blue-500 text-xl text-white" />
      </View>
    </View>
  </Window>
</Alloy>
```

```css title="app.tss"
/* PurgeTSS v7.2.7
 * Created by César Estrada
 * https://github.com/macCesar/purgeTSS
*/

/* Ti Elements */
'View': { width: Ti.UI.SIZE, height: Ti.UI.SIZE }
'Window': { backgroundColor: '#FFFFFF' }

/* Main Styles */
'.bg-blue-500': { backgroundColor: '#3b82f6' }
'.gap-y-2': { top: 8, bottom: 8 }
'.grid': { layout: 'horizontal', width: '100%' }
'.grid-cols-2': { width: '50%' }
'.h-10': { height: 40 }
'.mt-2': { top: 8 }
'.mx-auto': { right: null, left: null }
'.my-1': { top: 4, bottom: 4 }
'.rounded': { borderRadius: 4 }
'.text-blue-500': { color: '#3b82f6', textColor: '#3b82f6' }
'.text-gray-700': { color: '#374151', textColor: '#374151' }
'.text-white': { color: '#ffffff', textColor: '#ffffff' }
'.text-xl': { font: { fontSize: 20 } }
'.vertical': { layout: 'vertical' }
'.w-10': { width: 40 }

/* Default Font Awesome */
'.fa': { font: { fontFamily: 'FontAwesome7Free-Solid' } }
'.fa-home': { text: '\uf015', title: '\uf015' }

/* Material Icons */
'.mi': { font: { fontFamily: 'MaterialIcons-Regular' } }
'.mi-home': { text: '\ue88a', title: '\ue88a' }

/* Material Symbols */
'.ms': { font: { fontFamily: 'MaterialSymbolsOutlined-Regular' } }
'.ms-home': { text: '\ue88a', title: '\ue88a' }

/* Framework7 */
'.f7': { font: { fontFamily: 'Framework7-Icons' } }
'.f7-house': { text: 'house', title: 'house' }
```

<div align="center">
![iOS Screen - Icon Fonts](images/font-example-file-material-symbols.png)
</div>

## `build-fonts` command

The `build-fonts` command generates a `fonts.tss` file with class definitions and `fontFamily` selectors for serif, sans-serif, cursive, fantasy, or monospace fonts.

To use it, place all `.ttf` or `.otf` files in `./purgetss/fonts/`, then run the command. You can also use `--module` to generate a CommonJS module in `./app/lib/`.

```bash
> purgetss build-fonts

# alias:
> purgetss bf
```

1. This will create the `./purgetss/styles/fonts.tss` file with all class definitions and `fontFamily` selectors.
2. It will also copy the font files into the `./app/assets/fonts` folder.
3. PurgeTSS renames the font files to match their PostScript names so they work on both iOS and Android.

In this example, we use [Bevan and Dancing Script](https://fonts.google.com/share?selection.family=Bevan:ital@0;1%7CDancing%20Script:wght@400;500;600;700) from Google Fonts.

First, place the "ttf" font files into `./purgetss/fonts/` folder:

```bash title="./purgetss/fonts/"
purgetss
└─ fonts
   ├─ Bevan-Italic.ttf
   ├─ Bevan-Regular.ttf
   ├─ DancingScript-Bold.ttf
   ├─ DancingScript-Medium.ttf
   ├─ DancingScript-Regular.ttf
   └─ DancingScript-SemiBold.ttf
```

After running `> purgetss build-fonts` you will have the following classes:

```css title="./purgetss/styles/fonts.tss"
/* Fonts TSS file generated with PurgeTSS
 * https://github.com/macCesar/purgeTSS
*/

'.bevan-italic': { font: { fontFamily: 'Bevan-Italic' } }
'.bevan-regular': { font: { fontFamily: 'Bevan-Regular' } }

'.dancingscript-bold': { font: { fontFamily: 'DancingScript-Bold' } }
'.dancingscript-medium': { font: { fontFamily: 'DancingScript-Medium' } }
'.dancingscript-regular': { font: { fontFamily: 'DancingScript-Regular' } }
'.dancingscript-semibold': { font: { fontFamily: 'DancingScript-SemiBold' } }
```

You can now use these fonts in your project.

### Organizing the Fonts Folder

For better organization, you can group each font family in subfolders. For example:

```bash title="./purgetss/fonts/"
purgetss
└─ fonts
   └─ bevan
      ├─ Bevan-Italic.ttf
      ├─ Bevan-Regular.ttf
   └─ dancing-script
      ├─ DancingScript-Bold.ttf
      ├─ DancingScript-Medium.ttf
      ├─ DancingScript-Regular.ttf
      └─ DancingScript-SemiBold.ttf
```

By organizing the fonts folder in this way, you will get the same `fonts.tss` file as in the previous example, but with a much more organized "fonts" folder.

:::tip Tip

### Renaming `fontFamily` classes

If you want to use a shorter or different name for any of the font classes, simply rename the font file to your desired name.

For example:

```bash title="./purgetss/fonts/"
purgetss
└─ fonts
   └─ dancing-script
      ├─ Script-Bold.ttf
      ├─ Script-Medium.ttf
      ├─ Script-Regular.ttf
      └─ Script-SemiBold.ttf
```

Running `build-fonts` will adjust the class name accordingly:

```css title="./purgetss/styles/fonts.tss"
'.script-bold': { font: { fontFamily: 'DancingScript-Bold' } }
'.script-medium': { font: { fontFamily: 'DancingScript-Medium' } }
'.script-regular': { font: { fontFamily: 'DancingScript-Regular' } }
'.script-semibold': { font: { fontFamily: 'DancingScript-SemiBold' } }
```

:::

### Icon font libraries

You can add any icon font library that includes a `.ttf` or `.otf` file and a `.css` file with Unicode characters.

In this example, we use the [map-icons](http://map-icons.com) and [microns](https://www.s-ings.com/projects/microns-icon-font/) libraries.

```bash title="./purgetss/fonts/"
purgetss
└─ fonts
   └─ bevan
   └─ dancing-script
   └─ map-icons
      ├─ map-icons.css
      └─ map-icons.ttf
   └─ microns
      ├─ microns.css
      └─ microns.ttf
```

:::info

After running `purgetss build-fonts`, `fonts.tss` will include the `fontFamily` class definitions and Unicode characters.

:::

```css title="./purgetss/styles/fonts.tss"
/* Fonts TSS file generated with PurgeTSS */
/* https://github.com/macCesar/purgeTSS */

'.map-icons': { font: { fontFamily: 'map-icons' } }
'.microns': { font: { fontFamily: 'microns' } }

/* Unicode Characters */
/* To use your Icon Fonts in Buttons AND Labels each class sets 'text' and 'title' properties */

/* map-icons/map-icons.css */
'.map-icon-abseiling': { text: '\ue800', title: '\ue800' }
'.map-icon-accounting': { text: '\ue801', title: '\ue801' }
'.map-icon-airport': { text: '\ue802', title: '\ue802' }
'.map-icon-amusement-park': { text: '\ue803', title: '\ue803' }
'.map-icon-aquarium': { text: '\ue804', title: '\ue804' }
/* ... */

/* microns/microns.css */
'.mu-arrow-left': { text: '\ue700', title: '\ue700' }
'.mu-arrow-right': { text: '\ue701', title: '\ue701' }
'.mu-arrow-up': { text: '\ue702', title: '\ue702' }
'.mu-arrow-down': { text: '\ue703', title: '\ue703' }
'.mu-left': { text: '\ue704', title: '\ue704' }
/* ... */
```

<div align="center">
![Microns Icon Font](images/mapicon-font.png)
</div>

### Options

- `-m, --module`: Generate a CommonJS module in `./app/lib/`.
- `-f, --filename`: Use filenames as both font class names and icon prefixes (replaces the old `-p` flag).

### CommonJS Module

You can use the `--module` option to generate a CommonJS module called `purgetss-fonts.js` in `./app/lib/`.

To avoid conflicts with other icon libraries, PurgeTSS keeps each icon's prefix.

```bash
> purgetss build-fonts --module

# alias:
> purgetss bf -m
```

```javascript title="./app/lib/purgetss.fonts.js"
const icons = {
  // map-icons/map-icons.css
  'mapIcon': {
    'abseiling': '\ue800',
    'accounting': '\ue801',
    'airport': '\ue802',
    'amusementPark': '\ue803',
    // ...
  },
  // microns/microns.css
  'mu': {
    'arrowLeft': '\ue700',
    'arrowRight': '\ue701',
    'arrowUp': '\ue702',
    'arrowDown': '\ue703',
    // ...
  }
};
exports.icons = icons;

const families = {
  // map-icons/map-icons.css
  'mapIcon': 'map-icons',
  // microns/microns.css
  'mu': 'microns'
};
exports.families = families;
```

:::tip Tip

### Using filenames for class names and icon prefixes

Use the `--filename` option to apply the style's filename as both the font class name and the prefix for icon class names in `fonts.tss` and property names in `purgetss.fonts.js`.

```bash title="./purgetss/fonts/"
purgetss
└─ fonts
   └─ map-icons
      └─ map.ttf
      └─ mp.css
   └─ microns
      └─ mic.ttf
      └─ mc.css
```

```css title="./purgetss/styles/fonts.tss"
/* "fontFamily" classes use the font's filename */
'.map': { font: { fontFamily: 'map-icons' } }
'.mic': { font: { fontFamily: 'microns' } }

/* map-icons/mp.css */
'.mp-abseiling': { text: '\ue800', title: '\ue800' }
'.mp-accounting': { text: '\ue801', title: '\ue801' }
'.mp-airport': { text: '\ue802', title: '\ue802' }
'.mp-amusement-park': { text: '\ue803', title: '\ue803' }
'.mp-aquarium': { text: '\ue804', title: '\ue804' }
/* ... */

/* microns/mc.css */
'.mc-arrow-left': { text: '\ue700', title: '\ue700' }
'.mc-arrow-right': { text: '\ue701', title: '\ue701' }
'.mc-arrow-up': { text: '\ue702', title: '\ue702' }
'.mc-arrow-down': { text: '\ue703', title: '\ue703' }
'.mc-left': { text: '\ue704', title: '\ue704' }
/* ... */
```

```javascript title="./app/lib/purgetss.fonts.js"
const icons = {
  // map-icons/mp.css
  'mp': {
    'abseiling': '\ue800',
    'accounting': '\ue801',
    'airport': '\ue802',
    'amusementPark': '\ue803',
    // ...
  },
  // microns/mc.css
  'mc': {
    'arrowLeft': '\ue700',
    'arrowRight': '\ue701',
    'arrowUp': '\ue702',
    'arrowDown': '\ue703',
    // ...
  }
};
exports.icons = icons;

const families = {
  // map-icons/mp.css
  'mp': 'map-icons',
  // microns/mc.css
  'mc': 'microns'
};
exports.families = families;
```

Make sure the new prefix is unique and does not conflict with other class prefixes.

:::


## `shades` command

The `shades` command generates shades and tints for a given color and writes the palette to `config.cjs`.

```bash
> purgetss shades [hexcode] [name]

# alias:
> purgetss s [hexcode] [name]
```

### Arguments

- `[hexcode]`: The base hexcode value. *Omit this to create a random color.*
- `[name]`: The name of the color. *Omit this, and a name based on the color's hue will be automatically selected.*

### Options

- `-n, --name`: Specifies the name of the color.
- `-q, --quotes`: Retains double quotes in the `config.cjs` file.
- `-r, --random`: Generates shades from a random color.
- `-s, --single`: Generates a single color definition.
- `-t, --tailwind`: Logs the generated shades with a `tailwind.config.js` compatible structure.
- `-l, --log`: Logs the generated shades instead of saving them.
- `-j, --json`: Logs a JSON compatible structure, which can be used in `./app/config.json`, for example.

:::info

More than 66% of `utilities.tss` classes are related to color properties, so `shades` is a practical way to extend color choices.

:::

Basic usage:

```bash
> purgetss shades 53606b Primary

# alias:
> purgetss s 53606b Primary

::PurgeTSS:: "Primary" (#53606b) saved in config.cjs
```

The generated color shades will be added to your `config.cjs` file, which will subsequently generate the `utilities.tss` file with the newly added colors.

```js title="./purgetss/config.cjs"
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

Use the `--log` option to output to the console instead of saving to the `config.cjs` file.

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

Use the `--tailwind` option to output the generated shades to the console with a `tailwind.config.js` compatible structure.

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

To generate a random color value, use the `--random` option. Here, the `--log` option logs it to the console:

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

To log a Titanium `config.json` compatible structure to the console, use `--json`:

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

:::info
The `shades` command is the first one that writes to `config.cjs`. If you run into issues, please report them.
:::


## `color-module` command

This command creates `purgetss.colors.js` in the `lib` folder with all colors defined in `config.cjs`.

```bash
> purgetss color-module

# alias:
> purgetss cm
```

```js title="./lib/purgetss.colors.js"
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

This is handy if you want to use these colors in code and avoid hardcoding values in multiple places.


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

This works well with LiveView since it re-runs on changes such as adding or removing styles in views.

The command will install a task in the `alloy.jmk` file to enable this behavior:

```javascript
task('pre:compile', function(event, logger) {
  require('child_process').execSync('purgetss', logger.warn('::PurgeTSS:: Auto-Purging ' + event.dir.project));
});
```

:::info About the `watch` command

This feature works with standard Alloy projects compiled using `ti build`. It has not been tested with project types built using Webpack or Vue.

:::

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

- Animation: Methods for playing or applying basic animations and transformations to Alloy objects.

:::tip To learn more

See the [Animation Module](animation-module/introduction) documentation for details.

:::


## `update` command

The `update` command upgrades PurgeTSS to the latest version.

```bash
> purgetss update

# alias:
> purgetss u
```

PurgeTSS updates include new features, updated dependencies, and bug fixes.


## `sudo-update` command

The `sudo-update` command is the same as `update`, but uses `sudo` to install npm modules when needed.

```bash
> purgetss sudo-update

# alias:
> purgetss su
```
