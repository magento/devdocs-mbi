---
group: mbi
title: Automating Data Retrieval with the Data Export API
zendesk_id: 360016732271
functional_areas:
  - Export API
  - System
  - Setup
---

The Magento BI API is provided to customers who wish to access some of the raw data behind their Magento BI dashboard.

The API is entirely HTTP-based. Methods that retrieve data must be accessed with a GET request, and those that create, modify or delete data with a POST request.

### Authentication

API connections are authenticated via a token that must be passed in the HTTP header of every request.

1. Keys can be generated via **Export Data -> Export API**, and must be passed in the "X-RJM-API-Key" portion of every request header.

1. All keys must be associated with a single client and a set of IP addresses that the requests will be made from.  IP addresses can be specified either as a specific address, or as a range of addresses in [CIDR notation](http://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).  For example, the CIDR notation to allow ALL IP addresses would be: "0.0.0.0/0".

![mbi-valid-ip-address.png](% images/mbi-valid-ip-address.png %)

### Methods Overview

The Magento BI API is provided to customers who wish to access some of the raw data behind their Magento BI dashboard.  The API is entirely HTTP-based.  **Methods** that retrieve data must be accessed with a GET request, and those that create, modify or delete data with a POST request.

**Methods** are defined by an HTTP Verb (GET, POST, PUT, DELETE), a URL and a set of arguments. The response HTTP status code will be 200-Success for all successful requests unless otherwise specified. At this time, the following methods are available:

### Exporting Raw Data Exports

You need to have created a [raw data export](https://docs.magento.com/mbi/tutorials/export-raw-data.html) before being able to call the following methods.

#### GET /export \[no arguments\]

Returns a JSON-encoded list of raw exports available.

`curl -H "X-RJM-API-Key: _your\_key_" https://api.rjmetrics.com/0.1/export`

#### GET /export/_id_ \[no arguments\]

Returns

* a zip-compressed CSV file containing the raw data of a completed export
* a 404-Not Found for an export that doesn’t exist or is not complete
* a 410-Gone for an expired export

`curl -H "X-RJM-API-Key: _your\_key_" https://api.rjmetrics.com/0.1/export/51`

#### GET /export/_id_/info \[no arguments\]

Returns a JSON-encoded description of the export with this id, or a 404 if this export doesn’t exist.

`curl -H "X-RJM-API-Key: _your\_key_" https://api.rjmetrics.com/0.1/export/51/info`

#### POST /export/_id_/copy \[(optional)name=ExportName\]

Creates a new export with the exact same parameters as the specified export. If name parameter is specified, the new export will be assigned its value. **Note that modification of start and end time parameters is no longer supported.** Returns 201-Created along with a JSON-encoded description of the newly created export, including its ID which you can use to check status and download.

`curl -d "name=New Copied Export" -H "X-RJM-API-Key: _your\_key_" https://api.rjmetrics.com/0.1/export/51/copy`

### Exporting Data Tables

#### GET /client/_client\_id__/_table \[no arguments\]

Returns a JSON-encoded list of tables in the data warehouse of the client corresponding to \[_client\_id_\].

`curl -H "X-RJM-API-Key: _your\_key_" https://api.rjmetrics.com/0.1/client/12/table`

#### GET /client/_client\_id__/_table/_table\_id_ \[no arguments\]

Returns a JSON-encoded list of table columns in the table corresponding to \[_table\_id_\].

`curl -H "X-RJM-API-Key: _your\_key_" https://api.rjmetrics.com/0.1/client/12/table/3`

#### POST /****client/_client\_id__/_table/_table\_id_/export \[no arguments\]

Creates a new raw data export of the entire contents of the table corresponding to table\_id. Note that exports are capped at 10 million rows, so don't try this on very large tables. Returns a JSON-encoded message with a reference to the newly created export.

`curl -d "" -H "X-RJM-API-Key: _your\_key_" https://api.rjmetrics.com/0.1/client/12/table/3/export`

### Exporting Specific Reports

#### GET /figure \[no arguments\]

Returns a JSON-encoded list of figures that are available for export.

`curl -H "X-RJM-API-Key: your\_key" https://api.rjmetrics.com/0.1/figure`

#### POST /figure/_id_/export \[(optional)format=FormatType\]

Outputs the data used to create the figure with the specified id. Figure IDs can be found in the "Export Figure" dialogue in the Magento BI dashboard interface.

![](% images/Screen_Shot_2016-11-21_at_5.06.03_PM.png %)

The user can specify an output format of either .csv or .json.

`curl -d "format=csv&includeColumnHeaders=1" -H "X-RJM-API-Key: your\_key" https://api.rjmetrics.com/0.1/figure/360531/export`

#### POST /figure/_id_/info \[no arguments\]

Returns a JSON-encoded description of the figure with the specified _id,_ or a 404 if the figure does not exist. Figure IDs can be found in the "Export Figure" dialogue in the Magento BI dashboard interface.

`curl -H "X-RJM-API-Key: your\_key" https://api.rjmetrics.com/0.1/figure/360531/info`

### Exporting Specific Reports (created by cohort report builder)

#### GET /chart \[no arguments\]

Returns a JSON-encoded list of charts that are available for export.

`curl -H "X-RJM-API-Key: your\_key" https://api.rjmetrics.com/0.1/chart`

#### POST /chart/_id_/export \[(optional)format=FormatType\]

Outputs the data used to create the chart with the specified id. Chart IDs can be found in the "Export Chart" dialogue in the Magento BI dashboard interface.

![](% images/Screen_Shot_2016-11-21_at_5.09.06_PM.png %)

The user can specify an output format of either .csv or .json.

`curl -d "format=csv&includeColumnHeaders=1" -H "X-RJM-API-Key: your\_key" https://api.rjmetrics.com/0.1/chart/2038112/export`

#### POST /chart/_id_/info \[no arguments\]

Returns a JSON-encoded description of the chart with the specified _id,_ or a 404 if the chart does not exist. Chart IDs can be found in the "Export Chart" dialogue in the Magento BI dashboard interface.

`curl -H "X-RJM-API-Key: your\_key" https://api.rjmetrics.com/0.1/chart/2038112/info`
