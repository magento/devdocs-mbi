---
group: mbi
title: Magento BI Cloud Onboarding API
functional_areas:
  - Cloud API
  - System
  - Setup
---

NOTE: These endpoints are in BETA.

The Cloud Onboarding API allows users with specific permissions to create a new client, include an admin user and templated dashboard. This can be achieved by calling a series of endpoints as described below.

## Step 1. Create access token {#create-access-token}

An access token represents a time-limited session for a specific user. The request includes a field for a `clientSecret`. The `clientSecret` must be provided to you by Magento BI. Credentials for an existing user must also be supplied in the request.

```bash
curl http://api.rjmetrics.com/v1/tokens \
  -X POST \
  -H "Content-Type: application/json" \
  -d '
  {
   "password": "abc123",
   "scope":"default",
   "username":"you@here.com",
   "grant_type":"password",
   "clientId":1,
   "clientSecret": "XXXXXXX"
  }'
  ```
*Example Response*

```
{
  "refreshed_at": "2018-06-22 10:53:16",
  "oauth_client_id": 1,
  "token_type": "bearer",
  "id": 16,
  "access_token": "131c4...",
  "scope": "default",
  "user_id": 1,
  "create_at": "2018-06-22 10:53:16",
  "client_id": null,
  "expires_at": "2018-06-22 14:53:16",
  "grant_type": "password",
  "expires_in": 14400,
  "refresh_token": null
}
```

## Step 2. Create a new client {#create-new-client}

This request creates a new client. The response returns a JOB_ID which can be used to poll for the status of the client creation process.

```bash
curl http://api.rjmetrics.com/v3/signups \
 -X POST \
 -H "Content-Type: application/json" \
 -d '
  {
   "tier": "T100",
   "company": "Coco Co.",
   "first_name": "CSMFirst",
   "last_name": "CSMLast",
   "email": "blackhole+admin@rjmetrics.com",
   "role": "csm",
   "magento": {
    "customer_id": "MAG0000001",
    "subscription_id": "1000"
   }
 }'
 ```
*Example Response*

```
{
 "url": "http:\/\/api.rjmetrics.com\/v3\/signups\/1827c806-...",
 "id": "1827c806-..."
}
```

## Step 3. Poll signups status {#poll-signups-status}

Use the `url` from the previous step to check the status of the client creation process. Do not proceed to the following step until the status returns as complete.

```bash
curl http://api.rjmetrics.com/v3/signups/[JOB_ID] \
 -H "Content-Type: application/json"
 ```

*Example Response*

 ```
 {
 "pubkey": "ssh-rsa AAAAB3N...",
 "status": "completed"
 "client_id": 123
}
```

## Step 4. Create user {#create-user}

This request creates a new user on the newly created client. Be sure to use the client_id returned by the previous step.

```bash
curl http://api.rjmetrics.com/v3/invites \
  -X POST \
  -H "Authorization: Bearer <<access_token>>" \
  -H 'Content-Type: application/json' \
  -d '
  {
   "force_create": 1,
   "email": "customer@customer.com",
   "client_id": 2,
   "client_rights": {
    "admin": 1,
    "billing": 1,
    "readonly": 1,
    "technical": 0
   },
   "first_name": "tester",
   "last_name": "mctest"
}'
```

*Example Response*

```
{
 "inviter_user_id": 39203,
 "client_id": 2,
 "email": "customer@customer.com",
 "id": 3892
}
```

## Step 5. Update client properties {#update-client-properties}

This request sets many properities on the client.

```bash
curl http://api.rjmetrics.com/v1/clients/2 \
  -X PUT \
  -H "Content-Type: application/json" \
  -H "Cookie: DASHSESS2=<<access_token>>" \
  -d '
  {
   "name": "ughasoiuhdf",
   "properties": {
     "blackout_hours": "",
     "default_currency": "USD",
     "forced_update_hours": "",
     "has_guest_checkout": "0",
     "has_privacy_protection": null,
     "hosted_warehouse": null,
     "is_implemented": false,
     "magento_store_db_name": "magento_store",
     "magento_store_table_prefix": "",
     "magento_version": "1",
     "perform_updates": "1",
     "phone_number": null,
     "stale_email_summary_action": "1",
     "timezone": "America/New_York",
     "website_url": null,
     "week_start_day": "7"
   }
 }'
 ```

## Step 6. Create connection {#create-connection}

This request adds a new database connection to the client. The request should contain connection information, including credentials, for the clients’ Magento store MySQL database.

```bash
curl http://api.rjmetrics.com/v1/v2/clients/2/connections/input-connections \
 -X POST \
 -H 'Content-Type: application/json' \
 -H "Cookie: DASHSESS2=<<access_token>>" \
 -d '
  {
   "credentials": {
   "encryption_password": null,
   "password": "PASSWORD"
   },
   "display_name": "new conn",
   "name": "new conn",
   "properties": {
    "dbName": "magento_store",
    "desiredTZ": null,
    "encryption_gatewayAddress": null,
    "encryption_groupName": null,
    "encryption_groupSecret": null,
    "encryption_sshHostAddress": null,
    "encryption_sshPort": null,
    "encryption_sshUsername": null,
    "encryption_type": "none",
    "encryption_username": null,
    "host": "my-magento-db.com",
    "noPhpPasswordEncryption": "1",
    "port": "3306",
    "sourceTZ": "UTC",
    "status": "1",
    "useSSL": "false",
    "username": "magento"
   },
  "type": "mysql"
 }'
 ```

*Example Response*

 ```
 {
"meta_id": 5,
"id": 14,
"namespace": "input-connections",
"client_id": 2,
"type": "mysql",
"name": "new conn",
"properties": {
  "sourceTZ": "UTC",
  "dbName": "magento_store",
  "encryption_groupName": null,
  "encryption_type": "none",
  "useSSL": "false",
  "desiredTZ": null,
  "status": "1",
  "encryption_groupSecret": null,
  "host": "pinterest.cp83nhvyi9x1.us-east-1.rds.amazonaws.com",
  "noPhpPasswordEncryption": "1",
  "port": "3306",
  "encryption_sshUsername": null,
  "username": "magento",
  "encryption_sshHostAddress": null,
  "encryption_username": null,
  "encryption_gatewayAddress": null,
  "encryption_sshPort": null
  },
"display_name": "new conn"
}
```

## Step 7. Poll connection {#poll-connection}

Make requests to poll the connection creation status until it returns a completed status.

```bash
curl http://api.rjmetrics.com/v1/v2/clients/2/connections/input-connections?include_deleted=false \
 -H 'Accept: application/json' \
 -H 'Cookie: DASHSESS2=<<access_token>>'
 ```

*Example Response*
```
[
   {
    "namespace": "input-connections",
    "created_at": "2018-04-10 10:03:09.0",
    "display_name": null,
    "id": 5,
    "type": "mysql",
    "name": "vandelay",
    "integration_type": {
     "protocol": "mysql",
     "directions": ["inbound"],
     "cbi_is_available": true,
     "pricing_tier": "standard",
     "replication_type": "db",
     "pipeline_state": "released",
     "display_name": "MySQL",
     "type": "mysql"
    },
   "meta_id": 1,
   "client_id": 2,
   "updated_at": "2018-04-10 10:03:21.0",
   "properties": {
   "blackout": null,
   "sourceTZ": null,
   "encryption_type": "none",
   "useSSL": "false",
   "desiredTZ": null,
   "sslCa": null,
   "sslCert": null,
   "status": "1",
   "host": "vandelay.cp83nhvyi9x1.us-east-1.rds.amazonaws.com",
   "noPhpPasswordEncryption": "1",
   "port": "3306",
   "created": "2018-04-10 10:03:09",
   "username": "boxcutter",
   "frequency": null,
   "established": "2018-04-10 10:03:20",
   "sslKey": null
  }
 }
]
```

## Step 8. Test connection {#test-connection}

This request will kickoff a job that tests whether the connection can be made successfully. Use the connection id returned by the previous step in the url.

```bash
curl http://dashboard.rjmetrics.com/v2/client/2/job \
 -H 'Content-Type: application/x-www-form-urlencoded' \
 -H 'Cookie: dashuid=1; DASHSESS2=<<access_token>>' \
 -H 'Accept: application/json, text/plain, */*' \
 -H 'Referer: http://dashboard.rjmetrics.com/v2/client/2/dashapp/settings/connection/<<connection_id>>/' \
 --data 'jobType=27&workload[connectionId]=<<connection_id>>&workload[syncAndSample]=1'
 ```

*Example Response*

 ```
 {
 "status": {
 "name": "Queued",
 "id": 1
},
"end": null,
"start": null,
"type": {
 "name": "Connection Test",
 "id": 27
},
"id": null
}
```

## Step 9. Poll connection test status {#poll-connection-test-status}

Make requests to poll for the status of the connection test. Do not proceed to the next step until the response indicates a completed status.

```bash
curl 'http://dashboard.rjmetrics.com/v2/client/<<client_id>>/job/type/5' \
 -H 'Accept: application/json, text/plain, */*' \
 -H 'Cookie: dashuid=1; DASHSESS2=<<access_token>>'
 ```

## Step 10. Create templated dashboards {#create-templated-dashboards}

This request creates dashboards from a template for a given connection. Use the connection id provided by the response of the previous step. This reponse will contain an `id` that can be used to poll for the job’s status.

```bash
curl http://api.rjmetrics.com/v1/clients/<<client_id>>/autodash \
 -X POST \
 -H 'Content-Type: application/json' \
 -H "Cookie: DASHSESS2=<<access_token>>" \
 -d '
  {
   "connections": {
   "db": {
    "connection_id": <<connection_id>>,
    "database": "magento_store",
    "table_prefix": ""
   }
 },
 "package": "magento1_rjql1",
 "user_id": <<user_id>>
}'
```

*Example Response*

```
{"id":"d4740d5e-4c4e-45c4-b73b-6ca9c20fdc44"}
```

## Step 11. Poll dashboard creation status {#poll-dashboard-creation-status}

Make requests to poll for the status of the dashboard creation job. Once the response indicates a completed status, the client is fully onboarded.

```bash
curl http://api.rjmetrics.com/v1/clients/<<client_id>>/autodash/<<id>> \
 -H 'Content-Type: application/json' \
 -H "Cookie: DASHSESS2=<<access_token>>" \
 ```

*Example Response*

 ```
 {
 "status": "completed",
 "job_id": "d4740d5e-4c4e-45c4-b73b-6ca9c20fdc44"
}
```