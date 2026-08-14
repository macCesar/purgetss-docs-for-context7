# The `apply` directive

## Create complex classes and IDs

> ℹ️ **INFO**
>
> Use `apply` to bundle classes into a new class or pull a repeated pattern into one reusable name.


- Target any ID, class, or Ti Element.
- Use default classes.
- Use arbitrary values.
- Use classes defined in `config.cjs`.
- Pass a string of classes or an array of classes.
- Combine `apply` with platform, device, or conditional blocks.

## Set any ID, class, or Ti Element

`./purgetss/config.cjs`
```javascript
// ...
theme: {
  extend: {},
  Label: {
    apply: 'text-base font-bold text-gray-700'
  },
  fontWeight: {
    bold: 'bold'
  },
  fontFamily: {
    'saira-condensed': 'SairaCondensed-Regular'
  },
  '#carrousel': {
    apply: 'w-screen h-auto bg-teal-200 mx-2 my-4 horizontal'
  },
  '.my-custom-class': {
    apply: 'font-bold border-2 rounded wh-auto my-0.5 font-saira-condensed'
  }
}
// ...
```

`./purgetss/styles/utilities.tss`
```css
'Label': { color: '#374151', textColor: '#374151', font: { fontSize: 16, fontWeight: 'bold' } }

/* Custom Classes */
'#carrousel': { backgroundColor: '#99f6e4', height: Ti.UI.SIZE, layout: 'horizontal', right: 8, left: 8, top: 16, bottom: 16, width: Ti.UI.FILL }
'.my-custom-class': { borderRadius: 4, borderWidth: 2, top: 2, bottom: 2, width: Ti.UI.SIZE, height: Ti.UI.SIZE, font: { fontFamily: 'SairaCondensed-Regular', fontWeight: 'bold' } }
'.font-saira-condensed': { font: { fontFamily: 'SairaCondensed-Regular' } }
'.font-bold': { font: { fontWeight: 'bold' } }
```

## Use default classes

`./purgetss/config.cjs`
```javascript
// ...
theme: {
  '.btn': {
    apply: 'font-bold border-2 rounded wh-auto my-0.5 font-saira-condensed'
  },
  '.btn-primary': {
    apply: 'bg-green-500 text-green-100 border-green-200'
  },
}
// ...
```

`./purgetss/styles/utilities.tss`
```css
/* Custom Classes */
'.btn': { borderRadius: 4, borderWidth: 2, top: 2, bottom: 2, width: Ti.UI.SIZE, height: Ti.UI.SIZE, font: { fontFamily: 'SairaCondensed-Regular', fontWeight: 'bold' } }
'.btn-primary': { backgroundColor: '#22c55e', borderColor: '#bbf7d0', color: '#dcfce7', textColor: '#dcfce7' }
```

## Use icon font classes

Icon fonts bundled with PurgeTSS (FontAwesome, Material Icons, Material Symbols, and Framework7) can be referenced inside `apply` without running `build-fonts` first. The directive resolves these classes against the bundled `dist/*.tss` files, so the font family and the glyph are merged into the generated rule alongside the rest of the utilities:

`./purgetss/config.cjs`
```javascript
module.exports = {
  theme: {
    '.close-button': {
      apply: 'fas fa-times-circle wh-12 text-gray-700'
    }
  }
}
```

`./purgetss/styles/utilities.tss`
```css
'.close-button': { color: '#374151', textColor: '#374151', width: 48, height: 48, font: { fontFamily: 'FontAwesome7Free-Solid' }, text: '\uf057', title: '\uf057' }
```

The same lookup runs for `mi-*` (Material Icons), `ms-*` (Material Symbols), and `f7-*` (Framework7) classes. If the project has its own `purgetss/styles/fontawesome.tss` (for example, FontAwesome Pro or Beta), that file takes precedence over the bundled default, the same order that applies when the icon class appears directly in XML.

## Use arbitrary values

You can use [arbitrary values](./5-arbitrary-values.md) inside custom classes.

`./purgetss/config.cjs`
```javascript
// ...
theme: {
  extend: {},
  '.progress': {
    apply: 'h-(1rem) horizontal bg-(#e9ecef) text-(.75rem) rounded-(.25rem)'
  }
}
// ...
```

`./purgetss/styles/utilities.tss`
```css
/* Custom Classes */
'.progress': { backgroundColor: '#e9ecef', borderRadius: 4, height: 16, layout: 'horizontal', font: { fontSize: 12 } }
// ...
```

## Use newly defined classes in `config.cjs`

In this example, `corporate` color classes are defined first, then reused inside `apply` with `bg-corporate-500`, `text-corporate-100`, and `border-corporate-200`.

`./purgetss/config.cjs`
```javascript
// ...
theme: {
  extend: {
    colors: {
      // Generates bg-, text-, and border-color classes.
      corporate: {
        100: '#dddfe1', 200: '#babfc4', 500: '#53606b'
      }
    }
  },
  '.btn': {
    apply: 'wh-auto font-bold border-2 rounded my-0.5'
  },
  '.btn-corporate': {
    // Classes from extend.colors.corporate
    apply: 'bg-corporate-500 text-corporate-100 border-corporate-200'
  }
}
// ...
```

`./purgetss/styles/utilities.tss`
```css
/* Custom Classes */
'.btn': { borderRadius: 4, borderWidth: 2, top: 2, bottom: 2, width: Ti.UI.SIZE, height: Ti.UI.SIZE, font: { fontWeight: 'bold' } }
'.btn-corporate': { backgroundColor: '#53606b', borderColor: '#babfc4', color: '#dddfe1', textColor: '#dddfe1' }
/* ... */
/* color Property */
'.text-corporate-100': { color: '#dddfe1', textColor: '#dddfe1' }
'.text-corporate-200': { color: '#babfc4', textColor: '#babfc4' }
'.text-corporate-500': { color: '#53606b', textColor: '#53606b' }
/* backgroundColor Property */
'.bg-corporate-100': { backgroundColor: '#dddfe1' }
'.bg-corporate-200': { backgroundColor: '#babfc4' }
'.bg-corporate-500': { backgroundColor: '#53606b' }
/* borderColor Property */
'.border-corporate-100': { borderColor: '#dddfe1' }
'.border-corporate-200': { borderColor: '#babfc4' }
'.border-corporate-500': { borderColor: '#53606b' }
/* Other color properties are generated too. */
```

## Set a string of classes or an array of classes

`./purgetss/config.cjs`
```javascript
// ...
theme: {
  extend: {
    colors: {
      corporate: {
        100: '#dddfe1', 200: '#babfc4', 500: '#53606b'
      }
    }
  },
  // Use a string of classes
  '.btn': {
    apply: 'font-bold border-2 rounded wh-auto my-0.5'
  },
  // or an array of classes
  '.btn-corporate': {
    apply: [
      'bg-corporate-500',
      'text-corporate-100',
      'border-corporate-200'
    ]
  }
}
// ...
```

`./purgetss/styles/utilities.tss`
```css
/* Custom Classes */
'.btn': { borderRadius: 4, borderWidth: 2, top: 2, bottom: 2, width: Ti.UI.SIZE, height: Ti.UI.SIZE, font: { fontWeight: 'bold' } }
'.btn-corporate': { backgroundColor: '#53606b', borderColor: '#babfc4', color: '#dddfe1', textColor: '#dddfe1' }
/* ... */
```

## Combine with platform, device, or conditional blocks

`./purgetss/config.cjs`
```javascript
// ...
theme: {
  '.btn': {
    // Default .btn
    apply: 'font-bold border-2 rounded wh-auto my-0.5',

    // Specific to iOS devices
    ios: {
      apply: 'w-screen mx-4'
    },

    // Specific to handheld devices
    handheld: {
      apply: 'h-20'
    },

    // Specific to iPhoneX (if Alloy.Global.iPhoneX is set)
    '[if=Alloy.Globals.iPhoneX]': {
      apply: 'mb-12'
    }
  },
}
// ...
```

`./purgetss/styles/utilities.tss`
```css
/* Custom Classes */
'.btn': { borderRadius: 4, borderWidth: 2, top: 2, bottom: 2, width: Ti.UI.SIZE, height: Ti.UI.SIZE, font: { fontWeight: 'bold' } }
'.btn[platform=ios]': { right: 16, left: 16, width: Ti.UI.FILL }
'.btn[formFactor=handheld]': { height: 80 }
'.btn[if=Alloy.Globals.iPhoneX]': { bottom: 48 }
// ...
```

## Customizing Window, View, and ImageView

`Window`, `View`, and `ImageView` have built-in defaults: white Window background, `Ti.UI.SIZE` on View, and `hires: true` on ImageView for iOS. To change those defaults globally, put the customization under `theme.extend`, the same place you would extend `colors` or `spacing`:

`./purgetss/config.cjs`
```javascript
module.exports = {
  theme: {
    extend: {
      Window: {
        apply: 'exit-on-close-false bg-blue-500'
      }
    }
  }
}
```

Now every `<Window>` in the project picks up `backgroundColor: '#3b82f6'` and `exitOnClose: false`.

### Shorthand: no `default:` wrapper needed

The examples above use `{ apply: '...' }` directly. Internally that gets normalized to `{ default: { apply: '...' } }`, so both forms produce the same TSS:

`Both of these work`
```javascript
Window: { apply: 'exit-on-close-false bg-blue-500' }
Window: { default: { apply: 'exit-on-close-false bg-blue-500' } }
```

Use the explicit `default:` wrapper when you also need platform blocks (`ios:`, `android:`) next to it. For the common case of one bundle of defaults, the shorthand reads better.

### Extend mode vs replace mode

These three Ti Elements support two declaration modes depending on where they are placed in `config.cjs`:

- `theme.extend.Window` (or `View` / `ImageView`): your customization merges with the framework defaults. The white `backgroundColor`, `Ti.UI.SIZE` width/height, and iOS `hires: true` stay in place unless you override them with `apply`.
- `theme.Window` (top level, no `extend`): replace mode. Your config becomes the source of truth and the framework defaults are skipped.

Use replace mode when you want full control and do not want a preset mixed in. A common case is a Window declared at `theme.Window` with a `backgroundGradient`, where the default white `backgroundColor` would cover the gradient:

`./purgetss/config.cjs - replace mode`
```javascript
module.exports = {
  theme: {
    Window: {
      apply: 'bg-gradient-to-b from-blue-500 to-purple-600'
    }
  }
}
```

`./purgetss/styles/utilities.tss`
```css
'Window': { backgroundGradient: { type: 'linear', colors: [...], startPoint: ..., endPoint: ... } }
```

Note the missing `backgroundColor: '#FFFFFF'`. Replace mode skipped the framework default. If you had used `theme.extend.Window` here, the white `backgroundColor` would still be merged in and could cover the gradient.

### Apply wins over static defaults

If `apply` sets a property that the component already has as a built-in default, the applied value replaces the original instead of both ending up in the final TSS:

`./purgetss/config.cjs`
```javascript
module.exports = {
  theme: {
    extend: {
      Window: { apply: 'bg-blue-500' }
    }
  }
}
```

`./purgetss/styles/utilities.tss`
```css
/* Before dedup: { backgroundColor: '#FFFFFF', backgroundColor: '#3b82f6' } */
/* After dedup:  */
'Window': { backgroundColor: '#3b82f6' }
```

Without the dedup, both `backgroundColor` entries would land in the file. The last one wins at runtime, but two copies of the same property make the TSS harder to read. The builder keeps only the applied value.

## Platform-specific classes

Several classes in `utilities.tss` are platform-specific, such as `clip-enabled` and `status-bar-style-light-content`. These only exist with a `[platform=ios]` or `[platform=android]` suffix.

When you use these classes inside a platform block (`ios:` or `android:`), PurgeTSS finds the platform-specific version. No prefix is needed:

`./purgetss/config.cjs`
```javascript
module.exports = {
  theme: {
    '.my-view': {
      ios: {
        apply: 'bg-green-500 wh-32 clip-enabled'
      }
    }
  },
};
```

`./purgetss/styles/utilities.tss`
```css
/* Custom Classes */
'.my-view[platform=ios]': { backgroundColor: '#22c55e', clipMode: Ti.UI.iOS.CLIP_MODE_ENABLED, width: 128, height: 128 }
```

The `ios:` / `android:` prefix still works from a non-platform block, such as `default`, but use it with caution:

> 🚨 **WARNING**
>
> Cross-platform apps
> Using `ios:` or `android:` in a `default` block applies the property on all platforms. Some iOS-only or Android-only properties can cause errors on the other platform at compile time or when the view opens. For cross-platform apps, use platform blocks instead.


`./purgetss/config.cjs`
```javascript
module.exports = {
  theme: {
    // For single-platform apps, the prefix works from default:
    '.my-view': {
      apply: 'wh-32 bg-green-500 ios:clip-enabled'
    }

    // For cross-platform apps, use platform blocks instead:
    '.my-view': {
      apply: 'wh-32 bg-green-500',
      ios: {
        apply: 'clip-enabled'
      }
    }
  },
};
```

### Classes outside platform blocks

If a platform-specific class is used outside a platform block, without the `ios:` or `android:` prefix, PurgeTSS will not find it because it only exists with the platform suffix in `utilities.tss`:

`./purgetss/config.cjs`
```javascript
module.exports = {
  theme: {
    '.my-view': {
      // clip-enabled only exists as '.clip-enabled[platform=ios]' in utilities.tss
      // Without a platform block or ios: prefix, it won't be found
      apply: 'wh-32 clip-enabled bg-green-500'
    }
  },
};
```

`./purgetss/styles/utilities.tss`
```css
/* clip-enabled was not resolved because no platform context was available */
'.my-view': { backgroundColor: '#22c55e', width: 128, height: 128 }
```
