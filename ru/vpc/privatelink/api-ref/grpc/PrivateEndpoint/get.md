---
editable: false
sourcePath: en/_api-ref-grpc/vpc/v1/privatelink/api-ref/grpc/PrivateEndpoint/get.md
---

# Virtual Private Cloud API, gRPC: PrivateEndpointService.Get

Returns the specified Private Endpoint resource.

To get the list of all available PrivateEndpoint resources, make a [List](/docs/vpc/privatelink/api-ref/grpc/PrivateEndpoint/list#List)
request.

## gRPC request

**rpc Get ([GetPrivateEndpointRequest](#yandex.cloud.vpc.v1.privatelink.GetPrivateEndpointRequest)) returns ([PrivateEndpoint](#yandex.cloud.vpc.v1.privatelink.PrivateEndpoint))**

## GetPrivateEndpointRequest {#yandex.cloud.vpc.v1.privatelink.GetPrivateEndpointRequest}

```json
{
  "private_endpoint_id": "string"
}
```

#|
||Field | Description ||
|| private_endpoint_id | **string**

Required field. ID of the PrivateEndpoint resource to return.

To get PrivateEndpoint resource ID make a [PrivateEndpointService.List](/docs/vpc/privatelink/api-ref/grpc/PrivateEndpoint/list#List)
request. ||
|#

## PrivateEndpoint {#yandex.cloud.vpc.v1.privatelink.PrivateEndpoint}

```json
{
  "id": "string",
  "folder_id": "string",
  "created_at": "google.protobuf.Timestamp",
  "name": "string",
  "description": "string",
  "labels": "map<string, string>",
  "network_id": "string",
  "status": "Status",
  "address": {
    "subnet_id": "string",
    "address": "string",
    "address_id": "string"
  },
  "dns_options": {
    "private_dns_records_enabled": "bool"
  },
  // Includes only one of the fields `object_storage`
  "object_storage": "ObjectStorage"
  // end of the list of possible fields
}
```

#|
||Field | Description ||
|| id | **string**

ID of the private endpoint. Generated at creation time. ||
|| folder_id | **string**

ID of the folder that the private endpoint belongs to. ||
|| created_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

Creation timestamp. ||
|| name | **string**

Name of the private endpoint.
The name is unique within the folder.
Value must match the regular expression
``\\|[a-zA-Z]([-_a-zA-Z0-9]{0,61}[a-zA-Z0-9])?``. ||
|| description | **string**

Description of the private endpoint. 0-256 characters long. ||
|| labels | **object** (map<**string**, **string**>)

Private endpoint labels as `key:value` pairs.
No more than 64 per resource.
The maximum string length in characters for each value is 63.
Each value must match the regular expression `[-_0-9a-z]*`.
The string length in characters for each key must be 1-63.
Each key must match the regular expression `[a-z][-_0-9a-z]*`. ||
|| network_id | **string**

ID of the network that the private endpoint belongs to. ||
|| status | enum **Status**

Status of the private endpoint.

- `STATUS_UNSPECIFIED`
- `PENDING`: Private endpoint is still creating / updating.
- `AVAILABLE`: Private endpoint is available.
- `DELETING`: Private endpoint is deleting. ||
|| address | **[EndpointAddress](#yandex.cloud.vpc.v1.privatelink.PrivateEndpoint.EndpointAddress)**

Private endpoint ip address details. ||
|| dns_options | **[DnsOptions](#yandex.cloud.vpc.v1.privatelink.PrivateEndpoint.DnsOptions)**

Private endpoint dns options. ||
|| object_storage | **[ObjectStorage](#yandex.cloud.vpc.v1.privatelink.PrivateEndpoint.ObjectStorage)**

Yandex Cloud Object Storage.

Includes only one of the fields `object_storage`.

Service to connect with via private endpoint. ||
|#

## EndpointAddress {#yandex.cloud.vpc.v1.privatelink.PrivateEndpoint.EndpointAddress}

Private endpoint ip address details.

#|
||Field | Description ||
|| subnet_id | **string**

ID of the subnet that the private endpoint address belongs to. ||
|| address | **string**

IP address of the private endpoint. ||
|| address_id | **string**

ID of the private endpoint address. ||
|#

## DnsOptions {#yandex.cloud.vpc.v1.privatelink.PrivateEndpoint.DnsOptions}

#|
||Field | Description ||
|| private_dns_records_enabled | **bool**

If enabled - vpc will create private dns records for specified service. ||
|#

## ObjectStorage {#yandex.cloud.vpc.v1.privatelink.PrivateEndpoint.ObjectStorage}

Yandex Cloud Object Storage.

#|
||Field | Description ||
|| Empty | > ||
|#