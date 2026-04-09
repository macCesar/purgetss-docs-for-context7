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

