---
editable: false
sourcePath: en/_api-ref-grpc/ai/batch_inference/v1/batch-inference/api-ref/grpc/BatchInference/list.md
---

# Batch Inference Service API, gRPC: BatchInferenceService.List

Lists tasks in folder

## gRPC request

**rpc List ([ListBatchInferencesRequest](#yandex.cloud.ai.batch_inference.v1.ListBatchInferencesRequest)) returns ([ListBatchInferencesResponse](#yandex.cloud.ai.batch_inference.v1.ListBatchInferencesResponse))**

## ListBatchInferencesRequest {#yandex.cloud.ai.batch_inference.v1.ListBatchInferencesRequest}

```json
{
  "folder_id": "string",
  "page_size": "int64",
  "page_token": "string",
  "status": "Status"
}
```

#|
||Field | Description ||
|| folder_id | **string**

Required field. Folder ID for which the list of tasks will be provided. ||
|| page_size | **int64** ||
|| page_token | **string** ||
|| status | enum **Status**

Batch inference status for filtering

- `STATUS_UNSPECIFIED`
- `CREATED`
- `PENDING`
- `IN_PROGRESS`
- `COMPLETED`
- `FAILED`
- `CANCELED` ||
|#

## ListBatchInferencesResponse {#yandex.cloud.ai.batch_inference.v1.ListBatchInferencesResponse}

```json
{
  "tasks": [
    {
      "task_id": "string",
      "operation_id": "string",
      "folder_id": "string",
      "model_uri": "string",
      "source_dataset_id": "string",
      // Includes only one of the fields `completion_request`
      "completion_request": {
        "model_uri": "string",
        "source_dataset_id": "string",
        "completion_options": {
          "temperature": "google.protobuf.DoubleValue",
          "max_tokens": "google.protobuf.Int64Value",
          "reasoning_options": {
            "mode": "ReasoningMode"
          }
        },
        "data_logging_enabled": "bool",
        // Includes only one of the fields `json_object`, `json_schema`
        "json_object": "bool",
        "json_schema": {
          "schema": "google.protobuf.Struct"
        }
        // end of the list of possible fields
      },
      // end of the list of possible fields
      "status": "Status",
      "result_dataset_id": "string",
      "labels": "map<string, string>",
      "created_by": "string",
      "created_at": "google.protobuf.Timestamp",
      "started_at": "google.protobuf.Timestamp",
      "finished_at": "google.protobuf.Timestamp"
    }
  ],
  "next_page_token": "string"
}
```

#|
||Field | Description ||
|| tasks[] | **[BatchInferenceTask](#yandex.cloud.ai.batch_inference.v1.BatchInferenceTask)** ||
|| next_page_token | **string** ||
|#

## BatchInferenceTask {#yandex.cloud.ai.batch_inference.v1.BatchInferenceTask}

#|
||Field | Description ||
|| task_id | **string** ||
|| operation_id | **string** ||
|| folder_id | **string** ||
|| model_uri | **string** ||
|| source_dataset_id | **string** ||
|| completion_request | **[BatchCompletionRequest](#yandex.cloud.ai.batch_inference.v1.BatchCompletionRequest)**

Includes only one of the fields `completion_request`. ||
|| status | enum **Status**

- `STATUS_UNSPECIFIED`
- `CREATED`
- `PENDING`
- `IN_PROGRESS`
- `COMPLETED`
- `FAILED`
- `CANCELED` ||
|| result_dataset_id | **string** ||
|| labels | **object** (map<**string**, **string**>) ||
|| created_by | **string** ||
|| created_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)** ||
|| started_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)** ||
|| finished_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)** ||
|#

## BatchCompletionRequest {#yandex.cloud.ai.batch_inference.v1.BatchCompletionRequest}

#|
||Field | Description ||
|| model_uri | **string**

Required field.  ||
|| source_dataset_id | **string**

Required field.  ||
|| completion_options | **[CompletionOptions](#yandex.cloud.ai.batch_inference.v1.CompletionOptions)** ||
|| data_logging_enabled | **bool** ||
|| json_object | **bool**

Includes only one of the fields `json_object`, `json_schema`.

Unsupported ||
|| json_schema | **[JsonSchema](#yandex.cloud.ai.batch_inference.v1.JsonSchema)**

Includes only one of the fields `json_object`, `json_schema`.

Unsupported ||
|#

## CompletionOptions {#yandex.cloud.ai.batch_inference.v1.CompletionOptions}

#|
||Field | Description ||
|| temperature | **[google.protobuf.DoubleValue](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/double-value)** ||
|| max_tokens | **[google.protobuf.Int64Value](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/int64-value)** ||
|| reasoning_options | **[ReasoningOptions](#yandex.cloud.ai.batch_inference.v1.ReasoningOptions)** ||
|#

## ReasoningOptions {#yandex.cloud.ai.batch_inference.v1.ReasoningOptions}

#|
||Field | Description ||
|| mode | enum **ReasoningMode**

- `REASONING_MODE_UNSPECIFIED`
- `DISABLED`
- `ENABLED_HIDDEN` ||
|#

## JsonSchema {#yandex.cloud.ai.batch_inference.v1.JsonSchema}

#|
||Field | Description ||
|| schema | **[google.protobuf.Struct](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/struct)** ||
|#