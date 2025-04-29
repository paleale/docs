---
editable: false
sourcePath: en/_api-ref-grpc/kms/v1/asymmetricencryption/api-ref/grpc/AsymmetricEncryptionKey/get.md
---

# Key Management Service API, gRPC: AsymmetricEncryptionKeyService.Get

Returns the specified asymmetric KMS key.

To get the list of available asymmetric KMS keys, make a [SymmetricKeyService.List] request.

## gRPC request

**rpc Get ([GetAsymmetricEncryptionKeyRequest](#yandex.cloud.kms.v1.asymmetricencryption.GetAsymmetricEncryptionKeyRequest)) returns ([AsymmetricEncryptionKey](#yandex.cloud.kms.v1.asymmetricencryption.AsymmetricEncryptionKey))**

## GetAsymmetricEncryptionKeyRequest {#yandex.cloud.kms.v1.asymmetricencryption.GetAsymmetricEncryptionKeyRequest}

```json
{
  "key_id": "string"
}
```

#|
||Field | Description ||
|| key_id | **string**

Required field. ID of the asymmetric KMS key to return.
To get the ID of an asymmetric KMS key use a [AsymmetricEncryptionKeyService.List](/docs/kms/asymmetricencryption/api-ref/grpc/AsymmetricEncryptionKey/list#List) request. ||
|#

## AsymmetricEncryptionKey {#yandex.cloud.kms.v1.asymmetricencryption.AsymmetricEncryptionKey}

```json
{
  "id": "string",
  "folder_id": "string",
  "created_at": "google.protobuf.Timestamp",
  "name": "string",
  "description": "string",
  "labels": "map<string, string>",
  "status": "Status",
  "encryption_algorithm": "AsymmetricEncryptionAlgorithm",
  "deletion_protection": "bool"
}
```

An asymmetric KMS key that may contain several versions of the cryptographic material.

#|
||Field | Description ||
|| id | **string**

ID of the key. ||
|| folder_id | **string**

ID of the folder that the key belongs to. ||
|| created_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

Time when the key was created. ||
|| name | **string**

Name of the key. ||
|| description | **string**

Description of the key. ||
|| labels | **object** (map<**string**, **string**>)

Custom labels for the key as `key:value` pairs. Maximum 64 per key. ||
|| status | enum **Status**

Current status of the key.

- `STATUS_UNSPECIFIED`
- `CREATING`: The key is being created.
- `ACTIVE`: The key is active and can be used for encryption and decryption or signature and verification.
Can be set to INACTIVE using the [AsymmetricKeyService.Update] method.
- `INACTIVE`: The key is inactive and unusable.
Can be set to ACTIVE using the [AsymmetricKeyService.Update] method. ||
|| encryption_algorithm | enum **AsymmetricEncryptionAlgorithm**

Asymmetric Encryption Algorithm ID.

- `ASYMMETRIC_ENCRYPTION_ALGORITHM_UNSPECIFIED`
- `RSA_2048_ENC_OAEP_SHA_256`: RSA-2048 encryption with OAEP padding and SHA-256
- `RSA_3072_ENC_OAEP_SHA_256`: RSA-3072 encryption with OAEP padding and SHA-256
- `RSA_4096_ENC_OAEP_SHA_256`: RSA-4096 encryption with OAEP padding and SHA-256 ||
|| deletion_protection | **bool**

Flag that inhibits deletion of the key ||
|#