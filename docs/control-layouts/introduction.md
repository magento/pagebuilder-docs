# Introduction to viewports

What's a viewport? It's a window in a spacecraft. But that's not important right now. For development, we know a viewport refers to the size of the window (or device) used to view content. But in Page Builder, the term has more specific meanings.

Page Builder viewports refer to the buttons and their related device widths shown on the stage. So when we say things like viewport buttons, viewport widths, and viewport configuration data, we're usually talking about the UI elements and settings for the stage.

## Viewports for the stage

By default, Page Builder provides two viewports: **desktop** and **mobile**, as shown here:

![Viewports overview](../images/pagebuilder-viewport-ui.svg)

When the buttons are clicked, the stage width changes to the width defined in the viewport's CSS class for the stage. For example, the default `mobile` viewport narrows the stage canvas to 768px, as shown here:

![Viewports overview](../images/pagebuilder-viewport-ui-mobile.svg)

The stage width is controlled by the `.mobile-viewport` CSS (Less):

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

## Viewports for property breakpoints

In addition to providing the UI for frontend previews, viewports deliver one of Page Builder's best features: User-controlled property values that are breakpoint-specific. As a developer, you define the breakpoint for a viewport, and the end user defines the property value for that viewport. Without viewports, users can't set breakpoints on content properties.

So let's define another term: viewport properties.

Viewport properties refer to the properties of a content type that can accept different values for different viewport breakpoints.

Page Builder defines the **Minimum Height** form field as a viewport property for Rows, Columns, Tabs, Tab Items, Banners, Sliders, and Slides. Users can save two different minimum height values for these content types, one for the `desktop` viewport (`min-width: 1024px`) and one for the `mobile` viewport (`max-width: 768px`).

## How viewport properties work


[Diagram]

First, the user clicks the `mobile` viewport button. This changes the stage width accordingly. to change the viewport to `mobile`. The user then opens a form for a content type that defines a viewport property field. The user adds a value to the field and clicks the Save button on the form. Page Builder saves that value with the viewport.

At this time. and associates the viewport's `media` query (from `view.xml`) to any viewport property on any breakpoints that are applied  the viewport properties   made.


Later, when rendering content for the frontend, Page Builder starts generating styles for the content-types on the page. Viewport properties saved with a viewport (in the form) are added to the media query from the viewport's `admin` node in the `view.xml` file.

Finally, Page Builder adds all its content type styles — for both viewport properties and common properties — to an internal stylesheet on the page. So when the viewport of a device matches the media queries assigned to the content properties, responsive changes happen.


## How viewports work

The viewport files listed below provide all the HTML templates, functions, properties, configuration data, and events that make up the viewport framework in Page Builder.

{: .bs-callout-info }
To access the linked source code for each file listed here, you must have access to the private Page Builder repository: `magento/magento2-page-builder`.

- [Magento/PageBuilder/etc/view.xml](https://github.com/magento/magento2-page-builder/blob/develop/app/code/Magento/PageBuilder/etc/view.xml)
- [Magento/PageBuilder/view/adminhtml/web/template/page-builder.html](https://github.com/magento/magento2-page-builder/blob/develop/app/code/Magento/PageBuilder/view/adminhtml/web/template/page-builder.html)
- [Magento/PageBuilder/view/adminhtml/web/template/viewport/switcher.html](https://github.com/magento/magento2-page-builder/blob/develop/app/code/Magento/PageBuilder/view/adminhtml/web/template/viewport/switcher.html)
- [Magento/PageBuilder/view/adminhtml/web/ts/js/page-builder.ts](https://github.com/magento/magento2-page-builder/blob/develop/app/code/Magento/PageBuilder/view/adminhtml/web/ts/js/page-builder.ts)
- [Magento/PageBuilder/view/adminhtml/web/css/source/_mobile-viewport.less](https://github.com/magento/magento2-page-builder/blob/develop/app/code/Magento/PageBuilder/view/adminhtml/web/css/source/_mobile-viewport.less)
- [Magento/PageBuilder/view/adminhtml/web/css/images/switcher/switcher-desktop.svg](https://github.com/magento/magento2-page-builder/blob/develop/app/code/Magento/PageBuilder/view/adminhtml/web/css/images/switcher/switcher-desktop.svg)
- [Magento/PageBuilder/view/adminhtml/web/css/images/switcher/switcher-mobile.svg](https://github.com/magento/magento2-page-builder/blob/develop/app/code/Magento/PageBuilder/view/adminhtml/web/css/images/switcher/switcher-mobile.svg)

The following diagram shows how these files make viewports work.

![Viewports overview](../images/pagebuilder-viewport-files.svg)

### Summary descriptions

-  [page-builder.html](#page-builderhtml)—Template parent that hosts Page Builder's header, viewport buttons, template buttons, panel, and stage.
-  [switcher.html](#switcherhtml)—Template (`viewportTemplate`) that defines the viewport buttons using Knockout bindings for the `toggleViewport()` function, button images, and tooltips.
-  [page-builder.ts](#page-builderts)—ViewModel that defines the viewport properties and functions for the `switcher.html` template bindings.
-  [view.xml](#viewxml)—Configuration data that defines properties for viewport names, visibility, defaults, min- and max-widths, button icons, tooltip labels, and more.
-  [_mobile_viewport.less](#_mobile-viewportless)—Defines CSS classes that change the stage width. Classes in this file are assigned to the observable `viewportClasses` property and switched when `toggleViewport()` is triggered by the viewport buttons.
-  [Button icons](#button-icons)—SVG images for the viewport buttons.
-  [Stage events](#stage-events)—The events triggered from the `toggleViewport()` function—[`stage:viewportChangeAfter`](../reference/events.md#stageviewportchangeafter) and [`stage:${this.id}:viewportChangeAfter`](../reference/events.md#stageidviewportchangeafter). Content type JavaScript components (`preview.ts` and `widget.js`) can listen for these events and make responsive changes as needed.

## Viewport bindings and events

The following diagram is similar to the previous one, but focuses more on how the viewport bindings and events work. The numbers are not meant to show a strict operational sequence. They simply call out the important actions and connections between the files.

![Viewports overview](../images/pagebuilder-viewports-overview.svg)

1. **Templates**—The `page-builder.html` template renders the `switcher.html` viewport template in the header section of the Page Builder stage.
1. **Bindings**—The `switcher.html` binds to the properties and functions of its parent ViewModel: `page-builder.ts`.
1. **Initialization**—The `page-builder.ts` ViewModel initializes its properties from the `view.xml` config file and sets the default viewport.
1. **Button icons**—The button icons are referenced by URLs in the `view.xml` for display on the stage.
1. **Toggle function**—The `toggleViewport()` function is bound to the button click event in the `switcher.html` template. This function does two things:

   -  Sets the `viewportClasses` observable to a CSS class referenced by the selected viewport.
   -  Triggers the `stage:viewportChangeAfter` events for listeners.

1. **Events**—Content types handle the viewport events within their `widget.js` and `preview.ts` files.

## Summary

Understanding what viewports are and how they work in Page Builder is the first step toward making your content types responsive. The next two steps include [How to add viewports]() to Page Builder and [How to use viewports]() to create responsive content.
