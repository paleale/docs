# CreatePlatformApplication

Creates a [mobile push notification channel](../concepts/push.md).

## HTTP request {#request}

```http
POST https://{{ cns-host }}/
```

### Query parameters {#parameters}

#|
|| **Parameter** | **Description** ||

|| `Action` | **string**
This is a required field.
Operation type parameter.
Value: `CreatePlatformApplication`.||

|| `Name` | **string**
This is a required field.
Notification channel name. See the naming requirements in [{#T}](../operations/push/channel-create.md).
E.g., `com.example.app`.||

|| `Platform` | **string**
This is a required field.
Platform for sending mobile push notifications or in-browser notifications.
The possible values are:
- `APNS`: [Apple Push Notification service](https://developer.apple.com/notifications/).
- `APNS_SANDBOX`: Apple Push Notification service for app testing.
- `FCM`: [Firebase Cloud Messaging](https://firebase.google.com/).
- `HMS`: [Huawei Mobile Services](https://developer.huawei.com/consumer/).
- `RUSTORE`: [RuStore](https://www.rustore.ru/help/sdk/push-notifications/).
- `WEB`: [In-browser push notifications](https://developer.mozilla.org/en-US/docs/Web/API/Push_API).||

|| `FolderId` | **string**
Required field when authenticating via an IAM token.
[ID of the folder](../../resource-manager/operations/folder/get-id.md) the notification channel is created in. When authenticating via a static service account key, if FolderId is not specified, the channel is created in the same folder as the service account.
E.g., `b1gsm0k26v1l********`.||

|| `Attributes.entry.N.key` | **string**
This is a required field.
Attribute key. `N` is a numeric value.
E.g., `Attributes.entry.1.key=PlatformPrincipal&Attributes.entry.2.key=PlatformCredential`.||

|| `Attributes.entry.N.value` | **string**
This is a required field.
Attribute value. `N` is a numeric value.
E.g., `Attributes.entry.1.value=c8gzjriSVxDDzX2fAV********&Attributes.entry.2.value=CgB6e3x9iW/qiE9l9wAUPK0e/bJQe5uIgTlYUD4bP********`.||

|| `ResponseFormat` | **string**
Response format.
The possible values are:
- `XML` (default)
- `JSON`.||
|#

### Attributes {#attributes}

#### Common attributes {#attributes-common}

Attribute | Description
--- | ---
`Description` | **string**<br/>Application description.<br/>Example: `Test application`

#### APNS and APNS_SANDBOX attributes {#attributes-apns}

Attribute | Description
--- | ---
`PlatformPrincipal` | **string**<br/>Token ID in `.p8` format or SSL certificate in `.p12` format. Token-based authentication is preferred as a more modern option.
`PlatformCredential` | **string**<br/>Token or private key of the SSL certificate.
`ApplePlatformTeamID` | **string**<br/>Developer ID, only when using a token.
`ApplePlatformBundleID` | **string**<br/>App ID (bundle ID), only when using a token.

#### FCM attributes {#attributes-fcm}

Attribute | Description
--- | ---
`PlatformCredential` | **string**<br/>Key of the Google Cloud service account in JSON format for authentication with the HTTP v1 API or API key (server key) for authentication with the legacy API. The HTTP v1 API is preferred as [FCM will no longer support](https://firebase.google.com/docs/cloud-messaging/migrate-v1) the legacy API starting from June 2024.

#### HMS attributes {#attributes-hms}

Attribute | Description
--- | ---
`PlatformPrincipal` | **string**<br/>Key ID.
`PlatformCredential` | **string**<br/>API key.

#### RUSTORE attributes {#attributes-rustore}

Attribute | Description
--- | ---
`PlatformPrincipal` | **string**<br/>Project ID.
`PlatformCredential` | **string**<br/> Service token.

For more information about authentication attributes, see the [Mobile push notification channels](../concepts/push.md) subsection.

## Response {#response}

### Successful response {#response-200}

If there are no errors, {{ cns-name }} returns the `200` HTTP code.

A successful response contains additional data in XML or JSON format depending on the specified `ResponseFormat` parameter.

Data schema:

{% list tabs %}

- XML

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <CreatePlatformApplicationResponse>
	  <ResponseMetadata>
		  <RequestId>string</RequestId>
	  </ResponseMetadata>
	  <CreatePlatformApplicationResult>
		  <PlatformApplicationArn>string</PlatformApplicationArn>
	  </CreatePlatformApplicationResult>
  </CreatePlatformApplicationResponse>
  ```

- JSON

  ```json
  {
    "ResponseMetadata": {
      "RequestId": "string"
    },
    "CreatePlatformApplicationResult": {
      "PlatformApplicationArn": "string"
    }
  }
  ```

{% endlist %}

Where:
* `RequestId`: Request ID
* `PlatformApplicationArn`: Notification channel ID (ARN)

### Error response {#response-4xx}

In case of an error, {{ cns-name }} returns a message with the appropriate HTTP code and its additional description in XML or JSON format depending on the specified `ResponseFormat` parameter.

Data schema:

{% list tabs %}

- XML

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <ErrorResponseXML>
	  <RequestId>string</RequestId>
	  <Error>
		  <Code>string</Code>
		  <Message>string</Message>
	  </Error>
  </ErrorResponseXML>
  ```

- JSON

  ```json
  {
    "ErrorResponse": {
      "RequestId": "string",
      "Error": {
        "Code": "string",
        "SubCode": "string",
        "Message": "string"
      }
    }
  }
  ```

{% endlist %}

Where:
* `RequestId`: Request ID
* `Code`: Error code
* `Message`: Error description

For a list of common error codes for all actions, see [{#T}](common-errors.md).

Errors specific for `CreatePlatformApplication`:

HTTP | Error code | Extended code | Description
--- | --- | --- | ---
400 | InvalidParameter | AppAlreadyExists | A mobile push notification channel with such name and platform already exists.
400 | InvalidParameter | DeletedAppAlreadyExists | You cannot use the name and platform to create a new mobile push notification channel because a channel with the same parameters was recently deleted, and the mobile platform data has not yet been updated.

## See also {#see-also}

* [{#T}](index.md)
* [{#T}](send-request.md)
* [CreatePlatformApplication API action](https://docs.aws.amazon.com/sns/latest/api/API_CreatePlatformApplication.html) in the AWS documentation
