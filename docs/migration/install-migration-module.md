---
title: Install the migration module
redirect_to: https://developer.adobe.com/commerce/frontend-core/page-builder/migration/install-migration-module
status: migrated
---

# Install the migration module

The migration module is a composer package hosted within our repository. This package is only available for those with Commerce access keys.

{: .bs-callout-warning }
We recommend using the module in a development environment before deploying it to the production environment. We also recommend creating sufficient backups before completing any form of content migration.

## Prerequisites

Before installing the migration module, you need to prepare the environment you intend to migrate:

-  **Make a backup of your current database.**

-  Remove BlueFoot from your current Magento instance: `composer remove gene/bluefoot`.

  BlueFoot does not contain uninstall scripts, but we do preserve your data on uninstall.

-  Upgrade to Magento Commerce 2.3.1 (which includes Page Builder).

  Please see our [Command-line upgrade]({{ site.baseurl }}/guides/v2.3/comp-mgr/cli/cli-upgrade.html) instructions on how to complete this.
  Page Builder itself does not convert any of your content. We preserve your existing BlueFoot content when we install Page Builder.

## Composer installation

To install the migration module:

1. Navigate to the root directory of your Magento 2.3.1 installation.
1. Use the following composer command:

   ```bash
   composer require magento/module-page-builder-data-migration
   ```

1. [Disable the default migration-on-deployment feature](#disable-migration-on-deployment).

   {: .bs-callout-warning }
   This step is critical for migration development work. It disables the default migration module behavior that migrates your content as part of the deployment using `setup:upgrade`. We made this the default behavior so that deployment to production is easy. But during development, you need to turn it off so that you do not run your migrations accidentally, before you have made strategic changes to your migration code, or backups to your database.

After completing these steps, the data migration source code can be found within the `vendor/magento` directory with the other magento modules.

## GitHub installation

{: .bs-callout-info}
This installation option is for those who are part of the Magento organization on GitHub and want easier access to the migration source code during migration development work.

To install the migration module from the GitHub repo ([magento2/magento2-page-builder-data-migration](<https://github.com/magento/magento2-page-builder-data-migration>){:data-proofer-ignore='true'}):

1. Navigate into the directory above your Magento 2 installation.

1. Clone the `magento/magento2-page-builder-data-migration` repository using the following command:

   ```bash
   git clone git@github.com:magento/magento2-page-builder-data-migration.git
   ```

1. Symlink the `magento2-page-builder-data-migration` into your Magento installation:

   ```bash
   php <magento-root-directory>/dev/tools/build-ee.php --command=link --ce-source <magento-root-directory> --ee-source magento2-page-builder-data-migration
   ```

1. [Disable the default migration-on-deployment feature](#disable-migration-on-deployment).

   {: .bs-callout-warning }
   This step is critical for migration development work. It disables the default migration module behavior that migrates your content as part of the deployment using `setup:upgrade`. We made this the default behavior so that deployment to production is easy. But during development, you need to turn it off so that you do not run your migrations accidentally, before you have made strategic changes to your migration code, or backups to your database.

After completing these steps, the data migration source code should sit alongside the root directory of Magento 2.3.1 installation, with the symlinks placed within. If your directory structure differs, adjust your symlink paths as needed.

## Disable migration on deployment

This step disables the migration module feature that migrates your content as part of the deployment using `setup:upgrade`. During development, you often need to customize your migration code _before_ running migrations to ensure that your content migrates to Page Builder as intended. So it is best to turn off this auto-migration feature and use the explicit migration command as described in [running the migration module](run-migration-module.md).

To disable migration on deployment, run the following queries on the `setup_module` and `patch_list` tables in your database. These query values indicate that the migration module has already been installed, which prevents Magento from applying the patch and auto-running the migration before you are ready.

```sql
INSERT INTO `setup_module` (`module`, `schema_version`, `data_version`)
VALUES
    ('Magento_PageBuilderDataMigration', '1.0.0', '1.0.0');

INSERT INTO `patch_list` (`patch_name`)
VALUES
    ('Magento\\PageBuilderDataMigration\\Setup\\Patch\\Data\\MigrateToPageBuilder');
```

## Modifying migration source code

We do not plan on releasing any updates of the `PageBuilderDataMigration` module. This means you can modify the migration source code as needed to suite your needs.

## Next Steps

[Run the migration module](run-migration-module.md).
