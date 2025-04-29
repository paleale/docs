---
editable: false
sourcePath: en/_api-ref-grpc/video/v1/api-ref/grpc/Subtitle/list.md
---

# Video API, gRPC: SubtitleService.List

List subtitles.

## gRPC request

**rpc List ([ListSubtitlesRequest](#yandex.cloud.video.v1.ListSubtitlesRequest)) returns ([ListSubtitlesResponse](#yandex.cloud.video.v1.ListSubtitlesResponse))**

## ListSubtitlesRequest {#yandex.cloud.video.v1.ListSubtitlesRequest}

```json
{
  "page_size": "int64",
  "page_token": "string",
  // Includes only one of the fields `video_id`
  "video_id": "string"
  // end of the list of possible fields
}
```

#|
||Field | Description ||
|| page_size | **int64**

The maximum number of the results per page to return.
Default value: 100. ||
|| page_token | **string**

Page token for getting the next page of the result. ||
|| video_id | **string**

ID of the video.

Includes only one of the fields `video_id`. ||
|#

## ListSubtitlesResponse {#yandex.cloud.video.v1.ListSubtitlesResponse}

```json
{
  "subtitles": [
    {
      "id": "string",
      "language": "string",
      "label": "string",
      "status": "SubtitleStatus",
      "source_type": "SubtitleSourceType",
      "filename": "string",
      "created_at": "google.protobuf.Timestamp",
      "updated_at": "google.protobuf.Timestamp",
      // Includes only one of the fields `video_id`
      "video_id": "string"
      // end of the list of possible fields
    }
  ],
  "next_page_token": "string"
}
```

#|
||Field | Description ||
|| subtitles[] | **[Subtitle](#yandex.cloud.video.v1.Subtitle)** ||
|| next_page_token | **string**

Token for getting the next page. ||
|#

## Subtitle {#yandex.cloud.video.v1.Subtitle}

#|
||Field | Description ||
|| id | **string**

ID of the subtitle. ||
|| language | **string**

Subtitle language in any of the following formats:
* three-letter code according to ISO 639-2/T, ISO 639-2/B, or ISO 639-3
* two-letter code according to ISO 639-1 ||
|| label | **string**

Subtitle caption to be displayed on screen during video playback. ||
|| status | enum **SubtitleStatus**

Subtitle status.

- `SUBTITLE_STATUS_UNSPECIFIED`: Subtitle status unspecified.
- `WAIT_UPLOADING`: Waiting for all the bytes to be loaded.
- `UPLOADED`: Uploading is complete. ||
|| source_type | enum **SubtitleSourceType**

Source type.

- `SUBTITLE_SOURCE_TYPE_UNSPECIFIED`: Subtitle source type unspecified.
- `MANUAL`: Manually uploaded subtitle.
- `GENERATED`: Automatically generated subtitle. ||
|| filename | **string**

Subtitle filename. ||
|| created_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

Time when subtitle was created. ||
|| updated_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

Time of last subtitle update. ||
|| video_id | **string**

ID of the video.

Includes only one of the fields `video_id`. ||
|#