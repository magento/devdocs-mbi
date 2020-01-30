# MBI developer documentation

Welcome! This repo contains the source files for the Magento Business Intelligence (MBI) developer documentation. These files are available under the OSL-3.0 license.

## Contributors

Our goal is to provide the Magento community with comprehensive and quality technical documentation. We believe that to accomplish that goal we need experts from the community to share their knowledge with us and each other. We are thankful to all of our contributors for improving Magento documentation.

Check out our [Contribution Guide](https://github.com/magento/devdocs-mbi/blob/master/.github/CONTRIBUTING.md) and start contributing today!

## Build MBI docs

To build this repo locally:

1. Clone the [magento/devdocs](https://github.com/magento/devdocs) repo.

   We use custom automation in the magento/devdocs repo to clone and build MBI docs. This automation will not work if you do not first clone the magento/devdocs repo.

1. Initialize the MBI docs repo using rake:

   ```shell
   rake init
   ```

1. Build the site:

   ```shell
   rake preview
   ```
