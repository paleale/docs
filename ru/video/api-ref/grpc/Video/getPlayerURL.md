---
editable: false
sourcePath: en/_api-ref-grpc/video/v1/api-ref/grpc/Video/getPlayerURL.md
---

# Video API, gRPC: VideoService.GetPlayerURL

Get player url.

## gRPC request

**rpc GetPlayerURL ([GetVideoPlayerURLRequest](#yandex.cloud.video.v1.GetVideoPlayerURLRequest)) returns ([GetVideoPlayerURLResponse](#yandex.cloud.video.v1.GetVideoPlayerURLResponse))**

## GetVideoPlayerURLRequest {#yandex.cloud.video.v1.GetVideoPlayerURLRequest}

```json
{
  "video_id": "string",
  "params": {
    "mute": "bool",
    "autoplay": "bool",
    "hidden": "bool"
  },
  "signed_url_expiration_duration": "google.protobuf.Duration"
}
```

#|
||Field | Description ||
|| video_id | **string**

Required field. ID of the video. ||
|| params | **[VideoPlayerParams](#yandex.cloud.video.v1.VideoPlayerParams)** ||
|| signed_url_expiration_duration | **[google.protobuf.Duration](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/duration)**

Optional field, used to set custom url expiration duration for videos with sign_url_access ||
|#

## VideoPlayerParams {#yandex.cloud.video.v1.VideoPlayerParams}

#|
||Field | Description ||
|| mute | **bool**

If true, a player will be muted by default. ||
|| autoplay | **bool**

If true, playback will start automatically. ||
|| hidden | **bool**

If true, a player interface will be hidden by default. ||
|#

## GetVideoPlayerURLResponse {#yandex.cloud.video.v1.GetVideoPlayerURLResponse}

```json
{
  "player_url": "string",
  "html": "string"
}
```

#|
||Field | Description ||
|| player_url | **string**

Direct link to the video. ||
|| html | **string**

HTML embed code in Iframe format. ||
|#