---
editable: false
sourcePath: en/_api-ref/mdb/clickhouse/v1/api-ref/ClusterExtension/list.md
---

# Managed Service for ClickHouse API, REST: ClusterExtension.List

## HTTP request

```
GET https://{{ api-host-mdb }}/managed-clickhouse/v1/clusters/{clusterId}/extensions
```

## Path parameters

#|
||Field | Description ||
|| clusterId | **string**

Required field.  ||
|#

## Query parameters {#yandex.cloud.mdb.clickhouse.v1.ListClusterExtensionsRequest}

#|
||Field | Description ||
|| pageSize | **string** (int64) ||
|| pageToken | **string** ||
|#

## Response {#yandex.cloud.mdb.clickhouse.v1.ListClusterExtensionsResponse}

**HTTP Code: 200 - OK**

```json
{
  "extensions": [
    {
      "name": "string",
      "clusterId": "string",
      "version": "string"
    }
  ],
  "nextPageToken": "string"
}
```

#|
||Field | Description ||
|| extensions[] | **[ClusterExtension](#yandex.cloud.mdb.clickhouse.v1.ClusterExtension)** ||
|| nextPageToken | **string** ||
|#

## ClusterExtension {#yandex.cloud.mdb.clickhouse.v1.ClusterExtension}

#|
||Field | Description ||
|| name | **string**

Required field. Required. Extension name. ||
|| clusterId | **string**

Required field. Required. ID of the ClickHouse cluster. ||
|| version | **string**

Required field. Required. Extension version. ||
|#