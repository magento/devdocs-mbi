# MBI documentation

This repo contains the source files for the Magento Business Intelligence (MBI) developer documentation.

To build this repo locally:

1. Clone the [magento/devdocs](https://github.com/magento/devdocs) repo.
1. Initialize the MBI docs repo using rake.

   ```shell
   rake multirepo:add dir=mbi repo=git@github.com:magento/devdocs-mbi.git branch=master
   ```
