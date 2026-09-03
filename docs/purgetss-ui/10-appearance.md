# Appearance management

`Appearance` is a singleton that applies and persists a user interface style. It supports `system`, `light`, and `dark`.

Call `Appearance.init()` before opening the first window. It reads the saved `userInterfaceStyle` value from `Ti.App.Properties` and assigns it to `Ti.UI.overrideUserInterfaceStyle`.

## Methods

| Method | Behavior | Return value |
|---|---|---|
| `init()` | Restores and applies the saved style; defaults to `system` | `undefined` |
| `set(mode)` | Applies and stores `system`, `light`, or `dark` | `undefined` |
| `get()` | Reads the mode held by the singleton | Mode string |
| `toggle()` | Changes `dark` to `light`; any other current mode changes to `dark` | `undefined` |

`set()` silently ignores an unsupported value. `Appearance` does not emit a change event, so update labels, icons, or other non-color state in the same handler that calls `set()` or `toggle()`.

## Alloy

Initialize the singleton from the first controller:

`app/controllers/index.js`
```js
const { Appearance } = require('purgetss.ui')

Appearance.init()
$.index.open()
```

Place semantic colors in `app/assets/semantic.colors.json` and map them to PurgeTSS classes in `purgetss/config.cjs`:

`app/assets/semantic.colors.json`
```json
{
  "surfaceColor": { "light": "#F8FAFC", "dark": "#0F172A" },
  "textColor": { "light": "#0F172A", "dark": "#F8FAFC" },
  "accentColor": { "light": "#2563EB", "dark": "#60A5FA" }
}
```

`purgetss/config.cjs`
```js
module.exports = {
  theme: {
    extend: {
      colors: {
        surface: 'surfaceColor',
        text: 'textColor',
        accent: 'accentColor'
      }
    }
  }
}
```

`app/views/index.xml`
```xml
<Alloy>
  <Window class="bg-surface">
    <Label class="text-text" text="Appearance" />
    <Button class="bg-accent text-white" title="Toggle" onClick="toggleAppearance" />
  </Window>
</Alloy>
```

`app/controllers/index.js`
```js
const { Appearance } = require('purgetss.ui')

function toggleAppearance() {
  Appearance.toggle()
  Ti.API.info(`Mode: ${Appearance.get()}`)
}
```

## Titanium Classic

Classic stores the native file at `Resources/semantic.colors.json`:

`Resources/semantic.colors.json`
```json
{
  "surfaceColor": { "light": "#F8FAFC", "dark": "#0F172A" },
  "textColor": { "light": "#0F172A", "dark": "#F8FAFC" },
  "accentColor": { "light": "#2563EB", "dark": "#60A5FA" }
}
```

Use the semantic names directly in Titanium color properties. Classic does not need PurgeTSS utility classes, TSS, `config.cjs`, or any Alloy runtime file.

`Resources/app.js`
```js
const { Appearance } = require('lib/purgetss.ui')

Appearance.init()

const window = Ti.UI.createWindow({
  backgroundColor: 'surfaceColor'
})
const title = Ti.UI.createLabel({
  top: 80,
  text: `Mode: ${Appearance.get()}`,
  color: 'textColor'
})
const toggleButton = Ti.UI.createButton({
  width: 180,
  height: 48,
  title: 'Toggle appearance',
  color: 'surfaceColor',
  backgroundColor: 'accentColor'
})

function updateModeLabel() {
  title.text = `Mode: ${Appearance.get()}`
}

function toggleAppearance() {
  Appearance.toggle()
  updateModeLabel()
}

function useSystemAppearance() {
  Appearance.set('system')
  updateModeLabel()
}

function disposeWindow() {
  toggleButton.removeEventListener('click', toggleAppearance)
  window.removeEventListener('close', disposeWindow)
}

toggleButton.addEventListener('click', toggleAppearance)
window.add(title)
window.add(toggleButton)
window.addEventListener('close', disposeWindow)
window.open()

// Call useSystemAppearance() from your own System mode control.
```

The JSON is consumed by Titanium's native build. Run a full build after adding or changing semantic keys; a LiveView reload alone does not refresh the native color catalog.

> 💡 **TIP**
>
> The `purgetss semantic` command can write this file for both project types. In Classic it writes only `Resources/semantic.colors.json`; it does not create `config.cjs`, TSS, or utility mappings.


See [Using `purgetss.ui` in Titanium Classic](./2-titanium-classic.md#quick-start) for an animation and Appearance example in the same application.
