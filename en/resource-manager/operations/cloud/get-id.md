---
title: How to get a cloud ID in {{ yandex-cloud }}
description: In this tutorial, you will learn how to get a cloud ID in {{ yandex-cloud }}.
---

# Getting a cloud ID

{% list tabs group=instructions %}

- Management console {#console}

  1. Go to the [management console]({{ link-console-main }}) and [select](switch-cloud.md) the appropriate cloud. On the page that opens, the cloud ID is shown on top, next to the cloud name, and in the **{{ ui-key.yacloud.common.id }}** line of the **{{ ui-key.yacloud.iam.cloud.switch_overview }}** tab.
 
  1. To copy the ID, hover over it and click ![image](../../../_assets/console-icons/copy.svg).

- CLI {#cli}

  If you know the name of the cloud, provide it as a parameter in the `get` command:

  ```
  yc resource-manager cloud get <cloud_name>
  ```
  Result:

  ```
  id: b1gd129pp9ha********
  ...
  ```

  If you do not know the cloud ID, retrieve a list of clouds with their IDs:

  {% include [get-cloud-list](../../../_includes/resource-manager/get-cloud-list.md) %}

- API {#api}

  To get the list of clouds with IDs, use the [list](../../api-ref/Cloud/list.md) REST API method for the [Cloud](../../api-ref/Cloud/index.md) resource or the [CloudService/List](../../api-ref/grpc/Cloud/list.md) gRPC API call.

{% endlist %}
