---
editable: false
sourcePath: en/_api-ref-grpc/cic/v1/api-ref/grpc/PrivateConnection/removeStaticRoute.md
---

# Cloud Interconnect API, gRPC: PrivateConnectionService.RemoveStaticRoute

Removes specified static routes to a PrivateConnection resource.
Method starts an asynchronous operation that can be cancelled while it is in progress.

## gRPC request

**rpc RemoveStaticRoute ([RemoveStaticRouteRequest](#yandex.cloud.cic.v1.RemoveStaticRouteRequest)) returns ([operation.Operation](#yandex.cloud.operation.Operation))**

## RemoveStaticRouteRequest {#yandex.cloud.cic.v1.RemoveStaticRouteRequest}

```json
{
  "private_connection_id": "string",
  "ipv4_static_routes": [
    {
      "prefix": "string"
    }
  ]
}
```

#|
||Field | Description ||
|| private_connection_id | **string**

Required field. ID of the PrivateConnection resource. ||
|| ipv4_static_routes[] | **[StaticRoute](#yandex.cloud.cic.v1.PrivateConnection.StaticRoute)**

IPv4 StaticRoute configs to remove ||
|#

## StaticRoute {#yandex.cloud.cic.v1.PrivateConnection.StaticRoute}

#|
||Field | Description ||
|| prefix | **string**

Prefix.
It's an ip with format ipPrefix/length where address part of ipPrefix is 0. ||
|#

## operation.Operation {#yandex.cloud.operation.Operation}

```json
{
  "id": "string",
  "description": "string",
  "created_at": "google.protobuf.Timestamp",
  "created_by": "string",
  "modified_at": "google.protobuf.Timestamp",
  "done": "bool",
  "metadata": {
    "private_connection_id": "string"
  },
  // Includes only one of the fields `error`, `response`
  "error": "google.rpc.Status",
  "response": "google.protobuf.Empty"
  // end of the list of possible fields
}
```

An Operation resource. For more information, see [Operation](/docs/api-design-guide/concepts/operation).

#|
||Field | Description ||
|| id | **string**

ID of the operation. ||
|| description | **string**

Description of the operation. 0-256 characters long. ||
|| created_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

Creation timestamp. ||
|| created_by | **string**

ID of the user or service account who initiated the operation. ||
|| modified_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

The time when the Operation resource was last modified. ||
|| done | **bool**

If the value is `false`, it means the operation is still in progress.
If `true`, the operation is completed, and either `error` or `response` is available. ||
|| metadata | **[RemoveStaticRouteMetadata](#yandex.cloud.cic.v1.RemoveStaticRouteMetadata)**

Service-specific metadata associated with the operation.
It typically contains the ID of the target resource that the operation is performed on.
Any method that returns a long-running operation should document the metadata type, if any. ||
|| error | **[google.rpc.Status](https://cloud.google.com/tasks/docs/reference/rpc/google.rpc#status)**

The error result of the operation in case of failure or cancellation.

Includes only one of the fields `error`, `response`.

The operation result.
If `done == false` and there was no failure detected, neither `error` nor `response` is set.
If `done == false` and there was a failure detected, `error` is set.
If `done == true`, exactly one of `error` or `response` is set. ||
|| response | **[google.protobuf.Empty](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#google.protobuf.Empty)**

The normal response of the operation in case of success.
If the original method returns no data on success, such as Delete,
the response is [google.protobuf.Empty](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#google.protobuf.Empty).
If the original method is the standard Create/Update,
the response should be the target resource of the operation.
Any method that returns a long-running operation should document the response type, if any.

Includes only one of the fields `error`, `response`.

The operation result.
If `done == false` and there was no failure detected, neither `error` nor `response` is set.
If `done == false` and there was a failure detected, `error` is set.
If `done == true`, exactly one of `error` or `response` is set. ||
|#

## RemoveStaticRouteMetadata {#yandex.cloud.cic.v1.RemoveStaticRouteMetadata}

#|
||Field | Description ||
|| private_connection_id | **string**

ID of the PrivateConnection resource. ||
|#