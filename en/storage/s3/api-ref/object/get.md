# get method

Returns an object from {{ objstorage-name }}.

{% include [s3-api-intro-include](../../../../_includes/storage/s3-api-intro-include.md) %}

## Request {#request}

```http
GET /{bucket}/{key} HTTP/2
```

### Path parameters {#path-parameters}

Parameter | Description
----- | -----
`bucket` | Bucket name.
`key` | Object key.


### Request parameters {#request-params}

Parameter | Description
----- | -----
`response-content-type` | It sets the `Content-Type` response header.
`response-content-language` | It sets the `Content-Language` response header.
`response-expires` | It sets the `Expires` response header.
`response-cache-control` | It sets the `Cache-Control` response header.
`response-content-disposition` | It sets the `Content-Disposition` response header.
`response-content-encoding` | It sets the `Content-Encoding` response header.
`versionId` | Link to a specific version of the object.


### Headers {#request-headers}

Use the appropriate [common headers](../common-request-headers.md) in your request.

You can also use the following headers in your request:

Header | Description
----- | -----
`Range` | Sets the byte range to be uploaded from the object.<br/><br/>For more information about the `Range` header, see the HTTP specification [http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.35](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.35).
`If-Modified-Since` | If it is specified, {{ objstorage-name }} returns the following:<br/>- Object. If it has been modified since the specified time.<br/>- Code 304. If the object has not been modified since the specified time.<br/><br/>If a request has both the `If-Modified-Since` and `If-None-Match` headers and their checks result in `If-Modified-Since -> true` and `If-None-Match -> false`, {{ objstorage-name }} returns a 304 code. For more information, see [RFC 7232](https://tools.ietf.org/html/rfc7232).
`If-Unmodified-Since` | If it is specified, {{ objstorage-name }} returns the following:<br/>- Object. If it has not been modified since the specified time.<br/>- Code 412. If the object has been modified since the specified time.<br/><br/>If a request has both the `If-Unmodified-Since` and `If-Match` headers and their checks result in `If-Unmodified-Since -> false` and `If-Match -> true`, {{ objstorage-name }} returns a 200 code and the requested data. For more information, see [RFC 7232](https://tools.ietf.org/html/rfc7232).
`If-Match` | If it is specified, {{ objstorage-name }} returns the following:<br/>- Object. If its `ETag` matches the provided one.<br/>- Code 412. If its `ETag` does not match the provided one.<br/><br/><br/>If a request has both the `If-Unmodified-Since` and `If-Match` headers and their checks result in `If-Unmodified-Since -> false` and `If-Match -> true`, {{ objstorage-name }} returns a 200 code and the requested data. For more information, see [RFC 7232](https://tools.ietf.org/html/rfc7232).
`If-None-Match` | If it is specified, {{ objstorage-name }} returns the following:<br/>- Object. If its `ETag` does not match the provided one.<br/>- Code 304. If its `ETag` matches the provided one.<br/><br/><br/>If a request has both the `If-Modified-Since` and `If-None-Match` headers and their checks result in `If-Modified-Since -> true` and `If-None-Match -> false`, {{ objstorage-name }} returns a 304 code. For more information, see [RFC 7232](https://tools.ietf.org/html/rfc7232).

## Response {#response}

### Headers {#response-headers}

In addition to [common headers](../common-response-headers.md), you can see in a response the headers listed in the table below.


Header | Description
----- | -----
`X-Amz-Meta-*` | User-defined object metadata.
`X-Amz-Storage-Class` | [Storage class](../../../concepts/storage-class.md) of the object.<br/>It takes the `COLD` value if the object is in a cold storage, or `ICE`, if it is in an ice storage.<br/><br/>If the object is in a standard storage, there is no header.
`X-Amz-Server-Side-Encryption` | Encryption algorithm used to encrypt the object. It is returned if the object was uploaded with [encryption](../../../operations/buckets/encrypt.md) enabled.
`X-Amz-Server-Side-Encryption-Aws-Kms-Key-Id` | ID of the [{{ kms-short-name }} key](../../../../kms/concepts/key.md). It is returned if the object was uploaded with [encryption](../../../operations/buckets/encrypt.md) enabled.
`X-Amz-Object-Lock-Mode` | <p>Type of [retention](../../../concepts/object-lock.md) put on the object (if the bucket is [versioned](../../../concepts/versioning.md) and object lock is enabled in it):</p><ul><li>`GOVERNANCE`: Governance-mode retention.</li><li>`COMPLIANCE`: Compliance-mode retention.</li></ul><p>For an object version, you can use only retention (the `X-Amz-Object-Lock-Mode` and `X-Amz-Object-Lock-Retain-Until-Date` headers), only legal hold (`X-Amz-Object-Lock-Legal-Hold`), or both at the same time. For more information about their combined use, see [{#T}](../../../concepts/object-lock.md#types).</p>
`X-Amz-Object-Lock-Retain-Until-Date` | Retention end date and time in any format described in the [HTTP standard](https://www.rfc-editor.org/rfc/rfc9110#name-date-time-formats), e.g., `Mon, 12 Dec 2022 09:00:00 GMT`. Specify it only with the `X-Amz-Object-Lock-Mode` header.
`X-Amz-Object-Lock-Legal-Hold` | <p>Type of [legal hold](../../../concepts/object-lock.md) put on the object (if the bucket is [versioned](../../../concepts/versioning.md) and object lock is enabled in it):</p><ul><li>`ON`: Enabled.</li><li>`OFF`: Disabled.</li></ul><p>For an object version, you can use only retention (the `X-Amz-Object-Lock-Mode` and `X-Amz-Object-Lock-Retain-Until-Date` headers), only legal hold (`X-Amz-Object-Lock-Legal-Hold`), or both at the same time. For more information about their combined use, see [{#T}](../../../concepts/object-lock.md#types).</p>


### Response codes {#response-codes}

For a list of possible responses, see [{#T}](../response-codes.md).

{% include [the-s3-api-see-also-include](../../../../_includes/storage/the-s3-api-see-also-include.md) %}