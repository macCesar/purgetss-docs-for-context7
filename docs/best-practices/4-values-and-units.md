# Values and units

PurgeTSS writes plain numeric values into your TSS files. Titanium decides how to interpret them.

## The short version

Numeric values produced by PurgeTSS utility classes are **unitless**. When PurgeTSS compiles `rounded-lg` to:

```css
'.rounded-lg': { borderRadius: 8 }
```

the `8` is **not a pixel value**. Titanium reads it at runtime using the unit declared in `tiapp.xml`:

```xml
<ti:app>
  <property name="ti.ui.defaultunit" type="string">dp</property>
</ti:app>
```

The Alloy project template has used `dp` by default for years, so most PurgeTSS projects are working with **density-independent pixels**, not raw pixels. In practice, `borderRadius: 8` usually means `8dp`, not `8px`.

## Why this matters

Saying `rounded-lg` gives you "8 pixels" is only correct if your `tiapp.xml` explicitly sets `ti.ui.defaultunit` to `px`. Most projects do not.

A more accurate way to describe it is:

> `rounded-lg` applies `borderRadius: 8` — which Titanium interprets as **8 units of `ti.ui.defaultunit`**, defaulting to `dp` on standard Alloy projects.

This applies to every utility class that sets a dimension:
- Spacing: `m-*`, `p-*`, `mt-*`, `mb-*`, `mx-*`, `my-*`, `top-*`, `left-*`, `right-*`, `bottom-*`
- Sizes: `w-*`, `h-*`, `size-*`, `wh-*`
- Corners: `rounded-*`, `border-radius-*`
- Borders: `border-*` (width)
- Typography: `text-*` (font size), `letter-spacing-*`, `line-spacing-*`
- Shadows: `drop-shadow-*`, `shadow-radius-*`
- Elevation: `elevation-*`, `max-elevation-*`
- Transforms: `translate-*`, `move-by-*`
- Offsets: `x-offset-*`, `y-offset-*`

## Valid values for `ti.ui.defaultunit`

Titanium recognizes these unit identifiers:

| Value    | Meaning                                 | Notes                                                                                 |
|----------|-----------------------------------------|---------------------------------------------------------------------------------------|
| `dp`     | Density-independent pixels              | Alloy template default. Good default for cross-density consistency.                   |
| `dip`    | Alias for `dp`                          | Same as `dp`.                                                                          |
| `px`     | Raw pixels                              | Usually a poor fit for UI sizing on high-DPI devices.                                 |
| `mm`     | Millimeters                             | Physical measurement.                                                                 |
| `cm`     | Centimeters                             | Physical measurement.                                                                 |
| `in`     | Inches                                  | Physical measurement.                                                                 |
| `pt`     | Points (1/72 inch)                      | Physical measurement.                                                                 |
| `system` | Platform default                        | iOS uses points; Android uses pixels. This is inconsistent across platforms.           |

## Forcing pixels explicitly

Some PurgeTSS classes use explicit pixel values written as strings:

```css
'.border-radius-px': { borderRadius: '1px' }
'.w-px':             { width: '1px' }
'.h-px':             { height: '1px' }
```

If a value includes its own suffix, such as `'1px'`, `'2pt'`, or `'4mm'`, Titanium uses that unit directly and ignores `ti.ui.defaultunit`.

Use these when you actually need a 1px line or another exact unit.

## Percentages are unit-independent

Fractional utility classes produce percentage strings. Titanium resolves those against the parent size, not against `ti.ui.defaultunit`:

```css
'.w-1/2':  { width: '50%' }
'.h-full': { height: '100%' }
```

These behave the same no matter which default unit your app uses.

## Titanium constants are unit-independent

Utility classes that resolve to Titanium layout constants also ignore units:

```css
'.w-auto':   { width: Ti.UI.SIZE }
'.h-screen': { height: Ti.UI.FILL }
```

`Ti.UI.SIZE` and `Ti.UI.FILL` are layout directives, not numeric dimensions.

## Checking your project's setting

Open `tiapp.xml` and look for:

```xml
<property name="ti.ui.defaultunit" type="string">dp</property>
```

If that property is missing, Titanium falls back to `system`. That means iOS and Android can interpret the same number differently, so it is better to set `dp` explicitly.

## Summary

- PurgeTSS writes **unitless numeric values** into `app.tss`.
- Titanium resolves those values using `ti.ui.defaultunit` in `tiapp.xml`.
- In most Alloy projects, that unit is `dp`, **not raw pixels**.
- Values ending in `-px` (written as quoted strings) force raw pixels regardless of the project setting.
- Percentage-based and `auto`/`screen` utilities are unit-independent.
