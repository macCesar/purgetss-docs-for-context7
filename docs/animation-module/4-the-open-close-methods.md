---
sidebar_position: 4
slug: the-open-and-close-methods
---

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

```xml title="index.xml"
<Alloy>
  <Window>
    <Animation module="purgetss.ui" id="myAnimation" class="close:opacity-0 open:opacity-100" />

    <View id="myView" class="opacity-0" />
  </Window>
</Alloy>
```

```javascript title="index.js"
$.myAnimation.open($.myView, () => {
  console.log('Open animation complete');
});
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

```xml title="index.xml"
<Alloy>
  <Window>
    <Animation module="purgetss.ui" id="myAnimation" class="close:opacity-0 open:opacity-100" />

    <View id="myView" class="opacity-100" />
  </Window>
</Alloy>
```

```javascript title="index.js"
$.myAnimation.close($.myView, () => {
  console.log('Close animation complete');
});
```

In this example, `myView` uses the properties under `close`, making it fully transparent.
