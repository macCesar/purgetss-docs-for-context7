# The `opacity` modifier

> ℹ️ **INFO**
>
> Add opacity to any color class by appending a value from 0 to 100 after a slash (`/`).


## In your XML files

```xml
<Label class="w-11/12 h-8 text-center bg-sky-500/50 text-purple-900/75">My Button</Label>
```

```css
/* Main styles */
'Window': { backgroundColor: '#ffffff' }
'.h-8': { height: 32 }
'.text-center': { textAlign: Ti.UI.TEXT_ALIGNMENT_CENTER }
'.w-11/12': { width: '91.666667%' }

/* Styles with color opacity modifiers */
'.bg-sky-500/50': { backgroundColor: '#800ea5e9' }
'.text-purple-900/75': { color: '#bf581c87' }
```

## In the `apply` directive

Opacity modifiers also work inside `apply` in `config.cjs`.

`./purgetss/config.cjs`
```js
module.exports = {
  theme: {
    extend: {
      colors: {
        primary: '#ce10cc'
      }
    },
    '.main-banner': {
      apply: [
        'bg-primary/35',
        'border-primary/75'
      ]
    }
  }
}
```

`Generated classes`
```css
/* Custom Styles and Resets */
'.main-banner': { backgroundColor: '#59ce10cc', borderColor: '#bfce10cc' }

/* backgroundColor Property */
'.bg-primary': { backgroundColor: '#ce10cc' }
...
/* Other color properties are generated too. */
```

## Semantic colors

Opacity modifiers also work on classes that resolve to a [semantic color](../best-practices/2-semantic-colors.md). For example, `bg-surface/65` works when `surface` maps to `surfaceColor` in `semantic.colors.json`.

PurgeTSS creates a derived semantic key (`surfaceColor_65`) with the original `light` and `dark` hex values plus the requested alpha for both modes. It writes that key back to `semantic.colors.json` and emits the rule against the derived key. Light/Dark switching still works.

See [Opacity modifier auto-derivation](../best-practices/2-semantic-colors.md#opacity-modifier-auto-derivation) for the full behavior, including the rebuild requirement for new alpha entries and conflict handling.
