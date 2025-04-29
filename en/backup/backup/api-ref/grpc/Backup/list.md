---
editable: false
sourcePath: en/_api-ref-grpc/backup/v1/backup/api-ref/grpc/Backup/list.md
---

# Cloud Backup API, gRPC: BackupService.List

List backups using filters.

## gRPC request

**rpc List ([ListBackupsRequest](#yandex.cloud.backup.v1.ListBackupsRequest)) returns ([ListBackupsResponse](#yandex.cloud.backup.v1.ListBackupsResponse))**

## ListBackupsRequest {#yandex.cloud.backup.v1.ListBackupsRequest}

```json
{
  // Includes only one of the fields `compute_instance_id`, `archive`, `folder_id`, `instance_policy`, `resource_id`, `policy_id`
  "compute_instance_id": "string",
  "archive": {
    "archive_id": "string",
    "folder_id": "string"
  },
  "folder_id": "string",
  "instance_policy": {
    "compute_instance_id": "string",
    "policy_id": "string"
  },
  "resource_id": "string",
  "policy_id": "string",
  // end of the list of possible fields
  "order_by": "string",
  "filter": "string",
  "type": "ResourceType",
  "page_size": "int64",
  "page_token": "string"
}
```

#|
||Field | Description ||
|| compute_instance_id | **string**

List backups that belongs to specific Compute Cloud instance.

Includes only one of the fields `compute_instance_id`, `archive`, `folder_id`, `instance_policy`, `resource_id`, `policy_id`. ||
|| archive | **[ArchiveParameters](#yandex.cloud.backup.v1.ListBackupsRequest.ArchiveParameters)**

List backups that belongs to specific archive of specific folder.

Includes only one of the fields `compute_instance_id`, `archive`, `folder_id`, `instance_policy`, `resource_id`, `policy_id`. ||
|| folder_id | **string**

List backups that belongs to specific folder.

Includes only one of the fields `compute_instance_id`, `archive`, `folder_id`, `instance_policy`, `resource_id`, `policy_id`. ||
|| instance_policy | **[InstancePolicy](#yandex.cloud.backup.v1.ListBackupsRequest.InstancePolicy)**

List backups that belongs to specific instance and policy at the same time.

Includes only one of the fields `compute_instance_id`, `archive`, `folder_id`, `instance_policy`, `resource_id`, `policy_id`. ||
|| resource_id | **string**

List backups by specific resource ID.

Includes only one of the fields `compute_instance_id`, `archive`, `folder_id`, `instance_policy`, `resource_id`, `policy_id`. ||
|| policy_id | **string**

List backups by specific policy ID.

Includes only one of the fields `compute_instance_id`, `archive`, `folder_id`, `instance_policy`, `resource_id`, `policy_id`. ||
|| order_by | **string**

By which column the listing should be ordered and in which direction,
format is "createdAt desc". "createdAt desc" if omitted. ||
|| filter | **string**

Filter list by various parameters.
Supported parameters are:
* created_at

Supported logic operators:
* AND ||
|| type | enum **ResourceType**

Type of resource. Could be compute VM or baremetal server.

- `RESOURCE_TYPE_UNSPECIFIED`
- `COMPUTE`: Resource is Compute Cloud VM
- `BMS`: Resource is baremetal server ||
|| page_size | **int64**

Number of results per page. ||
|| page_token | **string**

Token for the results page. Not allowed to use if listing is performed by specific policy ID. ||
|#

## ArchiveParameters {#yandex.cloud.backup.v1.ListBackupsRequest.ArchiveParameters}

#|
||Field | Description ||
|| archive_id | **string**

Required field. Archive ID. ||
|| folder_id | **string**

Required field. Folder ID. ||
|#

## InstancePolicy {#yandex.cloud.backup.v1.ListBackupsRequest.InstancePolicy}

#|
||Field | Description ||
|| compute_instance_id | **string**

Compute Cloud instance ID. ||
|| policy_id | **string**

Policy ID. ||
|#

## ListBackupsResponse {#yandex.cloud.backup.v1.ListBackupsResponse}

```json
{
  "backups": [
    {
      "id": "string",
      "vault_id": "string",
      "archive_id": "string",
      "created_at": "google.protobuf.Timestamp",
      "last_seen_at": "google.protobuf.Timestamp",
      "size": "int64",
      "deduplicated_size": "int64",
      "backed_up_data_size": "int64",
      "original_data_size": "int64",
      "attributes": {
        "stream_name": "string",
        "uri": "string"
      },
      "compute_instance_id": "string",
      "disks": [
        {
          "device_model": "string",
          "name": "string",
          "size": "int64",
          "volumes": [
            {
              "free_space": "int64",
              "is_bootable": "bool",
              "is_system": "bool",
              "name": "string",
              "size": "int64",
              "mount_strid": "string"
            }
          ]
        }
      ],
      "type": "Type",
      "deleted": "bool",
      "policy_id": "string",
      "resource_id": "string"
    }
  ],
  "next_page_token": "string"
}
```

#|
||Field | Description ||
|| backups[] | **[Backup](#yandex.cloud.backup.v1.Backup)** ||
|| next_page_token | **string**

Token for the next results page. ||
|#

## Backup {#yandex.cloud.backup.v1.Backup}

#|
||Field | Description ||
|| id | **string**

ID of the backup. ||
|| vault_id | **string**

ID of the backup vault. ||
|| archive_id | **string**

ID of the backup archive. ||
|| created_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)** ||
|| last_seen_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)** ||
|| size | **int64**

Backup size. ||
|| deduplicated_size | **int64**

Deduplicated backup size. ||
|| backed_up_data_size | **int64**

Backed up data size. ||
|| original_data_size | **int64**

Original data size. ||
|| attributes | **[BackupAttributes](#yandex.cloud.backup.v1.Backup.BackupAttributes)** ||
|| compute_instance_id | **string**

Compute Cloud instance ID. ||
|| disks[] | **[Disk](#yandex.cloud.backup.v1.Disk)** ||
|| type | enum **Type**

- `TYPE_UNSPECIFIED`
- `FULL`
- `INCREMENTAL` ||
|| deleted | **bool**

If this field is true, it means that the backup was deleted. ||
|| policy_id | **string**

[Policy](/docs/backup/concepts/policy) ID. ||
|| resource_id | **string**

Resource ID. It identifies Compute Cloud instance in backup service. ||
|#

## BackupAttributes {#yandex.cloud.backup.v1.Backup.BackupAttributes}

Backup attributes.

#|
||Field | Description ||
|| stream_name | **string**

Backup stream name. ||
|| uri | **string**

URI of the backup archive. ||
|#

## Disk {#yandex.cloud.backup.v1.Disk}

#|
||Field | Description ||
|| device_model | **string**

Device model. ||
|| name | **string**

Disk name. ||
|| size | **int64**

Disk size. ||
|| volumes[] | **[Volume](#yandex.cloud.backup.v1.Volume)** ||
|#

## Volume {#yandex.cloud.backup.v1.Volume}

#|
||Field | Description ||
|| free_space | **int64**

Free space in the volume. ||
|| is_bootable | **bool**

If this field is true, it means that the volume is bootable. ||
|| is_system | **bool**

If this field is true, it means that the volume is a system volume. ||
|| name | **string**

Volume name. ||
|| size | **int64**

Volume size. ||
|| mount_strid | **string**

Mount string ID. ||
|#