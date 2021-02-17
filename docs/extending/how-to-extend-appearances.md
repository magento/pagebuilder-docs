## How to extend appearances

In this topic, you will learn how to add a new user-defined property to an existing content type. Specifically, we will add a `max-height` property to all the Banner appearances. Why? Because our customers [told us told us to drill sargent!](find clip)

Here's the scenario: Your customers use Banners to feature products on their site. But they don't like the fact that the Banner's grow to the height of the content they enter. So they want you to add a `max-height` field to all the Banner appearances. That way they can limit all their banners to the same height, and have their extra content scroll as needed.

![Page Builder Banner menu item](../images/extend-banner-menu.png){:width="815px" height="auto"}

## Steps for extending contents

These steps describe the basic process for adding new style properties to an existing content type:

1. [Create a Page Builder extension module](#step-1-create-a-page-builder-extension-module).
1. [Add your properties to elements](#step-2-add-new-properties-to-elements).
1. [Add your property fields to the form](#step-3-add-your-property-fields-to-the-form).
1. [Add admin and frontend styles](#step-4-add-admin-and-frontend-css) (as needed).

### Step 1: Create a Page Builder extension module

Use the [PB Modules CLI](https://github.com/magento-devdocs/pbmodules) to create the starting directory structure and files for a Banner extension. Instructions for using PB Modules can be found in the repo [`README.md`](https://github.com/magento-devdocs/pbmodules#pb-modules) file.

Navigate to your `<magento-instance-root>/app/code/` directory and run the following command:

```terminal
npx https://github.com/magento-devdocs/pbmodules.git
```

Extend the `Banner` and complete the remaining prompts. When finished, you should have a complete directory structure and all the files you need (plus extras) to start extending the Banner.

Before continuing, run `bin/magento setup:upgrade` to install and enable your module.

### Step 2: Add new properties to elements

Now we can add our `max-height` property to the right `element` in all the Banner appearances. But which `element`?

We know that the Banner's content is making the Banner grow. So we need to find the `element` node that controls text. And here it is:

```xml
<element name="content">
    <html name="message" preview_converter="Magento_PageBuilder/js/converter/html/directive"/>
</element>
```

The use of an `html` node confirms it. We didn't mention the `<html>` node earlier, but it allows the value for a field to be output as HTML. We can confirm this as the right element by looking at the `preview.html` and `master.html` templates for the HTML elements that are bound to the `content` element:

```html
<!-- master.html -->
<div attr="data.content.attributes"
     ko-style="data.content.style"
     css="data.content.css"
     html="data.content.html">
</div>

<!-- preview.html -->
<div if="isWysiwygSupported()"
     class="inline-wysiwyg"
     ko-style="data.content.style"
     css="data.content.css"
     attr="data.content.attributes"
     afterRender="afterRenderWysiwyg"
     contenteditable="true"
     event="mousedown: stopEvent, click: activateEditor, dblclick: handleDoubleClick">
</div>
```

Both templates have `<div>` elements with several attributes bound to `<element name="content">`, including the `html` attribute on the frontend template (`master.html`). We can safely say that this is right element for our `max-height` property.

Because `max-height` is an official CSS property, we will add it using the `style` node as follows:

```xml
<element name="content">
    <style name="max_height" source="max_height" converter="Magento_PageBuilder/js/converter/style/remove-px"/>
</element>
```

Attributes of the `style` node are described briefly here:

-  `style name` — name used to bind to the form field with the same name.
-  `style source` — name of the CSS property in snake_case. Page Builder changes `max_height` to `max-height` when writing it to the DOM.
-  `converter` — JavaScript function that converts internal property values to and from the DOM because DOM values are often not in the right format for internal processing.

{:.procedure}
To add the `max-width` property to the `content` element of each appearance,

1. Open your module's `banner.xml` file.
1. Remove the `preview` and `master` templates as well as the `main` element nodes from each appearance.
1. Add your new `content` element and `style` node to the `elements` node of each appearance.

When you are done, the `appearances` section of your `banner.xml` file should look like this:

```xml
<appearances>
    <appearance name="collage-left">
        <elements>
            <element name="content">
                <style name="max_height" source="max_height" converter="Magento_PageBuilder/js/converter/style/remove-px"/>
            </element>
        </elements>
    </appearance>
    <appearance name="collage-centered">
        <elements>
            <element name="content">
                <style name="max_height" source="max_height" converter="Magento_PageBuilder/js/converter/style/remove-px"/>
            </element>
        </elements>
    </appearance>
    <appearance name="collage-right">
        <elements>
            <element name="content">
                <style name="max_height" source="max_height" converter="Magento_PageBuilder/js/converter/style/remove-px"/>
            </element>
        </elements>
    </appearance>
    <appearance name="poster">
        <elements>
            <element name="content">
                <style name="max_height" source="max_height" converter="Magento_PageBuilder/js/converter/style/remove-px"/>
            </element>
        </elements>
    </appearance>
</appearances>
```

### Step 3: Add your property fields to the form

Before you add a field to the form of an existing content type, you need to decide where to add it. In other words, you need to pick a fieldset for your field. For our `max_height` field, it makes sense to add it below the Banner's existing `min_height` field, which is in the the `appearance_fieldset`.

In our example, we used the following markup for our `max_height` field:

```xml
<form xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Ui:etc/ui_configuration.xsd">
    <!-- Full Form: Magento/PageBuilder/view/adminhtml/ui_component/pagebuilder_banner_form.xml -->
    <fieldset name="appearance_fieldset">
        <field name="max_height" sortOrder="30" formElement="input">
            <argument name="data" xsi:type="array">
                <item name="config" xsi:type="array">
                    <item name="default" xsi:type="number">400</item>
                </item>
            </argument>
            <settings>
                <label translate="true">Maximum Height</label>
                <additionalClasses>
                    <class name="admin__field-small">true</class>
                </additionalClasses>
                <addAfter translate="true">px</addAfter>
                <dataType>text</dataType>
                <dataScope>max_height</dataScope>
                <validation>
                    <rule name="validate-number" xsi:type="boolean">true</rule>
                </validation>
            </settings>
        </field>
    </fieldset>
</form>
```

Explaining UI component form fields is beyond the scope of this topic, but a few brief descriptions of the most important nodes might help those of you who are not familiar with UI components. If you already know about UI components, feel free to skip this part.

| Elements   | Description                                                                                                                                                                                                                                                                                                                                                                                 |
|------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `fieldset` | The fieldset `name` should match the name of the fieldset from the Banner's form. The `appearance_fieldset` is common to all the content type forms and, by default, appears at the top of the forms using the `sortOrder` of 10. If you used [PB Modules](https://github.com/magento-devdocs/pbmodules/), the `fieldset` node names were copied from the Banner's form, so you're all set. |
| `field`    | The field `name` should match your `style` node's name in your `banner.xml` config file. Fields also have a `sortOrder` you can use to place your field above or below existing fields. The `formElement` for a field describes the HTML form type, such as input, checkbox, select, and more.                                                                                              |
| `config`   | Provides the initial configuration for the field, including the `default` value. We set our default `max_height` field to `400` (px).                                                                                                                                                                                                                                                       |
| `settings` | Provides the markup that gives your field a label, CSS styling, validation, and other properties as needed.                                                                                                                                                                                                                                                                                 |

### Step 4: Add admin and frontend CSS.

For this extension to work as expected, we need to apply a CSS `overflow: scroll` property to the `content` element in the DOM.

Open your module's Admin and frontend `_default.less` files (assuming you used PB Modules):

-  `adminhtml/web/css/source/content-type/banner/_default.less`
-  `frontend/web/css/source/content-type/banner/_default.less`

 Use Page Builder's [CSS selector override pattern](https://devdocs.magento.com/page-builder/docs/styling/how-to-override-pagebuilder-styles.html#css-selector-override-pattern) to target the `[data-element="content]` attribute in the DOM.

{:psa: .bs-callout-info }
 **Public Service Announcement**: If you don't know [how to override Page Builder styles](../styling/how-to-override-pagebuilder-styles.md), you need to stop reading this topic, go read that topic (as well as [how Page Builder styles content](../styling/how-pagebuilder-styles-content.md), then come back here and continue to the next paragraph. Seriously.

 Knowing how Page Builder styles its content and how to easily override those styles, will make your CSS selector life so easy that you'll rarely need to look at Page Builder's DOM output anymore. No more black-magic invocations to divine the tower of classes and elements needed to slay Page Builder's dragons of specificity. Your time is now. So arm yourself with knowledge, and win the specificity war! (Too much?)
 {: psa}

You can build your overriding CSS selectors just by knowing the configuration _names_ of the content type and `element` node you need to target in the DOM.

For our example, the configuration names for our content type and target element node are `banner` and `content`. Page Builder adds these as attributes (`data-* = "name"`) to the template's generated DOM nodes in the Admin and frontend pages. So all we need to do is start our selectors with `#html-body` followed by these two attributes:

```scss
#html-body [data-content-type='banner'] [data-element='content'] {
    overflow: scroll;
}
```

This selector has a specificity of `120`, more than we need to override any of Page Builder's styles. And we didn't even need to look at the DOM. We just need to know how Page Builder works.

The Less in our module's `_default.less` files looks like this:

```scss
// Targeting the DOM element in the Admin
#html-body [data-content-type='banner'] {
    [data-element='content'] {
        overflow: scroll;
    }
}

// Targeting the DOM element on the frontend
& when (@media-common = true) {
    #html-body [data-content-type='banner'] {
        [data-element='content'] {
            overflow: scroll;
        }
    }
}
```

### Testing your work

After adding your CSS/Less, run your Less transpiler, clean your cache (`bin/magento cache:clean`), drag a new Banner to the Admin stage, open the form editor, and take a look at your new field being rendered for every appearance in the form, similar to what's shown here:

![Appearance fieldset](../images/appearance-fieldset.png){:width="934px" height="auto"}

## Next steps

This topic walked you through the basic steps for extending existing appearances. Your next step is learning [How to add appearances](how-to-add-appearances.md). As you become more experienced working with appearances, you may be surprised at how often you can adapt Page Builder's existing content types to meet your customer's needs. Keep moving forward!
