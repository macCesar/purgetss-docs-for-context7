# Additional methods

These helpers implement serial motion, position changes, feedback effects, and reusable layouts. They receive views directly; the difference between Alloy and Classic is how those views and animation properties are created.

:::note Timing
`swap`, `snapTo`, `reorder`, and `transition` forward the animation object's native properties but do not define a 200 ms fallback. Set `duration`, `delay`, and `curve` on the `<Animation>` element or `createAnimation()` object when you need visible, predictable motion.
:::

## The `sequence` method

`sequence(views, callback)` animates one view at a time. It toggles the open/closed state once for the entire sequence and calls the optional callback only after the last view completes. An empty array produces no callback.

### Alloy

`app/views/index.xml`
```xml
<Alloy>
  <Window>
    <Animation id="reveal" module="purgetss.ui" class="open:opacity-100 close:opacity-0 duration-200" />
    <View class="vertical">
      <Label id="title" class="opacity-0" text="Welcome" />
      <Label id="subtitle" class="opacity-0" text="Motion in sequence" />
      <Button id="action" class="opacity-0" title="Continue" />
    </View>
  </Window>
</Alloy>
```

`app/controllers/index.js`
```js
$.reveal.sequence([$.title, $.subtitle, $.action], () => {
  Ti.API.info('Sequence complete')
})
```

### Titanium Classic

`Resources/app.js`
```js
const { createAnimation } = require('lib/purgetss.ui')

const reveal = createAnimation({
  duration: 200,
  animationProperties: {
    open: { opacity: 1 },
    close: { opacity: 0 }
  }
})

reveal.sequence([title, subtitle, action], () => {
  Ti.API.info('Sequence complete')
})
```

Use `play(array)` for parallel animation. Its configured delay is increased cumulatively for each view; `sequence(array)` waits for each native completion event.

## The `swap` method

`swap(view1, view2)` exchanges two rendered positions. It temporarily promotes both views, resets the source transform, persists their new `top`/`left` values, and updates the private origin used by drag helpers. Its z-index restoration follows the current draggable registry order; it does not restore arbitrary z-index values captured before the swap.

### Alloy

`app/views/index.xml`
```xml
<Alloy>
  <Window>
    <Animation id="motion" module="purgetss.ui" class="duration-200 ease-in-out" />
    <View id="cardA" class="left-8 wh-24 bg-red-500" />
    <View id="cardB" class="right-8 wh-24 bg-blue-500" />
  </Window>
</Alloy>
```

`app/controllers/index.js`
```js
$.motion.swap($.cardA, $.cardB)
```

### Titanium Classic

`Resources/app.js`
```js
const { createAnimation } = require('lib/purgetss.ui')

const motion = createAnimation({
  duration: 200,
  curve: Ti.UI.ANIMATION_CURVE_EASE_IN_OUT
})

window.addEventListener('open', () => motion.swap(cardA, cardB))
```

The method reads `rect`, so call it after layout. It has no callback or return value.

## The `pulse` method

`pulse(view, count = 1)` scales a view up and back. The scale comes from the animation object's transform, or defaults to `1.2`. The method forces `autoreverse: true`, `repeat: count`, and `ANIMATION_CURVE_EASE_IN_OUT`, then resets the transform to identity.

### Alloy

`app/views/index.xml`
```xml
<Alloy>
  <Window>
    <Animation id="pulseMotion" module="purgetss.ui" class="scale-(1.3) duration-150" />
    <View id="badge" class="wh-6 rounded-full-6 bg-red-500" />
  </Window>
</Alloy>
```

`app/controllers/index.js`
```js
$.pulseMotion.pulse($.badge, 3)
```

### Titanium Classic

`Resources/app.js`
```js
const { createAnimation } = require('lib/purgetss.ui')

const pulseMotion = createAnimation({ duration: 150, scale: 1.3 })
pulseMotion.pulse(badge, 3)
```

`count` is passed directly as the native `repeat` value. The helper exposes no completion callback.

## The `shake` method

`shake(view, intensity = 10)` gives horizontal error feedback. It starts at `-intensity`, animates to `+intensity`, forces autoreverse, three repeats, and ease-in-out, and finally resets the transform. The per-phase duration is `(duration ?? 400) / 6`, rounded to the nearest millisecond.

### Alloy

`app/views/index.xml`
```xml
<Alloy>
  <Window>
    <Animation id="errorMotion" module="purgetss.ui" class="duration-300" />
    <TextField id="email" />
  </Window>
</Alloy>
```

`app/controllers/index.js`
```js
$.errorMotion.shake($.email, 6)
```

### Titanium Classic

`Resources/app.js`
```js
const { createAnimation } = require('lib/purgetss.ui')

const errorMotion = createAnimation({ duration: 300 })
errorMotion.shake(emailField, 6)
```

The helper has no callback or return value.

## The `snapTo` method

`snapTo(view, targets)` selects the nearest target by center-to-center distance, animates the view to its center, resets its transform, and updates its private origin. It returns the chosen target immediately, or `null` when none can be selected; the return does not mean the animation has completed.

### Alloy

`app/views/index.xml`
```xml
<Alloy>
  <Window>
    <Animation id="snapMotion" module="purgetss.ui" class="duration-180 ease-out" />
    <View id="slotA" class="left-8 wh-24 border-2" />
    <View id="slotB" class="right-8 wh-24 border-2" />
    <View id="piece" class="wh-16 bg-blue-500" />
  </Window>
</Alloy>
```

`app/controllers/index.js`
```js
const selected = $.snapMotion.snapTo($.piece, [$.slotA, $.slotB])
Ti.API.info(selected && selected.id)
```

### Titanium Classic

`Resources/app.js`
```js
const { createAnimation } = require('lib/purgetss.ui')

const snapMotion = createAnimation({
  duration: 180,
  curve: Ti.UI.ANIMATION_CURVE_EASE_OUT
})
const selected = snapMotion.snapTo(piece, [slotA, slotB])
Ti.API.info(selected && selected.id)
```

Views and targets must already be laid out because the calculation uses `rect`. For automatic drag behavior, set `animationProperties.snap.center`; use `snap.back` to return a missed drop to its captured origin.

## The `reorder` method

`reorder(views, newOrder)` moves each view to the captured position at `newOrder[index]`. All movements start in parallel. If both arrays do not have the same length, the method returns without changing the views.

### Alloy

`app/views/index.xml`
```xml
<Alloy>
  <Window>
    <Animation id="orderMotion" module="purgetss.ui" class="duration-200" />
    <View id="first" class="left-8 wh-20 bg-red-500" />
    <View id="second" class="wh-20 bg-blue-500" />
    <View id="third" class="right-8 wh-20 bg-green-500" />
  </Window>
</Alloy>
```

`app/controllers/index.js`
```js
$.orderMotion.reorder([$.first, $.second, $.third], [2, 0, 1])
```

### Titanium Classic

`Resources/app.js`
```js
const { createAnimation } = require('lib/purgetss.ui')

const orderMotion = createAnimation({ duration: 200 })
orderMotion.reorder([first, second, third], [2, 0, 1])
```

The method normalizes the views to `top`/`left` using their current position or `rect`, then resets their transform. It exposes no callback or return value.

## The `transition` method

`transition(views, layouts)` applies one layout object to each matching view. Supported layout fields are:

| Property | Default | Effect |
|---|---|---|
| `translation` | `{ x: 0, y: 0 }` | Matrix translation |
| `rotate` | `0` | Matrix rotation in degrees |
| `scale` | `1` | Matrix scale |
| `zIndex` | unchanged | Assigned before animation |
| `width`, `height` | unchanged | Optional size animation |
| `opacity` | unchanged | Optional opacity animation |

Views without a matching layout fade to opacity `0`, receive `zIndex: 0`, and have touch disabled. If a hidden view later receives a layout, it fades back in and touch is restored. Extra layout entries are ignored.

The completion handler sets `touchEnabled: true` for every view that receives a layout, even if that view was intentionally non-interactive before the transition. Reapply `touchEnabled: false` afterward when needed.

### Alloy

`app/views/index.xml`
```xml
<Alloy>
  <Window>
    <Animation id="layoutMotion" module="purgetss.ui" class="duration-220 ease-in-out" />
    <View id="stage" class="wh-(400)">
      <View id="cardA" class="wh-(120) bg-red-500" />
      <View id="cardB" class="wh-(120) bg-blue-500" />
      <View id="cardC" class="wh-(120) bg-green-500" />
    </View>
  </Window>
</Alloy>
```

`app/controllers/index.js`
```js
const fan = [
  { translation: { x: -120, y: 20 }, rotate: -15, scale: 0.8, opacity: 0.8, zIndex: 1 },
  { translation: { x: 0, y: 0 }, rotate: 0, scale: 1, opacity: 1, zIndex: 3 },
  { translation: { x: 120, y: 20 }, rotate: 15, scale: 0.8, opacity: 0.8, zIndex: 2 }
]

$.layoutMotion.transition([$.cardA, $.cardB, $.cardC], fan)
```

### Titanium Classic

`Resources/app.js`
```js
const { createAnimation } = require('lib/purgetss.ui')

const layoutMotion = createAnimation({
  duration: 220,
  curve: Ti.UI.ANIMATION_CURVE_EASE_IN_OUT
})
const fan = [
  { translation: { x: -120, y: 20 }, rotate: -15, scale: 0.8, opacity: 0.8, zIndex: 1 },
  { translation: { x: 0, y: 0 }, rotate: 0, scale: 1, opacity: 1, zIndex: 3 },
  { translation: { x: 120, y: 20 }, rotate: 15, scale: 0.8, opacity: 0.8, zIndex: 2 }
]

layoutMotion.transition([cardA, cardB, cardC], fan)
```

On iOS, hiding a view preserves its last transform. Android resets transform, translation, rotation, and scale before that view reappears. On Mac Catalyst, use a fixed-size parent instead of `Ti.UI.FILL` for rotated layouts.

## Property inheritance

Most helpers spread the constructor's base object before adding method-specific fields. Native properties such as `duration`, `delay`, `opacity`, or `backgroundColor` may therefore reach a helper even when they are incidental to its main job. Method-specific assignments win when the same key appears later.

| Method | Inherited timing | Forced or computed values |
|---|---|---|
| `sequence` | `duration`, `delay`, `curve`, `repeat`, `autoreverse` | Uses state and child overrides |
| `swap` | Base argument object | Destination position; source identity transform |
| `pulse` | Base argument object | Transform fallback; autoreverse; repeat; curve |
| `shake` | `delay` and other non-overridden fields | Transform; divided duration; autoreverse; repeat; curve |
| `snapTo` | Base argument object | Destination position; identity transform |
| `reorder` | Base argument object | Destination position; identity transform |
| `transition` | Base argument object | Combined layout transform; optional size/opacity |

There is no shared 200 ms fallback. Only `shake` computes a 400 ms fallback before dividing it into six phases, and `pulse` supplies a default scale of `1.2`.

See [Using `purgetss.ui` in Titanium Classic](./2-titanium-classic.md) for the full Alloy/Classic matrix and lifecycle rules.
