---
sidebar_position: 1
slug: introduction
---

# Introduction

## The Animation Module
:::info
PurgeTSS includes an Animation module to apply simple 2D Matrix animations and transformations to any element, an array of elements, or individual children of an element.
:::

The Animation object describes an animation in a few ways:
- A single-phase animation with an end state
- A multi-phase animation using the `open`, `close`, and `complete` modifiers
- Global states for children of a View using the `children` modifier

When you call `play` on a View, it animates from its current state to the state described by the `Animation` object. You can animate position, size, colors, transformation matrix, and opacity. Control timing with classes like `duration-*` and `delay-*`.

## Available methods
- `play`, `toggle`: Animate an element, an array of elements, or individual children using the `Animation` object.
- `open`, `close`: Explicitly run opening and closing animations.
- `apply`: Apply properties instantly without animation.
- `draggable`: Convert a View or array of Views into draggable elements.

## Available modifiers
- `open:`, `close:`, `complete:`: Set different properties for each state.
- `children:`: Set global properties for all children of a View.
- `child:`: Set individual properties for each child of a View.
- `bounds:`: Set boundaries within which the View can move inside its parent.
- `drag:`, `drop:`: Set different properties when dragging or dropping elements.

## Timing and special classes
- `delay-*`: Delay before the animation starts.
- `duration-*`: Duration of the animation.
- `rotate-*`: Rotation of the element.
- `scale-*`: Scaling of the element.
- `repeat-*`: Number of repeats.
- `zoom-in-*`, `zoom-out-*`: Zoom in or out.
- `drag-apply`, `drag-animate`: Apply or animate properties while dragging.
- `ease-in`, `ease-out`, `ease-linear`, `ease-in-out`: Animation curve.
- `vertical-constraint`, `horizontal-constraint`: Constrain dragging to one axis.

## Installation
Use the **`purgetss module`** command to install the module in the `lib` folder.

```bash
> purgetss module

# alias:
> purgetss m
```

## Usage
This is the simplest `Animation` object, with a set of **PurgeTSS** classes. You can create as many animation objects as you want, each with its own properties.

```xml
<Animation id="myAnimation" module="purgetss.ui" class="a-set-of-purgetss-classes-and-modifiers" />
```

**You can use any position, size, color, transformation, and opacity classes from `utilities.tss`.**
