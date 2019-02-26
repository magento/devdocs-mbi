# MBI documentation

This repo contains the source files for the Magento Business Intelligence (MBI) developer documentation.

You have two options for building this repo locally:

## Rake + magento/devdocs

Clone the [magento/devdocs](https://github.com/magento/devdocs) repo and run the `<TBD>` rake task.

## CLone and copy

1. Clone the magento-devdocs/doc-starter repo.

   ```shell
   git clone git@github.com:magento-devdocs/doc-starter.git
   ```

1. Clone the magento-devdocs/devdocs-mbi repo.

   ```shell
   git clone git@github.com:magento-devdocs/devdocs-mbi.git
   ```

1. Copy the MBI docs to the `src/` directory in the starter project.

   ```shell
   cp -r ~/devdocs-mbi/docs/ ~/git/magento-devdocs/doc-starter/src/
   ```

1. Add MBI docs to the YAML navigation file in the starter project (`src/_data/main.yml`).

1. Install dependencies.
  
   ```shell
   npm install
   ```

1. Build the docs.

   ```shell
   npm start
   ```
