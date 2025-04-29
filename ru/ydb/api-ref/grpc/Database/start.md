---
editable: false
sourcePath: en/_api-ref-grpc/ydb/v1/api-ref/grpc/Database/start.md
---

# Managed Service for YDB API, gRPC: DatabaseService.Start

Starts the specified database.

## gRPC request

**rpc Start ([StartDatabaseRequest](#yandex.cloud.ydb.v1.StartDatabaseRequest)) returns ([operation.Operation](#yandex.cloud.operation.Operation))**

## StartDatabaseRequest {#yandex.cloud.ydb.v1.StartDatabaseRequest}

```json
{
  "database_id": "string"
}
```

#|
||Field | Description ||
|| database_id | **string**

Required field.  ||
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
    "database_id": "string",
    "database_name": "string"
  },
  // Includes only one of the fields `error`, `response`
  "error": "google.rpc.Status",
  "response": {
    "id": "string",
    "folder_id": "string",
    "created_at": "google.protobuf.Timestamp",
    "name": "string",
    "description": "string",
    "status": "Status",
    "endpoint": "string",
    "resource_preset_id": "string",
    "storage_config": {
      "storage_options": [
        {
          "storage_type_id": "string",
          "group_count": "int64"
        }
      ],
      "storage_size_limit": "int64"
    },
    "scale_policy": {
      // Includes only one of the fields `fixed_scale`, `auto_scale`
      "fixed_scale": {
        "size": "int64"
      },
      "auto_scale": {
        "min_size": "int64",
        "max_size": "int64",
        // Includes only one of the fields `target_tracking`
        "target_tracking": {
          // Includes only one of the fields `cpu_utilization_percent`
          "cpu_utilization_percent": "int64"
          // end of the list of possible fields
        }
        // end of the list of possible fields
      }
      // end of the list of possible fields
    },
    "network_id": "string",
    "subnet_ids": [
      "string"
    ],
    // Includes only one of the fields `zonal_database`, `regional_database`, `dedicated_database`, `serverless_database`
    "zonal_database": {
      "zone_id": "string"
    },
    "regional_database": {
      "region_id": "string"
    },
    "dedicated_database": {
      "resource_preset_id": "string",
      "storage_config": {
        "storage_options": [
          {
            "storage_type_id": "string",
            "group_count": "int64"
          }
        ],
        "storage_size_limit": "int64"
      },
      "scale_policy": {
        // Includes only one of the fields `fixed_scale`, `auto_scale`
        "fixed_scale": {
          "size": "int64"
        },
        "auto_scale": {
          "min_size": "int64",
          "max_size": "int64",
          // Includes only one of the fields `target_tracking`
          "target_tracking": {
            // Includes only one of the fields `cpu_utilization_percent`
            "cpu_utilization_percent": "int64"
            // end of the list of possible fields
          }
          // end of the list of possible fields
        }
        // end of the list of possible fields
      },
      "network_id": "string",
      "subnet_ids": [
        "string"
      ],
      "assign_public_ips": "bool",
      "security_group_ids": [
        "string"
      ]
    },
    "serverless_database": {
      "throttling_rcu_limit": "int64",
      "storage_size_limit": "int64",
      "enable_throttling_rcu_limit": "bool",
      "provisioned_rcu_limit": "int64",
      "topic_write_quota": "int64"
    },
    // end of the list of possible fields
    "assign_public_ips": "bool",
    "location_id": "string",
    "labels": "map<string, string>",
    "backup_config": {
      "backup_settings": [
        {
          "name": "string",
          "description": "string",
          "backup_schedule": {
            // Includes only one of the fields `daily_backup_schedule`, `weekly_backup_schedule`, `recurring_backup_schedule`
            "daily_backup_schedule": {
              "execute_time": "google.type.TimeOfDay"
            },
            "weekly_backup_schedule": {
              "days_of_week": [
                {
                  "days": [
                    "DayOfWeek"
                  ],
                  "execute_time": "google.type.TimeOfDay"
                }
              ]
            },
            "recurring_backup_schedule": {
              "start_time": "google.protobuf.Timestamp",
              "recurrence": "string"
            },
            // end of the list of possible fields
            "next_execute_time": "google.protobuf.Timestamp"
          },
          "backup_time_to_live": "google.protobuf.Duration",
          "source_paths": [
            "string"
          ],
          "source_paths_to_exclude": [
            "string"
          ],
          "type": "Type",
          "storage_class": "StorageClass"
        }
      ]
    },
    "document_api_endpoint": "string",
    "kinesis_api_endpoint": "string",
    "kafka_api_endpoint": "string",
    "monitoring_config": {
      "alerts": [
        {
          "alert_id": "string",
          "alert_template_id": "string",
          "name": "string",
          "description": "string",
          "notification_channels": [
            {
              "notification_channel_id": "string",
              "notify_about_statuses": [
                "AlertEvaluationStatus"
              ],
              "repeate_notify_delay_ms": "int64"
            }
          ],
          "alert_parameters": [
            {
              // Includes only one of the fields `double_parameter_value`, `integer_parameter_value`, `text_parameter_value`, `text_list_parameter_value`, `label_list_parameter_value`
              "double_parameter_value": {
                "name": "string",
                "value": "double"
              },
              "integer_parameter_value": {
                "name": "string",
                "value": "int64"
              },
              "text_parameter_value": {
                "name": "string",
                "value": "string"
              },
              "text_list_parameter_value": {
                "name": "string",
                "values": [
                  "string"
                ]
              },
              "label_list_parameter_value": {
                "name": "string",
                "values": [
                  "string"
                ]
              }
              // end of the list of possible fields
            }
          ],
          "alert_thresholds": [
            {
              // Includes only one of the fields `double_parameter_value`, `integer_parameter_value`, `text_parameter_value`, `text_list_parameter_value`, `label_list_parameter_value`
              "double_parameter_value": {
                "name": "string",
                "value": "double"
              },
              "integer_parameter_value": {
                "name": "string",
                "value": "int64"
              },
              "text_parameter_value": {
                "name": "string",
                "value": "string"
              },
              "text_list_parameter_value": {
                "name": "string",
                "values": [
                  "string"
                ]
              },
              "label_list_parameter_value": {
                "name": "string",
                "values": [
                  "string"
                ]
              }
              // end of the list of possible fields
            }
          ]
        }
      ]
    },
    "deletion_protection": "bool",
    "security_group_ids": [
      "string"
    ]
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
|| metadata | **[StartDatabaseMetadata](#yandex.cloud.ydb.v1.StartDatabaseMetadata)**

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
|| response | **[Database](#yandex.cloud.ydb.v1.Database)**

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

## StartDatabaseMetadata {#yandex.cloud.ydb.v1.StartDatabaseMetadata}

#|
||Field | Description ||
|| database_id | **string** ||
|| database_name | **string** ||
|#

## Database {#yandex.cloud.ydb.v1.Database}

YDB database.

#|
||Field | Description ||
|| id | **string** ||
|| folder_id | **string** ||
|| created_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)** ||
|| name | **string** ||
|| description | **string** ||
|| status | enum **Status**

- `STATUS_UNSPECIFIED`
- `PROVISIONING`
- `RUNNING`
- `UPDATING`
- `ERROR`
- `DELETING`
- `STARTING`
- `STOPPED` ||
|| endpoint | **string** ||
|| resource_preset_id | **string** ||
|| storage_config | **[StorageConfig](#yandex.cloud.ydb.v1.StorageConfig)** ||
|| scale_policy | **[ScalePolicy](#yandex.cloud.ydb.v1.ScalePolicy)** ||
|| network_id | **string** ||
|| subnet_ids[] | **string** ||
|| zonal_database | **[ZonalDatabase](#yandex.cloud.ydb.v1.ZonalDatabase)**

deprecated field

Includes only one of the fields `zonal_database`, `regional_database`, `dedicated_database`, `serverless_database`. ||
|| regional_database | **[RegionalDatabase](#yandex.cloud.ydb.v1.RegionalDatabase)**

deprecated field

Includes only one of the fields `zonal_database`, `regional_database`, `dedicated_database`, `serverless_database`. ||
|| dedicated_database | **[DedicatedDatabase](#yandex.cloud.ydb.v1.DedicatedDatabase)**

Includes only one of the fields `zonal_database`, `regional_database`, `dedicated_database`, `serverless_database`. ||
|| serverless_database | **[ServerlessDatabase](#yandex.cloud.ydb.v1.ServerlessDatabase)**

Includes only one of the fields `zonal_database`, `regional_database`, `dedicated_database`, `serverless_database`. ||
|| assign_public_ips | **bool** ||
|| location_id | **string** ||
|| labels | **object** (map<**string**, **string**>) ||
|| backup_config | **[BackupConfig](#yandex.cloud.ydb.v1.BackupConfig)** ||
|| document_api_endpoint | **string** ||
|| kinesis_api_endpoint | **string** ||
|| kafka_api_endpoint | **string** ||
|| monitoring_config | **[MonitoringConfig](#yandex.cloud.ydb.v1.MonitoringConfig)** ||
|| deletion_protection | **bool** ||
|| security_group_ids[] | **string** ||
|#

## StorageConfig {#yandex.cloud.ydb.v1.StorageConfig}

#|
||Field | Description ||
|| storage_options[] | **[StorageOption](#yandex.cloud.ydb.v1.StorageOption)** ||
|| storage_size_limit | **int64**

output only field: storage size limit of dedicated database. ||
|#

## StorageOption {#yandex.cloud.ydb.v1.StorageOption}

#|
||Field | Description ||
|| storage_type_id | **string** ||
|| group_count | **int64** ||
|#

## ScalePolicy {#yandex.cloud.ydb.v1.ScalePolicy}

#|
||Field | Description ||
|| fixed_scale | **[FixedScale](#yandex.cloud.ydb.v1.ScalePolicy.FixedScale)**

Includes only one of the fields `fixed_scale`, `auto_scale`. ||
|| auto_scale | **[AutoScale](#yandex.cloud.ydb.v1.ScalePolicy.AutoScale)**

Includes only one of the fields `fixed_scale`, `auto_scale`. ||
|#

## FixedScale {#yandex.cloud.ydb.v1.ScalePolicy.FixedScale}

#|
||Field | Description ||
|| size | **int64** ||
|#

## AutoScale {#yandex.cloud.ydb.v1.ScalePolicy.AutoScale}

Scale policy that dynamically changes the number of database nodes within a user-defined range.

#|
||Field | Description ||
|| min_size | **int64**

Minimum number of nodes to which autoscaling can scale the database. ||
|| max_size | **int64**

Maximum number of nodes to which autoscaling can scale the database. ||
|| target_tracking | **[TargetTracking](#yandex.cloud.ydb.v1.ScalePolicy.AutoScale.TargetTracking)**

Includes only one of the fields `target_tracking`.

Type of autoscaling algorithm. ||
|#

## TargetTracking {#yandex.cloud.ydb.v1.ScalePolicy.AutoScale.TargetTracking}

Autoscaling algorithm that tracks metric and reactively scale database nodes to keep metric
close to the specified target value.

#|
||Field | Description ||
|| cpu_utilization_percent | **int64**

A percentage of database nodes average CPU utilization.

Includes only one of the fields `cpu_utilization_percent`. ||
|#

## ZonalDatabase {#yandex.cloud.ydb.v1.ZonalDatabase}

#|
||Field | Description ||
|| zone_id | **string**

Required field.  ||
|#

## RegionalDatabase {#yandex.cloud.ydb.v1.RegionalDatabase}

#|
||Field | Description ||
|| region_id | **string**

Required field.  ||
|#

## DedicatedDatabase {#yandex.cloud.ydb.v1.DedicatedDatabase}

#|
||Field | Description ||
|| resource_preset_id | **string** ||
|| storage_config | **[StorageConfig](#yandex.cloud.ydb.v1.StorageConfig)** ||
|| scale_policy | **[ScalePolicy](#yandex.cloud.ydb.v1.ScalePolicy)** ||
|| network_id | **string** ||
|| subnet_ids[] | **string** ||
|| assign_public_ips | **bool** ||
|| security_group_ids[] | **string** ||
|#

## ServerlessDatabase {#yandex.cloud.ydb.v1.ServerlessDatabase}

#|
||Field | Description ||
|| throttling_rcu_limit | **int64**

Let's define 1 RU  - 1 request unit
Let's define 1 RCU - 1 request capacity unit, which is 1 RU per second.
If `enable_throttling_rcu_limit` flag is true, the database will be throttled using `throttling_rcu_limit` value.
Otherwise, the database is throttled using the cloud quotas.
If zero, all requests will be blocked until non zero value is set. ||
|| storage_size_limit | **int64**

Specify serverless database storage size limit. If zero, default value is applied. ||
|| enable_throttling_rcu_limit | **bool**

If false, the database is throttled by cloud value. ||
|| provisioned_rcu_limit | **int64**

Specify the number of provisioned RCUs to pay less if the database has predictable load.
You will be charged for the provisioned capacity regularly even if this capacity is not fully consumed.
You will be charged for the on-demand consumption only if provisioned capacity is consumed. ||
|| topic_write_quota | **int64**

write quota for topic service, defined in bytes per second. ||
|#

## BackupConfig {#yandex.cloud.ydb.v1.BackupConfig}

#|
||Field | Description ||
|| backup_settings[] | **[BackupSettings](#yandex.cloud.ydb.v1.BackupSettings)** ||
|#

## BackupSettings {#yandex.cloud.ydb.v1.BackupSettings}

#|
||Field | Description ||
|| name | **string**

name of backup settings ||
|| description | **string**

human readable description. ||
|| backup_schedule | **[BackupSchedule](#yandex.cloud.ydb.v1.BackupSchedule)**

provide schedule. if empty, backup will be disabled. ||
|| backup_time_to_live | **[google.protobuf.Duration](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/duration)**

provide time to live of backup. ||
|| source_paths[] | **string**

provide a list of source paths. Each path can be directory, table or even database itself.
Each directory (or database) will be traversed recursively and all childs of directory will be included to backup.
By default, backup will be created for full database. ||
|| source_paths_to_exclude[] | **string**

provide a list of paths to exclude from backup.
Each path is a directory, table, or database.
Each directory (or database) will be traversed recursively and all childs of directory will be excluded. ||
|| type | enum **Type**

- `TYPE_UNSPECIFIED`
- `SYSTEM`
- `USER` ||
|| storage_class | enum **StorageClass**

- `STORAGE_CLASS_UNSPECIFIED`
- `STANDARD`
- `REDUCED_REDUNDANCY`
- `STANDARD_IA`
- `ONEZONE_IA`
- `INTELLIGENT_TIERING`
- `GLACIER`
- `DEEP_ARCHIVE`
- `OUTPOSTS` ||
|#

## BackupSchedule {#yandex.cloud.ydb.v1.BackupSchedule}

#|
||Field | Description ||
|| daily_backup_schedule | **[DailyBackupSchedule](#yandex.cloud.ydb.v1.DailyBackupSchedule)**

Includes only one of the fields `daily_backup_schedule`, `weekly_backup_schedule`, `recurring_backup_schedule`. ||
|| weekly_backup_schedule | **[WeeklyBackupSchedule](#yandex.cloud.ydb.v1.WeeklyBackupSchedule)**

Includes only one of the fields `daily_backup_schedule`, `weekly_backup_schedule`, `recurring_backup_schedule`. ||
|| recurring_backup_schedule | **[RecurringBackupSchedule](#yandex.cloud.ydb.v1.RecurringBackupSchedule)**

Includes only one of the fields `daily_backup_schedule`, `weekly_backup_schedule`, `recurring_backup_schedule`. ||
|| next_execute_time | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

output only field: when next backup will be executed
using provided schedule. ||
|#

## DailyBackupSchedule {#yandex.cloud.ydb.v1.DailyBackupSchedule}

#|
||Field | Description ||
|| execute_time | **[google.type.TimeOfDay](https://github.com/googleapis/googleapis/blob/master/google/type/timeofday.proto)**

Required field.  ||
|#

## WeeklyBackupSchedule {#yandex.cloud.ydb.v1.WeeklyBackupSchedule}

#|
||Field | Description ||
|| days_of_week[] | **[DaysOfWeekBackupSchedule](#yandex.cloud.ydb.v1.DaysOfWeekBackupSchedule)** ||
|#

## DaysOfWeekBackupSchedule {#yandex.cloud.ydb.v1.DaysOfWeekBackupSchedule}

#|
||Field | Description ||
|| days[] | enum **DayOfWeek**

- `DAY_OF_WEEK_UNSPECIFIED`: The unspecified day-of-week.
- `MONDAY`: The day-of-week of Monday.
- `TUESDAY`: The day-of-week of Tuesday.
- `WEDNESDAY`: The day-of-week of Wednesday.
- `THURSDAY`: The day-of-week of Thursday.
- `FRIDAY`: The day-of-week of Friday.
- `SATURDAY`: The day-of-week of Saturday.
- `SUNDAY`: The day-of-week of Sunday. ||
|| execute_time | **[google.type.TimeOfDay](https://github.com/googleapis/googleapis/blob/master/google/type/timeofday.proto)**

Required field.  ||
|#

## RecurringBackupSchedule {#yandex.cloud.ydb.v1.RecurringBackupSchedule}

#|
||Field | Description ||
|| start_time | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

Required field. Timestamp of the first recurrence. ||
|| recurrence | **string**

Required field. An RRULE (https://tools.ietf.org/html/rfc5545#section-3.8.5.3) for how
this backup reccurs.
The FREQ values of MINUTELY, and SECONDLY are not supported. ||
|#

## MonitoringConfig {#yandex.cloud.ydb.v1.MonitoringConfig}

#|
||Field | Description ||
|| alerts[] | **[Alert](#yandex.cloud.ydb.v1.Alert)** ||
|#

## Alert {#yandex.cloud.ydb.v1.Alert}

#|
||Field | Description ||
|| alert_id | **string**

output only field. ||
|| alert_template_id | **string**

template of the alert. ||
|| name | **string**

name of the alert. ||
|| description | **string**

human readable description of the alert. ||
|| notification_channels[] | **[NotificationChannel](#yandex.cloud.ydb.v1.NotificationChannel)**

the notification channels of the alert. ||
|| alert_parameters[] | **[AlertParameter](#yandex.cloud.ydb.v1.AlertParameter)**

alert parameters to override. ||
|| alert_thresholds[] | **[AlertParameter](#yandex.cloud.ydb.v1.AlertParameter)**

alert paratemers to override. ||
|#

## NotificationChannel {#yandex.cloud.ydb.v1.NotificationChannel}

#|
||Field | Description ||
|| notification_channel_id | **string** ||
|| notify_about_statuses[] | enum **AlertEvaluationStatus**

- `ALERT_EVALUATION_STATUS_UNSPECIFIED`
- `ALERT_EVALUATION_STATUS_OK`
- `ALERT_EVALUATION_STATUS_NO_DATA`
- `ALERT_EVALUATION_STATUS_ERROR`
- `ALERT_EVALUATION_STATUS_ALARM`
- `ALERT_EVALUATION_STATUS_WARN` ||
|| repeate_notify_delay_ms | **int64** ||
|#

## AlertParameter {#yandex.cloud.ydb.v1.AlertParameter}

#|
||Field | Description ||
|| double_parameter_value | **[DoubleParameterValue](#yandex.cloud.ydb.v1.AlertParameter.DoubleParameterValue)**

Includes only one of the fields `double_parameter_value`, `integer_parameter_value`, `text_parameter_value`, `text_list_parameter_value`, `label_list_parameter_value`. ||
|| integer_parameter_value | **[IntegerParameterValue](#yandex.cloud.ydb.v1.AlertParameter.IntegerParameterValue)**

Includes only one of the fields `double_parameter_value`, `integer_parameter_value`, `text_parameter_value`, `text_list_parameter_value`, `label_list_parameter_value`. ||
|| text_parameter_value | **[TextParameterValue](#yandex.cloud.ydb.v1.AlertParameter.TextParameterValue)**

Includes only one of the fields `double_parameter_value`, `integer_parameter_value`, `text_parameter_value`, `text_list_parameter_value`, `label_list_parameter_value`. ||
|| text_list_parameter_value | **[TextListParameterValue](#yandex.cloud.ydb.v1.AlertParameter.TextListParameterValue)**

Includes only one of the fields `double_parameter_value`, `integer_parameter_value`, `text_parameter_value`, `text_list_parameter_value`, `label_list_parameter_value`. ||
|| label_list_parameter_value | **[LabelListParameterValue](#yandex.cloud.ydb.v1.AlertParameter.LabelListParameterValue)**

Includes only one of the fields `double_parameter_value`, `integer_parameter_value`, `text_parameter_value`, `text_list_parameter_value`, `label_list_parameter_value`. ||
|#

## DoubleParameterValue {#yandex.cloud.ydb.v1.AlertParameter.DoubleParameterValue}

#|
||Field | Description ||
|| name | **string**

Required. Parameter name ||
|| value | **double**

Required. Parameter value ||
|#

## IntegerParameterValue {#yandex.cloud.ydb.v1.AlertParameter.IntegerParameterValue}

#|
||Field | Description ||
|| name | **string**

Required. Parameter name ||
|| value | **int64**

Required. Parameter value ||
|#

## TextParameterValue {#yandex.cloud.ydb.v1.AlertParameter.TextParameterValue}

#|
||Field | Description ||
|| name | **string**

Required. Parameter name ||
|| value | **string**

Required. Parameter value ||
|#

## TextListParameterValue {#yandex.cloud.ydb.v1.AlertParameter.TextListParameterValue}

#|
||Field | Description ||
|| name | **string**

Required. Parameter name ||
|| values[] | **string**

Required. Parameter value ||
|#

## LabelListParameterValue {#yandex.cloud.ydb.v1.AlertParameter.LabelListParameterValue}

#|
||Field | Description ||
|| name | **string**

Required. Parameter name ||
|| values[] | **string**

Required. Parameter value ||
|#