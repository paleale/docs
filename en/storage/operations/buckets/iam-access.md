---
title: Configuring access permissions for a {{ objstorage-full-name }} bucket using {{ iam-name }}
description: Follow this guide to assign a role for a bucket.
---

# Configuring access permissions for a bucket using {{ iam-name }}

{% include [full-overview](../../../_includes/storage/security/full-overview.md) %}

To configure access to a [bucket](../../concepts/bucket.md) using [{{ iam-name }}](../../security/index.md), assign a user, user group, or service account a [role](../../security/index.md#roles-list) for that bucket:

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select **{{ ui-key.yacloud.iam.folder.dashboard.label_storage }}** from the list of services.
  1. Click the name of the bucket you want to grant access to.
  1. In the left-hand menu, select **{{ ui-key.yacloud.storage.bucket.switch_security }}**.
  1. Navigate to the **{{ ui-key.yacloud.common.resource-acl.label_access-bindings }}** tab.
  1. Click **{{ ui-key.yacloud.common.resource-acl.button_new-bindings }}**.
  1. Select a user from the list or use the user search option.
  1. Click **{{ ui-key.yacloud_components.acl.button.add-role }}**.
  1. Select a role for the user.
  1. Click **{{ ui-key.yacloud_components.acl.action.apply }}**.

{% endlist %}

You can also [assign](../../../iam/operations/roles/grant.md) a role for a bucket in {{ iam-name }}.