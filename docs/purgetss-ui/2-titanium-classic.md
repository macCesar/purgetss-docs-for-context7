# Using `purgetss.ui` in Titanium Classic

Titanium Classic uses the same runtime module as Alloy, but it does not compile Alloy XML, TSS, utility classes, controllers, or `$.*` references. Generate the module and configure it with JavaScript objects containing native Titanium properties.

## Quick start

From the Classic project root:

```bash
purgetss module
```

This creates `Resources/lib/purgetss.ui.js`. A minimal `Resources/app.js` can then load all commonly used exports:

`Resources/app.js`
```js
const {
  createAnimation,
  Appearance,
  deviceInfo,
  saveComponent
} = require('lib/purgetss.ui')

Appearance.init()

const window = Ti.UI.createWindow({ backgroundColor: 'surfaceColor' })
const card = Ti.UI.createView({
  width: 220,
  height: 120,
  borderRadius: 16,
  backgroundColor: 'accentColor',
  opacity: 0,
  transform: Ti.UI.createMatrix2D().scale(0.92)
})
const label = Ti.UI.createLabel({ text: 'Tap to animate', color: 'textColor' })
const cardMotion = createAnimation({
  duration: 220,
  curve: Ti.UI.ANIMATION_CURVE_EASE_OUT,
  animationProperties: {
    open: { opacity: 1, scale: 1 },
    close: { opacity: 0, scale: 0.92 }
  }
})

function onCardClick() { cardMotion.play(card) }
function onWindowOpen() { cardMotion.open(card) }
function onWindowClose() {
  card.removeEventListener('click', onCardClick)
  window.removeEventListener('open', onWindowOpen)
  window.removeEventListener('close', onWindowClose)
}

card.add(label)
card.addEventListener('click', onCardClick)
window.add(card)
window.addEventListener('open', onWindowOpen)
window.addEventListener('close', onWindowClose)
window.open()

// Optional diagnostics; this logs values and returns undefined.
// deviceInfo()

// saveComponent() is intentionally not called here because it writes a PNG
// and invokes the system photo-gallery API. See "Runtime utilities" below.
```

The semantic names in this example come from `Resources/semantic.colors.json`; see [Appearance](./10-appearance.md#titanium-classic).

## Public exports

| Export | Type and return | Notes |
|---|---|---|
| `AnimationProperties` | Constructor; returns the decorated animation view | Public, but `createAnimation()` is clearer in Classic code |
| `createAnimation(args)` | Factory; returns the decorated animation view | Preferred Classic entry point |
| `deviceInfo()` | Function; returns `undefined` | Logs platform and display data |
| `saveComponent({ source, directory? })` | Function; returns `undefined` | Writes a PNG and invokes the gallery API |
| `Appearance` | Singleton object | Exposes `init()`, `set()`, `get()`, and `toggle()` |

An animation instance is a zero-size, touch-disabled `Ti.UI.View` decorated with the methods below. Treat it as a behavior object; it does not need to be added to the window.

## Alloy and Classic matrix

| Functionality | Public API | Alloy example | Titanium Classic | Platform note or limitation |
|---|---|---|---|---|
| Toggle a state | [`play`, `toggle`](./2-the-play-method.md) | `<Animation>` plus `$.*` | `animation.play(view)` | Both names run the same function |
| Apply immediately | [`apply`](./3-the-apply-method.md) | Controller reference | `animation.apply(view)` | No native animation is started |
| Force a state | [`open`, `close`](./4-the-open-close-methods.md) | `open:` and `close:` classes | `animationProperties.open/close` | Does not toggle the requested state |
| Drag | [`draggable`](./5-the-draggable-method.md) | Classes plus controller | Direct view references | Register only after views are in the hierarchy |
| Remove drag | [`undraggable`](./5-the-draggable-method.md#the-undraggable-method) | Controller cleanup | `animation.undraggable(view)` | Required lifecycle cleanup |
| Collisions | [`detectCollisions`](./5-the-draggable-method.md#the-detectcollisions-method) | Alloy IDs | Direct view references | Tests the dragged view's center point |
| Serial motion | [`sequence`](./6-additional-methods.md#the-sequence-method) | Array of `$.*` views | Array of Titanium views | Waits between views; `play(array)` runs in parallel |
| Exchange positions | [`swap`](./6-additional-methods.md#the-swap-method) | Two `$.*` views | Two Titanium views | Depends on rendered `rect` values |
| Attention pulse | [`pulse`](./6-additional-methods.md#the-pulse-method) | View reference | `animation.pulse(view, count)` | Forces autoreverse and ease-in-out |
| Error feedback | [`shake`](./6-additional-methods.md#the-shake-method) | View reference | `animation.shake(view, intensity)` | Uses six short phases |
| Nearest target | [`snapTo`](./6-additional-methods.md#the-snapto-method) | View and target IDs | Direct view references | Returns the selected target or `null` |
| Index mapping | [`reorder`](./6-additional-methods.md#the-reorder-method) | Views and mapping | Views and mapping | Mapping length must match view count |
| Layout preset | [`transition`](./6-additional-methods.md#the-transition-method) | Views and layouts | Views and object layouts | Missing layouts hide and disable a view |
| Theme | `Appearance` | Semantic classes | Semantic names in native properties | Initialize before the first window |
| Diagnostics | `deviceInfo` | CommonJS import | CommonJS import | Logs only |
| Snapshot | `saveComponent` | CommonJS import | CommonJS import | Writes a file and invokes the gallery |

## Animation method reference

| Method | Signature | Return or callback |
|---|---|---|
| `play` | `play(viewOrViews, callback?)` | `undefined`; callback once per view |
| `toggle` | `toggle(viewOrViews, callback?)` | Same function and result as `play` |
| `apply` | `apply(viewOrViews, callback?)` | `undefined`; synchronous callback once per view |
| `open` | `open(viewOrViews, callback?)` | `undefined`; callback once per view |
| `close` | `close(viewOrViews, callback?)` | `undefined`; callback once per view |
| `draggable` | `draggable(viewOrViews)` | `undefined` |
| `undraggable` | `undraggable(viewOrViews)` | `undefined` |
| `detectCollisions` | `detectCollisions(views, dragCallback?, dropCallback?)` | `undefined` |
| `sequence` | `sequence(viewOrViews, callback?)` | `undefined`; callback once after the final view |
| `swap` | `swap(view1, view2)` | `undefined` |
| `pulse` | `pulse(view, count = 1)` | `undefined` |
| `shake` | `shake(view, intensity = 10)` | `undefined` |
| `snapTo` | `snapTo(view, targets)` | Selected target, `null`, or `undefined` for a missing source |
| `reorder` | `reorder(views, newOrder)` | `undefined` |
| `transition` | `transition(viewOrViews, layouts)` | `undefined` |

The state-method callback contains `type`, `bubbles`, `cancelBubble`, `action`, `state`, `id`, `targetId`, `index`, `total`, and `getTarget()`. `detectCollisions()` uses different callbacks: the hover callback receives `(source, targetOrNull)`, while the drop callback receives `(source, target)` only when a target was found.

## Native animation objects

Use native `Ti.UI.Animation` properties directly:

`Resources/app.js`
```js
const { createAnimation } = require('lib/purgetss.ui')

const motion = createAnimation({
  duration: 240,
  delay: 40,
  curve: Ti.UI.ANIMATION_CURVE_EASE_IN_OUT,
  repeat: 1,
  autoreverse: false,
  top: 24,
  left: 32,
  width: 180,
  height: 96,
  opacity: 0.8,
  backgroundColor: '#2563eb',
  scale: 1.05,
  rotate: 4
})
```

Top-level `scale`, `rotate`, and `anchorPoint` values are converted to a `Ti.UI.Matrix2D`. The same conversion is made for `open` and `close` states. Consult Titanium's [Animation](https://titaniumsdk.com/api/titanium/ui/animation.html) and [Matrix2D](https://titaniumsdk.com/api/titanium/ui/matrix2d.html) references for the native property contracts.

> ⚠️ **CAUTION**
>
> Anchor points are platform-specific
> Titanium documents `anchorPoint` on `Ti.UI.Animation` for Android and on `Ti.UI.View` for iOS. Test pivot-dependent motion on both platforms. In Classic, set the view's iOS anchor point before animating and pass the Android animation anchor point only inside a platform guard.


`Resources/app.js`
```js
const { createAnimation } = require('lib/purgetss.ui')

const pivot = { x: 0, y: 0.5 }
const properties = { duration: 220, rotate: 12 }
const isIOS = ['iphone', 'ipad'].includes(Ti.Platform.osname)

if (isIOS) card.anchorPoint = pivot
if (Ti.Platform.osname === 'android') properties.anchorPoint = pivot

const pivotMotion = createAnimation(properties)
```

## States and child animations

`Resources/app.js`
```js
const { createAnimation } = require('lib/purgetss.ui')

const panelMotion = createAnimation({
  duration: 220,
  animationProperties: {
    open: { opacity: 1, scale: 1 },
    close: { opacity: 0, scale: 0.94 },
    complete: { borderColor: '#22c55e' },
    children: { duration: 160 }
  }
})

titleLabel.animationProperties = {
  child: { delay: 40 },
  open: { opacity: 1, top: 16 },
  close: { opacity: 0, top: 24 },
  complete: { color: '#22c55e' }
}

panelMotion.open(panel)
```

Child properties are merged in this order: the animation's `children` object, the child's `child` object, and finally the child's active `open`, `close`, or `complete` object.

## Drag and drop objects

`Resources/app.js`
```js
const { createAnimation } = require('lib/purgetss.ui')

const dragMotion = createAnimation({
  duration: 160,
  bounds: { top: 12, right: 12, bottom: 12, left: 12 },
  draggingType: 'animate',
  draggable: {
    drag: { opacity: 0.7, scale: 1.04 },
    drop: { opacity: 1, scale: 1 }
  },
  animationProperties: {
    keepZIndex: true,
    snap: { back: true, center: true }
  }
})

piece.bounds = { bottom: 40 }
piece.constraint = 'horizontal'
piece.draggingType = 'apply'
piece.draggable = {
  drag: { opacity: 0.5 },
  drop: { opacity: 1 }
}

dragMotion.draggable(piece)
dragMotion.detectCollisions([piece, target], (source, hovered) => {
  target.borderColor = hovered === target ? '#22c55e' : 'transparent'
}, (source, droppedOn) => {
  Ti.API.info(`${source.id} dropped on ${droppedOn.id}`)
})
```

`view.bounds` overrides individual values from the animation's `bounds`. Constraints are read from `view.constraint`, while `draggingType` and `draggable.drag/drop` can be global or per-view. The module forwards every property supplied in `drag` and `drop`; it does not filter size, transform, or anchor properties.

`keepZIndex` prevents promotion on touch start. It does not preserve the values that existed before `draggable([view1, view2])`: array registration assigns each view its array index. Register views one at a time if their initial z-index values must remain unchanged.

Collision detection uses the dragged view's center point. `rect` and coordinate conversion require the views to be attached and laid out, so initialize the playground after `open` or `postlayout`.

## Lifecycle and cleanup

`Resources/app.js`
```js
const { createAnimation } = require('lib/purgetss.ui')

const dragMotion = createAnimation({
  animationProperties: { snap: { back: true } }
})
let dragReady = false

function enableDrag() {
  if (dragReady) return
  dragReady = true
  dragMotion.draggable(piece)
}

function disposeWindow() {
  dragMotion.undraggable(piece)
  window.removeEventListener('open', enableDrag)
  window.removeEventListener('close', disposeWindow)
  dragReady = false
}

window.addEventListener('open', enableDrag)
window.addEventListener('close', disposeWindow)
```

Do not call `draggable()` repeatedly for the same view: the current module does not deduplicate registrations. `undraggable()` removes its touch and orientation listeners and most private drag state, but the current release leaves the private `_wasDragged` flag on the view. Release your animation, view, and callback references after closing a window so collision callbacks cannot keep an inactive UI alive.

## Runtime utilities

`Resources/app.js`
```js
const {
  deviceInfo,
  saveComponent
} = require('lib/purgetss.ui')

deviceInfo()

function savePreview() {
  if (!Ti.Media.hasPhotoGalleryPermissions()) {
    Ti.Media.requestPhotoGalleryPermissions((event) => {
      if (event.success) savePreview()
    })
    return
  }

  saveComponent({
    source: preview,
    directory: Ti.Filesystem.applicationDataDirectory
  })
}
```

`deviceInfo()` logs platform and display values and returns `undefined`. It includes `xdpi` and `ydpi` only on Android. Its tablet flag recognizes iPad only, so an Android tablet is currently logged as `isTablet: false` and `isHandheld: true`.

`saveComponent()` renders `source.toImage()`, creates an MD5-based PNG filename, writes it to `directory` (or `Ti.Filesystem.tempDirectory`), and then invokes `Ti.Media.saveToPhotoGallery()`. It also returns `undefined` and exposes no completion or error callback. Check/request gallery authorization with [Ti.Media](https://titaniumsdk.com/api/titanium/media.html) before calling it; on iOS, declare `NSPhotoLibraryAddUsageDescription` as required by Titanium.

## Platform notes

- `transition()` preserves the last transform when hiding a view on iOS. On Android it resets transform, translation, rotation, and scale before the next fade-in to avoid animator conflicts.
- Android drag consolidates `translation`, `rotate`, `scale`, and the equivalent matrix on touch end. It does not rewrite the position as only `top`/`left`.
- Animated `zIndex` is documented differently across Titanium platforms. Use a composite layout where stacking order matters and verify the result on both targets.
- On Mac Catalyst, give parents of transitioned views fixed dimensions rather than `Ti.UI.FILL`; a resizable parent can distort rotated matrices.
