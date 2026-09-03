# Runtime implementation rules

This page describes the behavior of the generated module. It is useful when an Alloy class or Classic JavaScript object produces an unexpected result.

## Arguments are forwarded before method overrides

The animation constructor keeps the supplied object as its base configuration. Most helpers spread that object into `Ti.UI.createAnimation()` and then append method-specific values. A later method value wins when both define the same key.

For example, `shake()` forwards values such as `delay` and `opacity`, but then replaces `transform`, `duration`, `autoreverse`, `repeat`, and `curve` because those fields define the shake effect.

This is property forwarding, not a guarantee that every native Titanium property is meaningful for every helper. Refer to Titanium's [`Ti.UI.Animation`](https://titaniumsdk.com/api/titanium/ui/animation.html) contract for platform support.

## Transform conversion

At construction time, top-level `scale`, `rotate`, and `anchorPoint` values are converted into one `Ti.UI.Matrix2D` and removed from the base object. The same conversion prepares separate matrices for `animationProperties.open` and `.close`.

`pulse`, `shake`, `swap`, `snapTo`, `reorder`, and `transition` can replace or reset that transform as part of their own behavior. Do not assume that a transform survives a position helper.

## State resolution

`play()`, `toggle()`, `apply()`, and `sequence()` toggle the internal state. `open()` and `close()` set it explicitly. When `animationProperties` exists, the active state is merged into the current base object.

After `play()` or `apply()`, a top-level `animationProperties.complete` object runs on the target. For `play()` this is a second native animation; for `apply()` it is applied immediately.

For each direct child that declares an active state, properties are merged in this order:

1. The parent's `animationProperties.children` object.
2. The child's `animationProperties.child` object.
3. The child's active `open`, `close`, or `complete` object.

Later objects override earlier values. Child animation is only considered for direct children of the target view.

## Arrays and callbacks

`play()`, `open()`, `close()`, and `apply()` accept one view or an array. For an array, the callback runs once per view and includes `index`, `total`, and `getTarget()`. The configured base delay is accumulated for each successive view.

`sequence()` also accepts an array, but starts the next view only after the previous completion event. Its callback runs once, after the final view. Passing an empty array produces no callback.

The enriched callback object contains only selected native fields plus primitives and `getTarget()`; it is not the original Titanium event.

## Position state

The helpers use private fields to reconcile rendered and logical positions:

| Field | Purpose |
|---|---|
| `_originTop`, `_originLeft` | Captured logical home/current position |
| `_visualTop`, `_visualLeft` | Last calculated drag position |
| `_bouncingBack` | Guards an in-progress return-to-origin animation |
| `_collisionEnabled` | Includes the view in collision handling |
| `_dragListeners` | Stores listener functions for removal |
| `_wasDragged` | Records whether touch movement occurred |

`swap()`, `snapTo()`, and `reorder()` persist their destination with `applyProperties()` and update the private origin. They use `rect` when explicit position information is unavailable, so views must already be laid out.

## Drag configuration and precedence

- Global bounds come from the animation object. `view.bounds` overrides individual global edges.
- Axis restriction comes from `view.constraint`; constructor-level `constraint` is not read.
- `draggingType` uses the per-view value first and then the animation value.
- Global `draggable.drag/drop` properties are merged with per-view `view.draggable.drag/drop`; view properties win.
- `animationProperties.keepZIndex` prevents the default promotion of the active draggable.
- `animationProperties.snap.center` calls `snapTo()` after a valid drop.
- `animationProperties.snap.back` returns a missed drop to its captured origin.
- No `snap.magnet` behavior exists in the current implementation.

The runtime passes all `drag` and `drop` properties to Titanium. It does not enforce the previously documented exclusion of size, scale, or anchor-point properties.

Calling `draggable()` with an array assigns `zIndex` from the array position before listeners are registered. `keepZIndex` does not prevent that initial assignment; it only prevents later promotion on touch start. `swap()` also restores stacking according to the draggable registry order rather than preserving arbitrary original values.

Collision detection checks whether the center of the dragged view is inside another registered view's `rect`. On release, the last non-null hover target is used as a fallback if the final hit test returns `null`.

## Android drag behavior

Android performs zero-duration native animations during movement. On touch end, when translation state exists, the runtime consolidates `translation`, `rotate`, `scale`, and an equivalent `Matrix2D` through `applyProperties()` before collision, snap, or drop callbacks run.

This differs from the old documentation: the current code does not consolidate only `top` and `left`. iOS uses synchronous property application for standard dragging and has a separate path for transformed views.

## Transition behavior

`transition()` builds a single matrix per view from translation, rotation, and scale. It applies `zIndex` before starting the animation and can include width, height, and opacity.

If a view has no matching layout, it fades out, receives `zIndex: 0`, and has touch disabled. iOS preserves its last transform. Android resets transform, translation, rotation, and scale. If the view later receives a layout, the runtime fades it in and reenables touch.

The completion handler sets `touchEnabled: true` for every view that receives a layout. Reapply a disabled state if the view was intentionally non-interactive before the transition.

## Cleanup contract and current limitations

`undraggable()` removes the registered `touchstart`, `touchmove`, `touchend`, and global `orientationchange` listeners. It also removes the first matching draggable/collision registration and deletes most private position and collision fields.

Two current limitations require application discipline:

- `draggable()` does not deduplicate a view. Calling it repeatedly creates more listeners and registrations, while one `undraggable()` call removes only the latest stored listeners and the first registry entry.
- `undraggable()` does not delete `_wasDragged` in the current release.
- `detectCollisions()` keeps its hover and drop callbacks on the animation object. Passing `null` later does not clear them.

Register each view once. When its window closes, call `undraggable()` for draggable views and every registered collision target, remove listeners owned by your application, and release the animation object if its callbacks are no longer needed. These are runtime limitations, not extension points; application code should never depend on private underscore properties.

## Titanium Classic shape

These rules also apply to a JavaScript object passed from Classic:

`Resources/app.js`
```js
const { createAnimation } = require('lib/purgetss.ui')

const motion = createAnimation({
  duration: 180,
  delay: 20,
  curve: Ti.UI.ANIMATION_CURVE_EASE_OUT,
  bounds: { top: 12, right: 12, bottom: 12, left: 12 },
  draggable: {
    drag: { opacity: 0.7 },
    drop: { opacity: 1 }
  },
  animationProperties: {
    open: { opacity: 1, scale: 1 },
    close: { opacity: 0, scale: 0.9 },
    complete: { borderColor: '#22c55e' },
    children: { duration: 120 },
    snap: { back: true, center: true },
    keepZIndex: true
  }
})
```

See [Using `purgetss.ui` in Titanium Classic](./2-titanium-classic.md) for runnable examples.
