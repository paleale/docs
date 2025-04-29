---
editable: false
sourcePath: en/_api-ref-grpc/video/v1/api-ref/grpc/Episode/getManifests.md
---

# Video API, gRPC: EpisodeService.GetManifests

Get manifest urls.

## gRPC request

**rpc GetManifests ([GetEpisodeManifestsRequest](#yandex.cloud.video.v1.GetEpisodeManifestsRequest)) returns ([GetEpisodeManifestsResponse](#yandex.cloud.video.v1.GetEpisodeManifestsResponse))**

## GetEpisodeManifestsRequest {#yandex.cloud.video.v1.GetEpisodeManifestsRequest}

```json
{
  "episode_id": "string"
}
```

#|
||Field | Description ||
|| episode_id | **string**

Required field. ID of the episode. ||
|#

## GetEpisodeManifestsResponse {#yandex.cloud.video.v1.GetEpisodeManifestsResponse}

```json
{
  "manifests": [
    {
      "url": "string",
      "type": "ManifestType"
    }
  ]
}
```

#|
||Field | Description ||
|| manifests[] | **[Manifest](#yandex.cloud.video.v1.Manifest)** ||
|#

## Manifest {#yandex.cloud.video.v1.Manifest}

#|
||Field | Description ||
|| url | **string** ||
|| type | enum **ManifestType**

- `MANIFEST_TYPE_UNSPECIFIED`
- `DASH`
- `HLS` ||
|#