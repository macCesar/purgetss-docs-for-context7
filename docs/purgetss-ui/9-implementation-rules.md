# Implementation rules

Rules that every method in the Animation module must follow. They keep behavior consistent with the declarative model of PurgeTSS.

## Rule 1: Inherit from the `<Animation />` object via `...args`

Every method must inherit all properties from the Animation object by spreading `args`. Never cherry-pick individual properties.

The `<Animation />` object is the single source of truth for animation behavior. When a developer declares:

```xml
<Animation id="myAnim" module="purgetss.ui" class="curve-animation-ease-out opacity-50 delay-100 duration-300" />
```

Every timing property (`duration`, `delay`, `curve`) and visual property (`opacity`, `backgroundColor`, `width`, and so on) is available in `args` and must be inherited by all methods.

```javascript
// Correct: inherits everything, method-specific props override
view.animate({ ...args, left: destLeft, top: destTop, transform: Ti.UI.createMatrix2D() })

// Wrong: cherry-picks individual properties, breaks if new ones are added
view.animate({ duration: args.duration, delay: args.delay, left: destLeft, top: destTop })
```

### Why this matters

This is the same pattern used by the core `playView` function:

```javascript
// playView passes all of args to Ti.UI.createAnimation
const animation = Ti.UI.createAnimation(args)
view.animate(animation)
```

If a developer adds `opacity-50` to their `<Animation>`, they expect every method to animate opacity, including methods beyond `play`. The `<Animation />` declares behavior; methods execute it.

---

## Rule 2: Override by position, not by exclusion

If a method needs fixed values for specific properties, they go AFTER `...args` to override. Never filter or exclude properties from args.

```javascript
// Correct: shake inherits everything from args, then overrides what it needs
view.animate({
  ...args,                                                    // inherit all
  transform: Ti.UI.createMatrix2D().translate(intensity, 0),  // override: shake-specific transform
  duration: Math.round((args.duration ?? 400) / 6),           // override: divided for oscillation
  autoreverse: true,                                          // override: required for shake
  repeat: 3,                                                  // override: required for shake
  curve: Ti.UI.ANIMATION_CURVE_EASE_IN_OUT                    // override: required for shake
})

// Wrong: filters args, only picks what it thinks it needs
view.animate({
  duration: args.duration,
  transform: Ti.UI.createMatrix2D().translate(intensity, 0),
  autoreverse: true,
  repeat: 3
})
```

### Override order

Properties declared later in the object literal override earlier ones:

```javascript
// If args = { duration: 300, opacity: 0.5, curve: EASE_OUT }
view.animate({
  ...args,        // duration: 300, opacity: 0.5, curve: EASE_OUT
  duration: 50,   // overrides to 50
  curve: EASE_IN   // overrides to EASE_IN
})
// Result: { duration: 50, opacity: 0.5, curve: EASE_IN }
// opacity 0.5 is preserved from args, not lost by filtering
```

---

## Rule 3: No timing parameters in method signatures

The existing core methods (`play`, `open`, `close`, `apply`, `sequence`) do not accept `duration`, `delay`, or `curve` as parameters. New methods must follow the same pattern.

Only parameters specific to the method's unique functionality are allowed.

```javascript
// Correct: only method-specific parameters
animationView.swap = (view1, view2) => {
animationView.reorder = (views, newOrder) => {
animationView.shake = (view, intensity = 10) => {    // intensity is shake-specific
animationView.snapTo = (view, targets) => {
animationView.pulse = (view, scale = 1.2) => {       // future: scale is pulse-specific

// Wrong: timing parameters belong in the <Animation /> object
animationView.swap = (view1, view2, duration) => {
animationView.shake = (view, intensity, duration) => {
```

### How users control timing

All timing is controlled declaratively via the `<Animation>` object's classes:

```xml
<!-- Fast swap -->
<Animation id="fastSwap" module="purgetss.ui" class="duration-75" />

<!-- Slow swap with delay -->
<Animation id="slowSwap" module="purgetss.ui" class="delay-200 duration-500" />
```

```javascript
// Same method call, different behavior controlled by XML
$.fastSwap.swap($.card1, $.card2)
$.slowSwap.swap($.card1, $.card2)
```

---

## Rule 4: Consolidate state with `applyProperties` post-animation

After animating position (`left`/`top`), always consolidate with `applyProperties` in the callback so the final state is real, not only visual through `transform`.

```javascript
// Correct: consolidates after animation
view.animate({
  ...args, left: destLeft, top: destTop, transform: Ti.UI.createMatrix2D()
}, () => {
  view.applyProperties({ left: destLeft, top: destTop, transform: Ti.UI.createMatrix2D() })
})

// Wrong: animation ends but view's actual properties are stale
view.animate({
  ...args, left: destLeft, top: destTop, transform: Ti.UI.createMatrix2D()
})
```

### Why this matters

On iOS, dragging uses `transform.translate()`, so the view's `left`/`top` properties do not change. After a position animation, `applyProperties` ensures:
- The view's actual `left`/`top` match the visual position
- The transform is reset to identity
- Subsequent animations start from the correct position

---

## Rule 5: Track position with `_origin*` properties

Methods that move position (`swap`, `reorder`, `snapTo`, and future methods like `slideTo`) must update `_originTop`/`_originLeft` after the animation so later drag and swap operations work correctly.

```javascript
// Correct: updates origin tracking
view.animate({
  ...args, left: destLeft, top: destTop, transform: Ti.UI.createMatrix2D()
}, () => {
  view.applyProperties({ left: destLeft, top: destTop, transform: Ti.UI.createMatrix2D() })
})

view._originTop = destTop
view._originLeft = destLeft

// Wrong: origin tracking is stale
view.animate({
  ...args, left: destLeft, top: destTop, transform: Ti.UI.createMatrix2D()
})
// Next swap/drag uses the old position
```

### How `_origin*` works

- `_originTop`/`_originLeft` represent the view's "logical grid position"
- `swap` reads from `view._originTop ?? view.top`; it falls back to the actual `top` if no origin is set
- `onTouchStart` in the drag handler saves the current `top`/`left` as `_origin*` for bounce-back
- `undraggable` cleans up all `_origin*` properties

---

## Rule 6: Consolidate Android drag position before drop animations

On Android, drag uses `animate({ duration: 0 })`, which is asynchronous. The last frame may still be in flight when `touchend` fires. Any animation started on drop (`snapTo`, bounce-back, or `swap` via `dropCB`) can conflict with this pending drag animation.

Before starting any drop animation on Android, consolidate the view's position with `applyProperties`:

```javascript
if (!params.isIOS) {
  draggableView.applyProperties({
    top: draggableView._visualTop ?? draggableView.top,
    left: draggableView._visualLeft ?? draggableView.left
  })
}
```

This applies to both the snap path and the bounce-back path in `onTouchEnd`. iOS does not need this because drag uses synchronous `transform.translate()`.

### Collision fallback on drop

During drag, `checkCollision` runs on every `touchmove`. When the user releases while still in motion, the drag center may exit the target between the last `touchmove` and `touchend`. To handle this, the module tracks `lastKnownTarget`, the last non-null collision during drag, and uses it as a fallback when `checkCollision` returns null on drop.

---

## Rule 7: Clean up in `undraggable`

Every internal property added to views must be cleaned up in `undraggable`. This includes:

| Property                     | Set by                                      | Purpose                                                                          |
| ---------------------------- | ------------------------------------------- | -------------------------------------------------------------------------------- |
| `_originTop` / `_originLeft` | `swap`, `reorder`, `snapTo`, `onTouchStart` | Logical position tracking                                                        |
| `_visualTop` / `_visualLeft` | `handleTouchMove`                           | Visual position during drag                                                      |
| `_dragListeners`             | `makeViewsDraggable`                        | Touch event listener references                                                  |
| `_collisionEnabled`          | `detectCollisions`                          | Collision detection flag                                                         |
| `_wasDragged`                | `onTouchStart` / `handleTouchMove`          | Drag detection flag                                                              |
| `_bouncingBack`              | `onTouchEnd` (bounce-back)                  | Prevents origin capture during mid-animation; `swap` cancels it before animating |

When adding a new method that stores internal state on views, add the cleanup to `undraggable`:

```javascript
animationView.undraggable = (_views) => {
  const arr = Array.isArray(_views) ? _views : [_views]
  arr.forEach(view => {
    // ... existing cleanup ...
    delete view._newProperty  // ADD cleanup for any new internal property
  })
}
```

---

## Summary: method implementation template

When creating a new method, follow this template:

```javascript
animationView.newMethod = (view, specificParam = defaultValue) => {
  if (params.debug) { console.log('') }
  logger('`newMethod` method called on: ' + params.id)
  if (!view) { return notFound() }

  view.animate({
    ...args,                          // Rule 1: inherit all from <Animation />
    specificProp: computedValue,      // Rule 2: override AFTER ...args
  }, () => {
    view.applyProperties({ ... })    // Rule 4: consolidate state
  })

  view._originTop = newTop           // Rule 5: track position (if applicable)
  view._originLeft = newLeft
}
// Rule 3: no timing params in signature
// Rule 6: add cleanup to undraggable (if new internal state)
```
