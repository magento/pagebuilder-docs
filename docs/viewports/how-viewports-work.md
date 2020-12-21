# How viewports work

In Page Builder's full-screen design mode, the viewport switcher buttons appear in the Page Builder header above the stage. Clicking these buttons show previews of how page content will look on the storefront at different device widths.

![Viewports overview](../images/pagebuilder-viewport-ui.svg)

By default, Page Builder provides two viewports: **desktop** and **mobile**. You will learn how to add more viewports in [How to add new viewports](how-to-add-new-viewports.md). But first, let's see what's involved.

## Viewport files

The Page Builder files that provide a viewport's html template, functions, properties, and configuration data are introduced here and detailed after.

![Viewports overview](../images/pagebuilder-viewports-overview.svg)

### Viewport template

The `switcher.html` file is Page Builder's default template for each viewport button. Page Builder renders the template in its header within `web/template/page-builder.html` as shown here:

_Viewport template rendered in Page Builder header_

```html
<div class="admin__field pagebuilder-header"...>
    <span class="viewport-buttons">
        <render args="viewportTemplate"/> <!-- switcher.html -->
    </span>
    ...
</div>
```

The `switcher.html` template uses Knockout bindings to its parent's view-model, `page-builder.js`, where it retrieves viewport configuration data and binds a click function to each viewport defined within the `view.xml`.

_switcher.html_

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

### Viewport properties and functions

Page Builder's `page-builder.ts` file is the `$parent` view-model for the `switcher.html` template. It contains the viewport properties and functions the template (`switcher.html`) binds to.

#### Properties

**`viewports`**—Object array that contains all the viewports (and their properties) defined in the `view.xml` config file. These properties include viewport names, visibility, defaults, min- and max-widths, button icons, tooltip labels, and more. See [Viewport configuration](#viewport-configuration) for details.

**`viewport`**-`KnockoutObservable<string>` that provides the currently selected viewport by name: `desktop` or `mobile` by default.

**`defaultViewport`**—String name of viewport set to be displayed by default. This property is set from the `view.xml` file.

**`viewportClasses`**—Object array that provides the `page-builder.html` template with the CSS class that matches the currently selected viewport. These classes control the width and layout of the Page Builder stage and must be named by convention. You can find the usage of this property in `page-builder.html` as shown here:

```html
<div class="pagebuilder-stage-wrapper"
     css="Object.assign({'stage-full-screen': isFullScreen, 'stage-content-snapshot': isSnapshot},
     viewportClasses)"
```

The convention for naming a viewport CSS class is `.[viewport-name]`-`viewport`. For example, the CSS class for the mobile viewport is `.mobile-viewport`.

#### Functions

**`initViewports(config)`**—Sets the viewport configuration data to properties bound to the viewport template.

**`toggleViewport(viewport)`**—Changes the stage width using the CSS class (from `viewportClasses`) of the currently selected viewport name. It also triggers the `viewportChangeAfter` event (passing the current viewport's configuration data) so that content types can handle these events and make changes to their own layouts. For more details, see [How breakpoints work](how-breakpoints-work.md).

### Viewport configuration

Page Builder's `view.xml` file provides all the default configuration data for the desktop and mobile viewports, as shown here:

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

// Describe vars here

### Viewport CSS Classes

The `_mobile-viewport.less` file provides the `.mobile-viewport` CSS class. This class is assigned to `viewportClasses` when the `toggleViewport()` function is executed for the `mobile` viewport button.

Page Builder's default `.mobile-viewport` class shows the hierarchy of CSS selectors needed for targeting the stage for any viewport class:

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

### Viewport button icons

Finish description of where icons are declared in view.xml.

