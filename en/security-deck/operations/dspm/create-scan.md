---
title: Creating a scan in {{ dspm-name }}
description: Follow this tutorial to create scans in {{ sd-full-name }} {{ dspm-name }}.
---

# Creating a {{ dspm-name }} scan

{% include [note-preview-by-request](../../../_includes/note-preview-by-request.md) %}

[Data Security Posture Management](../../concepts/dspm.md) (DSPM) scans [data sources](../../concepts/dspm.md#data-source) to identify sensitive information in buckets.

To create a scan:

{% list tabs group=instructions %}

- {{ sd-name }} interface {#cloud-sd}

  1. Go to [{{ sd-full-name }}]({{ link-sd-main }}).
  1. In the left-hand panel, select ![Database-Magnifier](../../../_assets/console-icons/database-magnifier.svg) **Data Security Posture Management** and go to the **Scans** tab.
  1. In the top-right corner, click **New scan**.
  1. Under **Data sources**, select the data source you need.

      If you do not have a data source, [create one](./create-data-source.md).
  1. Under **Data search categories**, select the [data categories](../../concepts/dspm.md#data-source) to scan for:

      * `Personal data`: Full names, email addresses, document numbers, etc.
      * `Secrets`: Cloud access keys, passwords, tokens, SSH keys, etc.

      You can select all the categories at once or any one of them.

  1. Click **Create scan**.

{% endlist %}

The new scan will appear in the scan list, ready to run.

#### See also {#see-also}

* [{#T}](./create-data-source.md)
* [{#T}](../../concepts/dspm.md)
* [{#T}](../../security/index.md)