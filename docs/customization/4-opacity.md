---
sidebar_position: 4
slug: the-opacity-modifier
---

# The `opacity` modifier

:::info
Add an opacity modifier to any available color property by appending a value between 0 and 100 after a slash (`/`).
:::

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
You can also use color opacity modifiers in the `apply` directive in the `config.cjs` file.

```js title="./purgetss/config.cjs"
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

```css title="Generated classes"
/* Custom Styles and Resets */
'.main-banner': { backgroundColor: '#59ce10cc', borderColor: '#bfce10cc' }

/* backgroundColor Property */
'.bg-primary': { backgroundColor: '#ce10cc' }
...
/* And the rest of color properties! */
```

:::caution Semantic colors
Semantic colors can't be modified with the opacity modifier because they are defined as an object with light and dark values.
:::
