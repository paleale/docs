---
title: Static website hosting (html, css, javascript)
description: Static website hosting enables you to host your static website based on HTML, CSS, or JavaScript. Your website should not contain any scripts that run on the web server side.
---

# Hosting static websites

{% include [static-site-information](../../_includes/storage/static-site-information.md) %}

{% note info %}

To enable hosting, you need [public access](../operations/buckets/bucket-availability.md) to the bucket. Otherwise, {{ objstorage-name }} will return the 403 error code if you try to access the website.

{% endnote %}

{{ objstorage-name }} allows you to configure a bucket:

* For static website hosting.

  {% cut "Upload your website content to the bucket and specify the home page" %}

  ```xml
  <WebsiteConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
    <IndexDocument>
        <Suffix>index.html</Suffix>
    </IndexDocument>
    <ErrorDocument>
        <Key>error.html</Key>
    </ErrorDocument>
  </WebsiteConfiguration>
  ```

  {% endcut %}

* For redirecting all requests.

  {% cut "You can specify the host to which all requests will be redirected, as well as the protocol for transmitting requests" %}

  ```xml
  <WebsiteConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
    <RedirectAllRequestsTo>
        <HostName>example.com</HostName>
        <Protocol>http</Protocol>
    </RedirectAllRequestsTo>
  </WebsiteConfiguration>
  ```

  {% endcut %}

* For conditional redirects.

  Using routing rules, you can redirect requests based on the object name prefixes or HTTP response codes. This enables you to redirect object requests to different web pages (if the object was removed) or redirect the requests that return errors.

  {% cut "Example of a rule that redirects a request to a deleted folder to another page" %}

  ```xml
  <WebsiteConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
    <RoutingRules>
      <RoutingRule>
        <Condition>
          <KeyPrefixEquals>temp/</KeyPrefixEquals>
        </Condition>
        <Redirect>
          <ReplaceKeyWith>folderdeleted.html</ReplaceKeyWith>
        </Redirect>
      </RoutingRule>
    </RoutingRules>
  </WebsiteConfiguration>
  ```

  {% endcut %}

You can configure [static website hosting](../operations/hosting/setup.md#hosting), [redirection for all requests](../operations/hosting/setup.md#redirects), or [conditional request redirection](../operations/hosting/setup.md#redirects-on-conditions) using the {{ yandex-cloud }} management console.

All hosting settings are available through the Amazon S3-compatible [HTTP API](../s3/api-ref/hosting.md).

After you configure the bucket for hosting, the website will become accessible at:


```
http(s)://<bucket_name>.{{ s3-web-host }}
```

or

```
http(s)://{{ s3-web-host }}/<bucket_name>
```

{% include [bucket-https](../../_includes/storage/bucket-https.md) %}

{% include [redirect-https](../../_includes/storage/redirect-https.md) %}


{% include [tls-support-alert](../../_includes/storage/tls-support-alert.md) %}



When accessing your website, you will get responses with the codes described in [{#T}](../s3/api-ref/hosting/answer-codes.md).

When hosting a website, you can:

* [Support multiple domain names](../operations/hosting/multiple-domains.md).
* [Use your own domain](../operations/hosting/own-domain.md).

  To use HTTPS with your own domain, specify the FQDN of the required domain in the bucket name.

You can manage {{ dns-full-name }} domains in the bucket settings or in [{{ dns-name }}](../../dns/operations/index.md).

{% include [public-link](../../_includes/storage/public-link.md) %}


## Use cases {#examples}

* [{#T}](../tutorials/user-agent-statistics.md)
* [{#T}](../tutorials/static/index.md)
* [{#T}](../tutorials/gatsby-static-website.md)
* [{#T}](../tutorials/alice-shareable-todolist.md)
