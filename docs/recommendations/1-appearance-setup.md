# Appearance Setup

How to set up Light/Dark mode in a PurgeTSS project: Window defaults, semantic colors, and the Appearance toggle.

## 1. Window defaults in config.cjs

Set global Window defaults so Large Title and blur work correctly when the appearance changes:

`purgetss/config.cjs`
```js
module.exports = {
  theme: {
    extend: {
      colors: {
        surface: {
          DEFAULT: 'surfaceColor',
          high: 'surfaceHighColor'
        },
        'on-surface': 'textColor',
        'on-surface-variant': 'textSecondaryColor',
        border: 'borderColor',
        accent: 'accentColor'
      }
    },
    Window: {
      ios: {
        apply: 'auto-adjust-scroll-view-insets extend-edges-all large-title-enabled'
      }
    }
  }
}
```

See [Window Defaults](./3-window-defaults.md) for why these three properties are needed.

## 2. Semantic colors file

Create the semantic color definitions:

`app/assets/semantic.colors.json`
```json
{
  "surfaceColor": {
    "light": "#F9FAFB",
    "dark": "#0f172a"
  },
  "surfaceHighColor": {
    "light": "#FFFFFF",
    "dark": "#1e293b"
  },
  "textColor": {
    "light": "#111827",
    "dark": "#f1f5f9"
  },
  "textSecondaryColor": {
    "light": "#6B7280",
    "dark": "#94a3b8"
  },
  "borderColor": {
    "light": "#E5E7EB",
    "dark": "#334155"
  },
  "accentColor": {
    "light": "#3B82F6",
    "dark": "#60a5fa"
  }
}
```

See [Semantic Colors](./2-semantic-colors.md) for more on color definitions, nesting rules, and alpha transparency.

## 3. Initialize Appearance at startup

Call `Appearance.init()` once before opening the first window:

`app/controllers/index.js`
```js
const { Appearance } = require('purgetss.ui')
Appearance.init()

$.navWin.open()
```

This reads the saved preference from `Ti.App.Properties` and applies it through `Ti.UI.overrideUserInterfaceStyle`. If no preference is saved, the system default is used.

## 4. Build an Appearance toggle

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
│  2. Window opens with config.cjs defaults               │
│     └─ extendEdges + autoAdjustScrollViewInsets         │
│     └─ largeTitleEnabled                                │
│                                                         │
│  3. Semantic colors resolve automatically               │
│     └─ bg-surface → surfaceColor → light or dark hex    │
│     └─ text-on-surface → textColor → light or dark hex  │
│                                                         │
│  4. User taps "Dark" in settings                        │
│     └─ Appearance.set('dark')                           │
│     └─ All semantic colors update instantly             │
│     └─ Preference saved for next app launch             │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

> 💡 **TIP**
>
> This setup works identically whether your root container is a NavigationWindow or a TabGroup — the Window defaults from `config.cjs` apply to all windows.

