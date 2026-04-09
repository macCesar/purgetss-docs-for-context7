# Appearance Management

The `Appearance` export handles Light/Dark/System mode switching and persists the user's choice across app restarts.

## Setup

Call `Appearance.init()` once at app startup, before opening the first window:

`app/controllers/index.js`
```js
const { Appearance } = require('purgetss.ui')
Appearance.init()

$.navWin.open()
```

This reads the saved preference from `Ti.App.Properties` and applies it through `Ti.UI.overrideUserInterfaceStyle`.

## Methods

| Method      | Description                                                          |
| ----------- | -------------------------------------------------------------------- |
| `init()`    | Restore the saved mode from `Ti.App.Properties` and apply it         |
| `set(mode)` | Apply and persist a mode: `'system'`, `'light'`, or `'dark'`         |
| `get()`     | Returns the current mode string (`'system'`, `'light'`, or `'dark'`) |
| `toggle()`  | Switch between `'light'` and `'dark'` (skips `'system'`)             |

## Usage example

Build your own UI (buttons, switches, segmented controls) and call `Appearance.set()` from event handlers:

`app/views/settings.xml`
```xml
<View onClick="selectSystem">
  <Label text="System" />
  <Label id="systemCheck" class="fa-solid fa-circle-check hidden" />
</View>

<View onClick="selectLight">
  <Label text="Light" />
  <Label id="lightCheck" class="fa-solid fa-circle-check hidden" />
</View>

<View onClick="selectDark">
  <Label text="Dark" />
  <Label id="darkCheck" class="fa-solid fa-circle-check hidden" />
</View>
```

`app/controllers/settings.js`
```js
const { Appearance } = require('purgetss.ui')

updateUI(Appearance.get())

function selectDark() { selectMode('dark') }
function selectLight() { selectMode('light') }
function selectSystem() { selectMode('system') }

function selectMode(value) {
  Appearance.set(value)
  updateUI(value)
}

function updateUI(value) {
  $.darkCheck.visible = (value === 'dark')
  $.lightCheck.visible = (value === 'light')
  $.systemCheck.visible = (value === 'system')
}
```

## Semantic colors

To make views respond to mode changes, add a `semantic.colors.json` file in `app/assets/`:

`app/assets/semantic.colors.json`
```json
{
  "surfaceColor": {
    "light": "#FFFFFF",
    "dark": "#0f172a"
  },
  "textColor": {
    "light": "#111827",
    "dark": "#f1f5f9"
  },
  "borderColor": {
    "light": "#E5E7EB",
    "dark": "#334155"
  }
}
```

Then register them in `config.cjs`:

`purgetss/config.cjs`
```js
module.exports = {
  theme: {
    extend: {
      colors: {
        surface: { DEFAULT: 'surfaceColor' },
        'on-surface': 'textColor',
        border: 'borderColor'
      }
    }
  }
}
```

Use the semantic classes in your views:

```xml
<Window class="bg-surface" title="Settings">
  <Label class="text-on-surface" text="Hello" />
  <View class="bg-border h-px w-screen" />
</Window>
```

When `Appearance.set('dark')` is called, Titanium resolves the semantic color names to their dark variants automatically.

> 💡 **TIP**
>
> Complete setup guide
> For a step-by-step guide covering Window defaults, semantic colors, and Appearance together, see [Appearance Setup](../recommendations/1-appearance-setup.md).

