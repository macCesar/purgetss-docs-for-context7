# Appearance Setup

How to set up Light/Dark mode in a PurgeTSS project: semantic colors, initialization, and the Appearance toggle.

## 1. Define semantic colors

Create `app/assets/semantic.colors.json` and register the color names under `theme.extend.colors` in `config.cjs`.

See [Semantic Colors](./2-semantic-colors.md) for the full setup — JSON definitions, class mapping, nesting rules, and alpha transparency.

## 2. Initialize Appearance at startup

Call `Appearance.init()` once before opening the first window:

`app/controllers/index.js`
```js
const { Appearance } = require('purgetss.ui')
Appearance.init()

$.navWin.open()
```

This reads the saved preference from `Ti.App.Properties` and applies it through `Ti.UI.overrideUserInterfaceStyle`. If no preference is saved, the system default is used.

## 3. Build an Appearance toggle

Create a settings view where users can choose their preferred mode.

### XML

`app/views/settings.xml`
```xml
<Alloy>
  <Window class="bg-surface title-attributes-on-surface bar-surface nav-tint-accent" title="Settings">
    <ScrollView class="vertical content-w-screen content-h-auto">

      <Label class="mx-4 mb-2 mt-6 h-auto text-xs font-semibold text-on-surface-variant">APPEARANCE</Label>

      <View class="mx-4 mb-4 h-auto w-screen rounded-xl bg-surface-high shadow-sm vertical clip-enabled">

        <!-- System -->
        <View class="horizontal mx-4 h-12 w-screen" onClick="selectSystem">
          <Label class="fa-solid fa-mobile-screen ml-0 h-12 w-8 text-blue-500" />
          <Label class="h-12 text-sm font-semibold text-on-surface">System</Label>
          <Label id="themeSystemCheck" class="fa-solid fa-circle-check mr-0 h-12 w-screen text-right text-green-500" />
        </View>

        <View class="h-px w-screen bg-border" />

        <!-- Light -->
        <View class="horizontal mx-4 h-12 w-screen" onClick="selectLight">
          <Label class="fa-solid fa-sun ml-0 h-12 w-8 text-amber-500" />
          <Label class="h-12 text-sm font-semibold text-on-surface">Light</Label>
          <Label id="themeLightCheck" class="fa-solid fa-circle-check mr-0 hidden h-12 w-screen text-right text-green-500" />
        </View>

        <View class="h-px w-screen bg-border" />

        <!-- Dark -->
        <View class="horizontal mx-4 h-12 w-screen" onClick="selectDark">
          <Label class="fa-solid fa-moon ml-0 h-12 w-8 text-purple-500" />
          <Label class="h-12 text-sm font-semibold text-on-surface">Dark</Label>
          <Label id="themeDarkCheck" class="fa-solid fa-circle-check mr-0 hidden h-12 w-screen text-right text-green-500" />
        </View>

      </View>

    </ScrollView>
  </Window>
</Alloy>
```

### Controller

`app/controllers/settings.js`
```js
const { Appearance } = require('purgetss.ui')

updateUI(Appearance.get())

function selectDark() { selectAppearance('dark') }
function selectLight() { selectAppearance('light') }
function selectSystem() { selectAppearance('system') }

function selectAppearance(value) {
  Appearance.set(value)
  updateUI(value)
}

function updateUI(value) {
  $.themeDarkCheck.visible = (value === 'dark')
  $.themeLightCheck.visible = (value === 'light')
  $.themeSystemCheck.visible = (value === 'system')
}
```

## How it fits together

```
┌─ app startup ───────────────────────────────────────────┐
│                                                         │
│  1. Appearance.init()                                   │
│     └─ Reads saved mode from Ti.App.Properties          │
│     └─ Applies Ti.UI.overrideUserInterfaceStyle         │
│                                                         │
│  2. Semantic colors resolve automatically               │
│     └─ bg-surface → surfaceColor → light or dark hex    │
│     └─ text-on-surface → textColor → light or dark hex  │
│                                                         │
│  3. User taps "Dark" in settings                        │
│     └─ Appearance.set('dark')                           │
│     └─ All semantic colors update instantly             │
│     └─ Preference saved for next app launch             │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

> 💡 **TIP**
>
> Semantic colors apply everywhere you use `bg-*`, `text-*`, and `border-*` classes — Windows, Views, Labels, Buttons, TextFields, TextAreas, ListViews, and any custom class that references a semantic name. One appearance switch updates the entire UI.

