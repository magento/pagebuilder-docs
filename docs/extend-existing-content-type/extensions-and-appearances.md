# Introduction to extensions and appearances

Extending Page Builder's existing content types is the quickest way to customize Page Builder. And in many cases, it may be all you need to do to satisfy your customers.

Out of the box, Page Builder gives end users many ways to customize their content using the default form editors. But what if your end users want to create a Banner that has a different layout? Or maybe they want to change some properties of a content type that are not available out of the box? These are the use cases that beg you to extend an existing content type, rather than create a new one.

All content types provide end users with at least one layout with some style properties that end users can set. In Page Builder, we call these layouts _appearances_. Understanding appearances is essential to extending content types. The rest of this topic is dedicated to that goal.

## Understanding appearances

An **appearance** defines the layout and styles for a content type. Every content type has at least one appearance. Content types like the Banner provide users with four different appearances to choose from, all defined within the Banner's XML configuration file, `banner.xml`.

To help you understand the kind of changes you can make to appearances, let's look at a few nodes from the Banner's `collage-left` appearance in the `banner.xml` file. The following XML fragment is a condensed version of this appearance to help you focus the kind of changes you can make to an appearance.

```xml
<appearances>
    <appearance name="collage-left"
                preview_template="Magento_PageBuilder/content-type/banner/collage-left/preview"
                master_template="Magento_PageBuilder/content-type/banner/collage-left/master"
                reader="Magento_PageBuilder/js/master-format/read/configurable">
        <elements>
            <element name="wrapper">
                <style name="text_align" source="text_align"/>
                <attribute name="background_type" source="data-background-type"/>
                <css name="css_classes"/>
            </element>
        </elements>
    </appearance>
<appearances>
```

Even in a condensed form, appearance configurations can be confusing. A quick summary of the most important configuration nodes may help:

-  `appearance` — identifies an appearance configuration (by name) that all the HTML templates and nodes help define.
-  `preview_template` — path to the HTML template (`preview.html`) used to display the appearance in the Admin.
-  `master_template` — path to the HTML template (`master.html`) used to display the appearance on the storefront.
-  `element` — node type bound to an HTML element in the `preview.html` and `master.html` templates. The `element` provides its HTML element with the properties defined in the `style`, `attribute`, and `css` nodes.
-  `style` — adds a CSS property (like `text-align`) to the element.
-  `attribute` — adds a custom property (like `background_type`) to the element.
-  `css` — adds CSS classes to the element.

When you understand what these nodes are for, it's easier to group them into the two defining characteristics of appearances: layouts and styles.

### Appearance layouts

The layout for an appearance comes from the arrangement of HTML tags (`<div>`, `<a>`, `<button>`) in the templates. So if you need to change the layout, you start by changing the appearance templates:

-  `preview_template` — (`collage-left/preview.html`) for changing the appearance's layout in the Admin.
-  `master_template` — (`collage-left/master.html`) for changing the appearance's layout on the storefront.

### Appearance styles

The styles for an appearance come from the `element` nodes and all its subnodes. So if you need to change or add styles and properties to existing layouts, you start by changing and adding `style`, `attribute`, and `css` nodes to the existing `element` nodes:

-  `element` — for identifying the HTML element (in the templates) that you want to change using the `style`, `attribute`, and `css` nodes.
-  `style` — for changing or adding a CSS property (like `text-align`) to an element.
-  `attribute` — for changing or adding a custom property (like `background_type`) to an element.
-  `css` — for changing or adding CSS classes to an element.

For in-depth descriptions of the `style`, `attribute`, and `css` configuration nodes, take a look at the discussion section of [How to configure styling options](../styling/how-to-configure-styling-options.md#discussion).

## Next

The next topic provides a guided walk-through for [How to extend a content type](step-1-create-extension-module.md).
