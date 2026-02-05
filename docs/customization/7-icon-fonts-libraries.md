---
sidebar_position: 7
slug: icon-fonts-libraries
---

# Icon font libraries

:::info Official icon fonts for PurgeTSS
Previous versions of PurgeTSS included several icon font libraries such as Bootstrap Icons, Boxicons, LineIcons, and Tabler Icons. We reduced the list to keep maintenance manageable.

These are the official icon fonts supported by PurgeTSS:

- [Font Awesome 7 Free (upgrade with `purgetss il -v=fa`)](https://fontawesome.com)
- [Framework 7](https://framework7.io/icons/)
- [Material Icons](https://fonts.google.com/icons?icon.set=Material+Icons)
- [Material Symbols](https://fonts.google.com/icons?icon.set=Material+Symbols)

:::

## Recreate removed libraries
You can recreate them using the `build-fonts` command:

### 1. Download the libraries
Start by downloading the libraries from their official websites:

- [Bootstrap Icons](https://icons.getbootstrap.com)
- [Boxicons](https://boxicons.com)
- [LineIcons](https://lineicons.com/icons/?type=free)
- [Tabler Icons](https://tabler-icons.io)

### 2. The `fonts` folder
Put the desired libraries in the `./purgetss/fonts` folder.

:::info
Copy the TrueType or OpenType font files and the `.css` file.
:::

```bash title="./purgetss/fonts/"
purgetss
└─ fonts
   └─ boxicons
      ├─ boxicons.css
      └─ boxicons.ttf
   └─ lineicons
      ├─ lineicons.css
      └─ lineicons.ttf
```

### 3. The `build-fonts` command
Run the `build-fonts` command to create a custom `fonts.tss` file.

```bash
$ purgetss build-fonts [--modules]

# alias:
$ purgetss bf [-m]
```

#### The `fonts.tss` file
The `build-fonts` command generates `./purgetss/styles/fonts.tss` with Unicode characters and style rules.

```css title="./purgetss/styles/fonts.tss"
'.boxicons': { font: { fontFamily: 'boxicons' } }
'.lineicons': { font: { fontFamily: 'LineIcons' } }

/* Unicode Characters */
/* To use your Icon Fonts in Buttons AND Labels each class sets 'text' and 'title' properties */

/* boxicons.css */
'.bxl-meta': { text: '\uef27', title: '\uef27' }
'.bx-lemon': { text: '\uef28', title: '\uef28' }
'.bxs-lemon': { text: '\uef29', title: '\uef29' }
/* ... */

/* lineicons.css */
'.lni-500px': { text: '\uea03', title: '\uea03' }
'.lni-add-files': { text: '\uea01', title: '\uea01' }
'.lni-adobe': { text: '\uea06', title: '\uea06' }
/* ... */
```

#### Rename the style rule name
PurgeTSS uses the font file name as the style rule name. You can change it by renaming the font file.

```bash title="./purgetss/fonts/"
# Root of the project
purgetss
└─ fonts
   └─ boxicons
      └─ bx.ttf
```

New style rule name: `'.bx'`
```css title="./purgetss/styles/fonts.tss"
/* new style rule name */
'.bx': { font: { fontFamily: 'boxicons' } }
```

#### The `assets/fonts` folder
The `build-fonts` command copies the font files to `./app/assets/fonts` and renames them to their PostScript names so they work on both iOS and Android.

```bash title="./app/assets/fonts/"
app
└─ assets
   └─ fonts
      ├─ boxicons.ttf
      └─ LineIcons.ttf
```

#### The `--modules` option
When you use the `--modules` option, it generates a `./app/lib/purgetss.fonts.js` CommonJS module file.

```javascript title="./app/lib/purgetss.fonts.js"
const icons = {
  /* boxicons */
  'boxicons': {
    'bxlMeta': '\uef27',
    'bxLemon': '\uef28',
    'bxsLemon': '\uef29',
    /* ... */
  },
  /* lineicons */
  'lni': {
    '500px': '\uea03',
    'addFiles': '\uea01',
    'adobe': '\uea06',
    /* ... */
  }
};
exports.icons = icons;
```

#### The `--prefix` option

PurgeTSS determines the group's prefix for each icon family and class name. Use `--prefix` to apply the style's filename as the prefix for class names in `fonts.tss` and property names in `purgetss.fonts.js`.

```bash title="./purgetss/fonts/"
purgetss
└─ fonts
   └─ lineicons
      └─ li.css
```

New group prefix: `li`

```css title="./purgetss/styles/fonts.tss"
/* lineicons/li.css */
'.li-zoom-out': { text: '\uea02', title: '\uea02' }
'.li-zoom-in': { text: '\uea03', title: '\uea03' }
'.li-zip': { text: '\uea04', title: '\uea04' }
/* ... */
```

```javascript title="./app/lib/purgetss.fonts.js"
const icons = {
  /* lineicons/li.css */
  'li': {
    /* ... */
  },
  /* ... */
};
exports.icons = icons;
```

:::danger Warning
Make sure the new prefix remains unique to avoid conflicts with other class prefixes.
:::
