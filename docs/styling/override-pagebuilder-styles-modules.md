# Override Page Builder styles using modules

When you are extending a Page Builder content type with a custom module, you need to add your overriding content type style to your custom module.

Overriding Page Builder default styles to override Page Builder's default styles is within Admin and frontend themes. Magento adds theme styles to the `styles.css` output after the styles from modules. This action ensures that theme styles take precedence over the same styles in modules. It's also why you want to use Magento themes to style your Page Builder content whenever possible.

Overriding Page Builder styles within your modules is also an option also an option applied to content types on the Admin stage, add or import your CSS styles to the `_module.less` file in your module's `adminhtml` area:

```terminal
/view/adminhtml/web/css/source/_module.less
```

To override Page Builder styles applied to content types displayed on a storefront, add or import your CSS styles to the `_module.less` file in your module's `frontend` area:

```terminal
 /view/frontend/web/css/source/_module.less
```
