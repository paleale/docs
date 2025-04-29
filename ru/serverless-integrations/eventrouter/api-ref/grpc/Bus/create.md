---
editable: false
sourcePath: en/_api-ref-grpc/serverless/eventrouter/v1/eventrouter/api-ref/grpc/Bus/create.md
---

# EventRouter Service, gRPC: BusService.Create

Creates a bus in the specified folder.

## gRPC request

**rpc Create ([CreateBusRequest](#yandex.cloud.serverless.eventrouter.v1.CreateBusRequest)) returns ([operation.Operation](#yandex.cloud.operation.Operation))**

## CreateBusRequest {#yandex.cloud.serverless.eventrouter.v1.CreateBusRequest}

```json
{
  "folder_id": "string",
  "name": "string",
  "description": "string",
  "labels": "map<string, string>",
  "deletion_protection": "bool",
  "logging_enabled": "bool",
  "log_options": {
    // Includes only one of the fields `log_group_id`, `folder_id`
    "log_group_id": "string",
    "folder_id": "string",
    // end of the list of possible fields
    "min_level": "Level",
    "service_account_id": "string"
  }
}
```

#|
||Field | Description ||
|| folder_id | **string**

Required field. ID of the folder to create a bus in. ||
|| name | **string**

Name of the bus. ||
|| description | **string**

Description of the bus. ||
|| labels | **object** (map<**string**, **string**>)

Labels for the bus. ||
|| deletion_protection | **bool**

Flag that disallow deletion of the bus. ||
|| logging_enabled | **bool**

Is logging from the bus enabled. ||
|| log_options | **[LogOptions](#yandex.cloud.serverless.eventrouter.v1.LogOptions)**

Options for logging from the bus. ||
|#

## LogOptions {#yandex.cloud.serverless.eventrouter.v1.LogOptions}

#|
||Field | Description ||
|| log_group_id | **string**

Entry will be written to log group resolved by ID.

Includes only one of the fields `log_group_id`, `folder_id`.

Log entries destination. ||
|| folder_id | **string**

Entry will be written to default log group for specified folder.

Includes only one of the fields `log_group_id`, `folder_id`.

Log entries destination. ||
|| min_level | enum **Level**

Minimum log entry level.

See [LogLevel.Level](/docs/logging/api-ref/grpc/Export/run#yandex.cloud.logging.v1.LogLevel.Level) for details.

- `LEVEL_UNSPECIFIED`: Default log level.

  Equivalent to not specifying log level at all.
- `TRACE`: Trace log level.

  Possible use case: verbose logging of some business logic.
- `DEBUG`: Debug log level.

  Possible use case: debugging special cases in application logic.
- `INFO`: Info log level.

  Mostly used for information messages.
- `WARN`: Warn log level.

  May be used to alert about significant events.
- `ERROR`: Error log level.

  May be used to alert about errors in infrastructure, logic, etc.
- `FATAL`: Fatal log level.

  May be used to alert about unrecoverable failures and events. ||
|| service_account_id | **string**

Required field. Service account, which has permission to write to destination ||
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
    "bus_id": "string",
    "folder_id": "string"
  },
  // Includes only one of the fields `error`, `response`
  "error": "google.rpc.Status",
  "response": {
    "id": "string",
    "folder_id": "string",
    "cloud_id": "string",
    "created_at": "google.protobuf.Timestamp",
    "name": "string",
    "description": "string",
    "labels": "map<string, string>",
    "deletion_protection": "bool",
    "status": "Status",
    "logging_enabled": "bool",
    "log_options": {
      // Includes only one of the fields `log_group_id`, `folder_id`
      "log_group_id": "string",
      "folder_id": "string",
      // end of the list of possible fields
      "min_level": "Level",
      "service_account_id": "string"
    }
  }
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
|| metadata | **[CreateBusMetadata](#yandex.cloud.serverless.eventrouter.v1.CreateBusMetadata)**

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
|| response | **[Bus](#yandex.cloud.serverless.eventrouter.v1.Bus)**

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

## CreateBusMetadata {#yandex.cloud.serverless.eventrouter.v1.CreateBusMetadata}

#|
||Field | Description ||
|| bus_id | **string**

ID of the bus that is being created. ||
|| folder_id | **string**

ID of the folder that the bus is being created in. ||
|#

## Bus {#yandex.cloud.serverless.eventrouter.v1.Bus}

#|
||Field | Description ||
|| id | **string**

ID of the bus. ||
|| folder_id | **string**

ID of the folder that the bus belongs to. ||
|| cloud_id | **string**

ID of the cloud that the bus resides in. ||
|| created_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

Creation timestamp. ||
|| name | **string**

Name of the bus. ||
|| description | **string**

Description of the bus. ||
|| labels | **object** (map<**string**, **string**>)

Resource labels as `key:value` pairs. ||
|| deletion_protection | **bool**

Deletion protection. ||
|| status | enum **Status**

Status of the bus.

- `STATUS_UNSPECIFIED`
- `CREATING`
- `ACTIVE`
- `DELETING` ||
|| logging_enabled | **bool**

Is logging from the bus enabled. ||
|| log_options | **[LogOptions](#yandex.cloud.serverless.eventrouter.v1.LogOptions2)**

Options for logging from the bus. ||
|#

## LogOptions {#yandex.cloud.serverless.eventrouter.v1.LogOptions2}

#|
||Field | Description ||
|| log_group_id | **string**

Entry will be written to log group resolved by ID.

Includes only one of the fields `log_group_id`, `folder_id`.

Log entries destination. ||
|| folder_id | **string**

Entry will be written to default log group for specified folder.

Includes only one of the fields `log_group_id`, `folder_id`.

Log entries destination. ||
|| min_level | enum **Level**

Minimum log entry level.

See [LogLevel.Level](/docs/logging/api-ref/grpc/Export/run#yandex.cloud.logging.v1.LogLevel.Level) for details.

- `LEVEL_UNSPECIFIED`: Default log level.

  Equivalent to not specifying log level at all.
- `TRACE`: Trace log level.

  Possible use case: verbose logging of some business logic.
- `DEBUG`: Debug log level.

  Possible use case: debugging special cases in application logic.
- `INFO`: Info log level.

  Mostly used for information messages.
- `WARN`: Warn log level.

  May be used to alert about significant events.
- `ERROR`: Error log level.

  May be used to alert about errors in infrastructure, logic, etc.
- `FATAL`: Fatal log level.

  May be used to alert about unrecoverable failures and events. ||
|| service_account_id | **string**

Required field. Service account, which has permission to write to destination ||
|#