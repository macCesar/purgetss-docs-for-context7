# Introduction

## The UI module

> ℹ️ **INFO**
>
> `purgetss.ui` is the runtime module included with PurgeTSS. It provides animation, appearance management, and utility functions from a single `require('purgetss.ui')`.


The module exports three areas of functionality:

| Area | What it does |
|------|-------------|
| Animation | 2D Matrix animations and transformations on single elements, arrays, or children |
| [Appearance](appearance) | Light/Dark/System mode switching with persistent user preference |
| Utilities | `deviceInfo()` for platform diagnostics, `saveComponent()` for view snapshots |

---

## Animation

The Animation object describes an animation in a few ways:
- A single-phase animation with an end state
- A multi-phase animation using the `open`, `close`, and `complete` modifiers
- Global states for children of a View using the `children` modifier

When you call `play` on a View, it animates from its current state to the state described by the `Animation` object. You can animate position, size, colors, transformation matrix, and opacity. Control timing with classes like `duration-*` and `delay-*`.

### Available methods
- `play`, `toggle`: Animate an element, an array of elements, or individual children using the `Animation` object.
- `open`, `close`: Explicitly run opening and closing animations.
- `apply`: Apply properties instantly without animation.
- `draggable`: Convert a View or array of Views into draggable elements.
- `undraggable`: Remove draggable behavior and clean up all listeners.
- `detectCollisions`: Enable collision detection on draggable views with hover and drop callbacks.
- `sequence`: Animate views one after another (sequential, not parallel).
- `swap`: Animate two views exchanging positions.
- `shake`: Quick horizontal shake for error/feedback.
- `pulse`: Scale a view up and back for attention (badges, notifications).
- `snapTo`: Snap a view to the nearest target by center distance.
- `reorder`: Animate views to new positions based on index mapping.
- `transition`: Animate multiple views to new layout configurations using Matrix2D (translate, rotate, scale).

The `play`, `toggle`, `open`, `close`, `apply`, and `sequence` methods accept an optional callback. The callback receives an enriched event object, see [The `play` method](the-play-method#callback-event-object) for details.

### Available modifiers
- `open:`, `close:`, `complete:`: Set different properties for each state.
- `children:`: Set global properties for all children of a View.
- `child:`: Set individual properties for each child of a View.
- `bounds:`: Set boundaries within which the View can move inside its parent.
- `drag:`, `drop:`: Set different properties when dragging or dropping elements.

### Timing and special classes
- `delay-*`: Delay before the animation starts.
- `duration-*`: Duration of the animation.
- `rotate-*`: Rotation of the element.
- `scale-*`: Scaling of the element.
- `repeat-*`: Number of repeats.
- `zoom-in-*`, `zoom-out-*`: Zoom in or out.
- `drag-apply`, `drag-animate`: Apply or animate properties while dragging.
- `ease-in`, `ease-out`, `ease-linear`, `ease-in-out`: Animation curve.
- `vertical-constraint`, `horizontal-constraint`: Constrain dragging to one axis.

---

## Utilities

The module also exports two helper functions:

- `deviceInfo()`: Logs detailed platform and display information to the console. Works in both Alloy and Classic Titanium projects.
- `saveComponent({ source, directory })`: Saves a snapshot of a view as a PNG to the photo gallery.

---

## Installation
Use the `purgetss module` command to install the module in the `lib` folder.

```bash
> purgetss module

# alias:
> purgetss m
```

## Animation usage
This is the simplest `Animation` object, with a set of PurgeTSS classes. You can create as many animation objects as you want, each with its own properties.

```xml
<Animation id="myAnimation" module="purgetss.ui" class="a-set-of-purgetss-classes-and-modifiers" />
```

You can use any position, size, color, transformation, and opacity classes from `utilities.tss`.
