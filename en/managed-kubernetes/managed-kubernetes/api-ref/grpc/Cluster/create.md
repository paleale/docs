---
editable: false
sourcePath: en/_api-ref-grpc/k8s/v1/managed-kubernetes/api-ref/grpc/Cluster/create.md
---

# Managed Services for Kubernetes API, gRPC: ClusterService.Create

Creates a Kubernetes cluster in the specified folder.

## gRPC request

**rpc Create ([CreateClusterRequest](#yandex.cloud.k8s.v1.CreateClusterRequest)) returns ([operation.Operation](#yandex.cloud.operation.Operation))**

## CreateClusterRequest {#yandex.cloud.k8s.v1.CreateClusterRequest}

```json
{
  "folder_id": "string",
  "name": "string",
  "description": "string",
  "labels": "map<string, string>",
  "network_id": "string",
  "master_spec": {
    // Includes only one of the fields `zonal_master_spec`, `regional_master_spec`
    "zonal_master_spec": {
      "zone_id": "string",
      "internal_v4_address_spec": {
        "subnet_id": "string"
      },
      "external_v4_address_spec": {
        "address": "string"
      }
    },
    "regional_master_spec": {
      "region_id": "string",
      "locations": [
        {
          "zone_id": "string",
          "internal_v4_address_spec": {
            "subnet_id": "string"
          }
        }
      ],
      "external_v4_address_spec": {
        "address": "string"
      },
      "external_v6_address_spec": {
        "address": "string"
      }
    },
    // end of the list of possible fields
    "locations": [
      {
        "zone_id": "string",
        "subnet_id": "string"
      }
    ],
    "etcd_cluster_size": "int64",
    "external_v4_address_spec": {
      "address": "string"
    },
    "external_v6_address_spec": {
      "address": "string"
    },
    "version": "string",
    "maintenance_policy": {
      "auto_upgrade": "bool",
      "maintenance_window": {
        // Includes only one of the fields `anytime`, `daily_maintenance_window`, `weekly_maintenance_window`
        "anytime": "AnytimeMaintenanceWindow",
        "daily_maintenance_window": {
          "start_time": "google.type.TimeOfDay",
          "duration": "google.protobuf.Duration"
        },
        "weekly_maintenance_window": {
          "days_of_week": [
            {
              "days": [
                "DayOfWeek"
              ],
              "start_time": "google.type.TimeOfDay",
              "duration": "google.protobuf.Duration"
            }
          ]
        }
        // end of the list of possible fields
      }
    },
    "security_group_ids": [
      "string"
    ],
    "master_logging": {
      "enabled": "bool",
      // Includes only one of the fields `log_group_id`, `folder_id`
      "log_group_id": "string",
      "folder_id": "string",
      // end of the list of possible fields
      "audit_enabled": "bool",
      "cluster_autoscaler_enabled": "bool",
      "kube_apiserver_enabled": "bool",
      "events_enabled": "bool"
    },
    "scale_policy": {
      // Includes only one of the fields `fixed_scale`, `auto_scale`
      "fixed_scale": {
        "resource_preset_id": "string"
      },
      "auto_scale": {
        "min_resource_preset_id": "string"
      }
      // end of the list of possible fields
    }
  },
  "ip_allocation_policy": {
    "cluster_ipv4_cidr_block": "string",
    "node_ipv4_cidr_mask_size": "int64",
    "service_ipv4_cidr_block": "string",
    "cluster_ipv6_cidr_block": "string",
    "service_ipv6_cidr_block": "string"
  },
  // Includes only one of the fields `gateway_ipv4_address`
  "gateway_ipv4_address": "string",
  // end of the list of possible fields
  "service_account_id": "string",
  "node_service_account_id": "string",
  "release_channel": "ReleaseChannel",
  "network_policy": {
    "provider": "Provider"
  },
  "kms_provider": {
    "key_id": "string"
  },
  // Includes only one of the fields `cilium`
  "cilium": {
    "routing_mode": "RoutingMode"
  }
  // end of the list of possible fields
}
```

#|
||Field | Description ||
|| folder_id | **string**

Required field. ID of the folder to create a Kubernetes cluster in.
To get the folder ID use a [yandex.cloud.resourcemanager.v1.FolderService.List](/docs/resource-manager/api-ref/grpc/Folder/list#List) request. ||
|| name | **string**

Name of the Kubernetes cluster.
The name must be unique within the folder. ||
|| description | **string**

Description of the Kubernetes cluster. ||
|| labels | **object** (map<**string**, **string**>)

Resource labels as `key:value` pairs. ||
|| network_id | **string**

Required field. ID of the network. ||
|| master_spec | **[MasterSpec](#yandex.cloud.k8s.v1.MasterSpec)**

Required field. Master specification of the Kubernetes cluster. ||
|| ip_allocation_policy | **[IPAllocationPolicy](#yandex.cloud.k8s.v1.IPAllocationPolicy)**

IP allocation policy of the Kubernetes cluster. ||
|| gateway_ipv4_address | **string**

Gateway IPv4 address.

Includes only one of the fields `gateway_ipv4_address`. ||
|| service_account_id | **string**

Required field. Service account to be used for provisioning Compute Cloud and VPC resources for Kubernetes cluster.
Selected service account should have `edit` role on the folder where the Kubernetes cluster will be
located and on the folder where selected network resides. ||
|| node_service_account_id | **string**

Required field. Service account to be used by the worker nodes of the Kubernetes cluster to access Container Registry or to push node logs and metrics. ||
|| release_channel | enum **ReleaseChannel**

Release channel for the master.

- `RELEASE_CHANNEL_UNSPECIFIED`
- `RAPID`: Minor updates with new functions and improvements are often added.
You can't disable automatic updates in this channel, but you can specify a time period for automatic updates.
- `REGULAR`: New functions and improvements are added in chunks shortly after they appear on `RAPID`.
- `STABLE`: Only updates related to bug fixes or security improvements are added. ||
|| network_policy | **[NetworkPolicy](#yandex.cloud.k8s.v1.NetworkPolicy)** ||
|| kms_provider | **[KMSProvider](#yandex.cloud.k8s.v1.KMSProvider)**

KMS provider configuration. ||
|| cilium | **[Cilium](#yandex.cloud.k8s.v1.Cilium)**

Includes only one of the fields `cilium`. ||
|#

## MasterSpec {#yandex.cloud.k8s.v1.MasterSpec}

#|
||Field | Description ||
|| zonal_master_spec | **[ZonalMasterSpec](#yandex.cloud.k8s.v1.ZonalMasterSpec)**

Specification of the zonal master.

Includes only one of the fields `zonal_master_spec`, `regional_master_spec`. ||
|| regional_master_spec | **[RegionalMasterSpec](#yandex.cloud.k8s.v1.RegionalMasterSpec)**

Specification of the regional master.

Includes only one of the fields `zonal_master_spec`, `regional_master_spec`. ||
|| locations[] | **[LocationSpec](#yandex.cloud.k8s.v1.LocationSpec)**

Locations specification for Kubernetes control-plane (master) instances.
Works in conjunction with `etcd_cluster_size`. See it's documentation for details.
Possible combinations:
- 1 location and etcd_cluster_size = 1 - a single node cluster whose availability is limited by the availability of a single Compute Instance; downtime is expected during cluster updates.
- 1 location and etcd_cluster_size = 3 - a highly available cluster within a single availability zone; can survive the failure of a Compute Instance, a server, or an individual server rack.
- 3 location and etcd_cluster_size = 3 - a highly available cluster with each etcd instance located within separate availability zone; can survive the failure of a single availability zone. ||
|| etcd_cluster_size | **int64**

Number of etcd nodes in cluster.
Works in conjunction with `locations`. See it's documentation for details.
Optional. If not set, will be assumed equal to the number of locations. ||
|| external_v4_address_spec | **[ExternalAddressSpec](#yandex.cloud.k8s.v1.ExternalAddressSpec)**

Specification of parameters for external IPv4 networking. ||
|| external_v6_address_spec | **[ExternalAddressSpec](#yandex.cloud.k8s.v1.ExternalAddressSpec)**

Specification of parameters for external IPv6 networking. ||
|| version | **string**

Version of Kubernetes components that runs on the master. ||
|| maintenance_policy | **[MasterMaintenancePolicy](#yandex.cloud.k8s.v1.MasterMaintenancePolicy)**

Maintenance policy of the master. ||
|| security_group_ids[] | **string**

Master security groups. ||
|| master_logging | **[MasterLogging](#yandex.cloud.k8s.v1.MasterLogging)**

Cloud Logging for master components. ||
|| scale_policy | **[MasterScalePolicySpec](#yandex.cloud.k8s.v1.MasterScalePolicySpec)**

Scale policy of the master. ||
|#

## ZonalMasterSpec {#yandex.cloud.k8s.v1.ZonalMasterSpec}

#|
||Field | Description ||
|| zone_id | **string**

Required field. ID of the availability zone. ||
|| internal_v4_address_spec | **[InternalAddressSpec](#yandex.cloud.k8s.v1.InternalAddressSpec)**

Specification of parameters for internal IPv4 networking. ||
|| external_v4_address_spec | **[ExternalAddressSpec](#yandex.cloud.k8s.v1.ExternalAddressSpec)**

Specification of parameters for external IPv4 networking. ||
|#

## InternalAddressSpec {#yandex.cloud.k8s.v1.InternalAddressSpec}

#|
||Field | Description ||
|| subnet_id | **string**

ID of the subnet. If no ID is specified, and there only one subnet in specified zone, an address in this subnet will be allocated. ||
|#

## ExternalAddressSpec {#yandex.cloud.k8s.v1.ExternalAddressSpec}

#|
||Field | Description ||
|| address | **string**

IP address. ||
|#

## RegionalMasterSpec {#yandex.cloud.k8s.v1.RegionalMasterSpec}

#|
||Field | Description ||
|| region_id | **string**

Required field. ID of the availability zone where the master resides. ||
|| locations[] | **[MasterLocation](#yandex.cloud.k8s.v1.MasterLocation)**

List of locations where the master will be allocated. ||
|| external_v4_address_spec | **[ExternalAddressSpec](#yandex.cloud.k8s.v1.ExternalAddressSpec)**

Specify to allocate a static public IP for the master. ||
|| external_v6_address_spec | **[ExternalAddressSpec](#yandex.cloud.k8s.v1.ExternalAddressSpec)**

Specification of parameters for external IPv6 networking. ||
|#

## MasterLocation {#yandex.cloud.k8s.v1.MasterLocation}

#|
||Field | Description ||
|| zone_id | **string**

Required field. ID of the availability zone. ||
|| internal_v4_address_spec | **[InternalAddressSpec](#yandex.cloud.k8s.v1.InternalAddressSpec)**

If not specified and there is a single subnet in specified zone, address
in this subnet will be allocated. ||
|#

## LocationSpec {#yandex.cloud.k8s.v1.LocationSpec}

#|
||Field | Description ||
|| zone_id | **string**

Required field. ID of the availability zone where the master resides. ||
|| subnet_id | **string**

ID of the VPC network's subnet where the master resides.
If not specified and there is a single subnet in specified zone, address in this subnet will be allocated. ||
|#

## MasterMaintenancePolicy {#yandex.cloud.k8s.v1.MasterMaintenancePolicy}

#|
||Field | Description ||
|| auto_upgrade | **bool**

If set to true, automatic updates are installed in the specified period of time with no interaction from the user.
If set to false, automatic upgrades are disabled. ||
|| maintenance_window | **[MaintenanceWindow](#yandex.cloud.k8s.v1.MaintenanceWindow)**

Maintenance window settings. Update will start at the specified time and last no more than the specified duration.
The time is set in UTC. ||
|#

## MaintenanceWindow {#yandex.cloud.k8s.v1.MaintenanceWindow}

#|
||Field | Description ||
|| anytime | **[AnytimeMaintenanceWindow](#yandex.cloud.k8s.v1.AnytimeMaintenanceWindow)**

Updating the master at any time.

Includes only one of the fields `anytime`, `daily_maintenance_window`, `weekly_maintenance_window`.

Maintenance policy. ||
|| daily_maintenance_window | **[DailyMaintenanceWindow](#yandex.cloud.k8s.v1.DailyMaintenanceWindow)**

Updating the master on any day during the specified time window.

Includes only one of the fields `anytime`, `daily_maintenance_window`, `weekly_maintenance_window`.

Maintenance policy. ||
|| weekly_maintenance_window | **[WeeklyMaintenanceWindow](#yandex.cloud.k8s.v1.WeeklyMaintenanceWindow)**

Updating the master on selected days during the specified time window.

Includes only one of the fields `anytime`, `daily_maintenance_window`, `weekly_maintenance_window`.

Maintenance policy. ||
|#

## AnytimeMaintenanceWindow {#yandex.cloud.k8s.v1.AnytimeMaintenanceWindow}

#|
||Field | Description ||
|| Empty | > ||
|#

## DailyMaintenanceWindow {#yandex.cloud.k8s.v1.DailyMaintenanceWindow}

#|
||Field | Description ||
|| start_time | **[google.type.TimeOfDay](https://github.com/googleapis/googleapis/blob/master/google/type/timeofday.proto)**

Required field. Window start time, in the UTC timezone. ||
|| duration | **[google.protobuf.Duration](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/duration)**

Window duration. ||
|#

## WeeklyMaintenanceWindow {#yandex.cloud.k8s.v1.WeeklyMaintenanceWindow}

#|
||Field | Description ||
|| days_of_week[] | **[DaysOfWeekMaintenanceWindow](#yandex.cloud.k8s.v1.DaysOfWeekMaintenanceWindow)**

Days of the week and the maintenance window for these days when automatic updates are allowed. ||
|#

## DaysOfWeekMaintenanceWindow {#yandex.cloud.k8s.v1.DaysOfWeekMaintenanceWindow}

#|
||Field | Description ||
|| days[] | enum **DayOfWeek**

Days of the week when automatic updates are allowed.

- `DAY_OF_WEEK_UNSPECIFIED`: The unspecified day-of-week.
- `MONDAY`: The day-of-week of Monday.
- `TUESDAY`: The day-of-week of Tuesday.
- `WEDNESDAY`: The day-of-week of Wednesday.
- `THURSDAY`: The day-of-week of Thursday.
- `FRIDAY`: The day-of-week of Friday.
- `SATURDAY`: The day-of-week of Saturday.
- `SUNDAY`: The day-of-week of Sunday. ||
|| start_time | **[google.type.TimeOfDay](https://github.com/googleapis/googleapis/blob/master/google/type/timeofday.proto)**

Required field. Window start time, in the UTC timezone. ||
|| duration | **[google.protobuf.Duration](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/duration)**

Window duration. ||
|#

## MasterLogging {#yandex.cloud.k8s.v1.MasterLogging}

#|
||Field | Description ||
|| enabled | **bool**

Identifies whether Cloud Logging is enabled for master components. ||
|| log_group_id | **string**

ID of the log group where logs of master components should be stored.

Includes only one of the fields `log_group_id`, `folder_id`.

The destination of master components' logs. ||
|| folder_id | **string**

ID of the folder where logs should be stored (in default group).

Includes only one of the fields `log_group_id`, `folder_id`.

The destination of master components' logs. ||
|| audit_enabled | **bool**

Identifies whether Cloud Logging is enabled for audit logs. ||
|| cluster_autoscaler_enabled | **bool**

Identifies whether Cloud Logging is enabled for cluster-autoscaler. ||
|| kube_apiserver_enabled | **bool**

Identifies whether Cloud Logging is enabled for kube-apiserver. ||
|| events_enabled | **bool**

Identifies whether Cloud Logging is enabled for events. ||
|#

## MasterScalePolicySpec {#yandex.cloud.k8s.v1.MasterScalePolicySpec}

#|
||Field | Description ||
|| fixed_scale | **[FixedScale](#yandex.cloud.k8s.v1.MasterScalePolicySpec.FixedScale)**

Includes only one of the fields `fixed_scale`, `auto_scale`. ||
|| auto_scale | **[AutoScale](#yandex.cloud.k8s.v1.MasterScalePolicySpec.AutoScale)**

Includes only one of the fields `fixed_scale`, `auto_scale`. ||
|#

## FixedScale {#yandex.cloud.k8s.v1.MasterScalePolicySpec.FixedScale}

Fixed master instance resources.

#|
||Field | Description ||
|| resource_preset_id | **string**

Required field. Preset of computing resources to be used by master. ||
|#

## AutoScale {#yandex.cloud.k8s.v1.MasterScalePolicySpec.AutoScale}

Scalable master instance resources.

#|
||Field | Description ||
|| min_resource_preset_id | **string**

Required field. Preset of computing resources to be used as lower boundary for scaling. ||
|#

## IPAllocationPolicy {#yandex.cloud.k8s.v1.IPAllocationPolicy}

#|
||Field | Description ||
|| cluster_ipv4_cidr_block | **string**

CIDR block. IP range for allocating pod addresses.

It should not overlap with any subnet in the network the Kubernetes cluster located in. Static routes will be
set up for this CIDR blocks in node subnets. ||
|| node_ipv4_cidr_mask_size | **int64**

Size of the masks that are assigned for each node in the cluster.

If not specified, 24 is used. ||
|| service_ipv4_cidr_block | **string**

CIDR block. IP range Kubernetes service Kubernetes cluster IP addresses will be allocated from.

It should not overlap with any subnet in the network the Kubernetes cluster located in. ||
|| cluster_ipv6_cidr_block | **string**

IPv6 range for allocating pod IP addresses. ||
|| service_ipv6_cidr_block | **string**

IPv6 range for allocating Kubernetes service IP addresses ||
|#

## NetworkPolicy {#yandex.cloud.k8s.v1.NetworkPolicy}

#|
||Field | Description ||
|| provider | enum **Provider**

- `PROVIDER_UNSPECIFIED`
- `CALICO` ||
|#

## KMSProvider {#yandex.cloud.k8s.v1.KMSProvider}

#|
||Field | Description ||
|| key_id | **string**

KMS key ID for secrets encryption.
To obtain a KMS key ID use a [yandex.cloud.kms.v1.SymmetricKeyService.List](/docs/kms/api-ref/grpc/SymmetricKey/list#List) request. ||
|#

## Cilium {#yandex.cloud.k8s.v1.Cilium}

#|
||Field | Description ||
|| routing_mode | enum **RoutingMode**

- `ROUTING_MODE_UNSPECIFIED`
- `TUNNEL` ||
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
    "cluster_id": "string"
  },
  // Includes only one of the fields `error`, `response`
  "error": "google.rpc.Status",
  "response": {
    "id": "string",
    "folder_id": "string",
    "created_at": "google.protobuf.Timestamp",
    "name": "string",
    "description": "string",
    "labels": "map<string, string>",
    "status": "Status",
    "health": "Health",
    "network_id": "string",
    "master": {
      // Includes only one of the fields `zonal_master`, `regional_master`
      "zonal_master": {
        "zone_id": "string",
        "internal_v4_address": "string",
        "external_v4_address": "string"
      },
      "regional_master": {
        "region_id": "string",
        "internal_v4_address": "string",
        "external_v4_address": "string",
        "external_v6_address": "string"
      },
      // end of the list of possible fields
      "locations": [
        {
          "zone_id": "string",
          "subnet_id": "string"
        }
      ],
      "etcd_cluster_size": "int64",
      "version": "string",
      "endpoints": {
        "internal_v4_endpoint": "string",
        "external_v4_endpoint": "string",
        "external_v6_endpoint": "string"
      },
      "master_auth": {
        "cluster_ca_certificate": "string"
      },
      "version_info": {
        "current_version": "string",
        "new_revision_available": "bool",
        "new_revision_summary": "string",
        "version_deprecated": "bool"
      },
      "maintenance_policy": {
        "auto_upgrade": "bool",
        "maintenance_window": {
          // Includes only one of the fields `anytime`, `daily_maintenance_window`, `weekly_maintenance_window`
          "anytime": "AnytimeMaintenanceWindow",
          "daily_maintenance_window": {
            "start_time": "google.type.TimeOfDay",
            "duration": "google.protobuf.Duration"
          },
          "weekly_maintenance_window": {
            "days_of_week": [
              {
                "days": [
                  "DayOfWeek"
                ],
                "start_time": "google.type.TimeOfDay",
                "duration": "google.protobuf.Duration"
              }
            ]
          }
          // end of the list of possible fields
        }
      },
      "security_group_ids": [
        "string"
      ],
      "master_logging": {
        "enabled": "bool",
        // Includes only one of the fields `log_group_id`, `folder_id`
        "log_group_id": "string",
        "folder_id": "string",
        // end of the list of possible fields
        "audit_enabled": "bool",
        "cluster_autoscaler_enabled": "bool",
        "kube_apiserver_enabled": "bool",
        "events_enabled": "bool"
      },
      "resources": {
        "cores": "int64",
        "core_fraction": "int64",
        "memory": "int64"
      },
      "scale_policy": {
        // Includes only one of the fields `fixed_scale`, `auto_scale`
        "fixed_scale": {
          "resource_preset_id": "string"
        },
        "auto_scale": {
          "min_resource_preset_id": "string"
        }
        // end of the list of possible fields
      }
    },
    "ip_allocation_policy": {
      "cluster_ipv4_cidr_block": "string",
      "node_ipv4_cidr_mask_size": "int64",
      "service_ipv4_cidr_block": "string",
      "cluster_ipv6_cidr_block": "string",
      "service_ipv6_cidr_block": "string"
    },
    // Includes only one of the fields `gateway_ipv4_address`
    "gateway_ipv4_address": "string",
    // end of the list of possible fields
    "service_account_id": "string",
    "node_service_account_id": "string",
    "release_channel": "ReleaseChannel",
    "network_policy": {
      "provider": "Provider"
    },
    "kms_provider": {
      "key_id": "string"
    },
    "log_group_id": "string",
    // Includes only one of the fields `cilium`
    "cilium": {
      "routing_mode": "RoutingMode"
    },
    // end of the list of possible fields
    "scheduled_maintenance": {
      "delayed_until": "google.protobuf.Timestamp",
      "available_from": "google.protobuf.Timestamp",
      "no_later_than": "google.protobuf.Timestamp",
      "description": "string"
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
|| metadata | **[CreateClusterMetadata](#yandex.cloud.k8s.v1.CreateClusterMetadata)**

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
|| response | **[Cluster](#yandex.cloud.k8s.v1.Cluster)**

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

## CreateClusterMetadata {#yandex.cloud.k8s.v1.CreateClusterMetadata}

#|
||Field | Description ||
|| cluster_id | **string**

ID of the Kubernetes cluster that is being created. ||
|#

## Cluster {#yandex.cloud.k8s.v1.Cluster}

A Kubernetes cluster.

#|
||Field | Description ||
|| id | **string**

ID of the Kubernetes cluster. ||
|| folder_id | **string**

ID of the folder that the Kubernetes cluster belongs to. ||
|| created_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

Creation timestamp. ||
|| name | **string**

Name of the Kubernetes cluster. ||
|| description | **string**

Description of the Kubernetes cluster. 0-256 characters long. ||
|| labels | **object** (map<**string**, **string**>)

Resource labels as `key:value` pairs. Maximum of 64 per resource. ||
|| status | enum **Status**

Status of the Kubernetes cluster.

- `STATUS_UNSPECIFIED`
- `PROVISIONING`: Kubernetes cluster is waiting for resources to be allocated.
- `RUNNING`: Kubernetes cluster is running.
- `RECONCILING`: Kubernetes cluster is being reconciled.
- `STOPPING`: Kubernetes cluster is being stopped.
- `STOPPED`: Kubernetes cluster stopped.
- `DELETING`: Kubernetes cluster is being deleted.
- `STARTING`: Kubernetes cluster is being started. ||
|| health | enum **Health**

Health of the Kubernetes cluster.

- `HEALTH_UNSPECIFIED`
- `HEALTHY`: Kubernetes cluster is alive and well.
- `UNHEALTHY`: Kubernetes cluster is inoperable. ||
|| network_id | **string**

ID of the network the Kubernetes cluster belongs to. ||
|| master | **[Master](#yandex.cloud.k8s.v1.Master)**

Properties of the master for the Kubernetes cluster. ||
|| ip_allocation_policy | **[IPAllocationPolicy](#yandex.cloud.k8s.v1.IPAllocationPolicy2)**

Allocation policy for IP addresses of services and pods inside the Kubernetes cluster in different availability zones. ||
|| gateway_ipv4_address | **string**

Gateway IPv4 address.

Includes only one of the fields `gateway_ipv4_address`. ||
|| service_account_id | **string**

Service account to be used for provisioning Compute Cloud and VPC resources for Kubernetes cluster. ||
|| node_service_account_id | **string**

Service account to be used by the worker nodes of the Kubernetes cluster to access Container Registry or to push node logs and metrics. ||
|| release_channel | enum **ReleaseChannel**

When creating a Kubernetes cluster, you should specify one of three release channels. The release channel contains several Kubernetes versions.
Channels differ in the set of available versions, the management of auto-updates, and the updates received.
You can't change the channel once the Kubernetes cluster is created, you can only recreate the Kubernetes cluster and specify a new release channel.
For more details see [documentation](/docs/managed-kubernetes/concepts/release-channels-and-updates).

- `RELEASE_CHANNEL_UNSPECIFIED`
- `RAPID`: Minor updates with new functions and improvements are often added.
You can't disable automatic updates in this channel, but you can specify a time period for automatic updates.
- `REGULAR`: New functions and improvements are added in chunks shortly after they appear on `RAPID`.
- `STABLE`: Only updates related to bug fixes or security improvements are added. ||
|| network_policy | **[NetworkPolicy](#yandex.cloud.k8s.v1.NetworkPolicy2)** ||
|| kms_provider | **[KMSProvider](#yandex.cloud.k8s.v1.KMSProvider2)**

KMS provider configuration. ||
|| log_group_id | **string**

Log group where cluster stores cluster system logs, like audit, events, or controlplane logs. ||
|| cilium | **[Cilium](#yandex.cloud.k8s.v1.Cilium2)**

Includes only one of the fields `cilium`. ||
|| scheduled_maintenance | **[ScheduledMaintenance](#yandex.cloud.k8s.v1.ScheduledMaintenance)** ||
|#

## Master {#yandex.cloud.k8s.v1.Master}

#|
||Field | Description ||
|| zonal_master | **[ZonalMaster](#yandex.cloud.k8s.v1.ZonalMaster)**

Parameters of the availability zone for the master.

Includes only one of the fields `zonal_master`, `regional_master`. ||
|| regional_master | **[RegionalMaster](#yandex.cloud.k8s.v1.RegionalMaster)**

Parameters of the region for the master.

Includes only one of the fields `zonal_master`, `regional_master`. ||
|| locations[] | **[Location](#yandex.cloud.k8s.v1.Location)**

Locations specification for Kubernetes control-plane (master) instances. ||
|| etcd_cluster_size | **int64**

Number of etcd nodes in cluster. ||
|| version | **string**

Version of Kubernetes components that runs on the master. ||
|| endpoints | **[MasterEndpoints](#yandex.cloud.k8s.v1.MasterEndpoints)**

Endpoints of the master. Endpoints constitute of scheme and port (i.e. `https://ip-address:port`)
and can be used by the clients to communicate with the Kubernetes API of the Kubernetes cluster. ||
|| master_auth | **[MasterAuth](#yandex.cloud.k8s.v1.MasterAuth)**

Master authentication parameters are used to establish trust between the master and a client. ||
|| version_info | **[VersionInfo](#yandex.cloud.k8s.v1.VersionInfo)**

Detailed information about the Kubernetes version that is running on the master. ||
|| maintenance_policy | **[MasterMaintenancePolicy](#yandex.cloud.k8s.v1.MasterMaintenancePolicy2)**

Maintenance policy of the master. ||
|| security_group_ids[] | **string**

Master security groups. ||
|| master_logging | **[MasterLogging](#yandex.cloud.k8s.v1.MasterLogging2)**

Cloud Logging for master components. ||
|| resources | **[MasterResources](#yandex.cloud.k8s.v1.MasterResources)**

Computing resources of each master instance such as the amount of memory and number of cores. ||
|| scale_policy | **[MasterScalePolicy](#yandex.cloud.k8s.v1.MasterScalePolicy)**

Scale policy of the master. ||
|#

## ZonalMaster {#yandex.cloud.k8s.v1.ZonalMaster}

#|
||Field | Description ||
|| zone_id | **string**

ID of the availability zone where the master resides. ||
|| internal_v4_address | **string**

IPv4 internal network address that is assigned to the master. ||
|| external_v4_address | **string**

IPv4 external network address that is assigned to the master. ||
|#

## RegionalMaster {#yandex.cloud.k8s.v1.RegionalMaster}

#|
||Field | Description ||
|| region_id | **string**

ID of the region where the master resides. ||
|| internal_v4_address | **string**

IPv4 internal network address that is assigned to the master. ||
|| external_v4_address | **string**

IPv4 external network address that is assigned to the master. ||
|| external_v6_address | **string**

IPv6 external network address that is assigned to the master. ||
|#

## Location {#yandex.cloud.k8s.v1.Location}

#|
||Field | Description ||
|| zone_id | **string**

ID of the availability zone where the master resides. ||
|| subnet_id | **string**

ID of the VPC network's subnet where the master resides. ||
|#

## MasterEndpoints {#yandex.cloud.k8s.v1.MasterEndpoints}

#|
||Field | Description ||
|| internal_v4_endpoint | **string**

Internal endpoint that can be used to connect to the master from cloud networks. ||
|| external_v4_endpoint | **string**

External endpoint that can be used to access Kubernetes cluster API from the internet (outside of the cloud). ||
|| external_v6_endpoint | **string**

External IPv6 endpoint that can be used to access Kubernetes cluster API from the internet (outside of the cloud). ||
|#

## MasterAuth {#yandex.cloud.k8s.v1.MasterAuth}

#|
||Field | Description ||
|| cluster_ca_certificate | **string**

PEM-encoded public certificate that is the root of trust for the Kubernetes cluster. ||
|#

## VersionInfo {#yandex.cloud.k8s.v1.VersionInfo}

#|
||Field | Description ||
|| current_version | **string**

Current Kubernetes version, format: major.minor (e.g. 1.15). ||
|| new_revision_available | **bool**

Newer revisions may include Kubernetes patches (e.g 1.15.1 -> 1.15.2) as well
as some internal component updates - new features or bug fixes in platform specific
components either on the master or nodes. ||
|| new_revision_summary | **string**

Description of the changes to be applied when updating to the latest
revision. Empty if new_revision_available is false. ||
|| version_deprecated | **bool**

The current version is on the deprecation schedule, component (master or node group)
should be upgraded. ||
|#

## MasterMaintenancePolicy {#yandex.cloud.k8s.v1.MasterMaintenancePolicy2}

#|
||Field | Description ||
|| auto_upgrade | **bool**

If set to true, automatic updates are installed in the specified period of time with no interaction from the user.
If set to false, automatic upgrades are disabled. ||
|| maintenance_window | **[MaintenanceWindow](#yandex.cloud.k8s.v1.MaintenanceWindow2)**

Maintenance window settings. Update will start at the specified time and last no more than the specified duration.
The time is set in UTC. ||
|#

## MaintenanceWindow {#yandex.cloud.k8s.v1.MaintenanceWindow2}

#|
||Field | Description ||
|| anytime | **[AnytimeMaintenanceWindow](#yandex.cloud.k8s.v1.AnytimeMaintenanceWindow2)**

Updating the master at any time.

Includes only one of the fields `anytime`, `daily_maintenance_window`, `weekly_maintenance_window`.

Maintenance policy. ||
|| daily_maintenance_window | **[DailyMaintenanceWindow](#yandex.cloud.k8s.v1.DailyMaintenanceWindow2)**

Updating the master on any day during the specified time window.

Includes only one of the fields `anytime`, `daily_maintenance_window`, `weekly_maintenance_window`.

Maintenance policy. ||
|| weekly_maintenance_window | **[WeeklyMaintenanceWindow](#yandex.cloud.k8s.v1.WeeklyMaintenanceWindow2)**

Updating the master on selected days during the specified time window.

Includes only one of the fields `anytime`, `daily_maintenance_window`, `weekly_maintenance_window`.

Maintenance policy. ||
|#

## AnytimeMaintenanceWindow {#yandex.cloud.k8s.v1.AnytimeMaintenanceWindow2}

#|
||Field | Description ||
|| Empty | > ||
|#

## DailyMaintenanceWindow {#yandex.cloud.k8s.v1.DailyMaintenanceWindow2}

#|
||Field | Description ||
|| start_time | **[google.type.TimeOfDay](https://github.com/googleapis/googleapis/blob/master/google/type/timeofday.proto)**

Required field. Window start time, in the UTC timezone. ||
|| duration | **[google.protobuf.Duration](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/duration)**

Window duration. ||
|#

## WeeklyMaintenanceWindow {#yandex.cloud.k8s.v1.WeeklyMaintenanceWindow2}

#|
||Field | Description ||
|| days_of_week[] | **[DaysOfWeekMaintenanceWindow](#yandex.cloud.k8s.v1.DaysOfWeekMaintenanceWindow2)**

Days of the week and the maintenance window for these days when automatic updates are allowed. ||
|#

## DaysOfWeekMaintenanceWindow {#yandex.cloud.k8s.v1.DaysOfWeekMaintenanceWindow2}

#|
||Field | Description ||
|| days[] | enum **DayOfWeek**

Days of the week when automatic updates are allowed.

- `DAY_OF_WEEK_UNSPECIFIED`: The unspecified day-of-week.
- `MONDAY`: The day-of-week of Monday.
- `TUESDAY`: The day-of-week of Tuesday.
- `WEDNESDAY`: The day-of-week of Wednesday.
- `THURSDAY`: The day-of-week of Thursday.
- `FRIDAY`: The day-of-week of Friday.
- `SATURDAY`: The day-of-week of Saturday.
- `SUNDAY`: The day-of-week of Sunday. ||
|| start_time | **[google.type.TimeOfDay](https://github.com/googleapis/googleapis/blob/master/google/type/timeofday.proto)**

Required field. Window start time, in the UTC timezone. ||
|| duration | **[google.protobuf.Duration](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/duration)**

Window duration. ||
|#

## MasterLogging {#yandex.cloud.k8s.v1.MasterLogging2}

#|
||Field | Description ||
|| enabled | **bool**

Identifies whether Cloud Logging is enabled for master components. ||
|| log_group_id | **string**

ID of the log group where logs of master components should be stored.

Includes only one of the fields `log_group_id`, `folder_id`.

The destination of master components' logs. ||
|| folder_id | **string**

ID of the folder where logs should be stored (in default group).

Includes only one of the fields `log_group_id`, `folder_id`.

The destination of master components' logs. ||
|| audit_enabled | **bool**

Identifies whether Cloud Logging is enabled for audit logs. ||
|| cluster_autoscaler_enabled | **bool**

Identifies whether Cloud Logging is enabled for cluster-autoscaler. ||
|| kube_apiserver_enabled | **bool**

Identifies whether Cloud Logging is enabled for kube-apiserver. ||
|| events_enabled | **bool**

Identifies whether Cloud Logging is enabled for events. ||
|#

## MasterResources {#yandex.cloud.k8s.v1.MasterResources}

#|
||Field | Description ||
|| cores | **int64**

The number of cores available to each master instance. ||
|| core_fraction | **int64**

Baseline level of CPU performance with the ability to burst performance above that baseline level.
This field sets baseline performance for each core. ||
|| memory | **int64**

The amount of memory available to each master instance, specified in bytes. ||
|#

## MasterScalePolicy {#yandex.cloud.k8s.v1.MasterScalePolicy}

#|
||Field | Description ||
|| fixed_scale | **[FixedScale](#yandex.cloud.k8s.v1.MasterScalePolicy.FixedScale)**

Includes only one of the fields `fixed_scale`, `auto_scale`. ||
|| auto_scale | **[AutoScale](#yandex.cloud.k8s.v1.MasterScalePolicy.AutoScale)**

Includes only one of the fields `fixed_scale`, `auto_scale`. ||
|#

## FixedScale {#yandex.cloud.k8s.v1.MasterScalePolicy.FixedScale}

Fixed master instance resources.

#|
||Field | Description ||
|| resource_preset_id | **string**

ID of computing resources preset to be used by master. ||
|#

## AutoScale {#yandex.cloud.k8s.v1.MasterScalePolicy.AutoScale}

Autoscaled master instance resources.

#|
||Field | Description ||
|| min_resource_preset_id | **string**

ID of computing resources preset to be used as lower boundary for scaling. ||
|#

## IPAllocationPolicy {#yandex.cloud.k8s.v1.IPAllocationPolicy2}

#|
||Field | Description ||
|| cluster_ipv4_cidr_block | **string**

CIDR block. IP range for allocating pod addresses.

It should not overlap with any subnet in the network the Kubernetes cluster located in. Static routes will be
set up for this CIDR blocks in node subnets. ||
|| node_ipv4_cidr_mask_size | **int64**

Size of the masks that are assigned for each node in the cluster.

If not specified, 24 is used. ||
|| service_ipv4_cidr_block | **string**

CIDR block. IP range Kubernetes service Kubernetes cluster IP addresses will be allocated from.

It should not overlap with any subnet in the network the Kubernetes cluster located in. ||
|| cluster_ipv6_cidr_block | **string**

IPv6 range for allocating pod IP addresses. ||
|| service_ipv6_cidr_block | **string**

IPv6 range for allocating Kubernetes service IP addresses ||
|#

## NetworkPolicy {#yandex.cloud.k8s.v1.NetworkPolicy2}

#|
||Field | Description ||
|| provider | enum **Provider**

- `PROVIDER_UNSPECIFIED`
- `CALICO` ||
|#

## KMSProvider {#yandex.cloud.k8s.v1.KMSProvider2}

#|
||Field | Description ||
|| key_id | **string**

KMS key ID for secrets encryption.
To obtain a KMS key ID use a [yandex.cloud.kms.v1.SymmetricKeyService.List](/docs/kms/api-ref/grpc/SymmetricKey/list#List) request. ||
|#

## Cilium {#yandex.cloud.k8s.v1.Cilium2}

#|
||Field | Description ||
|| routing_mode | enum **RoutingMode**

- `ROUTING_MODE_UNSPECIFIED`
- `TUNNEL` ||
|#

## ScheduledMaintenance {#yandex.cloud.k8s.v1.ScheduledMaintenance}

#|
||Field | Description ||
|| delayed_until | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

Time until which the update should be postponed. ||
|| available_from | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

Time when the update became available. ||
|| no_later_than | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

The latest possible date by which a mandatory update must be applied. ||
|| description | **string**

Description of the planned operation, for example, "Infrastructure planned update". ||
|#