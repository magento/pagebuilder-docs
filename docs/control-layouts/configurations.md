# Breakpoint and viewport configurations

intro

## `view.xml`

Page Builder's `view.xml` file provides viewport and breakpoint configurations for the `desktop` and `mobile` viewports. The file also provides breakpoint configurations only for the tablet and mobile-small breakpoints. The `desktop` and `mobile` configurations are shown here:

```xml
<!-- view.xml -->

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

## Viewport media queries

Notice that the `mobile` breakpoint also has a `media` node query. Page Builder uses this query for content type properties that can be assigned to a viewport in the Admin, using viewport-aware form fields. So let's call the `media` node a **viewport media query**. This name will help us distinguish it from the `max-width` and `min-width` breakpoints, which are used for a different purpose, explained next.

## JavaScript media queries

Page Builder uses the `min-width` and `max-width` breakpoints to build media queries in JavaScript widgets for content types that require more than CSS to behave responsively. For example, the Products content type uses these breakpoints in its `widget.js` file to control the responsiveness of the slick carousel, described more below.

### Core configuration data

The properties that are common for all breakpoints:

-  `Magento_PageBuilder` - Defines the module view configuration.
-  `breakpoints` - Defines the parent object for all the named breakpoint objects in Page Builder.
-  `desktop` and `mobile` - Defines the two breakpoint objects Page Builder uses for its default viewports.
-  `label` - (string) Sets a viewport name or description for the tooltip.
-  `stage` - (bool) If set to `true`, the viewport is added to the stage. If set to `false`, it is not, and users will not be able to set responsive properties on a content type for that viewport.
-  `default` - (bool) Determines if the viewport is selected by default when the stage is loaded. Make sure at least one viewport has a `default` setting of `true`. Be aware that if you delete the `default` node entirely from the `desktop` viewport, Page Builder will always set it as the default, and ignore the `default` settings from all other viewports.
-  `class` - (string) Defines a CSS class to style the viewport switcher button.
-  `icon` - (string) - URL to the button image (SVG) location.
-  `media` - (string) Defines the media query Page Builder uses to save breakpoint-specific properties for a viewport. Page Builder then adds the media query (and assigned properties) to its internal stylesheet on the page where the property values are applied at the breakpoints.
-  `conditions` - Contains the `min-width` and `max-width` breakpoints Page Builder uses to build media queries for JavaScript widgets and preview components.
-  `max-width` and `min-width` - Define the breakpoint widths used to construct a media query in JavaScript. The default values are in pixels (`px`), but `em` units can also be used.

### Custom configuration data

Page Builder defines the following configuration data for use in its Products widget.

-  `options` - Parent object that defines custom data used by the `Products` content type. You can define similar nodes with unique names for your own content type options.
-  `products` - Parent object that defines the content type for the data. You can define similar nodes with unique names for your own content types.
-  `default` - Parent object that groups `Products` data by appearance. You can define similar nodes with unique names for your own content types.
-  `slidesToShow` - Property defined for a content type appearance. The Products content type uses the `slidesToShow` property within the `preview.js` and `widget.js` components to control responsive behavior in the Admin and the frontend. You can define similar properties for your own content types.

As shown for `Products`, you can define your own custom data for a breakpoint, and then use that data to control content layout from your widgets or preview components.

For example, let's say you want to create a content type that shows customer testimonials in a carousel. Like Products, you could use the [slick control](https://kenwheeler.github.io/slick/) to auto-loop the quotes across the screen. But you need a way to tell slick to increase and decrease the number of quotes to show on the screen for a given breakpoint. You can't do that with CSS media queries because slick is a contained control. You have to do it from your JavaScript widget. In this case, you could create a custom property for each breakpoint called `testimonialsToShow`. This property would define the ideal number of testimonials to show for a given breakpoint.

Your content type's custom configuration data might look like this:

```xml
<!-- view.xml -->

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

In short, whenever you need breakpoint-specific data in your `widget.js` and `preview.js` components, you can define your own custom data in the `view.xml` file, where you can access it from an event handler for `stage:viewportChangeAfter`.

Page Builder uses the `max-width` and `min-width` breakpoints (from the `conditions` node) to control the responsive behavior of content types using JavaScript `widgets`.

The Products `widget.js` provides a good example of how this works. Within the widget, the `min-width` and `max-width` strings are passed to a [matchMedia() function](https://www.w3schools.com/jsref/met_win_matchmedia.asp). This method creates a list of media queries created from the `min-width` and `max-width` values from all the breakpoints defined in the `view.xml` file. When the browser width matches one of the query breakpoints, `matchMedia()` invokes a callback function on the widget. The widget can then respond to the breakpoint by calling functions and changing configurations on other controls like the [slick carousel](https://kenwheeler.github.io/slick/).
