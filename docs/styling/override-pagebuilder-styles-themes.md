# Override Page Builder styles using themes

The best place to override Page Builder's default styles is within Admin and frontend themes. Magento adds theme styles to the `styles.css` output after the styles from modules. This action ensures that theme styles take precedence over the same styles in modules. It's also why you want to use Magento themes to style your Page Builder content whenever possible.

In this topic, you will learn how to use Admin and frontend themes to override Page Builder's default admin and storefront styles, respectively.

## Use Admin themes

To override Page Builder styles in the Admin, you need to:

1. Create or modify an Admin theme.
2. Apply the Admin theme to a module.

### Step 1: Create or modify an Admin theme

The following instructions refer only to the specifics of overriding Page Builder Admin styles. To learn more on creating Admin themes in general, see [Create an Admin theme]({{ site.baseurl }}/guides/v2.4/frontend-dev-guide/themes/admin_theme_create.html).

Your overriding Admin theme should have a directory and file structure similar to the following:

```terminal
app/design/adminhtml/VendorName/ThemeName/web/css/source/pagebuilder/<specific overrides>
app/design/adminhtml/VendorName/ThemeName/web/css/source/_module.less
```

![Page Builder admin theme files](../images/pagebuilder-admin-theme-files.svg)
_Admin theme directory structure_

The key takeaways are numbered in the image and described as follows:

1. **Content types**. Organize your overriding styles according to the Page Builder content-type names you want to override. In this example, we added a `pagebuilder` directory in `source` where we have added `heading` and `products` directories for the content types we want to override.

2. **Overriding stylesheets**. Name your overriding `.less` files to match the appearances of the content types you are overriding. In this example, the `heading` content type has one appearance: `default`. However, the `products` content type has two appearances `default` and `carousel`, so we create one `.less` file for each. This naming convention helps you find your overriding styles later when you need to update them.

3. **Import files**. Include an `_import.less` file for each content type directory. This file should only contain `@import` statements for all the overriding files in the directory. Using import files like this helps keep your changes closer to where they occur. In our example, the `_import.less` file for our `products` content type contains two imports:

    ```scss
    @import '_default.less';
    @import '_carousel.less';
    ```

4. **_module.less**. The `_module.less` file is required and must be added directly to your admin theme's `source` directory. Magento uses this file to add your admin styles to the `pub/static/adminhtml` output, where they can override default admin styles, including Page Builder's default content-type styles. Like the `_import.less` files, the `_module.less` file should only contain `@import` statements. In our example, our `_module.less` contains two imports:

    ```scss
    @import 'pagebuilder/heading/_import.less';
    @import 'pagebuilder/products/_import.less';
    ```

### Step 2: Apply the Admin theme to a module

The following instructions are specific to applying an Admin theme to a custom Page Builder module. To learn more on applying Admin themes to modules in general, see [Apply an Admin theme]({{ site.baseurl }}/guides/v2.4/frontend-dev-guide/themes/admin_theme_apply.html).

**To apply your Admin theme to a module**:

1. Create a `di.xml` file (in your module's `etc` directory) that references your Admin theme. The following example adds the `VendorName/ThemeName` Admin theme we created in step 1:

    ```xml
    <?xml version="1.0"?>
    <config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:ObjectManager/etc/config.xsd">

        <!-- Admin theme -->
        <type name="Magento\Theme\Model\View\Design">
            <arguments>
                <argument name="themes" xsi:type="array">
                    <item name="adminhtml" xsi:type="string">VendorName/ThemeName</item>
                </argument>
            </arguments>
        </type>

    </config>
    ```

2. Upgrade your module, clean cache, and reload the Admin in the browser:

    ```bash
    bin/magento setup:upgrade
    ```

    ```bash
    bin/magento cache:clean
    ```

## Use frontend themes

To override Page Builder styles on the storefront, you need to:

1. Create or modify a frontend theme.
2. Apply the frontend theme to a store view or page.

### Step 1: Create or modify a frontend theme

The following instructions refer only to the specifics of overriding Page Builder frontend styles. To learn more on creating frontend themes in general, see [Create a new storefront theme]({{ site.baseurl }}/guides/v2.4/frontend-dev-guide/themes/theme-create.html).

Your overriding frontend theme should have a directory and file structure similar to the following:

```terminal
app/design/frontend/VendorName/ThemeName/web/css/source/pagebuilder/<specific overrides>
app/design/frontend/VendorName/ThemeName/web/css/source/_extend.less
```

![Page Builder frontend theme files](../images/pagebuilder-frontend-theme-files.svg)
_Frontend theme directory structure_

The first three numbered callouts (1, 2, 3) in the theme directory structure are identical to those described for the [admin theme](#admin-themes). The only difference is that the `_module.less` file should be named `_extend.less`.

4. **_extend.less**. The `_extend.less` file is required and must be added directly to your frontend theme's `source` directory. Magento uses this file to add your frontend styles to the `pub/static/frontend` output in a location within the `styles-m.css` where they override (instead of replace) the default frontend styles, including Page Builder's default content-type styles. Like the `_import.less` files, the `_extend.less` file should only contain `@import` statements. In our example, our `_extend.less` contains the same two imports as seen in `module.less`:

    ```scss
    @import 'pagebuilder/heading/_import.less';
    @import 'pagebuilder/products/_import.less';
    ```

### Step 2: Apply the frontend theme

The following instructions are here as a **reminder** to apply your frontend theme to a store view or page. If you forget, you won't see your style overrides in the storefront, and you will wonder why. To learn more on applying themes in general, see [[Apply a storefront theme]({{ site.baseurl }}/guides/v2.4/frontend-dev-guide/themes/theme-apply.html).

**To apply your frontend theme to a store view**:

Navigate to Content > Design > Configuration and edit the store view where you can apply your frontend theme as the default theme:

![Set default theme on store view](../images/theme-default-setting-admin.svg)
_Set default frontend theme for store view_

**To apply your frontend theme to a page**:

During development, it's easy to apply your frontend theme to a single page for testing. But equally easy to forget to switch your page to that theme. Consider this your reminder.

On any CMS page, scroll to the Design section at the bottom, and select your theme from the New Theme selector.

![Set theme for page](../images/theme-page-setting-admin.svg)
_Set frontend theme for page_

## More about themes

For additional information on overriding styles using themes, see [Simple ways to customize a theme's styles]({{ site.baseurl }}/guides/v2.4/frontend-dev-guide/css-guide/css_quick_guide_approach.html).
