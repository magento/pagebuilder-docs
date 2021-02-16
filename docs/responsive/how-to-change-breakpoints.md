# How to change responsive breakpoints

By default, Page Builder defines four breakpoints in the `Magento/PageBuilder/etc/view.xml` file: `desktop`, `tablet`, `mobile`, and `mobile-small`. The default values for these breakpoints can be expressed in the following media queries:

-  desktop: `@media only screen and (min-width: 1024px)`
-  tablet: `@media only screen and (max-width: 1024px) and (min-width: 768px)`
-  mobile: `@media only screen and (max-width: 768px) and (min-width: 640px)`
-  mobile-small: `@media only screen and (max-width: 640px)`

To change these breakpoints to meet your own responsive specifications, you need to add a `view.xml` file to your module or Admin theme, then make your changes there. Steps follow.

## Steps for changing breakpoints

1. [Create an overriding view.xml file](#step-1-create-an-overriding-viewxml-file).
1. [Change the min- and max-widths](#step-2-change-the-min--and-max-widths). These breakpoint `conditions` set the range of breakpoints for your media queries.
1. [Change stage media queries to match](#step-3-change-stage-media-queries-to-match). The stage media query must match your `min-` and `max-widths`.
1. [Change your frontend media queries to match](#step-4-change-your-frontend-media-queries-to-match). The media queries in your frontend stylesheets should match your `view.xml` changes.

### Step 1: Create an overriding view.xml file

Start by copying Page Builder's `view.xml` file to your module or Admin theme's `etc` directory: `Vendor/Module/etc/view.xml`.

### Step 2: Change the min- and max-widths

Page Builder uses the `max-width` and `min-width` (defined the `conditions` node) to build and apply media queries to your content. To change the default widths, simply change the pixel values to match your own breakpoint values.

For example, to change Page Builder's `mobile` and `mobile-small` breakpoints, you would change their `min-` and `max-widths`, as shown here:

```xml
<var name="mobile">
    <var name="label">Mobile</var>
    <var name="stage">true</var>
    <var name="class">mobile-switcher</var>
    <var name="icon">Magento_PageBuilder::css/images/switcher/switcher-mobile.svg</var>
    <var name="media">only screen and (max-width: 768px)</var>
    <var name="conditions">
        <var name="max-width">768px</var>
        <var name="min-width">568px</var>
    </var>
</var>
<var name="mobile-small">
    <var name="conditions">
        <var name="max-width">568px</var>
    </var>
</var>
```

### Step 3: Change stage media queries to match

Changing the `media` query for a breakpoint is only required when the breakpoint is also used to create a stage viewport, which is true for the `mobile` breakpoint. As described in [Page Builder viewports](viewports-and-breakpoints.md#viewxml), Page Builder uses the `media` query to define and apply breakpoint-specific values to properties of your content types.

### Step 4: Change your frontend media queries to match





By default, when you configure Page Builder to render a background image for a container, it uses a mobile image when the container's width is equal to or less than 768px. Page Builder defines this mobile breakpoint in the `Magento_PageBuilder/etc/view.xml` file, as shown here:

```xml
<?xml version="1.0"?>
<view xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:Config/etc/view.xsd">
...
<vars module="Magento_PageBuilder">
    <var name="breakpoints">
        <var name="mobile">
            <var name="conditions">
                <var name="max-width">768px</var>
            </var>
        </var>
    </var>
</vars>
```

## Changing the mobile breakpoint

If your custom theme also uses this max-width breakpoint for your mobile layout, no changes are required.

However, if your custom theme uses a different mobile breakpoint, you need to overwrite the default breakpoint by either:

1. Adding the theme's breakpoint to the theme's `view.xml` file.
2. Adding a `view.xml` file in the `etc` directory of your content type as shown here:

    ![product conditions](../images/how-to-change-breakpoint.png "Add view.xml file")

In either case, if your theme uses a mobile breakpoint `max-width` of `600px`, you would add the following markup to the `view.xml` file:

```xml
<?xml version="1.0"?>
<view xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:Config/etc/view.xsd">
    <vars module="Magento_PageBuilder">
        <var name="breakpoints">
            <var name="mobile">
                <var name="conditions">
                    <var name="max-width">600px</var>
                </var>
            </var>
        </var>
    </vars>
</view>
```

This directs Page Builder to use this mobile breakpoint instead of its default breakpoint of 768px. You can add other responsive breakpoints from your custom theme in the same way.
