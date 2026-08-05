# Additional methods

These methods add sequential animations, position helpers, and feedback effects to the Animation module.

## The `sequence` method

Animates views one after another. Unlike `play(array)` which runs all animations in parallel, `sequence` waits for each view to complete before starting the next.

```javascript
$.myAnimation.sequence(views, callback)
```

### Parameters

| Parameter  | Type       | Description                                       |
| ---------- | ---------- | ------------------------------------------------- |
| `views`    | View/Array | The view or array of views to animate             |
| `callback` | Function   | Optional. Fires once after the last view finishes |

### How it works

- The `open`/`close` state is toggled once for the entire sequence
- Each view fully completes its animation before the next starts
- The callback receives the same enriched event object as `play`. See [Callback event object](./2-the-play-method.md#callback-event-object) for details

### Sequence example

`index.xml`
```xml
<Alloy>
  <Window class="keep-screen-on">
    <Animation id="fadeIn" module="purgetss.ui" class="open:opacity-100 close:opacity-0 duration-300" />

    <View class="vertical mt-16">
      <Label id="title" class="opacity-0 text-2xl font-bold" text="Welcome" />
      <Label id="subtitle" class="opacity-0 mt-2 text-lg text-gray-500" text="To the app" />
      <Button id="cta" class="opacity-0 mt-4 rounded bg-blue-500 text-white" title="Get Started" />
    </View>
  </Window>
</Alloy>
```

`index.js`
```javascript
$.index.open()

$.fadeIn.sequence([$.title, $.subtitle, $.cta], () => {
  console.log('All views animated in sequence')
})
```

Each view fades in one after another for a staggered reveal.

### Real-world use case: onboarding screen

Show app features one at a time as the user lands on the welcome screen:

`onboarding.xml`
```xml
<Alloy>
  <Window class="bg-slate-900">
    <Animation id="revealAnim" module="purgetss.ui" class="open:opacity-100 close:opacity-0 duration-300" />

    <View class="mx-4 mt-4 h-(300) w-screen rounded-xl bg-slate-800/50">
      <View id="logo" class="opacity-0 mt-8 wh-16 rounded-xl bg-blue-500 mx-auto">
        <Label class="touch-enabled-false text-2xl text-white" text="M" />
      </View>
      <Label id="headline" class="opacity-0 mt-(110) mx-8 text-xl font-bold text-center text-white" text="Welcome to MyApp" />
      <Label id="subtitle" class="opacity-0 mt-(150) mx-8 text-sm text-center text-slate-400" text="Discover features designed for you" />
      <Button id="startBtn" class="opacity-0 mt-(200) mx-16 h-10 w-screen rounded-lg bg-blue-500 text-sm font-semibold text-white" title="Get Started" />
    </View>

    <Button class="mr-2 h-8 w-28 rounded-lg bg-amber-500 text-xs font-semibold text-white" title="Reveal" onClick="doReveal" />
    <Button class="h-8 w-28 rounded-lg bg-teal-500/80 text-xs font-semibold text-white" title="Reset" onClick="doReset" />
  </Window>
</Alloy>
```

`onboarding.js`
```javascript
const views = [$.logo, $.headline, $.subtitle, $.startBtn]

function doReveal() {
  // Force open state first, then sequence
  $.revealAnim.sequence(views, (e) => {
    $.status.applyProperties({ text: `sequence done - ${e.index + 1}/${e.total} views revealed` })
  })
}

function doReset() {
  // Use close to properly reset the Animation state
  $.revealAnim.close(views, () => {
    $.status.applyProperties({ text: 'Reset - tap Reveal to start again' })
  })
}
```

`sequence` toggles open/close internally, so use `close()` to force the state back before revealing again.

## The `swap` method

Animates two views exchanging positions.

```javascript
$.myAnimation.swap(view1, view2)
```

### Parameters

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| `view1`   | View | First view  |
| `view2`   | View | Second view |

### How it works

- Inherits `duration`, `delay`, and `curve` from the Animation object's classes
- Falls back to 200ms duration, 0ms delay, and `EASE_IN_OUT` curve if not set
- Handles iOS transform reset (`Ti.UI.createMatrix2D()`) automatically during the swap
- Temporarily elevates the z-index of both views so the animation renders above siblings
- Restores original z-index order after the animation completes
- Updates internal `_originLeft`/`_originTop` for subsequent drag operations
- Automatic position normalization: views do not need explicit `top`/`left` properties. Views positioned with margins (`ml-`, `mr-`), `right`, or centered layout are resolved through `view.rect` and normalized to `top`/`left` on first swap
- Bounce-back safety: if either view has a bounce-back animation in progress when `swap` is called, it completes before the swap starts. This prevents both views from overlapping at the same position

### Swap example

`index.xml`
```xml
<Alloy>
  <Window class="keep-screen-on">
    <Animation id="myAnimation" module="purgetss.ui" />

    <View id="cardA" class="mt-8 ml-8 wh-24 rounded-lg bg-red-500" />
    <View id="cardB" class="mt-8 ml-40 wh-24 rounded-lg bg-blue-500" />

    <Button class="mb-8 rounded bg-purple-500 text-white" title="Swap" onClick="doSwap" />
  </Window>
</Alloy>
```

`index.js`
```javascript
$.index.open()

$.myAnimation.draggable([$.cardA, $.cardB])

function doSwap() {
  $.myAnimation.swap($.cardA, $.cardB)
}
```

### Real-world use case: memory card game

Players tap two cards to flip them. If they don't match, swap them back:

`game.js`
```javascript
let firstCard = null

function onCardTap({ source }) {
  const card = source
  $.flipAnim.open(card)  // Flip/reveal the card

  if (!firstCard) {
    firstCard = card
    return
  }

  if (firstCard.valor === card.valor) {
    // Match! Leave both revealed
    firstCard = null
  } else {
    // No match: swap positions and flip back
    $.gameAnim.swap(firstCard, card)
    setTimeout(() => {
      $.flipAnim.close([firstCard, card])
      firstCard = null
    }, 500)
  }
}
```

## The `pulse` method

Scales a view up and back down, useful for notifications or badges. Uses `autoreverse` + `repeat` natively.

```javascript
$.myAnimation.pulse(view, count)
```

### Parameters

| Parameter | Type   | Default | Description       |
| --------- | ------ | ------- | ----------------- |
| `view`    | View   | N/A     | The view to pulse |
| `count`   | Number | `1`     | Number of pulses  |

### How it works

- Scale value is inherited from the Animation object's `scale` class (e.g., `scale-(1.3)`). If not set, defaults to 1.2x
- Uses `autoreverse: true` and `repeat: count` natively, with no timers or callbacks
- Duration is inherited from the Animation object. This is the time for one scale-up; the full cycle takes twice as long
- Always uses `EASE_IN_OUT` curve for natural motion
- Resets transform to identity on completion

### Pulse example

`badge-pulse.xml`
```xml
<Alloy>
  <Window class="bg-slate-900">
    <Animation id="pulseAnim" module="purgetss.ui" class="scale-(1.3) autoreverse duration-150" />

    <!-- Simulated app icon with badge -->
    <View class="wh-24">
      <View class="wh-20 clip-disabled rounded-xl bg-blue-500">
        <Label class="touch-enabled-false text-2xl text-white" text="@" />
      </View>

      <View id="badge" class="wh-6 rounded-full-6 right-1 top-1 bg-red-500">
        <Label class="touch-enabled-false text-center text-xs text-white" text="3" />
      </View>
    </View>
  </Window>
</Alloy>
```

`badge-pulse.js`
```javascript
function doPulse() {
  $.pulseAnim.pulse($.badge)
}

function doPulse3() {
  $.pulseAnim.pulse($.badge, 3)
}
```

### Real-world use case: notification badge

Pulse the badge when a new notification arrives:

```javascript
Alloy.Events.on('newNotification', () => {
  $.pulseAnim.pulse($.badge)
})
```

The scale, duration, and easing come from the `<Animation />` object. The `count` parameter is the only thing you pass per call.

## The `shake` method

Shakes a view left and right around its original position using `autoreverse` + `repeat`. Good for form validation errors.

```javascript
$.myAnimation.shake(view, intensity)
```

### Parameters

| Parameter   | Type   | Default | Description                       |
| ----------- | ------ | ------- | --------------------------------- |
| `view`      | View   | N/A     | The view to shake                 |
| `intensity` | Number | `10`    | Horizontal displacement in pixels |

### Inherited properties

- `duration`: inherited from the Animation object; divided by 6 internally for each shake cycle. Fallback: 400ms
- `delay`: inherited from the Animation object; applied before the shake starts
- `curve`: always uses `EASE_IN_OUT` internally. It is not inherited because the shake effect depends on that curve
- `autoreverse` and `repeat`: always `true` and `3` internally. They are not inherited because the shake effect needs fixed values

### Shake example

`index.js`
```javascript
// Default shake
$.myAnimation.shake($.loginButton)

// Subtle, fast shake
$.myAnimation.shake($.inputField, 6)

// Strong shake for emphasis
$.myAnimation.shake($.errorLabel, 20)
```

The view shakes left and right around its original position and returns to its starting point automatically.

### Real-world use case: login validation

Shake the input field when the user enters invalid credentials:

`login-shake.xml`
```xml
<Alloy>
  <Window class="bg-slate-900">
    <Animation id="errorAnim" module="purgetss.ui" class="duration-300" />

    <View class="vertical mt-4 h-auto w-screen">
      <Label class="mx-4 mb-1 text-sm font-semibold text-slate-400" text="Email" />
      <TextField id="emailField" class="mx-4 mb-4 h-10 w-screen rounded-lg border-2 border-slate-700 bg-slate-800 px-3 text-sm text-white" hintText="you@example.com" />

      <Label class="mx-4 mb-1 text-sm font-semibold text-slate-400" text="Password" />
      <TextField id="passwordField" class="mx-4 mb-4 h-10 w-screen rounded-lg border-2 border-slate-700 bg-slate-800 px-3 text-sm text-white" hintText="********" passwordMask="true" />

      <Button class="mx-4 mt-2 h-10 w-screen rounded-lg bg-blue-500 text-sm font-semibold text-white" title="Sign In" onClick="doLogin" />
    </View>
  </Window>
</Alloy>
```

`login-shake.js`
```javascript
function doLogin() {
  // Reset borders
  $.emailField.applyProperties({ borderColor: '#334155' })
  $.passwordField.applyProperties({ borderColor: '#334155' })

  if (!$.emailField.value) {
    $.errorAnim.shake($.emailField, 5)
    $.emailField.applyProperties({ borderColor: '#ef4444' })
    $.status.applyProperties({ text: 'shake(emailField) - empty email' })
    return
  }

  if (!$.passwordField.value) {
    $.errorAnim.shake($.passwordField, 5)
    $.passwordField.applyProperties({ borderColor: '#ef4444' })
    $.status.applyProperties({ text: 'shake(passwordField) - empty password' })
    return
  }

  $.status.applyProperties({ text: 'Login successful!' })
}
```

`shake(view, 5)` gives a subtle 5px oscillation. The red border adds visual feedback. Leave fields empty and tap Sign In to see the effect.

## The `snapTo` method

Snaps a view to the nearest target by center-to-center distance. Useful after dragging to align a view with predefined slots or positions.

```javascript
const matched = $.myAnimation.snapTo(view, targets)
```

### Parameters

| Parameter | Type       | Description               |
| --------- | ---------- | ------------------------- |
| `view`    | View       | The view to snap          |
| `targets` | View/Array | Target view(s) to snap to |

### Return value

Returns the matched target view, or `null` if no targets are provided.

### How it works

- Inherits `duration`, `delay`, and `curve` from the Animation object's classes
- Falls back to 200ms duration, 0ms delay, and `EASE_IN_OUT` curve if not set
- Calculates the center point of the dragged view and each target
- Finds the closest target by Euclidean distance
- Animates the view to the target's position
- Handles iOS transform reset automatically
- Updates internal `_originLeft`/`_originTop` for subsequent drag operations
- Automatic position normalization: target views do not need explicit `top`/`left` properties

### SnapTo example

`index.xml`
```xml
<Alloy>
  <Window class="keep-screen-on">
    <Animation id="myAnimation" module="purgetss.ui" />

    <View id="slot1" class="mt-8 ml-8 wh-24 rounded-lg border-2 border-dashed border-gray-400" />
    <View id="slot2" class="mt-8 ml-40 wh-24 rounded-lg border-2 border-dashed border-gray-400" />
    <View id="slot3" class="mt-8 ml-72 wh-24 rounded-lg border-2 border-dashed border-gray-400" />

    <View id="card" class="mt-48 wh-20 rounded-lg bg-blue-500" />
  </Window>
</Alloy>
```

`index.js`
```javascript
$.index.open()

const slots = [$.slot1, $.slot2, $.slot3]
$.myAnimation.draggable($.card)

// After dragging ends, snap to nearest slot
$.card.addEventListener('touchend', () => {
  const matched = $.myAnimation.snapTo($.card, slots)
  if (matched) {
    console.log('Snapped to:', matched.id)
  }
})
```

### Real-world use case: puzzle game with collision detection

Drag puzzle pieces onto their matching slots. Correct matches lock the piece in place:

`puzzle.xml`
```xml
<Alloy>
  <Window class="bg-gray-100">
    <Animation id="puzzleAnim" module="purgetss.ui" class="snap-center snap-back duration-150" />

    <!-- Slots (drop zones) -->
    <View id="slotA" class="mt-8 ml-8 wh-20 rounded-lg border-2 border-dashed border-gray-400" valor="A" />
    <View id="slotB" class="mt-8 ml-40 wh-20 rounded-lg border-2 border-dashed border-gray-400" valor="B" />

    <!-- Pieces (draggable) -->
    <View id="pieceA" class="mt-48 ml-8 wh-16 rounded-lg bg-blue-500" valor="A">
      <Label class="text-center text-white font-bold touch-enabled-false" text="A" />
    </View>
    <View id="pieceB" class="mt-48 ml-40 wh-16 rounded-lg bg-red-500" valor="B">
      <Label class="text-center text-white font-bold touch-enabled-false" text="B" />
    </View>
  </Window>
</Alloy>
```

`puzzle.js`
```javascript
const pieces = [$.pieceA, $.pieceB]
const slots = [$.slotA, $.slotB]

$.puzzleAnim.draggable(pieces)
$.puzzleAnim.detectCollisions(pieces.concat(slots),
  null,  // no dragCB needed
  function dropCB(source, target) {
    if (source.valor === target.valor) {
      // Correct match: lock the piece
      $.puzzleAnim.undraggable(source)
      source.applyProperties({ opacity: 0.6 })
    }
    // Wrong match: snap-back handles it automatically
  }
)
```

`snap-center` auto-centers the piece on the slot. `snap-back` returns it to its origin if dropped outside any slot. `undraggable` locks it when correct.

## The `reorder` method

Animates an array of views to new positions based on an index mapping. All views animate simultaneously.

```javascript
$.myAnimation.reorder(views, newOrder)
```

### Parameters

| Parameter  | Type  | Description                                            |
| ---------- | ----- | ------------------------------------------------------ |
| `views`    | Array | Array of views to reorder                              |
| `newOrder` | Array | Index array mapping current positions to new positions |

### How `newOrder` works

The `newOrder` array maps each view's current index to the position it should move to. For example, with three views:

- `[2, 1, 0]`: reverses the order; first goes to last, last goes to first
- `[1, 2, 0]`: rotates all positions forward
- `[0, 1, 2]`: no change

The length of `newOrder` must match the length of `views`.

Inherits `duration`, `delay`, and `curve` from the Animation object's classes. Falls back to 200ms, 0ms delay, and `EASE_IN_OUT` curve.

Automatic position normalization: views do not need explicit `top`/`left` properties. Views positioned with margins or `right` are resolved through `view.rect` and normalized on first reorder.

### Reorder example

`index.xml`
```xml
<Alloy>
  <Window class="keep-screen-on">
    <Animation id="myAnimation" module="purgetss.ui" />

    <View class="horizontal mt-16">
      <View id="card1" class="mx-2 wh-20 rounded-lg bg-red-500">
        <Label class="text-center text-white" text="1" />
      </View>
      <View id="card2" class="mx-2 wh-20 rounded-lg bg-green-500">
        <Label class="text-center text-white" text="2" />
      </View>
      <View id="card3" class="mx-2 wh-20 rounded-lg bg-blue-500">
        <Label class="text-center text-white" text="3" />
      </View>
    </View>

    <Button class="mt-32 rounded bg-purple-500 text-white" title="Reverse Order" onClick="doReorder" />
  </Window>
</Alloy>
```

`index.js`
```javascript
$.index.open()

const cards = [$.card1, $.card2, $.card3]

function doReorder() {
  // Reverse: card1 → position of card3, card3 → position of card1
  $.myAnimation.reorder(cards, [2, 1, 0])
}
```

### Real-world use case: sort by priority

Let the user sort items by tapping a button. For example, reorder tasks by priority:

`tasks.js`
```javascript
const tasks = [$.taskHigh, $.taskMedium, $.taskLow]

function sortByPriority() {
  // Move high to first, low to last
  $.taskAnim.reorder(tasks, [0, 1, 2])
}

function sortByRecent() {
  // Reverse: most recent (low) first
  $.taskAnim.reorder(tasks, [2, 1, 0])
}

function shuffle() {
  // Random rotation
  $.taskAnim.reorder(tasks, [1, 2, 0])
}
```

All views animate simultaneously to their new positions.

## The `transition` method

Animates multiple views simultaneously to layout positions defined by `Matrix2D.translate().rotate().scale()`. Use it to switch between layout presets like fan-out, carousel, stack, or cascade.

```javascript
$.myAnimation.transition(views, layouts)
```

### Parameters

| Parameter | Type       | Description                                                              |
| --------- | ---------- | ------------------------------------------------------------------------ |
| `views`   | View/Array | The view or array of views to animate                                    |
| `layouts` | Array      | Array of layout objects; positional mapping (`layouts[i]` to `views[i]`) |

### Layout object properties

| Property      | Type   | Default      | Description                                                       |
| ------------- | ------ | ------------ | ----------------------------------------------------------------- |
| `translation` | Object | `{x:0, y:0}` | Translation offset `{x, y}` in pixels (matches TiDesigner format) |
| `rotate`      | Number | `0`          | Rotation in degrees                                               |
| `scale`       | Number | `1`          | Scale factor                                                      |
| `zIndex`      | Number | N/A          | Applied synchronously before animation                            |
| `width`       | Number | N/A          | Optional width change                                             |
| `height`      | Number | N/A          | Optional height change                                            |
| `opacity`     | Number | N/A          | Optional opacity change                                           |

### How it works

- Inherits `duration`, `delay`, and `curve` from the Animation object's classes
- Each view gets a single `Ti.UI.createAnimation()` call with a combined `Matrix2D` transform, avoiding concurrent animation conflicts on Android
- All animations launch simultaneously (`forEach` without await)
- The `transform` is persisted via `applyProperties` in the animation callback
- `zIndex` is applied synchronously before the animation starts

### Mismatched lengths

- More views than layouts: extra views fade out with `opacity: 0`
- More layouts than views: extra layout entries are ignored
- When a faded-out view receives a layout entry again, it fades back in as part of the same animation, with no separate animation call

### Reusable presets

Layouts are plain arrays, so you can define them once and apply to any group of views:

```javascript
const fanOut = [
  { translation: { x: -120, y: 20 }, rotate: -15, scale: 0.8, zIndex: 1 },
  { translation: { x: 0, y: 0 }, rotate: 0, scale: 1, zIndex: 3 },
  { translation: { x: 120, y: 20 }, rotate: 15, scale: 0.8, zIndex: 2 }
]

// Same preset, different view groups
$.anim.transition(screensA, fanOut)
$.anim.transition(screensB, fanOut)
```

### Transition example

`index.xml`
```xml
<Alloy>
  <Window class="keep-screen-on">
    <Animation id="anim" module="purgetss.ui" class="duration-200" />

    <!-- Fixed dimensions to avoid Mac Catalyst re-layout bug with rotated transforms -->
    <View id="stage" class="wh-(400) mt-4 rounded-xl bg-slate-800/50">
      <View id="screen1" class="wh-(120) rounded-xl bg-red-500" />
      <View id="screen2" class="wh-(120) rounded-xl bg-blue-500" />
      <View id="screen3" class="wh-(120) rounded-xl bg-green-500" />
    </View>

    <Button class="mt-8 rounded bg-amber-500 text-white" title="Fan Out" onClick="doFanOut" />
    <Button class="mt-2 rounded bg-purple-500 text-white" title="Stack" onClick="doStack" />
    <Button class="mt-2 rounded bg-teal-500 text-white" title="Reset" onClick="doReset" />
  </Window>
</Alloy>
```

`index.js`
```javascript
const views = [$.screen1, $.screen2, $.screen3]

const fanOut = [
  { translation: { x: -120, y: 20 }, rotate: -15, scale: 0.8, zIndex: 1 },
  { translation: { x: 0, y: 0 }, rotate: 0, scale: 1, zIndex: 3 },
  { translation: { x: 120, y: 20 }, rotate: 15, scale: 0.8, zIndex: 2 }
]

const stack = [
  { translation: { x: -8, y: 16 }, rotate: 0, scale: 0.9, zIndex: 1 },
  { translation: { x: 0, y: 8 }, rotate: 0, scale: 0.95, zIndex: 2 },
  { translation: { x: 8, y: 0 }, rotate: 0, scale: 1, zIndex: 3 }
]

const reset = [
  { translation: { x: 0, y: 0 }, rotate: 0, scale: 1, zIndex: 1 },
  { translation: { x: 0, y: 0 }, rotate: 0, scale: 1, zIndex: 2 },
  { translation: { x: 0, y: 0 }, rotate: 0, scale: 1, zIndex: 3 }
]

function doFanOut() { $.anim.transition(views, fanOut) }
function doStack() { $.anim.transition(views, stack) }
function doReset() { $.anim.transition(views, reset) }
```

### Real-world use case: photo gallery with layout presets

A gallery where users switch between different viewing modes:

`gallery.xml`
```xml
<Alloy>
  <Window class="bg-gray-900">
    <Animation id="galleryAnim" module="purgetss.ui" class="keep-z-index duration-150" />

    <View id="stage" class="wh-(400) mx-auto mt-8">
      <ImageView id="photo1" class="w-(120) h-(160) rounded-lg" image="/images/photo1.jpg" />
      <ImageView id="photo2" class="w-(120) h-(160) rounded-lg" image="/images/photo2.jpg" />
      <ImageView id="photo3" class="w-(120) h-(160) rounded-lg" image="/images/photo3.jpg" />
    </View>

    <View class="horizontal mx-8 mt-4">
      <Button class="mr-2 rounded-lg bg-blue-500 text-white" title="Fan" onClick="doFan" />
      <Button class="mr-2 rounded-lg bg-blue-500 text-white" title="Stack" onClick="doStack" />
      <Button class="rounded-lg bg-blue-500 text-white" title="Showcase" onClick="doShowcase" />
    </View>
  </Window>
</Alloy>
```

`gallery.js`
```javascript
const photos = [$.photo1, $.photo2, $.photo3]

// Presets use the same format as TiDesigner mockup layouts
const fan = [
  { zIndex: 3, translation: { x: 0, y: 0 }, rotate: 0, scale: 0.8 },
  { zIndex: 2, translation: { x: -63, y: 9 }, rotate: -5, scale: 0.6 },
  { zIndex: 1, translation: { x: 63, y: 9 }, rotate: 5, scale: 0.6 }
]

const stack = [
  { zIndex: 3, translation: { x: 8, y: 0 }, rotate: 0, scale: 1 },
  { zIndex: 2, translation: { x: 0, y: 8 }, rotate: 0, scale: 0.95 },
  { zIndex: 1, translation: { x: -8, y: 16 }, rotate: 0, scale: 0.9 }
]

const showcase = [
  { zIndex: 3, translation: { x: 0, y: 50 }, rotate: 0, scale: 1.0 },
  { zIndex: 2, translation: { x: -80, y: 60 }, rotate: -15, scale: 0.8 },
  { zIndex: 1, translation: { x: 80, y: 60 }, rotate: 15, scale: 0.8 }
]

function doFan() { $.galleryAnim.transition(photos, fan) }
function doStack() { $.galleryAnim.transition(photos, stack) }
function doShowcase() { $.galleryAnim.transition(photos, showcase) }

// Enable drag. Photos keep their rotation and scale while dragging
$.galleryAnim.draggable(photos)
```

`keep-z-index` preserves the layout order during drag. Presets are plain arrays, so the same layout works with any group of views.

### Mac Catalyst note

On Mac Catalyst, parent containers of transitioned views should use fixed dimensions, not `Ti.UI.FILL`. Resizable containers trigger a UIKit re-layout that distorts views with rotated `Matrix2D` transforms. This does not affect iOS or Android.

---

## Property inheritance from the Animation object

All methods in the Animation module inherit properties from the `<Animation>` object's classes. You configure animation behavior in XML and it applies to every method call.

### How it works

When you declare an Animation object with utility classes:

```xml
<Animation id="myAnim" module="purgetss.ui" class="duration-150 delay-100 curve-animation-ease-out" />
```

The parsed properties (`duration: 150`, `delay: 100`, `curve: EASE_OUT`) are stored in the internal `args` object. Each method reads from `args` as its default values.

### Inheritance matrix

| Property      | `play` / `toggle` | `open` / `close` | `apply` | `sequence` | `swap` | `reorder` | `snapTo` | `shake` | `pulse` | `transition` |
| ------------- | :---------------: | :--------------: | :-----: | :--------: | :----: | :-------: | :------: | :-----: | :-----: | :----------: |
| `duration`    | Yes | Yes | N/A | Yes | Yes | Yes | Yes | Yes, divided by 6 | Yes | Yes |
| `delay`       | Yes | Yes | N/A | Yes | Yes | Yes | Yes | Yes | Yes | Yes |
| `curve`       | Yes | Yes | N/A | Yes | Yes | Yes | Yes | Fixed | Fixed | Yes |
| `autoreverse` | Yes | Yes | N/A | Yes | N/A | N/A | N/A | Fixed | Fixed | N/A |
| `repeat`      | Yes | Yes | N/A | Yes | N/A | N/A | N/A | Fixed | Parameter | N/A |

- Yes means inherited from the Animation object's classes.
- N/A means the property does not apply to this method.
- Fixed means the method uses internal fixed values. `shake` always uses `autoreverse: true`, `repeat: 3`, and `curve: EASE_IN_OUT`; `pulse` always uses `autoreverse: true` and `curve: EASE_IN_OUT`.
- Parameter means a method parameter controls the value. For `pulse`, `count` sets the repeat value.

### Fallback defaults

When a property is not set on the Animation object and no explicit parameter is passed:

| Property   | `swap` / `reorder` / `snapTo` |        `shake`        |
| ---------- | :---------------------------: | :-------------------: |
| `duration` |             200ms             |         400ms         |
| `delay`    |              0ms              |          0ms          |
| `curve`    |         `EASE_IN_OUT`         | `EASE_IN_OUT` (fixed) |

### Explicit parameters take precedence

An explicit value passed as a method parameter always overrides the inherited value:

```javascript
// All timing controlled by the Animation object's classes
$.myAnim.swap($.card1, $.card2)
$.myAnim.reorder(cards, [2, 0, 1])
$.myAnim.shake($.errorField, 20)
$.myAnim.snapTo($.card, targets)
$.myAnim.transition(views, fanOutLayout)
```
