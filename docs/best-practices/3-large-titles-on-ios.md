# Large Titles on iOS

When using Large Titles with a ScrollView inside a NavigationWindow or TabGroup, three Window properties must work together. Missing any one of them causes visual glitches.

## The problem

On iOS, enabling `largeTitleEnabled` alone can cause two issues:

1. **Content overlaps behind the nav bar.** The ScrollView starts at y=0, hidden behind the navigation bar.
2. **Large title renders with a visible delay.** The nav bar area appears empty for a moment before the title draws.

## The solution: 3 interdependent properties

| Property                     | Value                     | What it does                                                      |
| ---------------------------- | ------------------------- | ----------------------------------------------------------------- |
| `autoAdjustScrollViewInsets` | `true`                    | iOS adjusts ScrollView insets so content starts below the nav bar |
| `extendEdges`                | `[Ti.UI.EXTEND_EDGE_ALL]` | Content extends behind bars, creating the blur/translucent effect |
| `largeTitleEnabled`          | `true`                    | Shows the large title that collapses on scroll                    |

**Without `autoAdjustScrollViewInsets`**, `extendEdges` pushes the content behind the nav bar without compensating the ScrollView insets.

**Without `extendEdges` + `autoAdjustScrollViewInsets`** (using only `largeTitleEnabled`), the large title renders with a visible delay: the empty nav bar area appears first, then the title draws. With all three properties, iOS calculates the full layout before displaying the window.

## Configuring as global defaults

Instead of repeating these properties on every window, set them once in `config.cjs`:

`purgetss/config.cjs`
```js
module.exports = {
  theme: {
    Window: {
      ios: {
        apply: 'auto-adjust-scroll-view-insets extend-edges-all large-title-enabled'
      }
    }
  }
}
```

This generates default styles for all Windows on iOS. Individual windows only need to set `largeTitleDisplayMode` if they want to override the collapse behavior.

## Works with NavigationWindow and TabGroup

On iOS, TabGroup wraps each Tab in an implicit NavigationWindow. The three-property pattern applies equally to both scenarios.

### NavigationWindow

`app/views/index.xml`
```xml
<Alloy>
  <NavigationWindow id="navWin">
    <Window title="Home">
      <ScrollView class="vertical content-w-screen content-h-auto">
        <!-- Content starts below the nav bar automatically -->
      </ScrollView>
    </Window>
  </NavigationWindow>
</Alloy>
```

### TabGroup

`app/views/index.xml`
```xml
<Alloy>
  <TabGroup>
    <Tab title="Home">
      <Window title="Home">
        <ScrollView class="vertical content-w-screen content-h-auto">
          <!-- Each tab has its own implicit NavigationWindow -->
        </ScrollView>
      </Window>
    </Tab>
  </TabGroup>
</Alloy>
```

## Controlling Large Title display

`largeTitleDisplayMode` controls how the title behaves in the navigation stack:

| Mode          | Constant                                       | Behavior                                           |
| ------------- | ---------------------------------------------- | -------------------------------------------------- |
| **Automatic** | `Ti.UI.iOS.LARGE_TITLE_DISPLAY_MODE_AUTOMATIC` | Inherits from previous window; collapses on scroll |
| **Always**    | `Ti.UI.iOS.LARGE_TITLE_DISPLAY_MODE_ALWAYS`    | Title stays large regardless of scroll             |
| **Never**     | `Ti.UI.iOS.LARGE_TITLE_DISPLAY_MODE_NEVER`     | Always uses standard title size                    |

In PurgeTSS, use the utility class `large-title-display-mode-never` on detail windows to show a standard-size title:

```xml
<Window class="large-title-display-mode-never" title="Detail">
  <!-- Standard nav bar title -->
</Window>
```

> 💡 **TIP**
>
> Set the three base properties (`autoAdjustScrollViewInsets`, `extendEdges`, `largeTitleEnabled`) as global defaults in `config.cjs`, and only override `largeTitleDisplayMode` per-window as needed.

