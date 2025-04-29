---
editable: false
sourcePath: en/_api-ref-grpc/video/v1/api-ref/grpc/StreamLine/getStreamKey.md
---

# Video API, gRPC: StreamLineService.GetStreamKey

Get unique stream key.

## gRPC request

**rpc GetStreamKey ([GetStreamKeyRequest](#yandex.cloud.video.v1.GetStreamKeyRequest)) returns ([PushStreamKey](#yandex.cloud.video.v1.PushStreamKey))**

## GetStreamKeyRequest {#yandex.cloud.video.v1.GetStreamKeyRequest}

```json
{
  "stream_line_id": "string"
}
```

#|
||Field | Description ||
|| stream_line_id | **string**

Required field. ID of the line. ||
|#

## PushStreamKey {#yandex.cloud.video.v1.PushStreamKey}

```json
{
  "key": "string"
}
```

Represents the stream key used for pushing video streams.

#|
||Field | Description ||
|| key | **string**

The unique stream key. ||
|#