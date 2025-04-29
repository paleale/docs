# Loading data from {{ mos-full-name }} to {{ objstorage-full-name }} using {{ data-transfer-full-name }}

With {{ data-transfer-name }}, you can transfer data from a {{ mos-name }} cluster to {{ objstorage-name }}.

To transfer data:

1. [Prepare the test data](#prepare-data).
1. [Prepare and activate your transfer](#prepare-transfer).
1. [Test the transfer](#verify-transfer).

If you no longer need the resources you created, [delete them](#clear-out).


## Required paid resources {#paid-resources}

The support cost includes:

* {{ mos-name }} cluster fee: Using computing resources allocated to hosts (including hosts with the `MANAGER` role) and disk space (see [{{ mos-name }} pricing](../../../managed-opensearch/pricing.md)).
* Fee for using public IP addresses for cluster hosts (see [{{ vpc-name }} pricing](../../../vpc/pricing.md)).
* {{ objstorage-name }} bucket fee: Storing data and performing operations with it (see [{{ objstorage-name }} pricing](../../../storage/pricing.md)).
* Per-transfer fee: Using computing resources and the number of transferred data rows (see [{{ data-transfer-name }} pricing](../../../data-transfer/pricing.md)).


## Getting started {#before-you-begin}


Set up your infrastructure:

{% list tabs group=instructions %}

- Manually {#manual}

    1. [Create a {{ mos-name }} cluster](../../../managed-opensearch/operations/cluster-create.md) in any suitable configuration with publicly available hosts.

    1. If using security groups in your cluster, make sure they are configured correctly and allow connecting to the [{{ mos-name }}](../../../managed-opensearch/operations/connect.md#configuring-security-groups) cluster.

    1. [Get an SSL certificate](../../../managed-opensearch/operations/connect.md#ssl-certificate) to connect to the {{ mos-name }} cluster.

    1. [Create an {{ objstorage-name }} bucket](../../../storage/operations/buckets/create.md).

    1. [Create a service account](../../../iam/operations/sa/create.md#create-sa) with the `storage.editor` role. The transfer will use it to access the bucket.

- {{ TF }} {#tf}

    1. {% include [terraform-install-without-setting](../../../_includes/mdb/terraform/install-without-setting.md) %}
    1. {% include [terraform-authentication](../../../_includes/mdb/terraform/authentication.md) %}
    1. {% include [terraform-setting](../../../_includes/mdb/terraform/setting.md) %}
    1. {% include [terraform-configure-provider](../../../_includes/mdb/terraform/configure-provider.md) %}

    1. Download the [opensearch-to-object-storage.tf](https://github.com/yandex-cloud-examples/yc-data-transfer-from-opensearch-to-object-storage/blob/main/opensearch-to-object-storage.tf) configuration file to the same working directory.

        This file describes:

        * [Network](../../../vpc/concepts/network.md#network).
        * [Subnet](../../../vpc/concepts/network.md#subnet).
        * [Security group](../../../vpc/concepts/security-groups.md) for connecting to a {{ mos-name }} cluster.
        * Service account with the `storage.editor` role.
        * {{ mos-name }} cluster.
        * {{ objstorage-name }} target bucket.
        * Endpoints.
        * Transfer.

    1. In the `opensearch-to-object-storage.tf` file, specify the following settings:

        * `folder_id`: [Folder ID](../../../resource-manager/operations/folder/get-id.md).
        * `mos_version`: {{ OS }} version.
        * `mos_password`: User password of the {{ OS }} cluster owner.
        * `bucket_name`: Bucket name consistent with the [naming conventions](../../../storage/concepts/bucket.md#naming).
        * `profile_name`: Your YC CLI profile name.

          {% include [cli-install](../../../_includes/cli-install.md) %}

    1. Make sure the {{ TF }} configuration files are correct using this command:

        ```bash
        terraform validate
        ```

        If there are any errors in the configuration files, {{ TF }} will point them out.

    1. Create the required infrastructure:

        {% include [terraform-apply](../../../_includes/mdb/terraform/apply.md) %}

        {% include [explore-resources](../../../_includes/mdb/terraform/explore-resources.md) %}

{% endlist %}

## Prepare the test data {#prepare-data}

1. [Connect to the {{ mos-name }} source cluster](../../../managed-opensearch/operations/connect.md).

1. Create a test index named `people` and set its schema:

    ```bash
    curl --user admin:<password> \
         --cacert ~/.opensearch/root.crt \
         --header 'Content-Type: application/json' \
         --request PUT 'https://<address_of_{{ OS }}_host_with_DATA_role>:{{ port-mos }}/people' && \
    curl --user admin:<password> \
         --cacert ~/.opensearch/root.crt \
         --header 'Content-Type: application/json' \
         --request PUT 'https://<address_of_{{ OS }}_host_with_DATA_role>:{{ port-mos }}/people/_mapping?pretty' \
         --data'
         {
               "properties": {
                  "name": {"type": "text"},
                  "age": {"type": "integer"}
               }
         }
         '
    ```

1. Populate the test index with data:

    ```bash
    curl --user admin:<password> \
         --cacert ~/.opensearch/root.crt \
         --header 'Content-Type: application/json' \
         --request POST 'https://<address_of_{{ OS }}_host_with_DATA_role>:{{ port-mos }}/people/_doc/?pretty' \
         --data'
         {
               "name" : "Alice",
               "age" : "30"
         }
         ' && \
    curl --user admin:<password> \
         --cacert ~/.opensearch/root.crt \
         --header 'Content-Type: application/json' \
         --request POST 'https://<address_of_{{ OS }}_host_with_DATA_role>:{{ port-mos }}/people/_doc/?pretty' \
         --data'
         {
               "name" : "Robert",
               "age" : "32"
         }
         '
    ```

1. Optionally, check the data in the test index:

    ```bash
    curl --user admin:<password> \
         --cacert ~/.opensearch/root.crt \
         --header 'Content-Type: application/json' \
         --request GET 'https://<address_of_{{ OS }}_host_with_DATA_role>:{{ port-mos }}/people/_search?pretty'
    ```

## Prepare and activate your transfer {#prepare-transfer}

1. [Create a target endpoint](../../../data-transfer/operations/endpoint/target/object-storage.md) of the `{{ objstorage-name }}` type with the following settings:

    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.object_storage.console.form.object_storage.ConnectionSettings.bucket.title }}**: `<name_of_previously_created_bucket>`
    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.object_storage.console.form.object_storage.ObjectStorageConnectionSettings.service_account_id.title }}**: `<name_of_previously_created_service_account>`.
    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.object_storage.console.form.object_storage.ObjectStorageTarget.output_format.title }}**: `{{ ui-key.yc-data-transfer.data-transfer.console.form.object_storage.console.form.object_storage.ObjectStorageSerializationFormatUI.OBJECT_STORAGE_SERIALIZATION_FORMAT_JSON.title }}`
    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.object_storage.console.form.object_storage.ObjectStorageTarget.output_encoding.title }}**: `{{ ui-key.yc-data-transfer.data-transfer.console.form.object_storage.console.form.object_storage.ObjectStorageCodecUI.UNCOMPRESSED }}`
    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.object_storage.console.form.object_storage.ObjectStorageAdvancedSettings.bucket_layout.title }}**: `from_MOS`

1. [Create a source endpoint](../../../data-transfer/operations/endpoint/source/opensearch.md#endpoint-settings) of the `{{ OS }}` type with the following settings:

    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.opensearch.console.form.opensearch.OpenSearchConnection.connection_type.title }}**: `{{ ui-key.yc-data-transfer.data-transfer.console.form.opensearch.console.form.opensearch.OpenSearchConnectionType.mdb_cluster_id.title }}`
    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.opensearch.console.form.opensearch.OpenSearchConnectionType.mdb_cluster_id.title }}**: Select a {{ mos-name }} cluster from the list.
    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.opensearch.console.form.opensearch.OpenSearchConnection.user.title }}**: `admin`
    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.opensearch.console.form.opensearch.OpenSearchConnection.password.title }}**: `<user_password>`.

1. Create a transfer:

    {% list tabs group=instructions %}

    - Manually {#manual}

      1. [Create a transfer](../../../data-transfer/operations/transfer.md#create) of the **{{ ui-key.yc-data-transfer.data-transfer.console.form.transfer.console.form.transfer.TransferType.snapshot.title }}** type that will use the created endpoints.

      1. [Activate the transfer](../../../data-transfer/operations/transfer.md#activate).

    - {{ TF }} {#tf}

      1. In the `opensearch-to-object-storage.tf` file, specify the values of the following variables:

          * `target_endpoint_id`: ID of the target endpoint.
          * `source_endpoint_id`: ID of the source endpoint.
          * `transfer_enabled`: Put `1` to create a transfer.

      1. Make sure the {{ TF }} configuration files are correct using this command:

          ```bash
          terraform validate
          ```

          If there are any errors in the configuration files, {{ TF }} will point them out.

      1. Create the required infrastructure:

          {% include [terraform-apply](../../../_includes/mdb/terraform/apply.md) %}

          Once created, your transfer will be activated automatically.

    {% endlist %}

## Test the transfer {#verify-transfer}

Make sure the data has been transferred from the {{ mos-name }} cluster to the {{ objstorage-name }} bucket:

1. Wait for the transfer status to change to **{{ ui-key.yacloud.data-transfer.label_connector-status-DONE }}**.
1. In the [management console]({{ link-console-main }}), select the folder where the bucket is located.
1. From the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_storage }}**.
1. Select the bucket from the list.
1. Navigate to the **{{ ui-key.yacloud.storage.bucket.switch_files }}** tab.
1. Make sure the {{ objstorage-name }} bucket contains the `from_MOS` folder with the test data JSON file.

## Delete the resources you created {#clear-out}

Some resources are not free of charge. To avoid paying for them, delete the resources you no longer need:

1. [Delete the transfer](../../../data-transfer/operations/transfer.md#delete).
1. [Delete the endpoints](../../../data-transfer/operations/endpoint/index.md#delete).
1. Delete the `from_MOS` folder from the bucket you created.

Delete the other resources depending on how they were created:

{% list tabs group=instructions %}

- Manually {#manual}

    1. [Delete the {{ objstorage-name }} bucket](../../../storage/operations/buckets/delete.md).
    1. [Delete the {{ mos-name }} cluster](../../../managed-opensearch/operations/cluster-delete.md).
    1. [Delete the service account](../../../iam/operations/sa/delete.md).

- {{ TF }} {#tf}

    {% include [terraform-clear-out](../../../_includes/mdb/terraform/clear-out.md) %}

{% endlist %}
