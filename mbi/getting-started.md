---
group: mbi
title: Magento BI Import API Getting Started
functional_areas:
  - Getting started
  - System
  - Setup
---

There are four ways to get your data into Magento BI:
* connect a database
* connect a SaaS integration
* upload a CSV file
* use the Import API.

This section describes how to use the Import API.

Before you begin, you should checkout the <a href="{{ page.baseurl }}/mbi/libraries.html">libraries</a> section to install your chosen client library. Here are some quick examples that can get you started with the Import API.


## Create a Users Table {#create-users-table}

Your most important table in RJMetrics is your Users table. In your app, you probably have a `user` object with some data like `id`, `email`, and `acquisition_source`.

Let's walk through how you would push this data to the Import API.

First, let's lay out a template for pushing the data. We'll make sure the client is NEW LINK TO IMPORT API WILL GO HERE:`authenticated <api-authentication>` and prepare to push the data to the Import API.

### code block will go here

Next, we want to actually push the data. Let's create a new function to do the dirty work of syncing the new data. Check out the NEW LINK TO API REF WILL GO HERE: `upsert documentation <api-upsert>` to learn about the specifics on data that can be pushed to the API.

### code block will go here

Putting it all together, we incorporate the `syncUser` function into our original script.

### code block will go here

This example is included with the <a href="{{ page.baseurl }}/mbi/libraries.html">client libraries</a> or can be [downloaded from github](http://www.github.com/rjmetrics).

### code block will go here

## Create an Orders Table {#create-an-orders-table}

Now, let's create an orders table with the following fields: `id`, `user_id`, `value` and `sku`.

We'll need a new function to push the order object:

### code block will go here

Now, we can plug this into the same template from the users table:

### code block will go here