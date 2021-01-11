# How to add viewports

By default, Page Builder defines four viewports, but only two are shown on the Admin stage: `desktop` and `mobile`. The other two viewports (hidden from the stage), are `tablet` and `small-mobile`. This topic shows you how to add the hidden viewports to the stage and customize them as needed.

![Add viewports illustration](../images/pagebuilder-adding-viewports.svg)

{: .bs-callout-info }
**Example available**. A fully functioning example for adding additional viewports is available for you to view and install here: [https://github.com/magento-devdocs/pagebuilder-viewports/tree/how-to-add-viewports](https://github.com/magento-devdocs/pagebuilder-viewports/tree/how-to-add-viewports). This example is implemented as an Admin theme for Page Builder, an approach we recommend for all viewport additions and customizations.

## Steps for adding viewports

1. [Create an Admin theme](#step-1-create-an-admin-theme).
1. [Add a view.xml file](#step-2-add-a-viewxml-file). This file should contain your configuration data for the additional viewports.
1. [Add stage CSS classes](#step-3-add-less-files). This file should contain your CSS/LESS classes that control the stage width for your additional viewports.
1. [Add SVG images](#step-4-add-svg-images). These image files provide the icons for the viewport buttons.
1. [Add Switcher template](#step-5-replace-switchhtml-template-optional) (Strictly optional). If you want to customize the tooltip content, you need to copy and paste Page Builder's `switcher.html` template to your Admin theme, then make changes to it as needed.

{: .bs-callout-info }
Unlike the `view.xml` configuration (which merges with Page Builder's `view.xml` configuration), a custom `switcher.html` template completely overrides Page Builder's `switcher.html` template. This means you may need to update your theme's `switcher.html` with any new template features or bindings introduced in future Page Builder versions of the `switcher.html` template to ensure that nothing breaks.

### Step 1: Create an Admin theme

To create and apply an Admin theme, follow the instructions described here:

-  [Create an Admin theme]({{ site.baseurl }}/guides/v2.4/frontend-dev-guide/themes/admin_theme_create.html).
-  [Apply an Admin theme]({{ site.baseurl }}/guides/v2.4/frontend-dev-guide/themes/admin_theme_apply.html).

Your Admin theme and module should have directory and file structures similar to these:

![Viewport icons](../images/pagebuilder-admin-viewport-theme-files.svg)

### Step 2: Add a `view.xml` file

As with other XML files in Magento, you can add to and override the existing viewport configurations by adding a `view.xml` file to your Admin theme. In our example Admin theme, we modified the hidden viewports to provide button icons and a custom `max-width` (`540px`) to the `mobile-small` viewport.

```xml
<?xml version="1.0"?>
<view xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:Config/etc/view.xsd">
    <vars module="Magento_PageBuilder">
        <var name="breakpoints">
            <var name="tablet">
                <var name="label">Tablet</var>
                <var name="stage">true</var>
                <var name="default">false</var>
                <var name="class">mobile-switcher</var>
                <var name="icon">images/switcher/switcher-tablet.svg</var>
                <var name="media">only screen and (max-width: 1024px)</var>
            </var>
            <var name="mobile-small">
                <var name="label">Small Mobile</var>
                <var name="stage">true</var>
                <var name="class">mobile-switcher</var>
                <var name="icon">images/switcher/switcher-mobile-small.svg</var>
                <var name="media">only screen and (max-width: 540px)</var>
                <var name="conditions">
                    <var name="max-width">540px</var>
                </var>
            </var>
        </var>
    </vars>
</view>
```

You can add as many viewports/breakpoints as you want. For example, you could add a viewport called `mobile-tiny` that has a `max-width` of 300px by adding it to `breakpoints` as follows:

```xml
<var name="breakpoints">
    ...
    <var name="mobile-tiny">
        <var name="label">Tiny Mobile</var>
        <var name="stage">true</var>
        <var name="class">mobile-switcher</var>
        <var name="icon">images/switcher/switcher-mobile-tiny.svg</var>
        <var name="media">only screen and (max-width: 300px)</var>
        <var name="conditions">
            <var name="max-width">300px</var>
        </var>
    </var>
    ...
```

### Step 3: Add stage CSS Classes

To change the stage width of the selected viewport, you need to add CSS classes to one or more `.less` files. These classes must be named using the `[viewport-name]-viewport` convention. In our example Admin theme, we add two CSS classes to control the stage width when the `tablet` and `mobile-small` viewports are selected:

```scss
.tablet-viewport {
    &.pagebuilder-stage-wrapper {
        &.stage-content-snapshot,
        &.stage-full-screen {
            .pagebuilder-stage {
                .pagebuilder-canvas {
                    left: 50%;
                    transform: translateX(-50%);
                    max-width: 1024px;
                }
            }
        }
    }
}

.mobile-small-viewport {
    &.pagebuilder-stage-wrapper {
        &.stage-content-snapshot,
        &.stage-full-screen {
            .pagebuilder-stage {
                .pagebuilder-canvas {
                    left: 50%;
                    transform: translateX(-50%);
                    max-width: 540px;
                }
            }
        }
    }
}
```

If you were to add a new viewport called `mobile-tiny` as described above, you need to add a CSS class to control the stage width for that viewport as shown here:

```scss
.mobile-tiny-viewport {
    &.pagebuilder-stage-wrapper {
        &.stage-content-snapshot,
        &.stage-full-screen {
            .pagebuilder-stage {
                .pagebuilder-canvas {
                    left: 50%;
                    transform: translateX(-50%);
                    max-width: 300px;
                }
            }
        }
    }
}
```

### Step 4: Add SVG images

You will need to create your own SVG images for your additional viewports. To match Page Builder's existing viewport button icons, adhere to the following guidelines:

- 18px height for all new icons.
- 20px max-width for all new icons, narrower as needed.
- Transparent backgrounds.
- Solid white shapes.

For example, the source button icons used in the example Admin theme, are shown here:

![Viewport icons](../images/pagebuilder-viewport-icons.png)

Giving all icons a height of 18px ensures they align nicely within Page Builder's stage header.

### Step 5: Customize `switch.html` template (optional)

As mentioned previously, you can replace Page Builder's `switcher.html` template if you want to change the tooltip language or add dynamic tooltips based on the viewport selected. Both motives are shown here in the `switcher.html` file included in the Admin theme example:

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
            <span translate="'Display your content for '"/><text args="name"/><br/>
            <span if="name !== 'desktop'" translate="'Width'"/>
            <span if="name === 'desktop'" translate="'Minimum Width'"/>:
            <text args="Object.values($parent.viewports[name].conditions)[0]"/>
        </span>
    </span>
</each>
```

In this example, we made two trivial changes to a copy of Page Builder's `switcher.html` template to show the process so that you will know how to make more significant changes as needed:

- **Line 12**—We changed one word of the text description.
- **Lines 13-14**— We added Knockout conditional `span` tags to display different tooltip content ("Width" vs. "Minimum Width") based on the selected viewport (`desktop` vs. others).

## Summary

In this topic you learned how to add more viewports to Page Builder's stage header so that end users can preview how their content will look a different widths on the storefront. To learn more about how to use these viewports to customize your content types, see [How to use viewports](how-to-use-viewports.md).
