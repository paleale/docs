---
editable: false
sourcePath: en/_api-ref-grpc/ai/assistants/v1/assistants/api-ref/grpc/Assistant/update.md
---

# AI Assistants API, gRPC: AssistantService.Update

Update an existing assistant.

## gRPC request

**rpc Update ([UpdateAssistantRequest](#yandex.cloud.ai.assistants.v1.UpdateAssistantRequest)) returns ([Assistant](#yandex.cloud.ai.assistants.v1.Assistant))**

## UpdateAssistantRequest {#yandex.cloud.ai.assistants.v1.UpdateAssistantRequest}

```json
{
  "assistant_id": "string",
  "update_mask": "google.protobuf.FieldMask",
  "name": "string",
  "description": "string",
  "expiration_config": {
    "expiration_policy": "ExpirationPolicy",
    "ttl_days": "int64"
  },
  "labels": "map<string, string>",
  "model_uri": "string",
  "instruction": "string",
  "prompt_truncation_options": {
    "max_prompt_tokens": "google.protobuf.Int64Value",
    // Includes only one of the fields `auto_strategy`, `last_messages_strategy`
    "auto_strategy": "AutoStrategy",
    "last_messages_strategy": {
      "num_messages": "int64"
    }
    // end of the list of possible fields
  },
  "completion_options": {
    "max_tokens": "google.protobuf.Int64Value",
    "temperature": "google.protobuf.DoubleValue"
  },
  "tools": [
    {
      // Includes only one of the fields `search_index`, `function`
      "search_index": {
        "search_index_ids": [
          "string"
        ],
        "max_num_results": "google.protobuf.Int64Value",
        "rephraser_options": {
          "rephraser_uri": "string"
        }
      },
      "function": {
        "name": "string",
        "description": "string",
        "parameters": "google.protobuf.Struct"
      }
      // end of the list of possible fields
    }
  ]
}
```

Request message for updating an existing assistant.

#|
||Field | Description ||
|| assistant_id | **string**

Required field. ID of the assistant to update. ||
|| update_mask | **[google.protobuf.FieldMask](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/field-mask)**

Required field. Field mask specifying which fields to update. ||
|| name | **string**

New name for the assistant. ||
|| description | **string**

New description for the assistant. ||
|| expiration_config | **[ExpirationConfig](#yandex.cloud.ai.common.ExpirationConfig)**

New expiration configuration for the assistant. ||
|| labels | **object** (map<**string**, **string**>)

New set of labels for the assistant. ||
|| model_uri | **string**

The new [ID of the model](/docs/foundation-models/concepts/yandexgpt/models) to be used for completion generation. ||
|| instruction | **string**

New instructions or guidelines for the assistant to follow. ||
|| prompt_truncation_options | **[PromptTruncationOptions](#yandex.cloud.ai.assistants.v1.PromptTruncationOptions)**

New configuration for truncating the prompt. ||
|| completion_options | **[CompletionOptions](#yandex.cloud.ai.assistants.v1.CompletionOptions)**

New configuration for completion generation. ||
|| tools[] | **[Tool](#yandex.cloud.ai.assistants.v1.Tool)**

New list of tools the assistant can use. ||
|#

## ExpirationConfig {#yandex.cloud.ai.common.ExpirationConfig}

#|
||Field | Description ||
|| expiration_policy | enum **ExpirationPolicy**

- `EXPIRATION_POLICY_UNSPECIFIED`
- `STATIC`
- `SINCE_LAST_ACTIVE` ||
|| ttl_days | **int64** ||
|#

## PromptTruncationOptions {#yandex.cloud.ai.assistants.v1.PromptTruncationOptions}

Defines the options for truncating thread messages within a prompt.

#|
||Field | Description ||
|| max_prompt_tokens | **[google.protobuf.Int64Value](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/int64-value)**

The maximum number of tokens allowed in the prompt.
If the prompt exceeds this limit, the thread messages will be truncated.
Default max_prompt_tokens: 7000 ||
|| auto_strategy | **[AutoStrategy](#yandex.cloud.ai.assistants.v1.PromptTruncationOptions.AutoStrategy)**

Includes only one of the fields `auto_strategy`, `last_messages_strategy`.

Specifies the truncation strategy to use when the prompt exceeds the token limit. ||
|| last_messages_strategy | **[LastMessagesStrategy](#yandex.cloud.ai.assistants.v1.PromptTruncationOptions.LastMessagesStrategy)**

Retains only the last `num_messages` messages in the thread.
If these messages exceed `max_prompt_tokens`, older messages will be further truncated to fit the limit.

Includes only one of the fields `auto_strategy`, `last_messages_strategy`.

Specifies the truncation strategy to use when the prompt exceeds the token limit. ||
|#

## AutoStrategy {#yandex.cloud.ai.assistants.v1.PromptTruncationOptions.AutoStrategy}

Auto truncation strategy.

#|
||Field | Description ||
|| Empty | > ||
|#

## LastMessagesStrategy {#yandex.cloud.ai.assistants.v1.PromptTruncationOptions.LastMessagesStrategy}

Truncates the prompt by retaining only the last `num_messages` messages in the thread.

#|
||Field | Description ||
|| num_messages | **int64**

The number of most recent messages to retain in the prompt.
If these messages exceed `max_prompt_tokens`, older messages will be further truncated to fit the limit. ||
|#

## CompletionOptions {#yandex.cloud.ai.assistants.v1.CompletionOptions}

Defines the options for completion generation.

#|
||Field | Description ||
|| max_tokens | **[google.protobuf.Int64Value](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/int64-value)**

The limit on the number of tokens used for single completion generation.
Must be greater than zero. This maximum allowed parameter value may depend on the model being used. ||
|| temperature | **[google.protobuf.DoubleValue](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/double-value)**

Affects creativity and randomness of responses. Should be a double number between 0 (inclusive) and 1 (inclusive).
Lower values produce more straightforward responses while higher values lead to increased creativity and randomness.
Default temperature: 0.3 ||
|#

## Tool {#yandex.cloud.ai.assistants.v1.Tool}

Represents a general tool that can be one of several types.

#|
||Field | Description ||
|| search_index | **[SearchIndexTool](#yandex.cloud.ai.assistants.v1.SearchIndexTool)**

SearchIndexTool tool that performs search across specified indexes.

Includes only one of the fields `search_index`, `function`. ||
|| function | **[FunctionTool](#yandex.cloud.ai.assistants.v1.FunctionTool)**

Function tool that can be invoked by the assistant.

Includes only one of the fields `search_index`, `function`. ||
|#

## SearchIndexTool {#yandex.cloud.ai.assistants.v1.SearchIndexTool}

Configures a tool that enables Retrieval-Augmented Generation (RAG) by allowing the assistant to search across a specified search index.

#|
||Field | Description ||
|| search_index_ids[] | **string**

A list of search index IDs that this tool will query. Currently, only a single index ID is supported. ||
|| max_num_results | **[google.protobuf.Int64Value](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/int64-value)**

The maximum number of results to return from the search.
Fewer results may be returned if necessary to fit within the prompt's token limit.
This ensures that the combined prompt and search results do not exceed the token constraints. ||
|| rephraser_options | **[RephraserOptions](#yandex.cloud.ai.assistants.v1.RephraserOptions)**

Options for rephrasing user queries.
Used to rewrite the last user message for search,
incorporating context from the previous conversation. ||
|#

## RephraserOptions {#yandex.cloud.ai.assistants.v1.RephraserOptions}

Options for configuring the rephrasing the last user message for search using context from previous conversation.

#|
||Field | Description ||
|| rephraser_uri | **string**

Required field. The ID of the model used to rephrase the last user message for search. ||
|#

## FunctionTool {#yandex.cloud.ai.assistants.v1.FunctionTool}

Represents a function tool that can be invoked by the assistant.

#|
||Field | Description ||
|| name | **string**

The name of the function. ||
|| description | **string**

A description of the function's purpose or behavior. ||
|| parameters | **[google.protobuf.Struct](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/struct)**

A JSON Schema that defines the expected parameters for the function.
The schema should describe the required fields, their types, and any constraints or default values. ||
|#

## Assistant {#yandex.cloud.ai.assistants.v1.Assistant}

```json
{
  "id": "string",
  "folder_id": "string",
  "name": "string",
  "description": "string",
  "created_by": "string",
  "created_at": "google.protobuf.Timestamp",
  "updated_by": "string",
  "updated_at": "google.protobuf.Timestamp",
  "expiration_config": {
    "expiration_policy": "ExpirationPolicy",
    "ttl_days": "int64"
  },
  "expires_at": "google.protobuf.Timestamp",
  "labels": "map<string, string>",
  "model_uri": "string",
  "instruction": "string",
  "prompt_truncation_options": {
    "max_prompt_tokens": "google.protobuf.Int64Value",
    // Includes only one of the fields `auto_strategy`, `last_messages_strategy`
    "auto_strategy": "AutoStrategy",
    "last_messages_strategy": {
      "num_messages": "int64"
    }
    // end of the list of possible fields
  },
  "completion_options": {
    "max_tokens": "google.protobuf.Int64Value",
    "temperature": "google.protobuf.DoubleValue"
  },
  "tools": [
    {
      // Includes only one of the fields `search_index`, `function`
      "search_index": {
        "search_index_ids": [
          "string"
        ],
        "max_num_results": "google.protobuf.Int64Value",
        "rephraser_options": {
          "rephraser_uri": "string"
        }
      },
      "function": {
        "name": "string",
        "description": "string",
        "parameters": "google.protobuf.Struct"
      }
      // end of the list of possible fields
    }
  ]
}
```

Assistant represents an AI assistant configuration with various settings and metadata.

#|
||Field | Description ||
|| id | **string**

Unique identifier of the assistant. ||
|| folder_id | **string**

ID of the folder that the assistant belongs to. ||
|| name | **string**

Name of the assistant. ||
|| description | **string**

Description of the assistant. ||
|| created_by | **string**

Identifier of the subject who created this assistant. ||
|| created_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

Timestamp representing when the assistant was created. ||
|| updated_by | **string**

Identifier of the subject who last updated this assistant. ||
|| updated_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

Timestamp representing the last time this assistant was updated. ||
|| expiration_config | **[ExpirationConfig](#yandex.cloud.ai.common.ExpirationConfig2)**

Configuration for the expiration of the assistant, defining when and how the assistant will expire. ||
|| expires_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

Timestamp representing when the assistant will expire. ||
|| labels | **object** (map<**string**, **string**>)

Set of key-value pairs that can be used to organize and categorize the assistant. ||
|| model_uri | **string**

The [ID of the model](/docs/foundation-models/concepts/yandexgpt/models) to be used for completion generation. ||
|| instruction | **string**

Instructions or guidelines that the assistant should follow when generating responses or performing tasks.
These instructions can help guide the assistant's behavior and responses. ||
|| prompt_truncation_options | **[PromptTruncationOptions](#yandex.cloud.ai.assistants.v1.PromptTruncationOptions2)**

Configuration options for truncating the prompt when the token count exceeds a specified limit. ||
|| completion_options | **[CompletionOptions](#yandex.cloud.ai.assistants.v1.CompletionOptions2)**

Configuration options for completion generation. ||
|| tools[] | **[Tool](#yandex.cloud.ai.assistants.v1.Tool2)**

List of tools that the assistant can use to perform additional tasks.
One example is the SearchIndexTool, which is used for Retrieval-Augmented Generation (RAG). ||
|#

## ExpirationConfig {#yandex.cloud.ai.common.ExpirationConfig2}

#|
||Field | Description ||
|| expiration_policy | enum **ExpirationPolicy**

- `EXPIRATION_POLICY_UNSPECIFIED`
- `STATIC`
- `SINCE_LAST_ACTIVE` ||
|| ttl_days | **int64** ||
|#

## PromptTruncationOptions {#yandex.cloud.ai.assistants.v1.PromptTruncationOptions2}

Defines the options for truncating thread messages within a prompt.

#|
||Field | Description ||
|| max_prompt_tokens | **[google.protobuf.Int64Value](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/int64-value)**

The maximum number of tokens allowed in the prompt.
If the prompt exceeds this limit, the thread messages will be truncated.
Default max_prompt_tokens: 7000 ||
|| auto_strategy | **[AutoStrategy](#yandex.cloud.ai.assistants.v1.PromptTruncationOptions.AutoStrategy2)**

Includes only one of the fields `auto_strategy`, `last_messages_strategy`.

Specifies the truncation strategy to use when the prompt exceeds the token limit. ||
|| last_messages_strategy | **[LastMessagesStrategy](#yandex.cloud.ai.assistants.v1.PromptTruncationOptions.LastMessagesStrategy2)**

Retains only the last `num_messages` messages in the thread.
If these messages exceed `max_prompt_tokens`, older messages will be further truncated to fit the limit.

Includes only one of the fields `auto_strategy`, `last_messages_strategy`.

Specifies the truncation strategy to use when the prompt exceeds the token limit. ||
|#

## AutoStrategy {#yandex.cloud.ai.assistants.v1.PromptTruncationOptions.AutoStrategy2}

Auto truncation strategy.

#|
||Field | Description ||
|| Empty | > ||
|#

## LastMessagesStrategy {#yandex.cloud.ai.assistants.v1.PromptTruncationOptions.LastMessagesStrategy2}

Truncates the prompt by retaining only the last `num_messages` messages in the thread.

#|
||Field | Description ||
|| num_messages | **int64**

The number of most recent messages to retain in the prompt.
If these messages exceed `max_prompt_tokens`, older messages will be further truncated to fit the limit. ||
|#

## CompletionOptions {#yandex.cloud.ai.assistants.v1.CompletionOptions2}

Defines the options for completion generation.

#|
||Field | Description ||
|| max_tokens | **[google.protobuf.Int64Value](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/int64-value)**

The limit on the number of tokens used for single completion generation.
Must be greater than zero. This maximum allowed parameter value may depend on the model being used. ||
|| temperature | **[google.protobuf.DoubleValue](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/double-value)**

Affects creativity and randomness of responses. Should be a double number between 0 (inclusive) and 1 (inclusive).
Lower values produce more straightforward responses while higher values lead to increased creativity and randomness.
Default temperature: 0.3 ||
|#

## Tool {#yandex.cloud.ai.assistants.v1.Tool2}

Represents a general tool that can be one of several types.

#|
||Field | Description ||
|| search_index | **[SearchIndexTool](#yandex.cloud.ai.assistants.v1.SearchIndexTool2)**

SearchIndexTool tool that performs search across specified indexes.

Includes only one of the fields `search_index`, `function`. ||
|| function | **[FunctionTool](#yandex.cloud.ai.assistants.v1.FunctionTool2)**

Function tool that can be invoked by the assistant.

Includes only one of the fields `search_index`, `function`. ||
|#

## SearchIndexTool {#yandex.cloud.ai.assistants.v1.SearchIndexTool2}

Configures a tool that enables Retrieval-Augmented Generation (RAG) by allowing the assistant to search across a specified search index.

#|
||Field | Description ||
|| search_index_ids[] | **string**

A list of search index IDs that this tool will query. Currently, only a single index ID is supported. ||
|| max_num_results | **[google.protobuf.Int64Value](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/int64-value)**

The maximum number of results to return from the search.
Fewer results may be returned if necessary to fit within the prompt's token limit.
This ensures that the combined prompt and search results do not exceed the token constraints. ||
|| rephraser_options | **[RephraserOptions](#yandex.cloud.ai.assistants.v1.RephraserOptions2)**

Options for rephrasing user queries.
Used to rewrite the last user message for search,
incorporating context from the previous conversation. ||
|#

## RephraserOptions {#yandex.cloud.ai.assistants.v1.RephraserOptions2}

Options for configuring the rephrasing the last user message for search using context from previous conversation.

#|
||Field | Description ||
|| rephraser_uri | **string**

Required field. The ID of the model used to rephrase the last user message for search. ||
|#

## FunctionTool {#yandex.cloud.ai.assistants.v1.FunctionTool2}

Represents a function tool that can be invoked by the assistant.

#|
||Field | Description ||
|| name | **string**

The name of the function. ||
|| description | **string**

A description of the function's purpose or behavior. ||
|| parameters | **[google.protobuf.Struct](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/struct)**

A JSON Schema that defines the expected parameters for the function.
The schema should describe the required fields, their types, and any constraints or default values. ||
|#