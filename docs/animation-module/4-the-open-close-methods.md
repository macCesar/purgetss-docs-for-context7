# The `open` and `close` Methods

Use `open` and `close` to run opening and closing animations based on the `open:` and `close:` modifiers. They do not toggle based on current view state, so you get explicit control.

## `open` Method

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

## `close` Method

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

The callback receives the same enriched event object as `play`. See [Callback event object](the-play-method#callback-event-object) for the full property reference.

In this example, `myView` uses the properties under `close`, making it fully transparent.
