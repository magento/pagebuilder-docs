# How to use viewports

Page Builder provides three ways to create responsive content using viewports:

-  [Create responsive CSS](#create-responsive-css) for Admin and storefront content.
-  [Create responsive JavaScript](#create-responsive-javascript) for JavaScript components.
-  [Create responsive forms](#create-responsive-forms) to add user-defined properties for each viewport/breakpoint.

**[Insert diagram]**

Each approach is describe next in detail.
## Create responsive CSS

**[Add link to example branch in repo]**

**[Insert diagram]**

**[Walk through the steps for creating theme CSS that matches viewport and style naming conventions to create responsive text for Page Builder's `Text` and `Heading` content types.]**

1. [Define a viewport](#step-1-define-your-viewport).
1. [Add viewport CSS selectors for Admin theme](#step-2-add-viewport-css-selectors-for-admin-theme).
1. [Add matching media queries frontend theme](#step-3-add-matching-media-queries-for-frontend-themes).

### Step 1: Define your viewport

### Step 2: Add viewport CSS selectors for Admin theme

### Step 3: Add matching media queries for frontend themes

## Create responsive JavaScript

**[Add link to example branch in repo]**

**[Insert diagram]**

To create responsive JavaScript components, you can create your own XML data for each viewport, then use that data within a `viewportChangeAfter` event handler to make responsive adjustments as needed.

For example, let's look at how Page Builder's `Products` content type uses viewport XML data from its [view.xml file](https://github.com/magento/magento2-page-builder/blob/develop/app/code/Magento/PageBuilder/etc/view.xml) to determine how many products to display in its carousel component at different viewport widths.

The XML data defined in the `mobile` viewport starts with the `options` node as shown here:

```xml
<var name="mobile">
    <var name="label">Mobile</var>
    <var name="stage">true</var>
    <var name="default">false</var>
    <var name="class">mobile-switcher</var>
    <var name="icon">Magento_PageBuilder::css/images/switcher/switcher-mobile.svg</var>
    <var name="media">only screen and (max-width: 768px)</var>
    <var name="conditions">
        <var name="max-width">768px</var>
        <var name="min-width">640px</var>
    </var>
    <var name="options">
        <var name="products">
            <var name="default">
                <var name="slidesToShow">3</var>
            </var>
        </var>
    </var>
</var>
```

The `options` node here defines the specific data that `Products` needs to change the number of products shown in its carousel component. For more information on the specific `view.xml` nodes, see the [view.xml section](viewports-and-breakpoints.md#viewxml) in [Page Builder viewports](viewports-and-breakpoints.md).

### Handling viewport changes

When users click the Admin viewport buttons, Page Builder triggers `viewportChangeAfter` events from `toggleViewport()`, passing the current `viewport` name to all event listeners.

To capture and respond to these events, you need to create event handlers in your content type's JavaScript components: `preview.js` and `widget.js`.

The event handlers for the `Products` content type are shown here:

```typescript
// products/preview.ts

events.on(`stage:${this.contentType.stageId}:viewportChangeAfter`, (args: {viewport: string}) => {
    const viewports = Config.getConfig("responsive");
    if (this.element && this.appearance() === "carousel") {
        this.slidesToShow = parseFloat(viewports[args.viewport].options.products.default.slidesToShow);
        this.destroySlider();
        this.initSlider();
    }
});

// products/appearance/carousel/widget.js

events.on('stage:viewportChangeAfter', function (args) {
    var breakpoint = config.breakpoints[args.viewport];
    initSlider($element, slickConfig, breakpoint);
});
```

In both components, the event handler uses the `viewport` name to access the `slidesToShow` value, then uses that value to re-initialize the `Products` carousel (the [slick slider](http://kenwheeler.github.io/slick/)) to show a suitable number of products for the selected viewport width, when displayed on the stage.

Notice how the viewport data in both event handlers is accessed using dot syntax according to the hierarchy defined in the `view.xml` configuration file. For example, the `preview.ts` file, data for the local `this.slidesToShow` property is accessed using the viewport name from `args`, as shown here:

```javascript
this.slidesToShow = parseFloat(viewports[args.viewport].options.products.default.slidesToShow);
```

Like other content type `widgets`, the Product's `widget` is loaded on the frontend to control the number of products displayed in the storefront when the viewport is resized. However, the `widget` also needs to listen for viewport stage events (`stage:viewportChangeAfter`) when the `Products` content type is rendered inside a `Block` or `Dynamic Block` on the stage. In such cases, the `widget` breakpoint functions (called on the frontend) do not work. That's why you see a frontend `widget` defining a backend event handler.

## Create responsive forms

**[Add link to example branch in repo]**

**[Insert diagram]**

[Describe creating the additional `tablet` and `mobile-small` forms for the the min-height field of the existing `Row`. Also show how you can add breakpoints to new fields and other existing fields]

1. [Define a viewport](#step-1-define-a-viewport).
1. [Add viewports to the content type configuration](#step-2-add-viewports-to-content-type-configurations).
1. [Add forms and layouts for viewports](#step-3-add-forms-and-layouts-for-viewports).
1. [Add CSS for form icons and tooltips](#step-4-add-css-for-form-icons-and-tooltips).
1. [Add SVG images for form icons](#step-5-add-svg-images-for-form-icons).

### Step 1: Define a viewport

### Step 2: Add viewports to content type configurations

### Step 3: Add forms and layouts for viewports

### Step 4: Add CSS for form icons and tooltips

### Step 5: Add SVG images for form icons


## Summary
