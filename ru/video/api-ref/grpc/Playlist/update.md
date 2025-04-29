---
editable: false
sourcePath: en/_api-ref-grpc/video/v1/api-ref/grpc/Playlist/update.md
---

# Video API, gRPC: PlaylistService.Update

Update playlist.

## gRPC request

**rpc Update ([UpdatePlaylistRequest](#yandex.cloud.video.v1.UpdatePlaylistRequest)) returns ([operation.Operation](#yandex.cloud.operation.Operation))**

## UpdatePlaylistRequest {#yandex.cloud.video.v1.UpdatePlaylistRequest}

```json
{
  "playlist_id": "string",
  "field_mask": "google.protobuf.FieldMask",
  "title": "string",
  "description": "string",
  "items": [
    {
      // Includes only one of the fields `video_id`, `episode_id`
      "video_id": "string",
      "episode_id": "string",
      // end of the list of possible fields
      "position": "int64"
    }
  ]
}
```

#|
||Field | Description ||
|| playlist_id | **string**

Required field. ID of the playlist. ||
|| field_mask | **[google.protobuf.FieldMask](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/field-mask)**

Required field. Field mask that specifies which fields of the playlist are going to be updated. ||
|| title | **string**

Playlist title. ||
|| description | **string**

Playlist description. ||
|| items[] | **[PlaylistItem](#yandex.cloud.video.v1.PlaylistItem)**

List of playlist items. ||
|#

## PlaylistItem {#yandex.cloud.video.v1.PlaylistItem}

#|
||Field | Description ||
|| video_id | **string**

ID of the video.

Includes only one of the fields `video_id`, `episode_id`. ||
|| episode_id | **string**

ID of the episode.

Includes only one of the fields `video_id`, `episode_id`. ||
|| position | **int64**

Item position (zero-indexed). ||
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
    "playlist_id": "string"
  },
  // Includes only one of the fields `error`, `response`
  "error": "google.rpc.Status",
  "response": {
    "id": "string",
    "channel_id": "string",
    "title": "string",
    "description": "string",
    "items": [
      {
        // Includes only one of the fields `video_id`, `episode_id`
        "video_id": "string",
        "episode_id": "string",
        // end of the list of possible fields
        "position": "int64"
      }
    ],
    "created_at": "google.protobuf.Timestamp",
    "updated_at": "google.protobuf.Timestamp"
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
|| metadata | **[UpdatePlaylistMetadata](#yandex.cloud.video.v1.UpdatePlaylistMetadata)**

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
|| response | **[Playlist](#yandex.cloud.video.v1.Playlist)**

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

## UpdatePlaylistMetadata {#yandex.cloud.video.v1.UpdatePlaylistMetadata}

#|
||Field | Description ||
|| playlist_id | **string**

ID of the playlist. ||
|#

## Playlist {#yandex.cloud.video.v1.Playlist}

Entity representing an ordered list of videos or episodes.

#|
||Field | Description ||
|| id | **string**

ID of the playlist. ||
|| channel_id | **string**

ID of the channel to create the playlist in. ||
|| title | **string**

Playlist title. ||
|| description | **string**

Playlist description. ||
|| items[] | **[PlaylistItem](#yandex.cloud.video.v1.PlaylistItem2)**

List of playlist items. ||
|| created_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

Time when playlist was created. ||
|| updated_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

Time of last playlist update. ||
|#

## PlaylistItem {#yandex.cloud.video.v1.PlaylistItem2}

#|
||Field | Description ||
|| video_id | **string**

ID of the video.

Includes only one of the fields `video_id`, `episode_id`. ||
|| episode_id | **string**

ID of the episode.

Includes only one of the fields `video_id`, `episode_id`. ||
|| position | **int64**

Item position (zero-indexed). ||
|#