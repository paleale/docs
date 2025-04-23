---
title: Bucket
description: A bucket is an {{ objstorage-name }} storage unit allocated for user data. A bucket name is used as part of a URL to access data. Names of {{ yandex-cloud }} buckets are unique, i.e., you cannot create two buckets with the same name, even in different folders of different clouds. Keep this in mind if you are going to create buckets automatically through the API.
keywords:
  - what is a bucket
  - what is a bucket
  - buckets
  - bucket
  - data storage bucket
  - cloud bucket
---

# Bucket in {{ objstorage-name }}

A _bucket_ is an {{ objstorage-name }} storage unit allocated for user data. Each {{ yandex-cloud }} bucket has a [unique name](#naming) used in requests to {{ objstorage-name }}.

Buckets store data as [objects](./object.md). To organize data, you can create multiple buckets or use [folders](./object.md#folder) (prefixes) within a single bucket.

You can also use buckets to [host static websites](./hosting.md).

For more information on getting started with buckets, see [{#T}](../quickstart.md).

You can [create a bucket](../operations/buckets/create.md) via the [management console]({{ link-console-main }}), [YC CLI](../../cli/quickstart.md), [{{ TF }}]({{ tf-provider-resources-link }}/storage_bucket), [API](../../api-design-guide/concepts/general.md), or using some popular [tools](../tools/index.md) designed to work with the Amazon S3 HTTP API.

## Naming buckets {#naming}

A bucket name is used as part of the data access URL and is visible to your users, e.g., `https://{{ s3-storage-host }}/bucket-name`.

The naming requirements are as follows:

- Bucket names are unique throughout {{ objstorage-name }}, i.e., you cannot create two buckets with the same name, even in different folders of different clouds. Keep this in mind if you are going to create buckets automatically through the API.
- Bucket names are subject to the following restrictions:

   {% include [bucket-name-reqs](../../_includes/bucket-name-reqs.md) %}

When choosing a name for your bucket, keep in mind that names containing dots are used for [static website hosting](hosting.md). You may encounter a name conflict that will prevent you or another user from hosting a website in Object Storage.

## Bucket URL {#bucket-url}

You can use the following URL formats to access a bucket:


* `http(s)://{{ s3-storage-host }}/<bucket_name>?<parameters>`
* `http(s)://<bucket_name>.{{ s3-storage-host }}?<parameters>`


{% include [storage-dotnet-host](../_includes_service/storage-dotnet-host.md) %}


## Accessing a bucket via HTTPS {#bucket-https}

{{ objstorage-name }} supports secure connections over HTTPS.

{% include [bucket-https](../../_includes/storage/bucket-https.md) %}

For more information on HTTPS support when hosting websites in {{ objstorage-name }}, see [{#T}](./hosting.md).


{% include [tls-support-alert](../../_includes/storage/tls-support-alert.md) %}



## Bucket settings {#bucket-settings}

You can:

- [Limit the maximum bucket size](../operations/buckets/limit-max-volume.md).

    {{ objstorage-name }} will not allow you to upload an object if doing so leads to exceeding the maximum bucket size.

- Set the default [storage class](storage-class.md).

     By default, objects uploaded to a bucket are saved with the storage class specified for that bucket.

- Configure a bucket for [static website hosting](hosting.md).
- Upload a [CORS configuration](cors.md) for a bucket.
- Enable [bucket encryption](../operations/buckets/encrypt.md).

    By default, objects added to the bucket are encrypted with the specified [{{ kms-short-name }} key](../../kms/concepts/key.md).

- Set up [object lifecycles](lifecycles.md).


## Accessing buckets from {{ vpc-full-name }} cloud networks {#access-via-vpc}

{% include [vpc-pe-preview](../../_includes/vpc/pe-preview.md) %}

{% include [intro-access-via-vpc](../../_includes/storage/intro-access-via-vpc.md) %}

For more information on configuring access, see [{#T}](../operations/buckets/access-via-vpc.md).


## Public access to buckets {#bucket-access}

{% include [public-access](../../_includes/storage/security/public-access.md) %}


## Statistics {#stats}

{{ objstorage-name }} automatically delivers bucket performance metrics to [{{ monitoring-full-name }}](../../monitoring/).

Performance statistics are available on the [bucket page](../operations/buckets/get-stats.md#storage-ui) or in the [{{ monitoring-name }} interface](../operations/buckets/get-stats.md#monitoring).

For the list of metrics delivered to {{ monitoring-name }}, see the [reference](../metrics.md).

You can also access aggregate bucket statistics [through the {{ yandex-cloud }} CLI](../operations/buckets/get-info.md#get-statistics).


## Recommendations and limitations {#details-of-usage}

- Updating bucket statistics may take up to 20 minutes. Therefore, sometimes the specified maximum bucket size may be exceeded (e.g., during fast sequential upload of multiple objects).  
- In the management console, the information about the number of objects in the bucket and used up space is updated with a delay.
- You cannot rename buckets.
- The number of buckets does not affect the performance of {{ objstorage-name }}. How many buckets you use to store your data is up to you.
- Buckets cannot be nested.
- You can only delete an empty bucket.
- After you delete objects from a bucket, the vacated space is not considered free for a while longer.
- After deleting a bucket, you may not be able to create a new one with the same name right away. There is also a risk that another {{ yandex-cloud }} user may create a bucket with this name before you claim it again. Do not delete buckets without a good reason.

  {% note info %}

  If you limit the maximum size of a bucket, it may remain temporarily unavailable for writes even after you free up enough space for new objects.

  {% endnote %}


## Use cases {#examples}

* [{#T}](../tutorials/data-processing-init-actions-geesefs.md)
* [{#T}](../tutorials/s3-disk-connect.md)
* [{#T}](../tutorials/bucket-to-bucket.md)
* [{#T}](../tutorials/batch-recognition-stt.md)
* [{#T}](../tutorials/mgp-config-server-for-s3.md)

### See also {#see-also}

* [{#T}](../security/overview.md)
