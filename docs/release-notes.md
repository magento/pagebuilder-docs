# Release notes for Page Builder

The following updates describe the latest improvements to Page Builder.

The release notes include:

-  {:.new}New features
-  {:.fix}Fixes and improvements

## 1.6.0 for Magento Commerce 2.4.2

-  {:.new}<!-- Issue 558 -->**New Page Builder styling**. Page Builder made massive improvements to how it styles content. The changes now make it easy to create simple CSS selectors to override Page Builder's existing content type styles. These changes make it possible to create Page Builder themes with rich responsiveness for all content types.

-  {:.new}<!-- Issue 429 -->**Row container now optional**. You can now create content in Page Builder without requiring a Row container. In addition to the Row, the following content types can now be dragged and dropped directly on the stage: HTML, Block, Dynamic Block, Columns, and Tabs.

-  {:.new}<!-- Issue 636 -->**New responsive viewport switcher**. You can now preview your Page Builder content at different device widths (breakpoints) using the new Admin viewport switcher buttons above the stage. The Admin viewport switcher simulates how your content will be styled when displayed on the storefront using different devices.

-  {:.new}<!-- Issue 637 -->**New breakpoint-specific form fields**. Content type field values can now be set to specific viewport breakpoints. Icon indicators next to the fields show which viewport the content type's field value is applied to.

-  {:.new}<!-- Issue 559 -->**Removed predefined form field entries**. Advanced form settings for margins, paddings, and border-related properties (border, border width, and border radius) are now set as `default`, so that values can be defined by a module's CSS.

-  {:.new}<!-- Issue 635, 557,  -->**Added stage spacing**. Rows and Columns now provide extra spacing affordances around contained content types on the stage, but not on the storefront. The extra stage spacing makes it easier to access the mouseover option menus for both the container and contained content types.

-  {:.fix}<!-- MC-37348 -->**Fixed auto-scrolling to reviews**. Auto-scrolling to product reviews on the storefront is now fixed.

-  {:.fix}<!-- MC-36227 -->**Fixed cropped content**. Lengthy category and product descriptions are no longer cropped.

-  {:.fix}<!-- MC-35402 -->**Fixed full-width, full-bleed Category content**. Category content can now be displayed in a full-width or full-bleed layout.

-  {:.fix}<!-- MC-37989 -->**Security enhancements**.

## 1.5.1 for Magento Commerce 2.4.1-p1

-  {:.fix}<!-- PB-1140, MC-38812 -->**Error popup.** Fixed an issue in which Page Builder displayed an error popup when adding Catalog subcategories in the Admin.

## 1.5.0 for Magento Commerce 2.4.1

-  {:.new}<!-- Issue 510, 511, 512, 513 -->**Immersive, full-screen editing.** Editing Page Builder content is now full-screen only for all areas controlled by Page Builder. This includes CMS pages, Product and Category pages, Blocks, and Dynamic Blocks. Full-screen editing puts the focus on your content and provides a view that better matches the user experience on the storefront.

-  {:.new}<!-- Issue 544 -->**Page Builder content previews.** By default, Page Builder now provides content previews not only for CMS pages, Blocks, and Dynamic Blocks, but for Product and Category pages as well. You can configure this feature to be on or off for Product and Category pages using the new Page Builder Content Preview setting, accessed in Stores > Configuration > Content Management > Advanced Content Tools.

-  {:.new}<!-- Issue 543 -->**Improved access to Product short descriptions.** By default, a Product's short description is now displayed before the longer description. This not only matches the order they appear on the storefront, but also prevents the need to scroll through the longer description's content to get access to the short description.

-  {:.new}<!-- Issue 419 -->**Prevented nested links in Banners and Slides.** Adding links to both the content and the outer elements of Banners and Slides created nested links which did not display or behave correctly on the storefront. To resolve the issue, we no longer allow links to be added to *both* the Message Text area and the Link attribute for Banners and Slides.

-  {:.fix}<!-- Issue 421 --> Fixed issue where setting empty border radius on a Tab Item caused errors and broke Tab Item content.

-  {:.fix}<!-- Issue 422 --> Fixed issue where adding certain combinations of conditions to the Products Condition filter caused errors that prevented saving the Products form.

-  {:.fix}<!-- Issue 425 -->Fixed issue in which the Slider content type did not behave correctly when the Infinite Loop setting was disabled.

-  {:.fix}<!-- Issue 426 -->Fixed the Minimum Advertised Price so that it now works in Page Builder.

-  {:.fix}<!-- Issue 436 -->Fixed the issue in Safari and IE 11 that prevented dragging and dropping an Image to the upload area in Banners and Slides.

-  {:.fix}<!-- Issue 525 -->Fixed the issue in which the Slider content type was not responsive in Firefox.

-  {:.fix}<!-- Issue 567 -->Fixed the issue where the widget configuration was creating incorrect selectors in the DOM for the different appearances.

-  {:.fix}<!-- Issue 609 -->Fixed the issue in which the Header toolbar hid the content type's option menu, which prevented access to the form and other options.

-  {:.fix}<!-- Issue 612, 613 -->Fixed issues where Sliders and multiple rows using parallax backgrounds would not render correctly after toggling the full-screen mode several times.

-  {:.fix}<!--MC-35220-->Fixed the issue where trying to copy the content from a Heading content type to a Text content type prevented Page Builder from saving.

-  {:.fix}<!-- MC-34620 -->Fixed the Text content type to correctly handle and save non-Latin-1 characters.

-  {:.fix}<!-- MC-34679 -->Fixed Page Builder CSS styles that caused Safari 13.1 to incorrectly render the Luma theme's site menu for small view ports and mobile screens.

-  {:.fix}<!-- MC-34848 -->Fixed the HTML content type to correctly display embedded widgets like "Order by SKU" on the Storefront.

## **1.4.1** for Magento Commerce 2.4.0-p1

-  {:.new}<!-- PB-590 --> Upgraded to TinyMCE 4. Removed TinyMCE 3 to improve security.

## **1.4.0** for Magento Commerce 2.4.0

-  {:.new}<!-- PB-494 -->Added support for PHP 7.4.

-  {:.fix}<!-- MC-31247 -->Fixed an issue where the Products content type did not show configurable products when the condition was set to price.

-  {:.fix}<!-- PB-179 -->Fixed the Products alignment configuration to position only the product container itself, not the contents of the product container, such as the product name, price, buttons, images, and other elements.

-  {:.fix}<!-- MC-33556 -->Performance improvements.

-  {:.fix}Security enhancements.


## **1.3.3-p1** for Magento Commerce 2.3.6-p1

-  {:.fix}Security enhancements.

## **1.3.3** for Magento Commerce 2.3.6

-  {:.fix}<!-- MC-32766 -->Fixed the Text content type to correctly save the Magento variable directives added to html attributes.
-  {:.fix}<!-- MC-34590 -->Fixed the Text content type to correctly handle and save non-Latin-1 characters.
-  {:.fix}<!-- MC-34677 -->Fixed Page Builder CSS styles that caused Safari 13.1 to incorrectly render the Luma theme's site menu for small view ports and mobile screens.
-  {:.fix}<!-- MC-34846 -->Fixed the HTML content type to correctly display embedded widgets like "Order by SKU" on the Storefront.
-  {:.fix}Fixed an error that prevented users from saving content-type forms in some cases. The error ("Page Builder was rendering for 5 seconds without releasing locks") caused the loader icon to spin indefinitely after trying to save a form.

## **1.3.2** for Magento Commerce 2.3.5-p2

-  {:.new}Security enhancements.

## **1.3.1** for Magento Commerce 2.3.5-p1

This version of Page Builder is just a version-number update for Magento 2.3.5-p1. All features described for version 1.3.0 apply to this version as well.

## **1.3.0** for Magento Commerce 2.3.5

-  {:.new}**Templates**<br/>
   Page Builder now has templates that can be created from existing content and applied to new content areas. Page Builder templates save both content and layouts of existing pages, blocks, dynamic blocks, product attributes, and category descriptions. For example, you can save an existing Page Builder CMS page as a template and then apply that template (with all its content and layouts) to quickly create new CMS Pages for your site. This new feature is documented here: [Templates](https://docs.magento.com/m2/ee/user_guide/cms/page-builder-templates.html).

-  {:.new}**Video Backgrounds for Rows, Banners, and Sliders**<br/>
   Page Builder Rows, Banners, and Sliders now have the option to use videos for their backgrounds. These new features are documented here: [Rows](https://docs.magento.com/m2/ee/user_guide/cms/page-builder-layout-row.html), [Banners](https://docs.magento.com/m2/ee/user_guide/cms/page-builder-media-banner.html), [Sliders](https://docs.magento.com/m2/ee/user_guide/cms/page-builder-media-slider.html).

-  {:.new}**Video support additions and enhancements**<br/>
   Page Builder now supports a wider variety of video formats. In addition to YouTube and Vimeo videos, the Video content type and the video backgrounds now support video URL links to video formats like `.mp4` and other browser-supported formats. The Video content type also adds an autoplay feature. These new features are documented here: [Video content type](https://docs.magento.com/m2/ee/user_guide/cms/page-builder-media-video.html).

-  {:.new}**Full Height Rows, Banners, and Sliders**<br/>
   Page Builder Rows, Banners, and Sliders now have the option to set their heights to the full-height of the page using a number with any CSS unit (px, %, vh, em) or a calculation between units (100vh - 237px). These new features are documented here: [Rows](https://docs.magento.com/m2/ee/user_guide/cms/page-builder-layout-row.html), [Banners](https://docs.magento.com/m2/ee/user_guide/cms/page-builder-media-banner.html), [Sliders](https://docs.magento.com/m2/ee/user_guide/cms/page-builder-media-slider.html).

-  {:.new}**Content type upgrade library**<br/>
   We can now create new versions of Page Builder content types without introducing backward-incompatible issues with previous versions. Prior to this release, significant changes to content type configurations would create display and data-loss issues with previously saved Page Builder content types. Our new upgrade library eliminates these issues. We designed the library to upgrade previous versions of content types saved to the database so that they match the configuration changes in the new versions. Page Builder runs the upgrade library on native content types as needed for a new release. This ensures that the built-in Page Builder content types are always upgraded to match any changes we make to content types for a new release.

   {: .bs-callout .bs-callout-warning }
   **Please Note:** If you have created additional database entities for storing Page Builder content, you _must_ add those entities to your `etc/di.xml`. If you do not, the Page Builder content stored in your entity will not be updated, causing potential data-loss and display issues. For example, if you have created a blog entity that stores Page Builder content, you must add your blog entity to your `etc/di.xml` file as an `UpgradableEntitiesPool` type so that the upgrade library can update the Page Builder content types used in your blog. For more information and instructions on using the upgrade library, see: [How to use the content type upgrade library](https://devdocs.magento.com/page-builder/docs/how-to/how-to-use-upgrade-library.html){:data-proofer-ignore='true'}.

-  {:.new}**Documentation on adding new Appearances**<br/>
   Everything you need to know about [adding appearances](https://devdocs.magento.com/page-builder/docs/extending/how-to-add-appearances.html) for existing or custom content types.

**Various fixes**

-  {:.fix}<!-- PB-50 -->Fixed an issue where the TinyMCE menu for slide content appears underneath other content types if the parent container of the slide is duplicated.
-  {:.fix}<!-- PB-166 -->Updated Page Builder to implement destroy method to prevent memory leaks in some scenarios.
-  {:.fix}<!-- PB-170 -->Improved TinyMCE performance when multiple instances are used on the Admin stage.
-  {:.fix}<!-- PB-252 -->Fixed an issue in which the Dynamic Block content type does not render on the Admin stage if the top row is marked as hidden.
-  {:.fix}<!-- PB-273 -->Refined mouse-hover events on the Admin stage by removing a 200ms delay from various UI controls. This makes it easier to work with nested content items on the stage.
-  {:.fix}<!-- PB-294 -->Fixed an issue in which the currency symbol was being escaped improperly in the Product List widget within the Block/Dynamic Block on the Admin stage.
-  {:.fix}<!-- PB-296 -->Fixed an issue in which the product total on the Page Builder edit panel did not work for custom MSI stock products.
-  {:.fix}<!-- PB-317 -->Fixed an issue in which saving Page Builder content with background images on Microsoft Edge does not render those images on the storefront.
-  {:.fix}<!-- PB-390 -->Fixed an issue in which nested Page Builder content fails to save if users click the Save button before the page fully renders.
-  {:.fix}<!-- PB-418 -->Fixed exception error being throw in cron jobs due to Page Builder analytics.

## **1.2.2** for Magento Commerce 2.3.4-p2

-  {:.new}Security enhancements.

## **1.2.1** for Magento Commerce 2.3.4-p1

-  {:.new}Security enhancements.

## **1.2.0** for Magento Commerce 2.3.4

**Page Builder integration with PWA Studio**

-  {:.new}Added Page Builder content rendering to the Venia app in PWA Studio. Page Builder content can now be viewed within the PWA Studio Venia app. See the Page Builder documentation within [PWA Studio][] for all the information on this new feature.

**Products content type enhancements**

-  {:.new}<!-- PB-77, PB-173, PB-175 -->Added Product carousel. The Products content type now provides an option to display your products in a carousel / slider format, including several options to customize the carousel to your needs.
-  {:.new}<!-- PB-69 -->Added Product SKU sorting. The Products content type now provides an option to sort your products by SKU in the order you add them to a list within the Admin.
-  {:.new}<!-- PB-181 -->Added Product Category sorting. The Products content type now provides an option to sort your products by category _position_, displaying them in same order that they appear within your Magento Catalog.
-  {:.new}<!-- PB-107 -->Added Product selection totals. The Products content type Admin editor now displays the total number of products that match your product selection options.

**Various fixes**

-  {:.fix}<!-- PB-237 -->Security enhancements.
-  {:.fix}<!-- PB-41 -->Fixed searches within UI select components to make only one AJAX request per search term.
-  {:.fix}<!-- PB-76, PB-84-->Updated Product previews in the Admin to match the storefront, including the star rating, color, and size options of the product when relevant.
-  {:.fix}<!-- PB-169 -->Fixed an issue in which Page Builder could not be saved when Magento's JavaScript minification and bundling are enabled.
-  {:.fix}<!-- PB-241 -->Fixed the Admin previews of Products, Blocks, and Dynamic Blocks to render correctly on Magento installations that define different URLs for the Admin and the frontend.
-  {:.fix}<!-- PB-238 -->Fixed the Admin previews of Products, Blocks, and Dynamic Blocks to render correctly on Magento installations with B2B installed with the "Login Only" option enabled. Prior to this fix, the Page Builder preview would cause the page to redirect to the customer account login.
-  {:.fix}<!-- PB-239 -->Fixed a session error that can occur when previewing a large page in the Page Builder Admin.
-  {:.fix}<!-- PB-248 -->Updated Page Builder LESS styles to prevent storefront style duplication.

## **1.1.1** for Magento Commerce 2.3.3-p1

-  {:.new}Security enhancements.

## **1.1.0** for Magento Commerce 2.3.3

-  {:.new}<!-- MC-15250 -->Added explicit product sorting to the Products content type.

-  {:.new}<!-- MC-17823 -->Added buttons for inserting images, widgets, and variables in the HTML content type.

-  {:.fix}Improved Page Builder security.

-  {:.fix}<!-- MC-1805 -->Updated Page Builder to support PHP version 7.3.

-  {:.fix}<!-- MC-4137 -->Updated TinyMCE to version 4.9.5. This update, along with our additional improvements, fixed several TinyMCE inline editor issues:

   -  {:.fix}Variables, images, & image links now get added where the cursor is place.
   -  {:.fix}Tables and table cells can now be center aligned.
   -  {:.fix}Copy/paste now pastes content at the cursor's position.
   -  {:.fix}Links can now be applied to selected text.
   -  {:.fix}Bullets are now properly aligned.
   -  {:.fix}Changes within the inline editor can now be saved without first clicking outside the editor.

-  {:.fix}<!-- MC-3880 -->Fixed an issue in which the minimum height & vertical alignment was inconsistent between sections on the edit panel for each content type.

-  {:.fix}<!-- MC-14994 -->Fixed an issue in which the toolbar from the Heading content type was positioned incorrectly when first dropped on the stage.

-  {:.fix}<!-- MC-15742 -->Fixed hard-coded margins in both Slider and Video content types.

-  {:.fix}<!-- MC-16241 -->Fixed an issue in which the required asterisk symbol was displayed twice on form fields.

## **1.0.3** for Magento Commerce 2.3.2-p2

-  {:.new}Security enhancements.

## **1.0.2** for Magento Commerce 2.3.2-p1

-  {:.new}Security enhancements.

## **1.0.1** for Magento Commerce 2.3.2

-  {:.new}Ensures compatibility with Magento Commerce 2.3.2.

## **1.0.0** for Magento Commerce 2.3.1

-  {:.new}General availability release!

### Documentation

To learn more about Page Builder and Page Builder development:

-  For developers: [What is Page Builder?](https://devdocs.magento.com/page-builder/docs/index.html)
-  For end-users: [Page Builder User Guide](https://docs.magento.com/m2/ee/user_guide/cms/page-builder.html)

[PWA Studio]: https://magento.github.io/pwa-studio/
