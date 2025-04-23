---
title: Invoking a function in {{ sf-full-name }}
description: In this guide, you will learn about invoking a {{ sf-name }} function using an HTTPS request, the CLI, a trigger or an {{ api-gw-name }} extension.
---

# Invoking a function in {{ sf-name }}

You can invoke a function: 
* [Using an HTTPS request](#http).
* [Using the CLI](#cli).
* [Using a trigger](#trigger).
* [Using a {{ api-gw-full-name }} extension](#extension).
* [Using {{ monitoring-short-name }} events](#monitoring).

Each method has its own data structure for function requests and responses. You can invoke a specific function [version](function.md#version) using a [tag](function.md#tag). Learn more about how to [invoke a function](../operations/function/function-invoke.md).

## Invoking a function using HTTPS {#http}

If a function is invoked for processing an HTTPS request, it gets the request data in JSON format in the first argument: the HTTP method name, headers, arguments, and other request parameters.

The result returned by the function should also be a JSON document. It should contain the HTTP response code, response headers, and response content. {{ sf-name }} automatically processes this JSON document and returns data in a standard HTTPS response to the user.

{% note info %}

You can run a function by specifying the `?integration=raw` request string parameter. When invoked this way, a function cannot parse or set HTTP headers: 
* HTTPS request body content is provided as the first argument (without converting to a JSON structure).
* The contents of the HTTPS response body is identical to the function response (without converting or checking the structure), and the HTTP response status is 200.

{% endnote %}

### Request structure {#request}

JSON query structure:

```
{
    "httpMethod": "<HTTP method name>",
    "headers": <dictionary with HTTP header string values>,
    "multiValueHeaders": <dictionary with lists of HTTP header values>,
    "queryStringParameters": <dictionary of queryString parameters>,
    "multiValueQueryStringParameters": <dictionary with lists of queryString parameter values>,
    "requestContext": <dictionary with request context>,
    "body": "<request contents>",
    "isBase64Encoded": <true or false>
}
```

Where:

- `httpMethod`: HTTP method name, such as: DELETE, GET, HEAD, OPTIONS, PATCH, POST, or PUT.
- `headers`: Dictionary of strings with HTTP request headers and their values. If the same header is provided multiple times, the dictionary contains the last provided value.
- `multiValueHeaders`: Dictionary with HTTP request headers and lists of their values. It contains the same keys as the `headers` dictionary; however, if any of the headers was repeated multiple times, its list will contain all the values provided for this header. If the header was provided only once, it is included into this dictionary and its list will contain a single value.
- `queryStringParameters`: Dictionary with query parameters. If the same parameter is specified multiple times, the dictionary will contain the last specified value.
- `multiValueQueryStringParameters`: Dictionary with the list of all specified values for each query parameter. If the same parameter is specified multiple times, the dictionary will contain all the specified values.
- `requestContext` has the following structure: 
  
    ```
    {
        "identity": "<a set of key:value pairs to authenticate the user>",
        "httpMethod": "<DELETE, GET, HEAD, OPTIONS, PATCH, POST, or PUT>",
        "requestId": "<request ID generated in the router>",
        "requestTime": "<request time in CLF format>",
        "requestTimeEpoch": "<request time in Unix format>",
        "authorizer": "<dictionary with authorization context>",
        "apiGateway": "<dictionary with specific data transmitted by API gateway during function invocation>",
        "connectionId": "<web socket connection ID>",
        "connectedAt": "<web socket connection time>",
        "eventType": "<type of web socket event or operation: CONNECT, MESSAGE, DISCONNECT>",
        "messageId": "<ID of the message received from the web socket>",
        "disconnectStatusCode": "<web socket close code>",
        "disconnectReason": "<text description of the reason the web socket was closed>"
    }
    ```
               
    Structure of the `identity` element:
    ```
    {
        "sourceIp": "<address the request originated from>",
        "userAgent": "<contents of the User-Agent HTTP header of the original request>"
    }
    ```

    Structure of the `apiGateway` element:
    ```
    {
        "operationContext": "<dictionary with operation context described in API gateway specification>"
    }
    ```  

    Structure of the `authorizer` element:
    ```
    {
        "jwt": { // Field filled in by the API Gateway JWT authorizer. It contains the token data about the user and the user's permissions'
          "claims": "<dictionary of JWT body fields>",
          "scopes": "<list of JWT owner permissions>"
        }
        // Other authorization context fields returned from the authorizer function
    }
    ```

- `body`: Request body in string format. Data can be Base64-encoded (in which case {{ sf-name }} will set `isBase64Encoded: true`).

    {% note info %}
    
    If the function is invoked with the `Content-Type: application/json` header, the contents of `body` remains in the original format (`isBase64Encoded: false`).
    
    {% endnote %}
    
- `isBase64Encoded`: If `body` contains Base64-encoded data, then {{ sf-name }} sets the parameter to `true`. 

#### Debugging functions {#example}

To debug parameter processing, use a function that returns the JSON structure of the request. An example of such function is given below.

```js
module.exports.handler = async (event) => {
    return { 
        body: JSON.stringify(event)
    };
};
```

For example, for the request:

```bash
curl \
  --request POST \
  --data "hello, world!" \
  "https://{{ sf-url }}/<function_ID>?a=1&a=2&b=1"
```

The result looks like this: 

```text
{
  "httpMethod": "POST",
  "headers": {
    "Accept": "*/*",
    "Content-Length": "13",
    "Content-Type": "application/x-www-form-urlencoded",
    "User-Agent": "curl/7.58.0",
    "X-Real-Remote-Address": "[88.99.0.24]:37310",
    "X-Request-Id": "cd0d12cd-c5f1-4348-9dff-c50a78f1eb79",
    "X-Trace-Id": "92c5ad34-54f7-41df-a368-d4361bf376eb"
  },
  "path": "",
  "multiValueHeaders": {
    "Accept": [ "*/*" ],
    "Content-Length": [ "13" ],
    "Content-Type": [ "application/x-www-form-urlencoded" ],
    "User-Agent": [ "curl/7.58.0" ],
    "X-Real-Remote-Address": [ "[88.99.0.24]:37310" ],
    "X-Request-Id": [ "cd0d12cd-c5f1-4348-9dff-c50a78f1eb79" ],
    "X-Trace-Id": [ "92c5ad34-54f7-41df-a368-d4361bf376eb" ]
  },
  "queryStringParameters": {
    "a": "2",
    "b": "1"
  },
  "multiValueQueryStringParameters": {
    "a": [ "1", "2" ],
    "b": [ "1" ]
  },
  "requestContext": {
    "identity": {
      "sourceIp": "88.99.0.24",
      "userAgent": "curl/7.58.0"
    },
    "httpMethod": "POST",
    "requestId": "cd0d12cd-c5f1-4348-9dff-c50a78f1eb79",
    "requestTime": "26/Dec/2019:14:22:07 +0000",
    "requestTimeEpoch": 1577370127
  },
  "body": "aGVsbG8sIHdvcmxkIQ==",
  "isBase64Encoded": true
}
```

### Service data {#service-data} 

Optionally, the function can accept the second argument with the following structure:

```
{
  "requestId": "<request ID>",
  "functionName": "<function ID>",
  "functionVersion": "<function version ID>",
  "memoryLimitInMB": "<function version memory size, MB>",
  "token": "<IAM token, optional>",
}
```

Where:

- `requestId`: ID of the function call, generated when the function is accessed and displayed in the function call log.
- `functionName`: Function ID.
- `functionVersion`: Function version ID.
- `memoryLimitInMB`: Memory size specified for the function version, in MB.
- `token`: [IAM token](../../iam/concepts/authorization/iam-token.md) of the service account specified for the function version. The current value is generated automatically. It is used for working with the [{{ yandex-cloud }} API](../../api-design-guide/). This field is present only if the correct service account is specified for the function version.

Example of using service data in a function:

```js
module.exports.handler = async (event, context) => {
    const iamToken = context.token;
    ...
    return { 
        body: ...
    };
};
```

### Response structure {#response}

{{ sf-name }} interprets the function execution result to fill in the HTTPS response contents, headers, and status code. For this to work, the function must return a response in the following structure:

``` 
{
    "statusCode": <HTTP response code>,
    "headers": <dictionary with HTTP header string values>,
    "multiValueHeaders": <dictionary with lists of HTTP header values>,
    "body": "<response contents>",
    "isBase64Encoded": <true or false>
}
```       

Where: 

- `statusCode`: HTTP status code, which the client uses to interpret the request results. 
- `headers`: Dictionary of strings with HTTP response headers and their values.
- `multiValueHeaders`: Dictionary where you can list one or more values for HTTP response headers. If the same header is specified in both `headers` and `multiValueHeaders`, the contents of the `headers` dictionary is ignored.
- `body`: Response body in string format. To work with binary data, the contents can be Base64-encoded. If this is the case, set `isBase64Encoded: true`.
- `isBase64Encoded`: If `body` is Base64-encoded, set the parameter to `true`.

### Handling errors in user-defined function code {#error}

If an unprocessed error occurs in user code, {{ sf-name }} will return a 502 error and error details in the form of this JSON structure: 

```
{
  "errorMessage": "<error message>",
  "errorType": "<error type>",
  "stackTrace": <list of invoked methods, optional>
}
```

Where:

- `errorMessage`: Error description string.
- `errorType`: Error or exception type that depends on the programming language.
- `stackTrace`: Function execution stack at the time of the error.

The specific contents of these fields depend on the programming language and your function's runtime environment.

#### Error in the event the response has an invalid JSON structure {#uncorrect-json}

If the response structure of your function does not match the description given in [Response data structure](#response), {{ sf-name }} returns the result with a 502 error code and the following response:

```
{
  "errorMessage": "Malformed serverless function response: not a valid json",
  "errorType": "ProxyIntegrationError",
  "payload": "<original contents of the function response>"
}
```

### Possible {{ sf-name }} response codes {#http-state}

If the error occurs in a user-defined function, the `X-Function-Error: true` header is added to the response.

{{ sf-name }} can return results with the following HTTP codes: 

- `200 OK`: Function executed successfully.
- `400 BadRequest`: Error in HTTPS request parameters.
- `403 Forbidden`: Cannot execute the request due to restrictions on client access to the function. 
- `404 Not Found`: Function is not found at the specified URL.
- `413 Payload Too Large`: Request JSON structure [limit](../concepts/limits.md#limits) is exceeded by more than 3.5 MB.
- `429 TooManyRequests`: Function call intensity is too high: 
    - The [quota](../concepts/limits.md#functions-quotas) on the number of requests executed is exceeded.
    - Cannot execute the request because all executors are already overloaded by existing requests to this function.
- `500 Internal Server Error`: Internal server error.
- `502 BadGateway`: Incorrect function code or format of returned JSON response.
- `503 Service Unavailable`: {{ sf-name }} is unavailable. 
- `504 Gateway Timeout`: Maximum function running time before the timeout is exceeded.

### Filtering message headers {#headers}

Your function receives and transmits the contents of HTTP headers as JSON fields (see the description [above](#http)). Some request and response headers are removed or renamed as described below.

{% list tabs %}

- Request headers
    
    Removed from a request:

    - "Expect"
    - "Te"
    - "Trailer"
    - "Upgrade"
    - "Proxy-Authenticate"
    - "Authorization"
    - "Connection"        
    - "Content-Md5"       
    - "Max-Forwards"
    - "Server"
    - "Transfer-Encoding"
    - "Www-Authenticate"
    - "Cookie"

- Response headers
        
    - Removed from a response:
        - "Host"
        - "Authorization"
        - "User-Agent"
        - "Connection"
        - "Max-Forwards"
        - "Cookie"
        - "X-Request-Id"
        - "X-Function-Id"
        - "X-Function-Version-Id"
        - "X-Content-Type-Options"
    
    - Cause an error if present in a response:
    
        - "Proxy-Authenticate"
        - "Transfer-Encoding"
        - "Via"
    
    - Overwritten by adding the `X-Yf-Remapped-` prefix:
        - "Content-Md5"
        - "Date"
        - "Server"
        - "Www-Authenticate"

{% endlist %}

### IP address of the request source {#ip}

If a request contains the [X-Forwarded-For](https://en.wikipedia.org/wiki/X-Forwarded-For) header, the specified IP addresses and the IP address of the client that invoked the function are provided in this header. If not, it only passes the IP address of the client that invoked the function.

### Use cases {#examples-https}

* [{#T}](../tutorials/connect-to-ydb-nodejs.md)

## Invoking a function using the {{ yandex-cloud }} CLI {#cli}

A function call via the CLI is an HTTPS request which uses the POST method and the `?integration=raw` parameter (without converting the request into a JSON structure or checking the response).

View the help for the function call command: 

```
yc serverless function invoke --help
Invoke the specified function

Usage:
  yc serverless function invoke <FUNCTION-NAME>|<FUNCTION-ID> [Flags...] [Global Flags...]

Flags:
      --id string          Function id.
      --name string        Function name.
      --tag string         Tag. Default $latest.
  -d, --data string        Data to be sent
      --data-file string   Data (file location) to be sent
      --data-stdin         Await stdin for data to be sent
```

Detailed description of how to transfer data using different flags and arguments:

- If a flag or argument is omitted, an empty string is provided.

- `-d, --data`: Data is provided as an argument.

    ```
    yc serverless function invoke <function ID> -d '{"queryStringParameters": {"parameter_name": "parameter_value"}}'
    ```

- `--data-file`: Data is read from a file.

    ```
    yc serverless function invoke <function ID> --data-file <File path>
    ```

    Similar to command with the `-d` argument set to `@<file_name>`: `yc serverless function invoke <function_ID> -d @<file_path>`

- `--data-stdin`: Data is read from the input stream. 

     ```
     echo '{"queryStringParameters": {"parameter_name": "parameter_value"}}' | yc serverless function invoke <function ID> --data-stdin
     ```

    Similar to command with the `-d` argument set to `@-`: 
    
    ```
    echo '{"queryStringParameters": {"parameter_name": "parameter_value"}}' | yc serverless function invoke <function ID> -d @-`.
    ```

## Invoking a function using a trigger {#trigger}

When invoking a function using a trigger, the JSON description of a trigger event is provided in the body of an HTTP request to the function. The request source IP is provided in the same way as when [invoking a function using HTTPS](#ip). Learn more about [triggers](trigger/index.md).

### Use cases {#examples-trigger}

* [{#T}](../tutorials/data-recording.md)
* [{#T}](../tutorials/events-from-postbox-to-yds.md)
* [{#T}](../tutorials/logging-functions.md)
* [{#T}](../tutorials/logging.md)
* [{#T}](../tutorials/regular-launch-datasphere.md)
* [{#T}](../tutorials/serverless-trigger-budget-vm.md)
* [{#T}](../tutorials/video-converting-queue.md)

## Invoking a function using a {{ api-gw-full-name }} extension {#extension}

When invoking a function using the {{ api-gw-name }} extension, the function receives an HTTP request addressed to the API gateway. In which case the `Host` header specifies the host used by the user to access the API gateway, not the function's host. The request source IP is provided in the same way as when [invoking a function using HTTPS](#ip). Learn more about the extension in the [{{ api-gw-full-name }} documentation](../../api-gateway/concepts/extensions/cloud-functions.md).

### Use cases {#examples-api-gw}

* [{#T}](../tutorials/canary-release.md)
* [{#T}](../tutorials/java-servlet-todo-list.md)
* [{#T}](../tutorials/serverless-url-shortener.md)
* [{#T}](../tutorials/slack-bot-serverless.md)
* [{#T}](../tutorials/telegram-bot-serverless.md)
* [{#T}](../tutorials/websocket-app.md)

## Invoking functions using {{ monitoring-short-name }} events {#monitoring}

You can integrate {{ sf-name }} functions into {{ monitoring-full-name }} for automatic processing of incidents and other events. To do this, add a function to the [notification channel](../../monitoring/operations/alert/create-channel-function.md). You can invoke a {{ sf-name }} function when an [alert](../../monitoring/concepts/alerting/alert.md) fires or in a {{ monitoring-short-name }} [escalation](../../monitoring/concepts/alerting/escalations.md).

A function must be invoked in [asynchronous mode](../concepts/function-invoke-async.md).

See [{#T}](../../monitoring/operations/alert/create-channel-function.md) for an example of a function for invoking an external API when an alert fires.
