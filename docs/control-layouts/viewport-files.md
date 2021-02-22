# Viewport files


## `page-builder.html`

The `page-builder.html` template hosts the `switcher.html` template (`viewportTemplate`) in the Page Builder's header:

```html
<!-- page-builder.html -->

<div class="admin__field pagebuilder-header"...>
    <span class="viewport-buttons">
        <render args="viewportTemplate"/> <!-- switcher.html -->
    </span>
    ...
</div>
```

The `page-builder.html` template also includes the `viewportClasses` observable. When this observable changes, the stage width changes to match the `width` defined in the viewport's CSS class.

```html
<!-- page-builder.html -->

<div class="pagebuilder-stage-wrapper"
     css="Object.assign({'stage-full-screen': isFullScreen, 'stage-content-snapshot': isSnapshot},
     viewportClasses)"
```

For more information about `viewportClasses`, see [Viewport properties](#viewport-properties) and [_mobile-viewports.less](#_mobile-viewportless), later in this topic.

## `switcher.html`

The `switcher.html` file is the template for the viewport buttons. Page Builder uses this template to render a button for each viewport defined in the `view.xml` file. The switcher template uses Knockout bindings to retrieve data and call the button click function from `page-builder.js`, as shown here:

```html
<!-- switcher.html -->

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

Notice how the `switcher.html` template uses dot syntax to access viewport data from the `page-builder.ts` ViewModel. This syntax corresponds to the data hierarchy in the `view.xml` configuration. For example, the `<img src>` and the `tooltip-label` are bound as follows:

```html
<!-- switcher.html -->

<img data-bind="attr:{src: $parent.viewports[name].icon}"/>
<span class="tooltip-label" translate="$parent.viewports[name].label"/>
```

These bindings map to the `icon` and `label` nodes defined in the `view.xml` file, starting with the viewport name, as shown here:

```xml
<!-- view.xml -->

<var name="desktop">
    <var name="label">Desktop</var>
    <var name="icon">Magento_PageBuilder::css/images/switcher/switcher-desktop.svg</var>
</var>

<var name="mobile">
    <var name="label">Mobile</var>
    <var name="icon">Magento_PageBuilder::css/images/switcher/switcher-mobile.svg</var>
</var>
```

The `view.xml` file makes it easy to customize the template without having to change it directly.

## `page-builder.ts`

As previously noted, Page Builder's `page-builder.ts` file is the `$parent` ViewModel for the `switcher.html` template. This ViewModel includes a viewport's properties and functions bound to the `switcher.html` template. The details follow.

### Viewport properties

**`viewports`**—Object that contains all the viewports (and their properties) defined in the `view.xml` config file. These properties include viewport names, stage visibility, defaults, min- and max-widths, button icons, tooltip labels, and more. See [view.xml](#viewxml) below for details.

**`viewport`**-`KnockoutObservable<string>` that provides the currently selected viewport by name: `desktop` or `mobile` by default.

**`defaultViewport`**—Name of the viewport selected by default. This property is set in the `view.xml` configuration.

**`viewportClasses`**—Observable object array of CSS classes used to change the stage width. The `toggleViewport()` appends the selected viewport name with `'-viewport'`, then assigns it as a CSS class to the`viewportClasses` observable. By default, Page Builder uses only one CSS class to set the stage width for its `mobile` viewport: `mobile-viewport`. The `desktop` viewport does not have a class because it does not set a stage width that is different from the stage's default width. See [_mobile-viewport.less](#_mobile-viewportless) for more information.

### Viewport functions

**`initViewports(config)`**—Sets the viewport property values from the `view.xml` config file. The `switcher.html` template (`viewportTemplate`) binds to these properties as previously described. This function also sets the default viewport for the stage. You can set the default viewport in the `view.xml`.

**`toggleViewport(viewport)`**—Assigns CSS classes to the `viewportClasses` observable, based on the selected viewport name. The CSS classes assigned to `viewportClasses` control the stage width. This function also triggers the `stage:viewportChangeAfter` events. Content types can then change their layouts from event handlers. For more details, see [Stage events](#stage-events) at the end of this topic.


## `_mobile-viewport.less`

The `_mobile-viewport.less` file includes the CSS class that **changes the stage width** to the width defined for the `mobile` breakpoint in `view.xml`. As mentioned in [Viewport properties](#viewport-properties), Page Builder assigns this class to the `viewportClasses` observable after the mobile viewport button is clicked. When this happens, the stage width changes to the width in the `.mobile-viewport` class. The name you give this class must follow this naming convention:

```terminal
.[breakpoint-name] + '-viewport'
```

The `[breakpoint-name]` refers to the name of the breakpoint for the viewport, as defined in the `view.xml` file. For example, the `mobile-small` breakpoint requires a CSS class named `.mobile-small-viewport`. If your viewport CSS class name doesn't follow this convention, it won't match the class name generated by the `toggleViewport()` function and assigned to `viewportClasses` for the stage. The code that determines this convention is shown here:

```javascript
// page-builder.ts
public toggleViewport(viewport: string) {
    ...
    this.viewportClasses[`${viewport}-viewport`](true);
    ...
}
```

Page Builder's default `.mobile-viewport` class shows the nesting of CSS selectors required to target the `pagebuilder-canvas` on the stage:

```scss
// _mobile-viewport.less

.mobile-viewport {
    &.pagebuilder-stage-wrapper {
        &.stage-content-snapshot,
        &.stage-full-screen {
            .pagebuilder-stage {
                .pagebuilder-canvas {
                    left: 50%;
                    transform: translateX(-50%);
                    width: 768px;
                }
            }
        }
    }
}
```

## Button icons

Page Builder's viewport buttons use SVG images. The URLs to these images are defined in the `view.xml` file for each viewport (`desktop` and `mobile`), as shown in these snippets:

```xml
<!-- view.xml -->

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

Page Builder triggers the viewport stage events from the `toggleViewport()` function. This function is bound to the viewport button in the `switcher.html` template. After the user clicks the viewport button, Page Builder calls `toggleViewport()`, which triggers the stage events as shown here:

```typescript
// page-builder.ts

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

Event handlers within Page Builder's content types (and yours) can then make responsive changes based on the selected `viewport` or `previousViewport`. To learn more about how to use these events, see [Create responsive JavaScript](use-viewports.md#create-responsive-javascript).
