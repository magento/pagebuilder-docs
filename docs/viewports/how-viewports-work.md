# How viewports work

Page Builder provides previews of your content at different device widths or viewports. In Page Builder's full-screen design mode, buttons for switching viewports appear in the header above the stage. By default, Page Builder provides two viewports: **desktop** and **mobile**, as shown here:

![Viewports overview](../images/pagebuilder-viewport-ui.svg)

Clicking these viewport buttons change the width of the stage to show previews of how page content (content types) will look on the storefront at different device widths. For example, the default mobile width narrows the stage viewport to 768px, as shown here:

![Viewports overview](../images/pagebuilder-viewport-ui-mobile.svg)

## Viewport files

The viewport files listed below provide the HTML templates, functions, properties, configuration data, and events associated with Page Builder viewports.

{: .bs-callout-info }
To access the linked source code for each file listed below, you must have access to the private Page Builder repository: `magento/magento2-page-builder`.

- [Magento/PageBuilder/etc/view.xml](https://github.com/magento/magento2-page-builder/blob/develop/app/code/Magento/PageBuilder/etc/view.xml)
- [Magento/PageBuilder/view/adminhtml/web/template/page-builder.html](https://github.com/magento/magento2-page-builder/blob/develop/app/code/Magento/PageBuilder/view/adminhtml/web/template/page-builder.html)
- [Magento/PageBuilder/view/adminhtml/web/template/viewport/switcher.html](https://github.com/magento/magento2-page-builder/blob/develop/app/code/Magento/PageBuilder/view/adminhtml/web/template/viewport/switcher.html)
- [Magento/PageBuilder/view/adminhtml/web/ts/js/page-builder.ts](https://github.com/magento/magento2-page-builder/blob/develop/app/code/Magento/PageBuilder/view/adminhtml/web/ts/js/page-builder.ts)
- [Magento/PageBuilder/view/adminhtml/web/css/source/_mobile-viewport.less](https://github.com/magento/magento2-page-builder/blob/develop/app/code/Magento/PageBuilder/view/adminhtml/web/css/source/_mobile-viewport.less)
- [Magento/PageBuilder/view/adminhtml/web/css/images/switcher/switcher-desktop.svg](https://github.com/magento/magento2-page-builder/blob/develop/app/code/Magento/PageBuilder/view/adminhtml/web/css/images/switcher/switcher-desktop.svg)
- [Magento/PageBuilder/view/adminhtml/web/css/images/switcher/switcher-mobile.svg](https://github.com/magento/magento2-page-builder/blob/develop/app/code/Magento/PageBuilder/view/adminhtml/web/css/images/switcher/switcher-mobile.svg)

The following diagram shows what these files contribute to the features and functions of viewports, followed by summary descriptions.

![Viewports overview](../images/pagebuilder-viewport-files.svg)

-  [page-builder.html](#page-builderhtml)—Template parent that hosts Page Builder's header, viewport buttons, template buttons, panel, and stage.

-  [switcher.html](#switcherhtml)—Template (`viewportTemplate`) that defines the viewport buttons using Knockout bindings for the `toggleViewport(viewport)` function, button images, and tooltips.

-  [page-builder.ts](#page-builderts)—View-model that defines the viewport properties and functions for the `switcher.html` template bindings.

-  [view.xml](#viewxml)—Configuration data that defines properties for viewport names, visibility, defaults, min- and max-widths, button icons, tooltip labels, and more.

-  [_mobile_viewport.less](#_mobile-viewportless)—Contains CSS classes that control the stage width. Classes in this file are assigned to the observable `viewportClasses` property and switched when `toggleViewport()` is triggered by the viewport buttons.

-  [Button icons](#button-icons)—SVG images for the viewport buttons.

-  [Stage events](#stage-events)—These are the events triggered in the `toggleViewport(viewport)` function—[`stage:viewportChangeAfter`](../reference/events.md#stageviewportchangeafter) and [`stage:${this.id}:viewportChangeAfter`](../reference/events.md#stageidviewportchangeafter). Content types can listen for these events and use the viewport data to change their own layouts and visual appearances accordingly.

## Viewport bindings and events

The following diagram is similar to the previous diagram, but focuses on the relationships and interactions between the viewport files. The numbers are not meant to show a sequence. They simply call out the important connections between the files.

![Viewports overview](../images/pagebuilder-viewports-overview.svg)

1. **Templates**—The `page-builder.html` template renders the `switcher.html` viewport template in the header section of the Page Builder stage.
1. **Bindings**—The `switcher.html` binds to the properties and functions in its parent view-module: `page-builder.ts`.
1. **Initialization**—The `page-builder.ts` view-model initializes its properties from the `view.xml` viewport data config file and sets the default viewport.
1. **Button icons**—The button icons are referenced by URLs in the `view.xml` file.
1. **Toggle function**—The `toggleViewport(viewport)` function is bound to the button click event in the `switcher.html` template. This function does two things:

   -  Sets the `viewportClasses` observable to a CSS class defined by the selected viewport.
   -  Triggers the `stage:viewportChangeAfter` events for listeners.

1. **Events**—Content types handle the stage events within their `widget.js` (for the storefront) and `preview.ts` (for the Admin stage) files.

The following sections provide details for each of the files described in the previous diagrams.

## `page-builder.html`

The `page-builder.html` template renders the `switcher.html` viewport template within the Page Builder header, as shown here:

```html
<div class="admin__field pagebuilder-header"...>
    <span class="viewport-buttons">
        <render args="viewportTemplate"/> <!-- switcher.html -->
    </span>
    ...
</div>
```

The `page-builder.html` template also contains the `viewportClasses` observable that changes the stage width based on the CSS class assigned to the viewport, as shown here:

```jsx
<div class="pagebuilder-stage-wrapper"
     css="Object.assign({'stage-full-screen': isFullScreen, 'stage-content-snapshot': isSnapshot},
     viewportClasses)"
```

For more information about `viewportClasses`, see [Viewport properties](#viewport-properties) and [_mobile-viewports.less](#_mobile-viewportless) later in this topic.

## `switcher.html`

The `switcher.html` file is Page Builder's default template for each viewport defined (in the `view.xml` file). The `switcher.html` template uses Knockout bindings to its parent's view-model—`page-builder.js`—where it retrieves viewport configuration data and binds a click function for each viewport as shown here:

```html
<each args="data: Object.keys(viewports), as: 'name'">
    <span class="tooltip">
        <button type="button"
                class="page-builder-viewport"
                css="$parent.viewports[name].class"
                disable="name === $parent.viewport()"
                click="$parent.toggleViewport.bind($parent, name)">
            <img data-bind="attr:{src: $parent.viewports[name].icon}" draggable="false" aria-hidden="true"/>
        </button>
        <span class="tooltip-content">
            <span class="tooltip-label" translate="$parent.viewports[name].label"/><br/>
            <span translate="'View your content for '"/><text args="name"/><br/>
            <span translate="'Width'"/>: <text args="Object.values($parent.viewports[name].conditions)[0]"/>
        </span>
    </span>
</each>
```

Notice how the `switcher.html` template uses dot syntax to access data. This syntax corresponds to the data hierarchy in the `view.xml` configuration. For example, the `<img>` source and the tooltip label are bound as follows:

```js
<img data-bind="attr:{src: $parent.viewports[name].icon}"/>
<span class="tooltip-label" translate="$parent.viewports[name].label"/>
```

These bindings map to the `icon` and `label` nodes of the viewport by `name`, as configured in the `view.xml` file:

```xml
<var name="desktop">
    <var name="label">Desktop</var>
    <var name="icon">Magento_PageBuilder::css/images/switcher/switcher-desktop.svg</var>
</var>

<var name="mobile">
    <var name="label">Mobile</var>
    <var name="icon">Magento_PageBuilder::css/images/switcher/switcher-mobile.svg</var>
</var>
```

## `page-builder.ts`

Page Builder's `page-builder.ts` file is the `$parent` view-model for the `switcher.html` template. This view-model contains the viewport properties (initialized in `initViewports()` from the `view.xml` data) and the function (`toggleViewport()`) bound to the button click event of the `switcher.html` template. The details follow.

### Viewport properties

**`viewports`**—Object array that contains all the viewports (and their properties) defined in the `view.xml` config file. These properties include viewport names, visibility, defaults, min- and max-widths, button icons, tooltip labels, and more. See [Viewport configuration](#viewport-configuration) for details.

**`viewport`**-`KnockoutObservable<string>` that provides the currently selected viewport by name: `desktop` or `mobile` by default.

**`defaultViewport`**—String name of viewport set to be displayed by default. This property is set from the `view.xml` file.

**`viewportClasses`**—Observable object array of CSS classes used to change the stage width for the currently selected viewport. The `toggleViewport(viewport)` function that assigns CSS classes to `viewportClasses` expects classes to be named with the following convention: `[viewport-name]`-`viewport`. By default, Page Builder uses only one CSS class to set the stage width for its `mobile` viewport: `mobile-viewport`. The `desktop` viewport does not have a class because it does not set a stage width different than the stage's default width. See [_mobile-viewport.less](#_mobile-viewportless) for more information.

### Viewport functions

**`initViewports(config)`**—Sets the viewport property values from the `view.xml` config file. The `switcher.html` template (the `viewportTemplate`) binds to these properties as previously described. This function also sets the default viewport for the stage. You can set the default viewport in the `view.xml` file as described below.

**`toggleViewport(viewport)`**—Assigns CSS classes to the `viewportClasses` observable, based on the selected viewport name passed in as an argument (`viewport`). The CSS classes in `viewportClasses` set the stage width. This function also triggers the `stage:viewportChangeAfter` events so that content types can handle these events and make changes to their own layouts. For more details, see [Stage events](#stage-events) at the end of this topic.

## `view.xml`

Page Builder's `view.xml` file provides the default configuration data for the `desktop` and `mobile` viewports, as shown here:

```xml
<vars module="Magento_PageBuilder">
    <var name="breakpoints">
        <var name="desktop">
            <var name="label">Desktop</var>
            <var name="stage">true</var>
            <var name="default">true</var>
            <var name="class">desktop-switcher</var>
            <var name="icon">Magento_PageBuilder::css/images/switcher/switcher-desktop.svg</var>
            <var name="conditions">
                <var name="min-width">1024px</var>
            </var>
            <var name="options">
                <var name="products">
                    <var name="default">
                        <var name="slidesToShow">5</var>
                    </var>
                </var>
            </var>
        </var>
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
    </var>
</var>
```

Configuration data descriptions:

- `Magento_PageBuilder` - Defines the module to which viewport data applies.
- `breakpoints` - Defines `viewports` object for the module.
- `desktop` and `mobile` - Defines viewport objects named `desktop` and `mobile`.
- `label` - (string) Defines title of tooltip.
- `stage` - (bool) Determines whether viewport button appears above the stage.
- `default` - (bool) Determines if the viewport is selected when the stage is loaded.
- `class` - (string) CSS class to apply to the viewport switcher button (not the stage).
- `icon` - (string) - URL to the button image (SVG) location.
- `media` - (string) Media query used in the Admin.
- `conditions` - Defines the `min` and `max` breakpoints for the viewport.
- `max-width` - (string) Defines the maximum width for the viewport in `px`.
- `min-width` - (string) Defines the minimum width for the viewport in `px`.
- `options` - Defines an `options` object for one or more content types.
- `products` - Defines a `products` object for use in Page Builder's `Products` content type.
- `default` - Defines the default appearance of the `Products` content type.
- `slidesToShow` - (number) Defines viewport data used by the `Products` content type.

As shown for the `Products` content type, you can define viewport data for your own content types. For example, if you have a custom content type called `testimonials`— which showed several customer quotes (side-by-side) similar to how the `Products` carousel works — you could create an `options` node for the `mobile` viewport that looks like this:

```xml
<var name="mobile">
   ...
    <var name="options">
        <var name="testimonials">
            <var name="default">
                <var name="testimonialsToShow">3</var>
            </var>
            <var name="with-images">
                <var name="testimonialsToShow">2</var>
            </var>
        </var>
    </var>
</var>
```

The takeaway here is that you can create any node hierarchy and variable names you want for use in your content types. To learn about defining and using viewport data in your own content types, see [How to use viewports](how-to-use-viewports.md).

## `_mobile-viewport.less`

The `_mobile-viewport.less` file provides the CSS class for Page Builder's `mobile-viewport`. This class is assigned to the `viewportClasses` observable when the mobile viewport button is clicked, at which point the stage width changes to the width defined in `mobile-viewport`.

As mentioned, the CSS classes used to control the stage width must follow a strict naming convention:

```css
.[viewport-name]-viewport
```

The `[viewport-name]` refers to the name of the viewport as defined in the `view.xml` file. For example, if there was a viewport named `mobile-small` in the `view.xml` file, the CSS class for setting the stage width must be named `mobile-small-viewport`. Otherwise, the `toggleViewport(viewport)` function won't assign the correct CSS class to `viewportClasses` for the stage:

```javascript
public toggleViewport(viewport: string) {
    ...
    this.viewportClasses[`${viewport}-viewport`](true);
    ...
}
```

Page Builder's default `mobile-viewport` class shows the hierarchy of CSS selectors needed for targeting the stage:

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

## Button icons

As mentioned, the viewport button icons are SVG files. Their URLs are referenced in the `view.xml` file for each viewport (`desktop` and `mobile`), as shown in these snippets:

```xml
<var name="desktop">
    <var name="icon">Magento_PageBuilder::css/images/switcher/switcher-desktop.svg</var>
</var>

<var name="mobile">
    <var name="icon">Magento_PageBuilder::css/images/switcher/switcher-mobile.svg</var>
</var>
```

As the URLs indicate, Page Builder's SVG images for the buttons are located here:

![Viewport switcher button images](../images/pagebuilder-switcher-icon-location.svg)

## Stage events

Page Builder triggers stage events from the `toggleViewport()` function. This function is bound to the button click event in the `switcher.html` template, as shown previously. The triggered events are shown here:

```typescript
public toggleViewport(viewport: string) {
    ...
    events.trigger(`stage:${this.id}:viewportChangeAfter`, {
        viewport,
        previousViewport,
    });
    events.trigger(`stage:viewportChangeAfter`, {
        viewport,
        previousViewport,
    });
}
```

Page Builder content types listen for these events and reconfigure their layouts based on the current `viewport` or `previousViewport` names (as passed by the event).

To learn about how to use viewport stage events in your custom content types, see [How to use viewports](how-to-use-viewports.md).

### How content types use stage events

To understand how Page Builder uses stage events in its content types, let's look at how the `Products` content type changes the number of products to display based on viewport properties.

When the stage events are triggered from `toggleViewport()`, the `Products` content type uses the `slidesToShow` value defined for the current viewport (in `view.xml`) to set the number of products to show.

The event handler in the `preview.ts` file controls the number of products to show on the _Admin stage_ as follows:

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

As mentioned previously, notice how the viewport data is accessed according to the hierarchy defined in the `view.xml` configuration file.

In the `preview.ts` file, data for the local `this.slidesToShow` property is accessed starting with the viewport name passed with the `args`, as shown here:

```javascript
this.slidesToShow = parseFloat(viewports[args.viewport].options.products.default.slidesToShow);
```

This dot syntax corresponds to the `view.xml` hierarchy:

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

## Summary

Viewports in Page Builder provide a way to control the responsiveness of your content. Knowing how viewports work is the first step. Learning how to add your own viewports is next. Continue with [How to add viewports](how-to-add-viewports.md) to create and customize additional viewports for Page Builder.
