---
group: mbi
title: Magento BI Import API
functional_areas:
  - Import API
  - System
  - Setup
---

There are four ways to get your data into Magento BI: connect a database, connect a SaaS integration, upload a CSV file, or use our API.

This section describes how to use the API.

The Magento Data Import API allows you to push arbitrary data into your Magento data warehouse using REST.

This API accepts and returns valid JSON for all its methods. Each method uses a standard HTTP verb (GET/POST/PUT) and uses standard HTTP response codes for returning statuses.

## Authentication {#authentication}

Authentication with the Data Import API is done with a single API key and your RJMetrics client id. You can create an API key by logging into RJMetrics and selecting *Data* > *Connections* and clicking *Add a Data Source*. Select the *Data Import API* data source.

![](../mbi/images/apikey.png)

You can authenticate to the API by providing your API key as a GET parameter on your request.

```bash
curl -v https://connect.rjmetrics.com/
curl -v https://connect.rjmetrics.com/v2/client/:cid/authenticate?apikey=:apikey
```

{%
include note.html
type='warning'
content='This key has write access to your RJMetrics data warehouse. Do not distribute this key to untrusted third parties.'
%}

## Return Codes {#return-codes}

The Data Import API uses standard HTTP return codes to indicate the status of a request. Your app should handle each of the following return statuses gracefully.

Generally speaking, codes in the 2xx range indicate a successful transaction, codes in the 4xx range indicate a bad request, and codes in the 5xx range indicate an error on our end. If errors in the 5xx range persist, please contact RJMetrics support.

* 200 OK - request was successful.
* 201 Created - new data was added as a result of the request.
* 400 Bad request - Your request was missing a required parameter.
* 401 Unauthorized - Authorization failed. Double check your API key.
* 404 Not Found - The resource you are looking for does not exist.
* 500 Server Error - Something went wrong on RJMetrics' end.

## Versioning {#versioning}

The current version of the Import API is v2.

v1 is still available, but will be deprecated in the future.

## Test Environment {#test-environment}
The Data Import API has a full test (sandbox) environment.

The sandbox environment uses the same keys and return codes as the production API, but does not persist incoming data. You can use this environment to test your integration.

```bash
curl -v https://sandbox-connect.rjmetrics.com/v2/client/:cid/:endpoint?apikey=:apikey
```

## Methods {#methods}

### Status {#status}

You can always check the status of the Data Import API. This is called when you instantiate the client. This will return a 200 OK response if the API is operational.

```bash
curl -v https://connect.rjmetrics.com
```

## Upsert {#upsert}

The upsert method allows you to push data into your RJMetrics data warehouse. You can push entire arrays of data or single data points. This endpoint will only accept data that have the following properties:

* The data must be valid JSON.
* Each data point must contain a `keys` field. The `keys` field should specify which fields in the records represent the primary key(s).
* An array of data must contain no more than 100 individual data points.

{%
include note.html
type='info'
content='Each data point in your data warehouse will be uniquely indexed by the fields specified in `keys`. If a new data point has keys that conflict with a pre-existing data point, the old data point will be replaced.'
%}

Tables in the Data Import API are schemaless. There is no command to create or destroy a table - you can push data to any table name and it will be dynamically generated.

Here are some guidelines for managing tables:

* Create one table for each type of data point you are pushing.
* Generally speaking, each data point pushed into a table should have the same schema.
* Typically, one type of ‘thing’ will correspond to one table. For example, a typical eCommerce company might have a ‘customer’, ‘order’, ‘order_item’, and ‘product’ table.
* Table names must be alphanumeric (plus underscores). Bad table names will result in a `400 Bad Request` return code.

Here's an example of an Upsert call:

```json
curl -X POST -d @filename https://connect.rjmetrics.com/v2/client/:cid/table/:table/data?apikey=:apikey --header "Content-type: application/json"

:cid - your client id
:table - table name
:apikey - your API key
"status": "complete",
"created_at": "2012-08-05 04:51:02"
client.push_data(
    "table_name",
    test_data
)
Example 1: Single data point

{
  "keys": ["id"],
  "id": 1,
  "email": "joe@schmo.com",
  "status": "pending",
  "created_at": "2012-08-01 14:22:32"
}
Example 2: Array of data points

[{
  "keys": ["id"],
  "id": 1,
  "email": "joe@schmo.com",
  "status": "pending",
  "created_at": "2012-08-01 14:22:32"
},{
  "keys": ["id"],
  "id": 2,
  "email": "anne@schmo.com",
  "status": "pending",
  "created_at": "2012-08-03 23:12:30"
},{
  "keys": ["id"],
  "id": 1,
  "email": "joe@schmo.com",
  "status": "complete",
  "created_at": "2012-08-05 04:51:02"
}]
```