# Semantic Colors

Semantic colors let your app respond to Light/Dark mode changes without extra code. You define color names that resolve to different hex values depending on the current appearance.

## Setting up semantic.colors.json

Create `app/assets/semantic.colors.json` with your color definitions:

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
  "textMutedColor": {
    "light": "#9CA3AF",
    "dark": "#64748b"
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

Each key is a color name. Each value is an object with `light` and `dark` hex values.

### Using alpha transparency

To include transparency, use the 8-digit hex format (`#RRGGBBAA`):

```json
}
```

## Registering in config.cjs

Map the semantic color names to PurgeTSS class names in `config.cjs`:

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
        muted: 'textMutedColor',
        border: 'borderColor',
        accent: 'accentColor'
      }
    }
  }
}
```

This generates utility classes like `bg-surface`, `bg-surface-high`, `text-on-surface`, `text-accent`, `bg-border`, etc.

### Nesting rules

You can nest one level deep using an object with `DEFAULT`:

```js
// ✅ Correct — generates bg-surface and bg-surface-high
surface: {
  DEFAULT: 'surfaceColor',
  high: 'surfaceHighColor'
}
```

> 🛑 **DANGER**
>
> Common error: nested objects without DEFAULT
> ```js
> // ❌ Wrong — generates [object Object] instead of a color
> surface: {
>   regular: 'surfaceColor',
>   high: 'surfaceHighColor'
> }
> ```
> 
> If you nest without a `DEFAULT` key and use the base class (`bg-surface`), PurgeTSS will serialize the object as `[object Object]`. Always include `DEFAULT` for the base variant, or use a flat structure.


### Flat structure alternative

If you prefer no nesting at all:

```js
colors: {
  surface: 'surfaceColor',
  'surface-high': 'surfaceHighColor',
  'on-surface': 'textColor'
}
```

Both approaches work. Choose based on how you want to organize your class names.

## Using semantic classes in views

```xml
<Window class="bg-surface" title="Settings">
  <ScrollView class="vertical content-w-screen content-h-auto">
    <Label class="text-on-surface font-bold" text="Title" />
    <Label class="text-on-surface-variant text-sm" text="Subtitle" />
    <View class="h-px w-screen bg-border" />
  </ScrollView>
</Window>
```

When the appearance changes (via `Appearance.set()` or system toggle), Titanium resolves each semantic color name to its `light` or `dark` value on its own.

## Using semantic colors in controllers

Semantic colors also work from JavaScript. You have three options, depending on whether you are creating a new component or styling an existing one.

### Option 1: Direct assignment by semantic name

Titanium resolves the semantic name at runtime, so you can assign it straight to a color-accepting property:

```js
$.titleLabel.color = 'textColor'
$.card.backgroundColor = 'surfaceHighColor'
$.divider.backgroundColor = 'borderColor'
```

This skips PurgeTSS entirely. Use it when you only need one or two color changes and no other utilities.

### Option 2: `$.UI.create()` with PurgeTSS classes

When you build a component dynamically, use `$.UI.create()` so you get the full set of utilities — colors included:

```js
const card = $.UI.create('View', {
  classes: ['bg-surface-high', 'rounded-lg', 'mx-4', 'my-2']
})

const title = $.UI.create('Label', {
  text: 'Settings',
  classes: ['text-on-surface', 'font-bold', 'text-lg']
})

card.add(title)
```

### Option 3: `Alloy.createStyle()` + `applyProperties()`

To swap styles on an **existing** component — for example, to react to a state change — create the style and apply it:

```js
function setActive(isActive) {
  const style = Alloy.createStyle('index', {
    apiName: 'Ti.UI.Label',
    classes: isActive
      ? ['text-accent', 'font-bold']
      : ['text-on-surface-variant', 'font-normal']
  })

  $.statusLabel.applyProperties(style)
}
```

> 💡 **TIP**
>
> When to use which
> - **Option 1** — single property change, no other utilities needed.
> - **Option 2** — creating new components from scratch.
> - **Option 3** — restyling components that already exist in the view.


## Recommended color palette

A minimal semantic palette for most apps:

| Purpose            | Semantic name        | Light     | Dark      | Classes generated          |
| ------------------ | -------------------- | --------- | --------- | -------------------------- |
| Background         | `surfaceColor`       | `#F9FAFB` | `#0f172a` | `bg-surface`               |
| Cards/elevated     | `surfaceHighColor`   | `#FFFFFF` | `#1e293b` | `bg-surface-high`          |
| Primary text       | `textColor`          | `#111827` | `#f1f5f9` | `text-on-surface`          |
| Secondary text     | `textSecondaryColor` | `#6B7280` | `#94a3b8` | `text-on-surface-variant`  |
| Borders/dividers   | `borderColor`        | `#E5E7EB` | `#334155` | `bg-border`                |
| Accent/interactive | `accentColor`        | `#3B82F6` | `#60a5fa` | `text-accent`, `bg-accent` |

> 💡 **TIP**
>
> Start with these 5-6 colors. Add more only when the design requires it. Fewer semantic colors means easier maintenance.


## Alternative: numeric scale with inversion

Instead of purpose-based names, you can take any color palette with 11 tonal steps and map them to a numeric scale (`50` through `950`), where each light-mode value inverts in dark mode. This gives you a full tonal range from a single palette.

The example below uses a neutral gray palette for clarity, but the same inversion pattern works with any hue — blues, greens, warm tones, or a custom brand palette. What matters is that the 11 stops share a consistent tonal progression.

`app/assets/semantic.colors.json`
```json
{
  "color50": {
    "light": "#030712",
    "dark": "#f9fafb"
  },
  "color200": {
    "light": "#1f2937",
    "dark": "#e5e7eb"
  },
  "color500": {
    "light": "#6b7280",
    "dark": "#6b7280"
  },
  "color800": {
    "light": "#e5e7eb",
    "dark": "#1f2937"
  },
  "color950": {
    "light": "#f9fafb",
    "dark": "#030712"
  }
}
```

Map them as a nested `primary` palette in `config.cjs`:

`purgetss/config.cjs`
```js
module.exports = {
  theme: {
    extend: {
      colors: {
        primary: {
          50: 'color50',
          200: 'color200',
          500: 'color500',
          800: 'color800',
          950: 'color950'
        }
      }
    }
  }
}
```

Generates `bg-primary-50`, `text-primary-950`, `border-primary-500`, etc. — the tonal contrast automatically flips with the appearance.

### How the inversion works

| Role                | Light     | Dark      | Notes                                   |
| ------------------- | --------- | --------- | --------------------------------------- |
| `color50` (extreme) | `#030712` | `#f9fafb` | Darkest in light mode, lightest in dark |
| `color500` (middle) | `#6b7280` | `#6b7280` | **Anchor** — identical in both modes    |
| `color950` (mirror) | `#f9fafb` | `#030712` | Mirror of `color50` — extremes reversed |

Fill in intermediate stops (`100`, `300`, `400`, `600`, `700`, `900`) as needed. The full tonal scale is 11 steps.

> 💡 **TIP**
>
> Use this pattern when you want a familiar numeric tonal scale. Use the purpose-based pattern (above) when you prefer semantic intent in the class names. Both can coexist in the same project.


### Generating semantic colors with the `semantic` command

Writing the 11 JSON entries (palette) or each purpose-based color (single) by hand is mechanical and error-prone. The [`semantic`](../Commands.md#semantic-command) command does both, dispatched by `--single`.

#### Palette mode — auto-generated tonal scale

One base hex → 11 entries with mirror inversion + matching `config.cjs` mapping. Both files in one step.

```bash
> purgetss semantic '#15803d' amazon
```

> 💡 **TIP**
>
> Pro Tip — palette in one command
> `pt semantic <hex> <name>` runs the full tonal-inversion workflow:
> 
> 1. Generates the 11-step tonal palette from the input hex (same algorithm as `shades`).
> 2. Writes `app/assets/semantic.colors.json` with mirror-by-index values — `50` ↔ `950`, `100` ↔ `900`, …, `500` as the identical anchor.
> 3. Updates `purgetss/config.cjs` to map the family to those semantic keys (`{ 50: 'amazon50', 100: 'amazon100', … }`).
> 4. Strips any prior keys for the same family before writing — re-runs cleanly replace, never duplicate.
> 
> Classes like `bg-amazon-50`, `text-amazon-950`, `border-amazon-500` flip tonal contrast automatically when the appearance changes.


Flags that pair well with palette mode:

- `--log` (`-l`) — preview the JSON on the console without writing anything.
- `--override` (`-o`) — place the mapping in `theme.colors` instead of `theme.extend.colors`.
- `--random` (`-r`) with `--name` (`-n`) — pick a random base color for a named family.

```bash
> purgetss semantic '#15803d' amazon --log         # preview only
> purgetss semantic '#15803d' amazon --override    # goes into theme.colors
> purgetss semantic --random --name brand          # random base, named family
```

#### Single mode — purpose-based with explicit per-mode hex

For colors like `surfaceColor`, `textColor`, `borderColor`, `overlayColor`, etc. — where light and dark values are **hand-picked from the design system**, not derived algorithmically. Pass `--single`, the light hex, the name, and optionally `--dark` and `--alpha`.

```bash
> purgetss semantic --single '#F9FAFB' surfaceColor       --dark '#0f172a'
> purgetss semantic --single '#FFFFFF' surfaceHighColor   --dark '#1e293b'
> purgetss semantic --single '#111827' textColor          --dark '#f1f5f9'
> purgetss semantic --single '#6B7280' textSecondaryColor --dark '#94a3b8'
> purgetss semantic --single '#E5E7EB' borderColor        --dark '#334155'
> purgetss semantic --single '#3B82F6' accentColor        --dark '#60a5fa'
> purgetss semantic --single '#000000' overlayColor       --alpha 50
```

The name is preserved verbatim as the JSON key (camelCase respected). When `--dark` is omitted, it defaults to the light hex — useful for overlays/glass surfaces where alpha is the only variation.

Single mode writes **both files** in one shot — the JSON entry and an auto-generated class mapping in `config.cjs`. The class name is derived from the semantic key by stripping the conventional `Color` suffix and kebab-casing the rest:

`./purgetss/config.cjs (auto-generated)`
```js
theme: {
  extend: {
    colors: {
      surface:         'surfaceColor',
      'surface-high':  'surfaceHighColor',
      text:            'textColor',
      'text-secondary':'textSecondaryColor',
      border:          'borderColor',
      accent:          'accentColor',
      overlay:         'overlayColor'
    }
  }
}
```

After the batch above you can use `bg-surface`, `bg-surface-high`, `text-text`, `bg-accent`, `bg-overlay`, etc. immediately.

If your design system uses different class names (e.g. `on-surface` instead of `text`, or the nested `surface: { DEFAULT, high }` form from earlier in this page), edit `config.cjs` after running the commands — overriding one mapping is one keystroke; typing the whole structure from scratch is many.

> 💡 **TIP**
>
> Smart in-place updates
> If a `--single` name matches an existing palette shade — e.g. `pt semantic --single '#000' amazon500` while palette `amazon` exists — the entry is **updated in place** in the JSON (preserving its position) and `config.cjs` is left untouched. The palette already maps to that key, so the operation is interpreted as "edit one shade", not "create a duplicate top-level color".


> 💡 **TIP**
>
> Re-running replaces the family
> Re-running on the same family fully replaces it: prior keys (the bare name plus the 11 shade keys) are stripped before the new entries are written. Other palettes and manually-defined entries (`textSecondaryColor`, etc.) survive untouched. Switching between palette and single forms works cleanly — no orphans.


#### Alpha details

Alpha follows the Titanium spec exactly: range `0.0–100.0`, stored as a **string**, wrapped per-mode as `{ color, alpha }`. Without `--alpha`, values stay as bare hex strings. Out-of-range values are rejected before any file is written.
