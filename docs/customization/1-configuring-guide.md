---
sidebar_position: 1
slug: the-config-file
---

# The `config` file

:::info What's new in v7.2.x
The configuration file is now named `config.cjs` (it used to be `config.js`). The structure is the same.

Legacy mode was removed in PurgeTSS v7.2.x along with its related options.
:::

By default, **PurgeTSS** looks for `./purgetss/config.cjs`, where you can define customizations.

## Create the `config.cjs` file

:::info
`config.cjs` is created automatically the first time you run `purgetss` in a project.
:::

If you want a clean `config.cjs`, delete the existing one and run:

```bash
> purgetss init
```

This creates a minimal `./purgetss/config.cjs` file:

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

Every section is optional. Only add what you want to change. Anything missing falls back to the defaults.

## Structure
The config file has two main sections: `purge` and `theme`.

### `purge` section
The `purge` section controls how PurgeTSS removes unused classes or keeps the ones you want.

```javascript title="The purge section"
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

- **`mode.all`**

  By default, PurgeTSS searches XML files everywhere: comments, attributes, classes, IDs, and Ti Elements.

  Use this mode if you want PurgeTSS to parse Ti Elements you style in `config.cjs`.

- **`mode.method`**

  The `method` setting controls how the auto-purge task runs: `sync` (default) or `async`.

  If changes are not showing up when rebuilding a project with TiKit Components and LiveView, set the method to `async`.

- **`mode.class`**

  Use `class` to search only class and ID attributes in XML files.

- **`options.missing`**

  Set `missing` to `true` if you want a list of missing or misspelled classes at the end of `app.tss`.

  :::info
  Helpful when you want to confirm you did not forget class definitions or when you are upgrading from PurgeTSS v5 to v6.
  :::

- **`options.widgets`**

  Set `widgets` to `true` to also parse all XML files under the Widgets folder.

- **`options.safelist`**

  The `safelist` is a list of classes and Ti Elements you want to keep no matter the purge mode or whether they appear in XML.

  If the list is large, put it in a CommonJS module and require it in `config.cjs`:

  ```javascript title="External safelist"
  module.exports = {
    purge: {
      options: {
        safelist: require('./safelist') // Array of classes to keep
      }
    },
  }
  ```

  Keep the safelist inside the `purgetss` folder:

  ```javascript title="./purgetss/safelist.js"
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

- **`options.plugins`**

  The `plugins` option lets you disable classes PurgeTSS would normally generate.

  To disable specific classes, provide an array of properties (or plugins) to disable:

  ```javascript title="The plugins section"
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

The `theme` section in `config.cjs` is where you define and extend your project's color palette, type scale, font stacks, border radius values, and other properties.

```javascript title="The theme section"
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

This will completely replace the original default `opacity` values with the new ones.

:::info
Keys you do not provide are inherited from the default theme. In the example above, colors, spacing, border radius, background position, and other defaults remain.
:::

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

PurgeTSS includes Tailwind's default color palette. Customize it under the `colors` key in the `theme` section of your `config.cjs` file:

```javascript title="Customizing Colors"
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

```javascript title="Using custom colors"
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

These colors will be available across utilities like text, border, and background colors.

### Color object syntax

Colors can be defined as a simple list of key-value pairs or as nested objects. Nested keys are added to the base color name as modifiers.

```javascript title="Color object syntax"
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

### Override a default color

If you want to override one of the default colors but keep the rest, provide the new values in `theme.extend.colors`.

For example, here we've replaced the default cool grays with a neutral gray palette:

```javascript title="Overriding a default color"
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

```javascript title="Extending the default palette"
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

This will generate classes like `bg-regal-blue` in addition to all of Tailwind's default colors.

:::info
You can use the `shades` command to generate a range of shades for a color and add them to `config.cjs`.

**For details, see the** [**shades command**](/docs/commands#shades-command).
:::

## Customize spacing

The `spacing` section controls the global spacing and sizing scale values.

```javascript title="Customizing Spacing"
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

> When you include the `spacing` section, PurgeTSS will automatically generate all spacing-related properties and merge them with any other spacing-related properties present in the configuration file.

```javascript title="Shared spacing"
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

```javascript title="Overriding the default spacing scale"
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

```javascript title="Extending the default spacing scale"
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

This will generate classes like `p-72`, `m-84`, and `h-96` in addition to all of the default spacing and sizing utilities.

## List of customizable properties

### Global Properties
- All color properties inherit from the `theme.colors` property.
- All spacing properties inherit from the `theme.spacing` property.

You can customize any of the following properties individually by adding them in the `theme` section of your `config.cjs` file, or by extending them in the `theme.extend` section.

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
- Create your own custom rules and include Ti Elements with any number of attributes or conditional statements. See the [**Custom rules section**](2-custom-rules.md) for details.
