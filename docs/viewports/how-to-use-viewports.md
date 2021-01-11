# How to use viewports

This topic describes how to use viewports to create responsive content types. We will look at how Page Builder's `Products` content type uses viewport data (from `view.xml`) to determine how many products to display at different viewport widths.



## Custom viewport configurations

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

When the stage events are triggered from `toggleViewport()`, the `Products` content type uses the `slidesToShow` value defined for the current viewport (in `view.xml`) to set the number of products to show.

The event handler in Page Builder's `products/preview.ts` file controls the number of products to show on the _Admin stage_ as follows:

```typescript
events.on(`stage:${this.contentType.stageId}:viewportChangeAfter`, (args: {viewport: string}) => {
    const viewports = Config.getConfig("viewports");
    if (this.element && this.appearance() === "carousel") {
        this.slidesToShow = parseFloat(viewports[args.viewport].options.products.default.slidesToShow);
        this.destroySlider();
        this.initSlider();
    }
});
```

The event handler in the `widget.js` file controls the number of products to show on the _storefront_ as follows:

```javascript
events.on('stage:viewportChangeAfter', function (args) {
    var breakpoint = config.breakpoints[args.viewport];
    initSlider($element, slickConfig, breakpoint);
});
```

Notice how the viewport data is accessed according to the hierarchy defined in the `view.xml` configuration file.

In the `products/preview.ts` file, data for the local `this.slidesToShow` property is accessed using the viewport name from `args`, as shown here:

```javascript
this.slidesToShow = parseFloat(viewports[args.viewport].options.products.default.slidesToShow);
```

Again, the dot syntax for accessing viewport data corresponds to the `view.xml` hierarchy, as shown here:

```xml
<var name="mobile"> <!-- viewports[args.viewport] -->
    ...
    <var name="options">
        <var name="products">
            <var name="default">
                <var name="slidesToShow">3</var>
            </var>
        </var>
    </var>
</var>
```

The same dot syntax hierarchy is used in the `widget.js` file for the storefront, where the slider is initialized with the `slidesToShow` value assigned to the current viewport, or `breakpoint` as it is named here:

```javascript
function initSlider($element, slickConfig, breakpoint) {
    ...
    var slidesToShow = breakpoint.options.products[carouselMode] ?
            breakpoint.options.products[carouselMode].slidesToShow :
            breakpoint.options.products.default.slidesToShow;

    slickConfig.slidesToShow = parseFloat(slidesToShow);
    ...
}
```
