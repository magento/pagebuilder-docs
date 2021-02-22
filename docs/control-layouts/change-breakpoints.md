# How to change responsive breakpoints

By default, Page Builder defines four named breakpoints in the `Magento/PageBuilder/etc/view.xml` file: `desktop`, `tablet`, `mobile`, and `mobile-small`. The default values in XML are shown here:

**desktop**

-  `<var name="min-width">1024px</var>`

**tablet**

-  `<var name="max-width">1024px</var>`
-  `<var name="min-width">768px</var>`

**mobile**

-  `<var name="max-width">768px</var>`
-  `<var name="min-width">640px</var>`
-  `<var name="media">only screen and (max-width: 768px)</var>`

**mobile-small**

-  `<var name="max-width">640px</var>`

## Steps for changing breakpoints

1. [Create an overriding view.xml file](#step-1-create-an-overriding-viewxml-file).
1. [Change the min-width and max-width](#step-2-change-the-min-width-and-max-width).
1. [Change the viewport media queries](#step-3-change-the-viewport-media-queries).
1. [Change your frontend media queries](#step-4-change-your-frontend-media-queries).
1. [Change the viewport stage CSS](#step-5-change-the-viewport-stage-css).

### Step 1: Create an overriding view.xml file

Start by copying Page Builder's `view.xml` file to your module or Admin theme's `etc` directory: `Vendor/Module/etc/view.xml`. Technically, in your own `view.xml` file, you can remove the nodes you don't change and rely on Magento's XML merging to add those nodes back from Page Builder's `view.xml`. However, during development, you might find it helpful to keep Page Builder's nodes in place as a reminder of the configuration data Page Builder uses from the nodes you don't change. It's up to you.

### Step 2: Change the min-width and max-width

Page Builder uses the `min-width` and `max-width` values to generate media queries that control the breakpoint settings for JavaScript widgets. The default `min-width` and `max-width` values are in pixels (`px`). But you can use `em` units or any other CSS unit suitable to media queries. For example:

```xml
<var name="mobile">
    <var name="label">Mobile</var>
    <var name="stage">true</var>
    <var name="class">mobile-switcher</var>
    <var name="icon">Magento_PageBuilder::css/images/switcher/switcher-mobile.svg</var>
    <var name="media">only screen and (max-width: 48em)</var>
    <var name="conditions">
        <var name="max-width">48em</var>
        <var name="min-width">30em</var>
    </var>
</var>
<var name="mobile-small">
    <var name="conditions">
        <var name="max-width">30em</var>
    </var>
</var>
```

### Step 3: Change the viewport media queries

If you are changing a breakpoint that doesn't define a `media` query for a viewport, you can skip this step. Otherwise, read on.

Page Builder uses the `media` query nodes to wrap user-defined breakpoints for content on the frontend. So the `max-width` for these queries must match the `conditions.max-width` for the JavaScript.

Here's why. Parts of the page content might be controlled by the viewport `media` queries, while other parts of the content might be controlled by the `min-width` and `max-width` for the JavaScript media queries. The result could lead to janky, unpredictable behavior on the frontend.

The previous example shows the properly matching `max-width` values, including the matching of `em` units.

### Step 4: Change your frontend media queries

Make sure you also synchronize the breakpoint's `max-width` with your own frontend media queries in your content modules or frontend themes.

Continuing with our previous example, if you had a bunch of media queries that targeted Page Builder's default `mobile-small` breakpoint (`640px`), you need to change those queries to match your new `max-width`. Example:

```scss
//  Standard media queries
//  ________________________________________________________

@media only screen and (max-width: 30em){}

//  Magento media queries: @screen__xs = 480px = 30em
//  ________________________________________________________

.media-width(@extremum, @break) when (@extremum = 'max') and (@break = @screen__xs){}
```

### Step 5: Change the viewport stage CSS

 For breakpoints used as viewports, the CSS for the viewport's stage width is the last thing you need to update when changing `max-width` values and `media` queries set in the `view.xml` file. Matching these widths to the width in your stage CSS ensures that your stage previews show the breakpoint widths that customers see on the storefront.

 ```scss
.mobile-viewport {
    &.pagebuilder-stage-wrapper {
        &.stage-content-snapshot,
        &.stage-full-screen {
            .pagebuilder-stage {
                .pagebuilder-canvas {
                    left: 50%;
                    margin: 0;
                    transform: translateX(-50%);
                    width: 768px;
                }
            }
        }
    }
}
```

## Summary

Changing breakpoint widths in Page Builder is pretty simple as long as you are careful to synchronize your `max-width` and `min-width` breakpoints across your viewport, Javascript, and frontend media queries, as well as in your viewport CSS classes for the stage.


# How to add breakpoints

At this point, you can create a brand new breakpoint with a viewport by copying one of the breakpoint nodes (with all its children), and add it as another viewport.

For example, to create a viewport for an even smaller phone with a `max-width` of `320px`, just copy and paste the `mobile-small` node, then change its name and viewport properties:

```xml
<var name="breakpoints">
    ...
    <var name="mobile-tiny">
        <var name="label">Mobile Tiny</var>
        <var name="stage">true</var>
        <var name="class">mobile-tiny-switcher</var>
        <var name="icon">images/switcher/switcher-mobile-tiny.svg</var>
        <var name="media">only screen and (max-width: 320px)</var>
    </var>
    ...
```
