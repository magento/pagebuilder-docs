# How viewports work

Page Builder provides previews of your content at different device widths or viewports. In Page Builder's full-screen design mode, buttons for switching viewports appear in the header above the stage. By default, Page Builder provides two viewports: **desktop** and **mobile**, as shown here:

![Viewports overview](../images/pagebuilder-viewport-ui.svg)

Clicking these viewport buttons change the width of the stage to show previews of how page content (content types) will look on the storefront at different device widths. For example, the default mobile width narrows the stage viewport to 768px, as shown here:

![Viewports overview](../images/pagebuilder-viewport-ui-mobile.svg)

## Viewport files

This topic provides detailed information on the Page Builder files that make up a viewport. These files include the HTML template, functions, properties, configuration data, and events for a viewport.

The following diagram shows the Page Builder files that make a viewport, followed by summary descriptions of each.

![Viewports overview](../images/pagebuilder-viewport-files.svg)

-  **`page-builder.html`**—HTML Template parent that hosts Page Builder's header, viewport buttons, template buttons, panel, and stage.

-  **`switcher.html`**—HTML Template (`viewportTemplate`) that defines the viewport buttons using Knockout bindings for the `toggleViewport()` function, button images, and tooltips.

-  **`page-builder.ts`**—View-model that defines the viewport properties and functions for the `switcher.html` template bindings.

-  **`view.xml`**—Configuration data that defines properties for viewport names, visibility, defaults, min- and max-widths, button icons, tooltip labels, and more.

-  **`_mobile_viewport.less`**—Contains CSS classes that control the stage width and other properties. Classes in this file are assigned to the observable `viewportClasses` property and switched when `toggleViewport()` is triggered by the viewport buttons.

-  **`button icons`**—SVG images for the viewport buttons.

-  **`stage events`**—These are the events triggered in the `toggleViewport()` function—[`stage:viewportChangeAfter`](../reference/events.md#stageviewportchangeafter) and [`stage:${this.id}:viewportChangeAfter`](../reference/events.md#stageidviewportchangeafter). Content types can listen for these events and use the viewport data to change their own layouts and visual appearances accordingly.

## Viewport bindings and events

The following diagram is similar to the previous diagram, but focuses on the relationships and interactions between the files. The numbers are not meant to show the order of events. They are only meant to highlight the most important parts of the process.

![Viewports overview](../images/pagebuilder-viewports-overview.svg)

1. The `page-builder.html` template renders the `switcher.html` viewport template in header.
1. The `switcher.html` binds to the properties and functions in its parent view-module: `page-builder.ts`.
1. The `page-builder` view-model initializes its properties from the `view.xml` config file.
1. The button icons are referenced by URLs in the `view.xml` file.
1. The `toggleViewport()` function is bound to the button click event in the `switcher.html` template. This function does two things: (1) sets the `viewportClasses` observable to CSS class defined by the viewport, and (2) triggers the stage events for listeners.

1. Content types handle these events within their `widget.js` file (for the storefront) and `preview.ts` file (for the Admin stage).

The following sections provide details for each of the files noted in the previous diagrams.

## `page-builder.html`

The `page-builder.html` template renders the `switcher.html` viewport template within the Page Builder header, as shown here:

_Viewport template rendered in Page Builder header_

```html
<div class="admin__field pagebuilder-header"...>
    <span class="viewport-buttons">
        <render args="viewportTemplate"/> <!-- switcher.html -->
    </span>
    ...
</div>
```

The `page-builder.html` template also contains the `viewportClasses` observable that changes the stage width based on the CSS class assigned to the viewport. See [Viewport properties](#viewport-properties) below for more information about `viewportClasses`.

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

Page Builder's `page-builder.ts` file is the `$parent` view-model for the `switcher.html` template. The view-model contains the viewport properties (from the `view.xml` configurations) and functions that the `switcher.html` template binds to

### Viewport properties

**`viewports`**—Object array that contains all the viewports (and their properties) defined in the `view.xml` config file. These properties include viewport names, visibility, defaults, min- and max-widths, button icons, tooltip labels, and more. See [Viewport configuration](#viewport-configuration) for details.

**`viewport`**-`KnockoutObservable<string>` that provides the currently selected viewport by name: `desktop` or `mobile` by default.

**`defaultViewport`**—String name of viewport set to be displayed by default. This property is set from the `view.xml` file.

**`viewportClasses`**—Object array that provides the `page-builder.html` template with the CSS class that matches the currently selected viewport. These classes control the width and layout of the Page Builder stage and must be named by convention. You can find the usage of this property in `page-builder.html` as shown here:

```html
<div class="pagebuilder-stage-wrapper"
     css="Object.assign({'stage-full-screen': isFullScreen, 'stage-content-snapshot': isSnapshot},
     viewportClasses)"
```

The convention for naming viewport CSS classes is `.[viewport-name]`-`viewport`. For example, the CSS class for the mobile viewport is `.mobile-viewport`.

### Viewport functions

**`initViewports(config)`**—Sets the viewport configuration data to properties bound to the viewport template.

**`toggleViewport(viewport)`**—Changes the stage width using the CSS class (from `viewportClasses`) of the currently selected viewport name. It also triggers the `viewportChangeAfter` event (passing the current viewport's name) so that content types can handle these events and make changes to their own layouts. For more details, see [Stage events](#stage-events) at the end of this topic.

## `view.xml`

Page Builder's `view.xml` file provides the default configuration data for the desktop and mobile viewports, as shown here:

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

Descriptions of the configuration data follow:

- `Magento_PageBuilder` - Defines the module to which viewport data applies.
- `breakpoints` - Defines `viewports` object for the module.
- `desktop` and `mobile` - Defines viewport objects named `desktop` and `mobile`.
- `label` - (string) Defines title of tooltip.
- `stage` - (bool) Determines whether viewport button appears.
- `default` - (bool) Determines whether viewport is selected when the stage is loaded.
- `class` - (string) CSS class to apply to the viewport switcher button (not the stage).
- `icon` - (string) - URL to the button image (SVG) location.
- `media` - (string) media query for viewport CSS.
- `conditions` - Defines the `min` and `max` breakpoints for the viewport.
- `max-width` - (string) Defines the maximum width for the viewport in `px`.
- `min-width` - (string) Defines the minimum width for the viewport in `px`.
- `options` - Defines an `options` object for one or more content types.
- `products` - Defines the `products` sub-object for the option data.
- `default` - Defines the default appearance of the `products` content type.
- `slidesToShow` - (number) Defines viewport data used by the `products` content type.

As shown for the `products` content type, you can define viewport data for your own content types. For example, if you have a custom content type called `testimonials`— which showed several customer quotes (side-by-side) similar to how `products` works—you could create an `options` node for the `mobile` viewport that looks like this:

```xml
<var name="mobile">
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

More on this in [How to customize viewports](how-to-add-new-viewports.md).

## `_mobile-viewport.less`

The `_mobile-viewport.less` file provides the CSS class that Page Builder uses to change the stage width for its `mobile` viewport. The convention required for naming viewport CSS classes is:

```terminal
.[viewport-name]-viewport
```

For example, the CSS class for the mobile viewport is `.mobile-viewport`.

This class is assigned to `viewportClasses` when the `toggleViewport()` function is executed for the `mobile` viewport button. As a result, the stage width is changed as defined by the viewport's class.

Page Builder's default `.mobile-viewport` class shows the hierarchy of CSS selectors needed for targeting the stage:

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

Viewports in Page Builder provide a way to control the responsive nature of Page Builder content. Knowing how they work is the first step. The next step is learning how to use these viewports in your own content types. Continue with [How to use viewports](how-to-add-new-viewports.md) to create and customize your own viewports for existing and custom content types.
