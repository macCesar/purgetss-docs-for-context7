# Semantic colors

Semantic colors let your app respond to Light/Dark mode changes without extra code. You define color names that resolve to different hex values depending on the current appearance.

## Setting up semantic.colors.json

Create `semantic.colors.json` with your color definitions. The file location follows the TiDev project layout:

- Alloy: `app/assets/semantic.colors.json`
- Classic: `Resources/semantic.colors.json`

> ℹ️ **INFO**
>
> The `semantic` command, covered later on this page, detects the project layout and writes to the right location. The examples below use the Alloy path; Classic projects write under `Resources/` automatically.


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
{
  "overlayColor": {
    "light": "#00000033",
    "dark": "#00000066"
  }
}
```

## Registering utility classes in config.cjs (Alloy)

This section applies to Alloy projects that use the PurgeTSS utility-class workflow. Map the semantic color names to PurgeTSS class names in `config.cjs`:

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

This generates classes such as `bg-surface`, `bg-surface-high`, `text-on-surface`, `text-accent`, and `bg-border`.

Classic projects do not need `config.cjs`. Use the semantic name directly in Titanium properties, for example `backgroundColor: 'surfaceColor'` or `color: 'textColor'`.

### Nesting rules

You can nest one level deep using an object with `DEFAULT`:

```js
// Correct: generates bg-surface and bg-surface-high
surface: {
  DEFAULT: 'surfaceColor',
  high: 'surfaceHighColor'
}
```

> 🛑 **DANGER**
>
> Common error: nested objects without DEFAULT
> ```js
> // Wrong: generates [object Object] instead of a color
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

Both approaches work. Pick the one that gives you the class names you want.

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

When the appearance changes, either through `Appearance.set()` or the system toggle, Titanium resolves each semantic color name to its `light` or `dark` value.

### Opacity modifier auto-derivation

You can apply an opacity modifier (`/N`) to any class that resolves to a semantic name, and PurgeTSS will derive a new semantic key with that alpha pre-applied for both `light` and `dark`:

```xml
<View class="bg-surface/65" />
```

On the next `purgetss build` or `purgetss` run, PurgeTSS:

1. Detects that `bg-surface` maps to the semantic name `surfaceColor`.
2. Adds a derived key `surfaceColor_65` to `semantic.colors.json` with the original hex values and `alpha: "65"` for both modes:

   ```json
   "surfaceColor_65": {
     "light": { "color": "#F9FAFB", "alpha": "65" },
     "dark":  { "color": "#0f172a", "alpha": "65" }
   }
   ```

3. Emits the rule against the derived key, e.g. `'.bg-surface/65': { backgroundColor: 'surfaceColor_65' }`.

Light/Dark switching still works because Titanium handles the lookup like any other semantic color. The same flow runs for opacity inside an `apply:` string in `config.cjs`.

> ℹ️ **INFO**
>
> Native rebuild required for new alpha entries
> `semantic.colors.json` is read at native build time, so the first time a new opacity variant is auto-derived, the running app will not see it until the next full Titanium build. For example, if you have never used `bg-surface/65` before, that new key needs a native rebuild. Later runs reuse the existing entry.
> 
> In practice: run `purgetss build` once after introducing a new opacity class, then start your usual Liveview / `appc run` cycle. The Liveview hot-reload alone does not refresh `semantic.colors.json` for the running app.


Re-runs are idempotent. Keys are reused, not duplicated. If you manually edit a derived key with different values, the next build halts with a `Conflict` error instead of overwriting your changes.

Constraints:

- Alpha must be an integer in the `0–100` range, matching the existing opacity modifier syntax.
- The base key (`surfaceColor` in this example) must already exist in `semantic.colors.json`. Without it, PurgeTSS emits a warning (direct XML usage) or throws an Error (apply directives) with three concrete suggestions.
- The naming convention is `<originalKey>_<alphaPercent>` (underscore + integer percent). It mirrors the `/65` you typed and stays quote-free in `config.cjs`.

## Using semantic colors in controllers

Semantic colors also work from JavaScript. You have three reasonable options, depending on whether you are creating a new component or styling an existing one.

### Option 1: Direct assignment by semantic name

Titanium resolves the semantic name at runtime, so you can assign it straight to a color-accepting property:

```js
$.titleLabel.color = 'textColor'
$.card.backgroundColor = 'surfaceHighColor'
$.divider.backgroundColor = 'borderColor'
```

This skips PurgeTSS entirely. Use it when you only need one or two color changes and nothing else.

### Option 2: `$.UI.create()` with PurgeTSS classes

When you build a component dynamically, use `$.UI.create()` so you get the full set of utilities, including colors:

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

To restyle an existing component, for example when state changes, create the style and apply it:

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
> - Option 1: single property change, no other utilities needed.
> - Option 2: creating new components from scratch.
> - Option 3: restyling components that already exist in the view.


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
> Start with these 5-6 colors. Add more only when the design actually needs them. Fewer semantic colors are easier to maintain.


## Alternative: numeric scale with inversion

Instead of purpose-based names, you can take any palette with 11 tonal steps and map it to a numeric scale (`50` through `950`), where each light-mode value inverts in dark mode. That gives you a full tonal range from a single palette.

The example below uses a neutral gray palette for clarity, but the same inversion pattern works with any hue: blues, greens, warm tones, or a custom brand palette. What matters is that the 11 stops share a consistent tonal progression.

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

Generates `bg-primary-50`, `text-primary-950`, `border-primary-500`, and similar classes. The tonal contrast automatically flips with the appearance.

### How the inversion works

| Role                | Light     | Dark      | Notes                                   |
| ------------------- | --------- | --------- | --------------------------------------- |
| `color50` (extreme) | `#030712` | `#f9fafb` | Darkest in light mode, lightest in dark |
| `color500` (middle) | `#6b7280` | `#6b7280` | Anchor, identical in both modes         |
| `color950` (mirror) | `#f9fafb` | `#030712` | Mirror of `color50`, extremes reversed  |

Fill in intermediate stops (`100`, `300`, `400`, `600`, `700`, `900`) as needed. The full tonal scale is 11 steps.

> 💡 **TIP**
>
> Use this pattern when you want a familiar numeric tonal scale. Use the purpose-based pattern above when you want the class names to describe intent. Both can live in the same project.


### Generating semantic colors with the `semantic` command

Writing 11 palette entries by hand is tedious. So is creating each purpose-based color one by one. The [`semantic`](../commands.md#semantic-command) command handles both forms; `--single` chooses the purpose-based mode.

#### Palette mode: generated tonal scale

One base hex creates 11 entries with mirror inversion. Alloy also receives the matching `config.cjs` mapping; Classic receives only the native `Resources/semantic.colors.json` file.

```bash
> purgetss semantic '#15803d' amazon
```

> 💡 **TIP**
>
> Palette in one command
> `pt semantic <hex> <name>` runs the full tonal-inversion workflow:
> 
> 1. Generates the 11-step tonal palette from the input hex (same algorithm as `shades`).
> 2. Writes `semantic.colors.json` at the project's canonical location (`app/assets/` on Alloy, `Resources/` on Classic) with mirror-by-index values: `50` ↔ `950`, `100` ↔ `900`, and `500` as the identical anchor.
> 3. On Alloy, updates `purgetss/config.cjs` to map the family to those semantic keys, for example `{ 50: 'amazon50', 100: 'amazon100' }`. Classic skips this step.
> 4. Removes prior keys for the same family before writing, so re-runs replace instead of duplicating.
> 
> Classes like `bg-amazon-50`, `text-amazon-950`, `border-amazon-500` flip tonal contrast automatically when the appearance changes.


Flags that pair well with palette mode:

- `--log` (`-l`): preview the JSON on the console without writing anything.
- `--override` (`-o`): place the mapping in `theme.colors` instead of `theme.extend.colors`.
- `--random` (`-r`) with `--name` (`-n`): pick a random base color for a named family.

```bash
> purgetss semantic '#15803d' amazon --log         # preview only
> purgetss semantic '#15803d' amazon --override    # goes into theme.colors
> purgetss semantic --random --name brand          # random base, named family
```

#### Single mode: purpose-based colors

Use single mode for colors like `surfaceColor`, `textColor`, `borderColor`, and `overlayColor`, where the light and dark values come from your design system instead of a generated scale. Pass `--single`, the light hex, the name, and optionally `--dark` and `--alpha`.

```bash
> purgetss semantic --single '#F9FAFB' surfaceColor       --dark '#0f172a'
> purgetss semantic --single '#FFFFFF' surfaceHighColor   --dark '#1e293b'
> purgetss semantic --single '#111827' textColor          --dark '#f1f5f9'
> purgetss semantic --single '#6B7280' textSecondaryColor --dark '#94a3b8'
> purgetss semantic --single '#E5E7EB' borderColor        --dark '#334155'
> purgetss semantic --single '#3B82F6' accentColor        --dark '#60a5fa'
> purgetss semantic --single '#000000' overlayColor       --alpha 50
```

The name is preserved verbatim as the JSON key, including camelCase. When `--dark` is omitted, it defaults to the light hex. That is useful for overlays or glass surfaces where alpha is the only variation.

On Alloy, single mode writes the JSON entry and an auto-generated class mapping in `config.cjs`. The class name is derived from the semantic key by stripping the conventional `Color` suffix and kebab-casing the rest:

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

On Classic, single mode writes only `Resources/semantic.colors.json`; it does not create `purgetss/config.cjs`, TSS, `app/`, or an Alloy hook. Use the generated names directly in Titanium properties.

If your design system uses different class names, such as `on-surface` instead of `text`, or the nested `surface: { DEFAULT, high }` form from earlier in this page, edit `config.cjs` after running the commands. Adjusting one generated mapping is faster than typing the whole structure from scratch.

> 💡 **TIP**
>
> Smart in-place updates
> If a `--single` name matches an existing palette shade, the entry is updated in place in the JSON and `config.cjs` is left untouched. For example, `pt semantic --single '#000' amazon500` edits the existing `amazon500` shade when the `amazon` palette already exists. The palette already maps to that key, so PurgeTSS treats the command as "edit one shade" instead of "create a duplicate top-level color".


> 💡 **TIP**
>
> Re-running replaces the family
> Re-running on the same family replaces it. Prior keys, including the bare name and the 11 shade keys, are removed before the new entries are written. Other palettes and manually-defined entries such as `textSecondaryColor` stay untouched. Switching between palette and single forms does not leave orphaned keys.


#### Alpha details

Alpha follows the Titanium spec: range `0.0–100.0`, stored as a string, wrapped per-mode as `{ color, alpha }`. Without `--alpha`, values stay as bare hex strings. Out-of-range values are rejected before any file is written.
