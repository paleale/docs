---
editable: false
sourcePath: en/_api-ref-grpc/organizationmanager/v1/saml/api-ref/grpc/Certificate/create.md
---

# SAML Federation API, gRPC: CertificateService.Create

Creates a certificate in the specified federation.

## gRPC request

**rpc Create ([CreateCertificateRequest](#yandex.cloud.organizationmanager.v1.saml.CreateCertificateRequest)) returns ([operation.Operation](#yandex.cloud.operation.Operation))**

## CreateCertificateRequest {#yandex.cloud.organizationmanager.v1.saml.CreateCertificateRequest}

```json
{
  "federation_id": "string",
  "name": "string",
  "description": "string",
  "data": "string"
}
```

#|
||Field | Description ||
|| federation_id | **string**

ID of the federation to add new certificate.
To get the federation ID make a [yandex.cloud.organizationmanager.v1.saml.FederationService.List](/docs/organization/saml/api-ref/grpc/Federation/list#List) request. ||
|| name | **string**

Name of the certificate.
The name must be unique within the federation. ||
|| description | **string**

Description of the certificate. ||
|| data | **string**

Certificate data in PEM format. ||
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
    "certificate_id": "string"
  },
  // Includes only one of the fields `error`, `response`
  "error": "google.rpc.Status",
  "response": {
    "id": "string",
    "federation_id": "string",
    "name": "string",
    "description": "string",
    "created_at": "google.protobuf.Timestamp",
    "data": "string"
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
|| metadata | **[CreateCertificateMetadata](#yandex.cloud.organizationmanager.v1.saml.CreateCertificateMetadata)**

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
|| response | **[Certificate](#yandex.cloud.organizationmanager.v1.saml.Certificate)**

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

## CreateCertificateMetadata {#yandex.cloud.organizationmanager.v1.saml.CreateCertificateMetadata}

#|
||Field | Description ||
|| certificate_id | **string**

ID of the certificate that is being created. ||
|#

## Certificate {#yandex.cloud.organizationmanager.v1.saml.Certificate}

A certificate.

#|
||Field | Description ||
|| id | **string**

Required field. ID of the certificate. ||
|| federation_id | **string**

Required field. ID of the federation that the certificate belongs to. ||
|| name | **string**

Name of the certificate. ||
|| description | **string**

Description of the certificate. ||
|| created_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

Creation timestamp. ||
|| data | **string**

Required field. Certificate data in PEM format. ||
|#