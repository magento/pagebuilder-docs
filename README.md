# Page Builder developer documentation

This project contains the source code of the Page Builder documentation for Adobe Commerce and Magento Open Source developer documentation website.

> **Important update**
>
> Adobe Commerce and Magento Open Source 2.4.x documentation has been migrated to Adobe sites. See our new landing pages to access the most current information.
>
>[Adobe Commerce Developer Documentation](https://developer.adobe.com/commerce/docs/) (Adobe Developer site)—Develop, customize, integrate, extend, and use advanced capabilities
>
>[Adobe Commerce Documentation](https://experienceleague.adobe.com/docs/commerce.html) (Adobe Experience League)—Plan, implement, operate, upgrade, and maintain your Commerce projects
>
> Some content was consolidated or moved to different guides. If you have trouble finding a topic, see the [Migrated topics](https://commerce-docs.github.io/devdocs-archive/migrated-topics.html).
>
> We welcome contributions to migrated content! You can find similar links to GitHub on the Adobe sites.

## Build the docs

To build this repo locally:

1. Clone the [magento/devdocs](https://github.com/magento/devdocs) repo.

   We use custom automation in the magento/devdocs repo to clone and build MBI docs. This automation will not work if you do not first clone the magento/devdocs repo.

1. Initialize the PB docs repo using rake:

   ```shell
   rake init
   ```

1. Build the site:

   ```shell
   rake preview
   ```

## Archived docs

To view the archived documentation, see <https://commerce-docs.github.io/devdocs-archive/>.
