---
group: mbi
title: Automating Data Retrieval with the Data Export API
zendesk_id: 360016732271
functional_areas:
  - Export API
  - System
  - Setup
---

The Magento BI API allows customers to access some of the raw data behind their Magento BI dashboard. The API is entirely HTTP-based.

## Authentication

API connections are authenticated using a token that must be passed in the HTTP header of every request.

* Generate a key from **Export Data -> Export API**. You must specify this key in the `X-RJM-API-Key` header of every request.

* All keys must be associated with a single client and a set of IP addresses that are allowed to make requests. IP addresses can be specified either as a specific address, or as a range of addresses in [CIDR notation](http://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). For example, the CIDR notation to allow ALL IP addresses would be: `0.0.0.0/0`.

![mbi-valid-ip-address.png](../docs/images/mbi-valid-ip-address.png)

## Methods

Use a GET request to retrieve data or a POST request to create, modify, or delete data.

Methods are defined by an HTTP Verb (GET, POST, PUT, DELETE), a URL and a set of arguments. The response HTTP status code will be 200-Success for all successful requests unless otherwise specified.

### Exporting Raw Data Exports

{:.bs-callout-info}
You need a [raw data export](https://docs.magento.com/mbi/tutorials/export-raw-data.html) before calling the following methods.

**Endpoint**
`GET /export \[no arguments\]`

**Response**
Returns a JSON-encoded list of raw exports available.

```bash
curl -H "X-RJM-API-Key: _your\_key_" https://api.rjmetrics.com/0.1/export
```

**Endpoint**
`GET /export/_id_ \[no arguments\]`

**Response**
Returns

* a zip-compressed CSV file containing the raw data of a completed export
* a 404-Not Found for an export that doesn’t exist or is not complete
* a 410-Gone for an expired export

```bash
curl -H "X-RJM-API-Key: _your\_key_" https://api.rjmetrics.com/0.1/export/51
```

**Endpoint**
`GET /export/_id_/info \[no arguments\]`

**Response**
Returns a JSON-encoded description of the export with this id, or a 404 if this export doesn’t exist.

```bash
curl -H "X-RJM-API-Key: _your\_key_" https://api.rjmetrics.com/0.1/export/51/info
```

**Endpoint**
`POST /export/_id_/copy \[(optional)name=ExportName\]`

**Response**
Creates a new export with the exact same parameters as the specified export. If name parameter is specified, the new export will be assigned its value. **Note that modification of start and end time parameters is no longer supported.** Returns 201-Created along with a JSON-encoded description of the newly created export, including its ID which you can use to check status and download.

```bash
curl -d "name=New Copied Export" -H "X-RJM-API-Key: _your\_key_" https://api.rjmetrics.com/0.1/export/51/copy
```

### Exporting Data Tables

**Endpoint**
`GET /client/_client\_id__/_table \[no arguments\]`

**Response**
Returns a JSON-encoded list of tables in the data warehouse of the client corresponding to \[_client\_id_\].

```bash
curl -H "X-RJM-API-Key: _your\_key_" https://api.rjmetrics.com/0.1/client/12/table
```

**Endpoint**
`GET /client/_client\_id__/_table/_table\_id_ \[no arguments\]`

**Response**
Returns a JSON-encoded list of table columns in the table corresponding to \[_table\_id_\].

```bash
curl -H "X-RJM-API-Key: _your\_key_" https://api.rjmetrics.com/0.1/client/12/table/3
```

**Endpoint**
`POST /****client/_client\_id__/_table/_table\_id_/export \[no arguments\]`

**Response**
Creates a new raw data export of the entire contents of the table corresponding to table\_id. Note that exports are capped at 10 million rows, so don't try this on very large tables. Returns a JSON-encoded message with a reference to the newly created export.

```bash
curl -d "" -H "X-RJM-API-Key: _your\_key_" https://api.rjmetrics.com/0.1/client/12/table/3/export
```

### Exporting Specific Reports

**Endpoint**
`GET /figure \[no arguments\]`

**Response**
Returns a JSON-encoded list of figures that are available for export.

```bash
curl -H "X-RJM-API-Key: your\_key" https://api.rjmetrics.com/0.1/figure
```

**Endpoint**
`POST /figure/_id_/export \[(optional)format=FormatType\]`

**Response**
Outputs the data used to create the figure with the specified id. Figure IDs can be found in the "Export Figure" dialogue in the Magento BI dashboard interface.

![](../docs/images/figure-id.png)

The user can specify an output format of either .csv or .json.

```bash
curl -d "format=csv&includeColumnHeaders=1" -H "X-RJM-API-Key: your\_key" https://api.rjmetrics.com/0.1/figure/360531/export
```

**Endpoint**
`POST /figure/_id_/info \[no arguments\]`

**Response**
Returns a JSON-encoded description of the figure with the specified _id,_ or a 404 if the figure does not exist. Figure IDs can be found in the "Export Figure" dialogue in the Magento BI dashboard interface.

```bash
curl -H "X-RJM-API-Key: your\_key" https://api.rjmetrics.com/0.1/figure/360531/info
```

### Exporting Specific Reports (created by cohort report builder)

**Endpoint**
`GET /chart \[no arguments\]`

**Response**
Returns a JSON-encoded list of charts that are available for export.

```bash
curl -H "X-RJM-API-Key: your\_key" https://api.rjmetrics.com/0.1/chart
```

**Endpoint**
`POST /chart/_id_/export \[(optional)format=FormatType\]`

**Response**
Outputs the data used to create the chart with the specified id. Chart IDs can be found in the "Export Chart" dialogue in the Magento BI dashboard interface.

![](../docs/images/chart-id.png)

The user can specify an output format of either .csv or .json.

```bash
curl -d "format=csv&includeColumnHeaders=1" -H "X-RJM-API-Key: your\_key" https://api.rjmetrics.com/0.1/chart/2038112/export
```

**Endpoint**
`POST /chart/_id_/info \[no arguments\]`

**Response**
Returns a JSON-encoded description of the chart with the specified _id,_ or a 404 if the chart does not exist. Chart IDs can be found in the "Export Chart" dialogue in the Magento BI dashboard interface.

```bash
curl -H "X-RJM-API-Key: your\_key" https://api.rjmetrics.com/0.1/chart/2038112/info
```
