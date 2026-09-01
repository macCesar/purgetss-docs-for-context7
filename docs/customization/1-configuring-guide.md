# The `config` file

> ℹ️ **INFO**
>
> Since v7.2.x
> The configuration file is now named `config.cjs` (it used to be `config.js`). The structure is the same.
> 
> Legacy mode was removed in PurgeTSS v7.2.x along with its related options.


By default, PurgeTSS looks for `./purgetss/config.cjs`. That is where project-level customization lives.

## Create the `config.cjs` file

> ℹ️ **INFO**
>
> `config.cjs` is created automatically the first time you run `purgetss` in a project.


If you want a clean `config.cjs`, delete the existing one and run:

```bash
> purgetss init
```

This creates a `./purgetss/config.cjs` file with the default sections:

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
    background: '#FFFFFF',      // inherited by pieces that use an opaque background canvas
    artworkCornerRadius: '0%',  // rounded non-icon artwork: splashes, Feature Graphic and LaunchLogo (0-50)
    confirmOverwrites: true,    // prompt before overwriting files (set false to skip)
    optimize: false,            // true = quantize the generated PNGs to a palette (lossy, ~71% smaller)

    // One block per piece. Artwork comes from purgetss/brand/logo-<piece>.{svg,png};
    // these keys are for numbers, colors and activation. Padding is never inherited.
    // iosSplash, androidSplash, featureGraphic and launchLogo accept cornerRadius.
    // Store and launcher icons stay square for platform masking.
    // iOS/store icons are full-bleed by default; increase padding only for logo artwork.
    icon:             { padding: '0%' },    // DefaultIcon.png + DefaultIcon-ios.png
    dark:             { background: null }, // DefaultIcon-Dark.png
    tinted:           {},                   // DefaultIcon-Tinted.png
    iosSplash:        { padding: '26%' },   // assets/iphone/Default*.png × 16
    launchLogo:       { padding: '12%' },   // LaunchLogo.png (1024×1024)
    marketplace:      {},                   // iTunesConnect.png + MarketplaceArtwork.png
    featureGraphic:   { padding: '12%' },   // MarketplaceArtworkFeature.png (1024×500)
    adaptive:         { padding: '18%' },   // ic_launcher_{foreground,background,monochrome}.png × 5 + ic_launcher.xml
    legacyIcon:       { padding: '10%' },   // ic_launcher.png × 5
    appicon:          { padding: '10%' },   // appicon.png (128×128)
    androidSplash:    { padding: '26%' },   // assets/android/default.png + images/res-*/default.png × 11

    // Opt-in: inert until you edit the Android theme / FCM meta-data by hand.
    splashIcon:       { enabled: false },   // drawable-*/splash_icon.png × 5
    notificationIcon: { enabled: false },   // drawable-*/ic_stat_notify.png × 5
    ninePatch:        { enabled: false }    // background.9.png (not implemented yet)
  },
  images: {
    quality: 85,             // JPEG/WebP/AVIF quality (0-100)
    format: null,            // null = keep original; 'webp' | 'jpeg' | 'png' to convert every image
    confirmOverwrites: true  // prompt before overwriting files (set false to skip)
  },
  theme: {
    extend: {}
  }
};
```

`init` also creates empty `purgetss/fonts/`, `purgetss/brand/`, and `purgetss/images/` folders so the project structure is clear from the start.

Every section is optional. Only add what you want to change. Anything missing falls back to the defaults.

## Structure
The config file has four main sections: `purge`, `brand`, `images`, and `theme`.

`brand:` and `images:` configure the matching commands. Their option lists live in the [`brand` guide](../app-assets/1-app-icons-and-branding.md) and the [`images` guide](../app-assets/2-multi-density-images.md). The rest of this page covers `purge` and `theme`.

For `brand`, the structure is one block per piece of artwork, each accepting the same five keys where they apply:

- `logo`: path to this piece's artwork, when it lives outside `purgetss/brand/`
- `padding`: inset per side, as a number or a percentage string like `'19%'`. Never inherited
- `cornerRadius`: integer or percentage string from `0` through `50`, valid only in `iosSplash`, `androidSplash`, `featureGraphic`, and `launchLogo`
- `background`: hex color, or `null` for transparent. Inherited from `brand.background`
- `enabled`: `false` turns a default piece off, `true` turns an opt-in piece on

Plus `brand.background`, `brand.artworkCornerRadius` (shared non-icon artwork radius, default `'0%'`), optional `brand.splashCornerRadius` (splash-only override), `brand.confirmOverwrites`, `brand.optimize`, `brand.logo` (the main logo) and `brand.monochromeLogo`.

Use flags for temporary artwork sources, visual geometry, the shared background, one-run selection or activation, and optimization. Keep persistent preferences in config: `confirmOverwrites`, permanent `enabled` values, and exceptional backgrounds for individual pieces. For corner radius, a piece-specific flag wins over the splash shortcut when applicable, then the shared artwork flag, piece config, splash-only config when applicable, `brand.artworkCornerRadius`, and the `0%` default.

A `brand:` block written for an older PurgeTSS is rewritten to this structure on the next run, carrying over every value that had been customized. A key that belongs to no structure at all, a typo for instance, aborts the run with the list of valid ones instead. For the property-by-property reference, see [App icons and branding](../app-assets/1-app-icons-and-branding.md#brand-config-reference).

### Overriding logo paths

By default, PurgeTSS auto-discovers logo files from `purgetss/brand/`: `logo.{svg,png}` for the main artwork and `logo-<piece>.{svg,png}` for a specific piece. If you want to use custom paths, set `logo` on the piece:

`Example: Custom logo paths`
```javascript
module.exports = {
  brand: {
    background: '#FFFFFF',      // inherited by pieces that use an opaque background canvas
    artworkCornerRadius: '0%',  // rounded non-icon artwork: splashes, Feature Graphic and LaunchLogo (0-50)
    confirmOverwrites: true,    // prompt before overwriting files (set false to skip)
    optimize: false,            // true = quantize the generated PNGs to a palette (lossy, ~71% smaller)

    // One block per piece. Artwork comes from purgetss/brand/logo-<piece>.{svg,png};
    // these keys are for numbers, colors and activation. Padding is never inherited.
    // iosSplash, androidSplash, featureGraphic and launchLogo accept cornerRadius.
    // Store and launcher icons stay square for platform masking.
    // iOS/store icons are full-bleed by default; increase padding only for logo artwork.
    icon:             { padding: '0%' },    // DefaultIcon.png + DefaultIcon-ios.png
    dark:             { background: null }, // DefaultIcon-Dark.png
    tinted:           {},                   // DefaultIcon-Tinted.png
    iosSplash:        { padding: '26%' },   // assets/iphone/Default*.png × 16
    launchLogo:       { padding: '12%' },   // LaunchLogo.png (1024×1024)
    marketplace:      {},                   // iTunesConnect.png + MarketplaceArtwork.png
    featureGraphic:   { padding: '12%' },   // MarketplaceArtworkFeature.png (1024×500)
    adaptive:         { padding: '18%' },   // ic_launcher_{foreground,background,monochrome}.png × 5 + ic_launcher.xml
    legacyIcon:       { padding: '10%' },   // ic_launcher.png × 5
    appicon:          { padding: '10%' },   // appicon.png (128×128)
    androidSplash:    { padding: '26%' },   // assets/android/default.png + images/res-*/default.png × 11

    // Opt-in: inert until you edit the Android theme / FCM meta-data by hand.
    splashIcon:       { enabled: false },   // drawable-*/splash_icon.png × 5
    notificationIcon: { enabled: false },   // drawable-*/ic_stat_notify.png × 5
    ninePatch:        { enabled: false }    // background.9.png (not implemented yet)
  }
}
```

You only need to override the ones you're using. Missing overrides still auto-discover from `purgetss/brand/`.

### `purge` section
The `purge` section controls how PurgeTSS removes unused classes or keeps the ones you want.

`The purge section`
```javascript
module.exports = {
  purge: {
    mode: 'all',
    method: 'sync', // How to execute the auto-purging task: sync or async

    // These options are passed through directly to PurgeTSS
    options: {
      missing: true, // Reports missing classes
      widgets: false, // Purges widgets too
      safelist: [], // Array of classes to keep
      plugins: [] // Array of properties to ignore
    }
  },
}
```

- `mode: 'all'`

  By default, PurgeTSS searches XML files everywhere: comments, attributes, classes, IDs, and Ti Elements.

  Use this mode if you want PurgeTSS to parse Ti Elements you style in `config.cjs`.

- `method`

  The `method` setting controls how the auto-purge task runs: `sync` (default) or `async`.

  If changes are not showing up when rebuilding a project with TiKit Components and LiveView, set the method to `async`.

- `mode: 'class'`

  Use `class` to search only class and ID attributes in XML files.

- `options.missing`

  Set `missing` to `true` if you want a list of missing or misspelled classes at the end of `app.tss`.

  > ℹ️ **INFO**
>
> Helpful when you want to confirm you did not forget class definitions or when you are upgrading from PurgeTSS v5 to v6.


- `options.widgets`

  Set `widgets` to `true` to also parse all XML files under the Widgets folder.

- `options.safelist`

  The `safelist` is a list of classes and Ti Elements you want to keep no matter the purge mode or whether they appear in XML.

  If the list is large, put it in a CommonJS module and require it in `config.cjs`:

  `External safelist`
```javascript
  module.exports = {
    purge: {
      options: {
        safelist: require('./safelist') // Array of classes to keep
      }
    },
  }
  ```

  Keep the safelist inside the `purgetss` folder:

  `./purgetss/safelist.js`
```javascript
  // ./purgetss/safelist.js
  exports.safelist = [
    // A large list of classes to keep
    'Label',
    'Button',
    'Window',
    'ListView',
    'TableView',
    'ScrollView',
    'ScrollableView',
    // ...
    // ...
    // ...
    'bg-indigo-50',
    'bg-indigo-100',
    // ...
    // ...
    'bg-indigo-800',
    'bg-indigo-900',
  ];
  ```

- `options.plugins`

  The `plugins` option lets you disable classes PurgeTSS would normally generate.

  To disable specific classes, provide an array of properties (or plugins) to disable:

  `The plugins section`
```javascript
  module.exports = {
    purge: {
      options: {
        plugins: [
          opacity,
          borderRadius
        ]
      }
    },
  }
  ```

### `theme` section

The `theme` section defines your project's color palette, type scale, font stacks, border radius values, and other properties.

`The theme section`
```javascript
module.exports = {
  theme: {
    fontFamily: {
      display: 'AlfaSlabOne-Regular',
      body: 'BarlowSemiCondensed-Regular'
    },
    borderWidth: {
      DEFAULT: 1,
      0: 0,
      2: 2,
      4: 4,
    },
    extend: {
      colors: {
        cyan: '#9cdbff',
      },
      spacing: {
        96: '24rem',
        128: '32rem',
      }
    }
  }
}
```

### Default `font-sans`, `font-serif`, `font-mono`

PurgeTSS generates three `fontFamily` classes by default, even when `theme.fontFamily` is not set in `config.cjs`. iOS and Android get different values on purpose so each platform picks its native system font:

| Class        | iOS              | Android      |
| ------------ | ---------------- | ------------ |
| `font-sans`  | `Helvetica Neue` | `sans-serif` |
| `font-serif` | `Georgia`        | `serif`      |
| `font-mono`  | `monospace`      | `monospace`  |

If you define a value for `sans`, `serif`, or `mono` in `theme.fontFamily` (or `theme.extend.fontFamily`), your value replaces the default on both platforms, no per-platform fork needed:

`./purgetss/config.cjs`
```javascript
module.exports = {
  theme: {
    extend: {
      fontFamily: {
        sans: 'Inter-Regular'   // replaces Helvetica Neue / sans-serif on both platforms
      }
    }
  }
}
```

## Overriding and extending properties

By default, your project inherits values from the default theme. You have two options depending on your goal.

### Override properties

To override a default property, add it directly in the `theme` section.

```javascript
module.exports = {
  theme: {
    // Replaces all of the default `opacity` values
    opacity: {
      15: '0.15',
      35: '0.35',
      65: '0.65',
      85: '0.85'
    }
  }
}
```

This replaces the default `opacity` values with the new ones.

> ℹ️ **INFO**
>
> Keys you do not provide are inherited from the default theme. In the example above, colors, spacing, border radius, background position, and other defaults remain.


### Extend properties

If you want to keep the defaults and add new values, place them under `theme.extend`.

For example, if you want to add an extra color but preserve the existing ones, you could extend the `colors` section:

```javascript
module.exports = {
  theme: {
    extend: {
      // Adds a new color in addition to the default colors
      colors: {
        primary: '#002359',
      }
    }
  }
}
```

You can override some parts of the default theme and extend others within the same configuration:

```javascript
module.exports = {
  theme: {
    opacity: {
      15: '0.15',
      35: '0.35',
      65: '0.65',
      85: '0.85'
    },
    extend: {
      colors: {
        primary: '#002359',
      }
    }
  }
}
```

## Customize colors

PurgeTSS includes a default color palette. Customize it under the `colors` key in the `theme` section of your `config.cjs` file:

`Customizing Colors`
```javascript
module.exports = {
  theme: {
    colors: {
      // Configure your color palette here
    }
  }
}
```

### Use custom colors

To replace the default color palette, add your colors directly under `theme.colors`:

`Using custom colors`
```javascript
module.exports = {
  theme: {
    colors: {
      transparent: 'transparent',
      white: '#ffffff',
      purple: '#3f3cbb',
      midnight: '#121063',
      metal: '#565584',
      tahiti: '#3ab7bf',
      silver: '#ecebff',
      'bubble-gum': '#ff77e9',
      bermuda: '#78dcca',
    },
  },
}
```

These colors are available across utilities like text, border, and background colors.

### Color object syntax

Colors can be defined as a simple list of key-value pairs or as nested objects. Nested keys are added to the base color name as modifiers.

`Color object syntax`
```javascript
module.exports = {
  theme: {
    colors: {
      highlight: '#ffff00',
      primary: {
        solid: '#002359',
        dark: '#000030',
        transparent: '#D9002359'
      },
      tahiti: {
        100: '#cffafe',
        200: '#a5f3fc',
        300: '#67e8f9',
        400: '#22d3ee',
        500: '#06b6d4',
        600: '#0891b2',
        700: '#0e7490',
        800: '#155e75',
        900: '#164e63',
      },
    }
  }
};
```

The nested keys are combined with the parent key to form class names like `bg-tahiti-400` or `text-tahiti-400`.

### Nesting beyond one level

`theme` and `theme.extend` values are walked recursively, so you can group categories more than one level deep. Each level flattens into a kebab-case suffix on the generated class names:

`./purgetss/config.cjs`
```javascript
module.exports = {
  theme: {
    extend: {
      colors: {
        brand: {
          primary: {
            500: '#0ea5e9',
            900: '#0c4a6e'
          },
          accent: '#f97316'
        }
      }
    }
  }
}
```

`Generated classes`
```css
'.bg-brand-primary-500': { backgroundColor: '#0ea5e9' }
'.bg-brand-primary-900': { backgroundColor: '#0c4a6e' }
'.bg-brand-accent': { backgroundColor: '#f97316' }
```

The same flattening applies to other property categories that accept nested objects, including `backgroundGradient` and `backgroundSelectedGradient`. One-level configs behave the same as before.

### Override a default color

If you want to override one of the default colors but keep the rest, provide the new values in `theme.extend.colors`.

For example, here we've replaced the default cool grays with a neutral gray palette:

`Overriding a default color`
```javascript
module.exports = {
  theme: {
    extend: {
      colors: {
        gray: {
          50: '#f7f7f7',
          100: '#ededed',
          200: '#dfdfdf',
          300: '#c8c8c8',
          400: '#adadad',
          500: '#9e9e9e',
          600: '#888888',
          700: '#7b7b7b',
          800: '#676767',
          900: '#545454'
        }
      }
    }
  }
}
```

### Extend the default palette
If you want to extend the default color palette, use `theme.extend.colors`.

`Extending the default palette`
```javascript
module.exports = {
  theme: {
    extend: {
      colors: {
        'regal-blue': '#243c5a',
      }
    }
  }
}
```

This generates classes like `bg-regal-blue` alongside the default colors.

> ℹ️ **INFO**
>
> You can use the `shades` command to generate a range of shades for a color and add them to `config.cjs`.
> 
> For details, see the [shades command](../commands.md#shades-command).


## Customize spacing

The `spacing` section sets the global spacing and sizing scale.

`Customizing Spacing`
```javascript
module.exports = {
  theme: {
    spacing: {
      1: '8px',
      2: '12px',
      3: '16px',
      4: '24px',
      5: '32px',
      6: '48px',
    }
  }
}
```

By default, the spacing scale is inherited by the padding, margin, width, height, and gap core plugins.

### Shared spacing
The `spacing` section is shared by the `padding`, `margin`, `width`, and `height` properties.

> When you include the `spacing` section, PurgeTSS generates all spacing-related properties and merges them with any other spacing-related properties in the configuration file.

`Shared spacing`
```javascript
module.exports = {
  theme: {
    spacing: {
      tight: '0.25rem',
      loose: '1.0rem'
    },
    width: {
      banner: '5rem'
    },
    height: {
      xl: '3rem',
      '1/3': '33.333333%'
    }
  }
};
```

```css
/* width Property */
'.w-tight': { width: 4 }
'.w-loose': { width: 16 }
'.w-banner': { width: 80 }

/* height Property */
'.h-tight': { height: 4 }
'.h-loose': { height: 16 }
'.h-xl': { height: 48 }
'.h-1/3': { height: '33.333334%' }

/* Margin */
'.m-tight': { top: 4, right: 4, bottom: 4, left: 4 }
'.m-loose': { top: 16, right: 16, bottom: 16, left: 16 }
'.my-tight': { top: 4, bottom: 4 }
'.my-loose': { top: 16, bottom: 16 }
    ...

/* padding Property */
'.p-tight': { padding: { top: 4, right: 4, bottom: 4, left: 4 } }
'.p-loose': { padding: { top: 16, right: 16, bottom: 16, left: 16 } }
'.py-tight': { padding: { top: 4, bottom: 4 } }
'.py-loose': { padding: { top: 16, bottom: 16 } }
    ...

/* Rest of inherited properties */
```

### Override the default spacing scale
If you want to override the default spacing scale, use `theme.spacing` in `config.cjs`:

`Overriding the default spacing scale`
```javascript
module.exports = {
  theme: {
    spacing: {
      sm: 8,
      md: 12,
      lg: 16,
      xl: 24,
    }
  }
}
```

This disables the default spacing scale and generates classes like `p-sm` for padding, `m-md` for margin, `w-lg` for width, and `h-xl` for height.

### Extend the default spacing scale
If you want to extend the default spacing scale, use `theme.extend.spacing`:

`Extending the default spacing scale`
```javascript
module.exports = {
  theme: {
    extend: {
      spacing: {
        72: '18rem',
        84: '21rem',
        96: '24rem',
      }
    }
  }
}
```

This generates classes like `p-72`, `m-84`, and `h-96` alongside the default spacing and sizing utilities.

## List of customizable properties

### Global Properties
- All color properties inherit from the `theme.colors` property.
- All spacing properties inherit from the `theme.spacing` property.

You can customize any of the following properties by adding them to the `theme` section of your `config.cjs` file, or by extending them in `theme.extend`.

### Color properties

- activeTintColor
- activeTitleColor
- backgroundColor
- backgroundDisabledColor
- backgroundFocusedColor
- backgroundGradient
- backgroundSelectedColor
- backgroundSelectedGradient
- badgeColor
- barColor
- borderColor
- color
- colors
- contentScrimColor
- currentPageIndicatorColor
- dateTimeColor
- disabledColor
- highlightedColor
- hintTextColor
- iconColor
- imageTouchFeedbackColor
- indicatorColor
- keyboardToolbarColor
- lightColor
- navigationIconColor
- navTintColor
- onTintColor
- pageIndicatorColor
- pagingControlColor
- pullBackgroundColor
- resultsBackgroundColor
- resultsSeparatorColor
- selectedBackgroundColor
- selectedButtonColor
- selectedColor
- selectedSubtitleColor
- selectedTextColor
- separatorColor
- shadowColor
- statusBarBackgroundColor
- subtitleColor
- subtitleTextColor
- tabsBackgroundColor
- tabsBackgroundSelectedColor
- thumbTintColor
- tint
- tintColor
- titleAttributes
- titleColor
- titleTextColor
- touchFeedbackColor
- trackTintColor
- viewShadowColor

### Configurable properties

- activeTab
- backgroundLeftCap
- backgroundPaddingBottom
- backgroundPaddingLeft
- backgroundPaddingRight
- backgroundPaddingTop
- backgroundTopCap
- borderRadius
- borderWidth
- bottom
- cacheSize
- columnCount
- contentHeight
- contentWidth
- countDownDuration
- delay
- duration
- elevation
- fontSize
- height
- horizontalMargin
- indentionLevel
- keyboardToolbarHeight
- left
- leftButtonPadding
- leftTrackLeftCap
- leftTrackTopCap
- leftWidth
- lineHeightMultiple
- lines
- lineSpacing
- maxElevation
- maximumLineHeight
- maxLines
- maxRowHeight
- maxZoomScale
- minimumFontSize
- minimumLineHeight
- minRowHeight
- minZoomScale
- opacity
- padding
- paddingBottom
- paddingLeft
- paddingRight
- paddingTop
- pageHeight
- pageWidth
- pagingControlAlpha
- pagingControlHeight
- pagingControlTimeout
- paragraphSpacingAfter
- paragraphSpacingBefore
- repeat
- repeatCount
- right
- rightButtonPadding
- rightTrackLeftCap
- rightTrackTopCap
- rightWidth
- rotate
- rowCount
- rowHeight
- scale
- scalesPageToFit
- scaleX
- scaleY
- sectionHeaderTopPadding
- separatorHeight
- shadowRadius
- shiftMode
- timeout
- top
- uprightHeight
- uprightWidth
- verticalMargin
- width
- xOffset
- yOffset
- zIndex
- zoomScale

### Custom rules and Ti Elements
- Create your own custom rules and include Ti Elements with any number of attributes or conditional statements. See the [custom rules section](2-custom-rules.md) for details.
