# The `open` and `close` methods

Use `open` and `close` to run opening and closing animations based on the `open:` and `close:` modifiers. They do not toggle based on current view state, so you get explicit control.

## `open` method

`open` triggers the opening animation for the specified views and uses properties defined under the `open` modifier.

### Usage

```javascript
$.myAnimation.open(views, callback);
```

- `views`: The view or array of views to animate.
- `callback`: Optional function called when the animation completes.

### Example

`index.xml`
```xml
<Alloy>
  <Window>
    <Animation module="purgetss.ui" id="myAnimation" class="close:opacity-0 open:opacity-100" />

    <View id="myView" class="opacity-0" />
  </Window>
</Alloy>
```

`index.js`
```javascript
$.myAnimation.open($.myView, (e) => {
  console.log('Open animation complete')
  console.log(e.state)    // 'open'
  console.log(e.targetId) // ID of the animated view
})
```

In this example, `myView` uses the properties under `open`, making it fully opaque.

## `close` method

`close` triggers the closing animation for the specified views and uses properties defined under the `close` modifier.

### Usage

```javascript
$.myAnimation.close(views, callback);
```

- `views`: The view or array of views to animate.
- `callback`: Optional function called when the animation completes.

### Example

`index.xml`
```xml
<Alloy>
  <Window>
    <Animation module="purgetss.ui" id="myAnimation" class="close:opacity-0 open:opacity-100" />

    <View id="myView" class="opacity-100" />
  </Window>
</Alloy>
```

`index.js`
```javascript
$.myAnimation.close($.myView, (e) => {
  console.log('Close animation complete')
  console.log(e.state)    // 'close'
  console.log(e.targetId) // ID of the animated view
})
```

The callback receives the same enriched event object as `play`. See [Callback event object](the-play-method#callback-event-object) for the property reference.

In this example, `myView` uses the properties under `close`, making it fully transparent.

## Real-world use case: Panel with zoom-in effect

A common pattern in mobile apps: show a modal panel with a zoom "pop" effect and fade overlay.

`panel-zoom.xml`
```xml
<Alloy>
  <Window class="bg-slate-900">
    <Button class="h-8 w-28 rounded-lg bg-amber-500 text-xs font-semibold text-white" title="Show Panel" onClick="showPanel" />

    <!-- Panel overlay -->
    <View id="panel" class="hidden opacity-0" onClick="onOverlayTap">
      <View class="h-screen w-screen bg-(#80000000)" />
      <View id="content" class="zoom-in-110 close:duration-0 open:duration-100 mx-6 rounded-xl bg-slate-800">
        <View class="vertical mx-4 mt-4 mb-4 h-auto w-screen">
          <Label class="w-screen text-xl font-bold text-white" text="Select an Option" />
          <Label class="mt-2 h-auto w-screen text-sm text-slate-400" text="This panel uses zoom-in-110 for a subtle pop effect on open, and closes instantly with close:duration-0." />

          <Button class="mt-4 h-8 w-screen rounded-lg bg-blue-500 text-sm font-semibold text-white" title="Option A" onClick="closePanel" />
          <Button class="mt-2 h-8 w-screen rounded-lg bg-blue-500 text-sm font-semibold text-white" title="Option B" onClick="closePanel" />
          <Button class="mt-2 h-8 w-screen rounded-lg bg-slate-700 text-sm font-semibold text-slate-300" title="Cancel" onClick="closePanel" />
        </View>
      </View>
    </View>

    <Animation id="panelAnim" module="purgetss.ui" class="opacity-to-100 duration-75 ease-out" />
  </Window>
</Alloy>
```

`panel-zoom.js`
```javascript
function showPanel() {
  $.panel.show()
  $.panelAnim.open($.panel)
}

function closePanel() {
  $.panelAnim.close($.panel, () => {
    $.panel.hide()
  })
}

function onOverlayTap({ source }) {
  if (source.id === 'panel') closePanel()
}
```

The main classes:
- `zoom-in-110`: scales the content to 110% on open, then `complete` resets it to 100% for a subtle pop.
- `opacity-to-100`: fades the overlay from transparent to opaque.
- `close:duration-0 open:duration-100`: opens at 100ms and closes instantly.
- `duration-75`: keeps the overlay fade quick.

Show and hide are two lines each. The animation behavior lives in the XML.
