---
group: mbi
title: Magento BI API Getting Started
functional_areas:
  - Getting started
  - System
  - Setup
redirect_to: https://developer.adobe.com/commerce/services/reporting/
status: migrated
---

You can use the Magento BI API to [import](../docs/import-api.html) data into your Magento data warehouse and [export](../docs/export-api.html) raw data from your Magento BI dashboards.

The API is entirely HTTP-based. Methods are defined by an HTTP verb (GET, POST, PUT, DELETE), a URL, and a set of arguments. The response HTTP status code will be `200-Success` for all successful requests, unless otherwise specified. This API accepts and returns valid JSON for all its methods.

To make integrating with the API easier, you can use one of the following [libraries](../docs/libraries.html).

## Authentication {#authentication}

Magento BI API calls require an authorization token. The token must be passed in the HTTP header of every request, which allows Magento to verify that the caller is authorized to access a system resource. You must use two different tokens depending on if you are using the import API or the export API.

### Generating authentication token for the import API

Authentication with the Data Import API is done with a single API key and your RJMetrics client ID.

To create an import API key:

1. Log into Magento BI and select __Manage Data__ > __Integrations__.
1. Click __Add an Integration__.
1. Select the **Data Import API** data source.

With the API key you can now authenticate, but first check the status of the Data Import API by calling:

```bash
curl -v https://connect.rjmetrics.com
```

This will return a `200-Success` response if the API is operational.

Next, authenticate to the API by passing in your client ID and API key:

```bash
curl -v https://connect.rjmetrics.com/v2/client/:cid/authenticate?apikey=:apikey
```

{:.bs-callout-warning}
This key has write access to your Magento BI data warehouse. Do not distribute this key to untrusted third parties.

### Generating authentication token for the export API

​To create an export API authentication token:

1. Log into Magento BI and select **Manage Data** > **Export Data** > **Export API**.
1. Click **Create a new Export API Key**.

You must specify this key in the `X-RJM-API-Key` header of every export request.
​
All keys must be associated with a single client and a set of IP addresses that are allowed to make requests. IP addresses can be specified either as a specific address, or as a range of addresses in [CIDR notation](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). For example, the CIDR notation to allow ALL IP addresses would be: `0.0.0.0/0`.

![](../docs/images/mbi-valid-ip-address.png)
