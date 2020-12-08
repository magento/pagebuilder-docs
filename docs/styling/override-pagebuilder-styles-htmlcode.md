# Override Page Builder styles with HTML Code

To quickly develop and test custom CSS overrides before moving them to your modules and themes, you can drag an HTML Code content type to the page you are developing, then add your custom CSS styles to a `<style>` block in the WYSIWYG editor.

![HTML Code styling during development](../images/htmlcode-styling-during-dev.png)

Using HTML Code in this way creates an internal stylesheet (on the page) that overrides any same-specificity CSS defined in the external stylesheets of your theme and module. This can be handy when you want to test changes for existing theme and module styles without having to recompile `.less` files.

## Steps for adding CSS to modules and themes

1. Create one of the following directory structures within your own Page Builder module or Magento theme.

    ![Stylesheet file structure](../images/stylesheet-file-structure.png)

    This module directory structure shows only the `frontend` area of a Page Builder module. However, the same directory structure applies to the `adminhtml` area when you want to style the content type for the Admin stage view.

2. Add a `.less` file named `_module.less` to the `web/css/source/` directory, as shown in step 1.

    By convention, Magento merges the CSS/LESS imported in `_module.less` with all the other CSS in the `pub/static` directory: `styles-m.css` and `styles-l.css`.

3. Add your custom CSS rulesets to your `.less` files. The simple ruleset below is for a custom content type named `vendor_modulename`:

    ```css
    #html-body [data-content-type='vendor_modulename'].my-css-class {
        display: flex;
        justify-content: center;
        align-items: center;
        color: blue;
    }
    ```

    In this example, we use the `#html-body` id from the `<body>` element of all Magento storefront pages, coupled with an attribute (`data-content-type`) that targets our custom content type, and finally your own CSS class. The combination of the id, attribute, and class increases the CSS specificity to 1-2-0, high enough to override Page Builder's CSS specificity of 1-1-0.

4. Add your custom CSS class name to the CSS Classes field for the content type. This ensures that Page Builder includes your CSS class name in the DOM of the content type.

    ![Add CSS class to content type](../images/css-classes-field.svg)

That's it. The browser does the rest, resulting in a clean override of Page Builder's native styling.