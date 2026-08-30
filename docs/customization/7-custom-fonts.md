# Custom fonts

Use the `purgetss/fonts/` folder and the `build-fonts` command to register any font in your Titanium app: brand typefaces, custom icon fonts, or community icon libraries that PurgeTSS no longer bundles. The command detects Alloy and Classic projects automatically.

| Output | Alloy | Classic |
|---|---|---|
| Font files | `app/assets/fonts/` | `Resources/fonts/` |
| Generated TSS classes | `purgetss/styles/fonts.tss` | Not generated |
| `--module` output | `app/lib/purgetss.fonts.js` | `Resources/lib/purgetss.fonts.js` |

Classic projects receive only native Titanium resources and, when requested, a CommonJS module. They do not receive TSS files, `app/`, or an Alloy build hook.

## The `fonts` folder

Place `.ttf` or `.otf` files in `./purgetss/fonts/`. For icon fonts, also include the `.css` file that ships with the library. PurgeTSS reads it to extract the Unicode characters for each icon class.

This example uses [Bevan and Dancing Script](https://fonts.google.com/share?selection.family=Bevan:ital@0;1%7CDancing%20Script:wght@400;500;600;700) from Google Fonts:

`./purgetss/fonts/`
```bash
purgetss
└─ fonts
   ├─ Bevan-Italic.ttf
   ├─ Bevan-Regular.ttf
   ├─ DancingScript-Bold.ttf
   ├─ DancingScript-Medium.ttf
   ├─ DancingScript-Regular.ttf
   └─ DancingScript-SemiBold.ttf
```

### Organizing the fonts folder

If you prefer to keep things tidy, group each family in a subfolder. The output is the same.

`./purgetss/fonts/`
```bash
purgetss
└─ fonts
   └─ bevan
      ├─ Bevan-Italic.ttf
      └─ Bevan-Regular.ttf
   └─ dancing-script
      ├─ DancingScript-Bold.ttf
      ├─ DancingScript-Medium.ttf
      ├─ DancingScript-Regular.ttf
      └─ DancingScript-SemiBold.ttf
```

## The `build-fonts` command

```bash
$ purgetss build-fonts [-m] [-f]

# alias:
$ purgetss bf [-m] [-f]
```

In an Alloy project it:

1. Creates `./purgetss/styles/fonts.tss` with one `fontFamily` class per file.
2. Copies the font files into `./app/assets/fonts/`, renamed to their PostScript names so they work on both iOS and Android.

In a Classic project it copies the renamed files into `./Resources/fonts/`. It deliberately skips `fonts.tss` and utility-class definitions because those belong to the Alloy/PurgeTSS styling workflow.

> ℹ️ **INFO**
>
> How this differs from the official icon fonts
> In Alloy, unlike the [official icon fonts](./8-icon-fonts-libraries.md), custom fonts generate `./purgetss/styles/fonts.tss`. That file is folded into the generated `app/styles/app.tss` when you run `purgetss` (or compile your app). Classic projects skip this step and use the font's PostScript name directly through Titanium's `fontFamily` property.


After running `purgetss build-fonts` with the Bevan and Dancing Script example above:

`./purgetss/styles/fonts.tss`
```css
// Fonts TSS file generated with PurgeTSS
// https://purgetss.com/docs/customization/custom-fonts

'.bevan-italic': { font: { fontFamily: 'Bevan-Italic' } }
'.bevan-regular': { font: { fontFamily: 'Bevan-Regular' } }

'.dancingscript-bold': { font: { fontFamily: 'DancingScript-Bold' } }
'.dancingscript-medium': { font: { fontFamily: 'DancingScript-Medium' } }
'.dancingscript-regular': { font: { fontFamily: 'DancingScript-Regular' } }
'.dancingscript-semibold': { font: { fontFamily: 'DancingScript-SemiBold' } }
```

In Alloy, you can now use these classes on any Titanium component with a `font` property: Labels, Buttons, TextFields, TextAreas, ListItems, TableViewRows, and ActivityIndicators.

In Classic, use the same PostScript name directly:

`Resources/app.js`
```javascript
const label = Ti.UI.createLabel({
  text: 'Custom font',
  font: { fontFamily: 'DancingScript-Regular', fontSize: 24 }
})
```

### Renaming the class

To use a shorter or different class name, rename the font file. For example:

`./purgetss/fonts/`
```bash
purgetss
└─ fonts
   └─ dancing-script
      ├─ Script-Bold.ttf
      ├─ Script-Medium.ttf
      ├─ Script-Regular.ttf
      └─ Script-SemiBold.ttf
```

Running `build-fonts` produces:

`./purgetss/styles/fonts.tss`
```css
'.script-bold': { font: { fontFamily: 'DancingScript-Bold' } }
'.script-medium': { font: { fontFamily: 'DancingScript-Medium' } }
'.script-regular': { font: { fontFamily: 'DancingScript-Regular' } }
'.script-semibold': { font: { fontFamily: 'DancingScript-SemiBold' } }
```

The class name changes, but the actual `fontFamily` value (the PostScript name) stays the same.

## Adding icon fonts

Any icon font that ships a `.ttf` (or `.otf`) plus a `.css` file with Unicode characters works the same way.

This example uses [map-icons](http://map-icons.com) and [microns](https://www.s-ings.com/projects/microns-icon-font/):

`./purgetss/fonts/`
```bash
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

After `purgetss build-fonts`, the generated `fonts.tss` includes the family classes and one class per icon:

`./purgetss/styles/fonts.tss`
```css
// Fonts TSS file generated with PurgeTSS
// https://purgetss.com/docs/customization/custom-fonts

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
![Microns Icon Font](../images/mapicon-font.png)
</div>

## Options

Two optional flags adjust what `build-fonts` generates:

- `-m, --module`: generates a CommonJS module in `./app/lib/purgetss.fonts.js` on Alloy or `./Resources/lib/purgetss.fonts.js` on Classic. It exposes each icon's Unicode string to JavaScript.
- `-f, --font-class-from-filename`: uses the font's filename as the font class name and icon prefix instead of the font family name. Useful when you want shorter prefixes. Replaces the old `-p` flag.

### Using `--module`

```bash
$ purgetss build-fonts --module

# alias:
$ purgetss bf -m
```

To avoid prefix conflicts between libraries, each icon keeps its prefix as a nested object:

`app/lib/purgetss.fonts.js (Alloy) or Resources/lib/purgetss.fonts.js (Classic)`
```javascript
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

### Using `--font-class-from-filename`

When you want the class name and the icon prefix to come from the **filename** of the `.ttf` and `.css` instead of the font family, use `-f`.

`./purgetss/fonts/`
```bash
purgetss
└─ fonts
   └─ map-icons
      ├─ map.ttf
      └─ mp.css
   └─ microns
      ├─ mic.ttf
      └─ mc.css
```

`./purgetss/styles/fonts.tss`
```css
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

`app/lib/purgetss.fonts.js (Alloy) or Resources/lib/purgetss.fonts.js (Classic)`
```javascript
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

> 🛑 **DANGER**
>
> Warning
> Make sure the new prefix is unique and does not conflict with other class prefixes.


## Custom proprietary icon fonts

If your design system ships its own icon font, treat it like any other icon library: drop the `.ttf` + `.css` pair into `./purgetss/fonts/<name>/` and run `build-fonts`. Alloy receives TSS classes. Classic can use the generated CommonJS module (`--module`) together with the exported font family, without adopting PurgeTSS utility classes.
