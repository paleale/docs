---
title: Invoking a container in {{ serverless-containers-full-name }}
description: You can invoke a container using an HTTPS, trigger, or the {{ api-gw-full-name }} extension.
---

# Invoking a container in {{ serverless-containers-name }}

You can invoke a container:
* Over [HTTPS](#https).
* Using a [trigger](#trigger).
* Using a [{{ api-gw-full-name }} extension](#extension).

{% include [active-revision](../../_includes/serverless-containers/active-revision.md) %}

{% include [port-variable-note](../../_includes/serverless-containers/port-variable-note.md) %}

{% include [invoke-container](../../_includes/serverless-containers/invoke-container.md) %}

## HTTPS {#https}

When calling a container over HTTPS, an HTTP request is passed to the application deployed in the container.

### Filtering message headers {#filter}

When being passed to the container, some HTTP request and response headers change, as described below.

{% list tabs %}

- Request headers {#request-headers}
    
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

- Response headers {#response-headers}
        
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

{% include [ip](../../_includes/serverless-containers/ip.md) %}

## Triggers {#trigger}

When invoking a container using a trigger, an HTTP POST request is sent to the [address the container is invoked at](../operations/invocation-link.md). The request body contains a JSON description of the trigger event. The request source IP is provided in the same way as when [invoking a container using HTTPS](#ip). Learn more about [triggers](trigger/index.md).

## {{ api-gw-full-name }} extension {#extension}

When invoking a container using the {{ api-gw-name }}, the container is handed over an HTTP request addressed to the API gateway. In which case the `Host` header specifies the host used by the user to access the API gateway, not the container's host. The request source IP is provided in the same way as when [invoking a container using HTTPS](#ip). Learn more about the extension in the [{{ api-gw-full-name }} documentation](../../api-gateway/concepts/extensions/containers.md).

## Use cases {#examples}

* [{#T}](../tutorials/movies-database.md)
* [{#T}](../tutorials/pg-connect.md)
* [{#T}](../tutorials/functions-framework-to-container.md)