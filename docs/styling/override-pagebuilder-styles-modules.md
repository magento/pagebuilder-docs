# Override Page Builder styles using modules

When you are creating a Page Builder module by either extending one of Page Builder's existing content types or creating your own custom content type, you will override Page Builder styles from within the module. The overriding CSS selector pattern is the same: `#html-body [attribute] class`.

// insert pattern graphic

Using these selector patterns in your modules makes them easy to override from a theme. In fact, you can use the same selectors in your themes because Magento adds theme styles _after_ module styles in the `styles.css`. CSS cascading rules do the rest. Browsers will apply the _last_ same-specificity CSS selectors (from your theme) to your module's content.

// insert graphic of Magento's process

## Overriding Admin styles

Overriding Page Builder styles on the Admin stage, add or import your CSS styles to the `_module.less` file in your module's `adminhtml` area:

```terminal
/view/adminhtml/web/css/source/_module.less
```

## Overriding frontend styles

To override Page Builder styles applied to content types displayed on a storefront, add or import your CSS styles to the `_module.less` file in your module's `frontend` area:

```terminal
 /view/frontend/web/css/source/_module.less
```

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
