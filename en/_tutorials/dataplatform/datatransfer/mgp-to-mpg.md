You can migrate a database from {{ GP }} to the {{ PG }} cluster using {{ data-transfer-full-name }}.

To transfer a database from {{ GP }} to {{ PG }}:

1. [Set up your transfer](#prepare-transfer).
1. [Activate the transfer](#activate-transfer).
1. [Test the copy function upon re-activation](#example-check-copy).

If you no longer need the resources you created, [delete them](#clear-out).


## Required paid resources {#paid-resources}

The support cost includes:

* {{ mgp-name }} cluster fee: Using computing resources allocated to hosts and disk space (see [{{ mgp-name }} pricing](../../../managed-greenplum/pricing/index.md)).
* {{ mpg-name }} cluster fee: Using computing resources allocated to hosts and disk space (see [{{ mpg-name }} pricing](../../../managed-postgresql/pricing.md)).
* Fee for using public IP addresses for cluster hosts (see [{{ vpc-name }} pricing](../../../vpc/pricing.md)).
* Per-transfer fee: Using computing resources and the number of transferred data rows (see [{{ data-transfer-name }} pricing](../../../data-transfer/pricing.md)).


## Getting started {#before-you-begin}

For clarity, we will create all required resources in {{ yandex-cloud }}. Set up your infrastructure:

{% list tabs group=instructions %}

- Manually {#manual}

    1. [Create a {{ mgp-full-name }} source cluster](../../../managed-greenplum/operations/cluster-create.md#create-cluster) in any suitable configuration with the `gp-user` admin username and public hosts.

    1. [Create a {{ mpg-full-name }} target cluster](../../../managed-postgresql/operations/cluster-create.md#create-cluster) in any suitable configuration with publicly available hosts. When creating a cluster, specify:

        * **{{ ui-key.yacloud.mdb.forms.database_field_user-login }}**: `pg-user`
        * **{{ ui-key.yacloud.mdb.forms.database_field_name }}**: `db1`

    
    1. If you are using security groups in clusters, make sure they are set up correctly and allow connecting to the clusters:

        * [{{ mpg-name }}](../../../managed-postgresql/operations/connect.md#configuring-security-groups).
        * [{{ mgp-name }}](../../../managed-greenplum/operations/connect.md#configuring-security-groups).


- {{ TF }} {#tf}

    1. {% include [terraform-install-without-setting](../../../_includes/mdb/terraform/install-without-setting.md) %}
    1. {% include [terraform-authentication](../../../_includes/mdb/terraform/authentication.md) %}
    1. {% include [terraform-setting](../../../_includes/mdb/terraform/setting.md) %}
    1. {% include [terraform-configure-provider](../../../_includes/mdb/terraform/configure-provider.md) %}

    1. Download the [greenplum-postgresql.tf](https://github.com/yandex-cloud-examples/yc-data-transfer-from-greenplum-to-postgresql/blob/main/greenplum-postgresql.tf) configuration file to the same working directory.

        This file describes:

        * [Networks](../../../vpc/concepts/network.md#network) and [subnets](../../../vpc/concepts/network.md#subnet) for hosting the clusters.
        * [Security groups](../../../vpc/concepts/security-groups.md) to connect to clusters.
        * {{ mgp-name }} source cluster.
        * {{ mpg-name }} target cluster.
        * Target endpoint.
        * Transfer.

    1. In the `greenplum-postgresql.tf` file, specify the admin user passwords and {{ GP }} and {{ PG }} versions.
    1. Run the `terraform init` command in the directory with the configuration file. This command initializes the provider specified in the configuration files and enables you to use the provider resources and data sources.
    1. Make sure the {{ TF }} configuration files are correct using this command:

        ```bash
        terraform validate
        ```

        If there are any errors in the configuration files, {{ TF }} will point them out.

    1. Create the required infrastructure:

        {% include [terraform-apply](../../../_includes/mdb/terraform/apply.md) %}

        {% include [explore-resources](../../../_includes/mdb/terraform/explore-resources.md) %}

{% endlist %}

## Set up your transfer {#prepare-transfer}

1. [Create a source endpoint](../../../data-transfer/operations/endpoint/source/greenplum.md) of the `{{ GP }}` type and specify these cluster connection settings in it:

    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.greenplum.console.form.greenplum.GreenplumConnection.connection_type.title }}**: `{{ ui-key.yc-data-transfer.data-transfer.console.form.greenplum.console.form.greenplum.GreenplumConnectionType.mdb_cluster_id.title }}`
    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.greenplum.console.form.greenplum.GreenplumConnectionType.mdb_cluster_id.title }}**: `<{{ GP }}_source_cluster_name>` from the drop-down list
    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.greenplum.console.form.greenplum.GreenplumConnection.database.title }}**: `postgres`
    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.greenplum.console.form.greenplum.GreenplumConnection.user.title }}**: `gp-user`
    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.greenplum.console.form.greenplum.GreenplumConnection.password.title }}**: `<user_password>`
    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.greenplum.console.form.greenplum.GpSourceAdvancedSettings.service_schema.title }}**: `public`

1. Create a target endpoint and transfer:

    {% list tabs group=instructions %}

    - Manually {#manual}

        1. [Create a target endpoint](../../../data-transfer/operations/endpoint/target/postgresql.md) of the `{{ PG }}` type and specify the cluster connection settings in it:

            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.Connection.connection_type.title }}**: `Yandex Managed Service for PostgreSQL cluster`
            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.postgres.console.form.postgres.PostgresConnectionType.mdb_cluster_id.title }}**: `<{{ PG }}>`_target_cluster_name from the drop-down list
            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.Connection.database.title }}**: `db1`
            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.Connection.user.title }}**: `pg-user`
            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.Connection.password.title }}**: `<user_password>`

        1. [Create a transfer](../../../data-transfer/operations/transfer.md#create) of the **_{{ ui-key.yc-data-transfer.data-transfer.console.form.transfer.console.form.transfer.TransferType.snapshot.title }}_** type that will use the created endpoints.

            Replication is not available for this endpoint pair, but you can set up regular copying when creating a transfer. To do this, in the **{{ ui-key.yc-data-transfer.data-transfer.console.form.transfer.console.form.transfer.TransferType.snapshot.title }}** field under **{{ ui-key.yc-data-transfer.data-transfer.console.form.transfer.console.form.transfer.Transfer.title }}**, select **Regular** and specify the copy interval. This will activate a transfer automatically after the specified time interval.

            {% note warning %}

            Before configuring regular copying, make sure the [target endpoint parameters](../../../data-transfer/operations/endpoint/target/postgresql.md#additional-settings) idicate either a `DROP` or a `TRUNCATE` cleanup policy. Otherwise, data on the target will be duplicated.

            {% endnote %}

    - {{ TF }} {#tf}

        1. In the `greenplum-postgresql.tf` file, specify these variables:

            * `gp_source_endpoint_id`: ID of the source endpoint.
            * `transfer_enabled`: `1` to create a transfer.

        1. Make sure the {{ TF }} configuration files are correct using this command:

            ```bash
            terraform validate
            ```

            If there are any errors in the configuration files, {{ TF }} will point them out.

        1. Create the required infrastructure:

            {% include [terraform-apply](../../../_includes/mdb/terraform/apply.md) %}

    {% endlist %}

## Activate the transfer {#activate-transfer}

1. [Connect to the {{ mgp-name }} cluster](../../../managed-greenplum/operations/connect.md), create a table named `x_tab`, and populate it with data:

    ```sql
    CREATE TABLE x_tab
    (
        id NUMERIC,
        name CHARACTER(5)
    );
    CREATE INDEX ON x_tab (id);
    INSERT INTO x_tab (id, name) VALUES
    (40, 'User1'),
    (41, 'User2'),
    (42, 'User3'),
    (43, 'User4'),
    (44, 'User5');
    ```

1. [Activate the transfer](../../../data-transfer/operations/transfer.md#activate) and wait for its status to change to **{{ ui-key.yacloud.data-transfer.label_connector-status-DONE }}**.
1. To check that the data was transferred correctly, connect to the {{ mpg-name }} target cluster and make sure that the columns of the `x_tab` table in the `db1` database match those of the `x_tab` table in the source database:

   ```sql
   SELECT id, name FROM db1.public.x_tab;
   ```

   ```text
   ┌─id─┬─name──┐
   │ 40 │ User1 │
   │ 41 │ User2 │
   │ 42 │ User3 │
   │ 43 │ User4 │
   │ 44 │ User5 │
   └────┴───────┘
   ```

## Test the copy function upon re-activation {#example-check-copy}

1. In the [target endpoint parameters](../../../data-transfer/operations/endpoint/target/postgresql#additional-settings), select either `DROP` or `TRUNCATE` as cleanup policy.
1. [Connect to the {{ mgp-name }} cluster](../../../managed-greenplum/operations/connect.md).
1. In the `x_tab` table, delete the row with the `41` ID and update the one with the `42` ID:

    ```sql
    DELETE FROM x_tab WHERE id = 41;
    UPDATE x_tab SET name = 'Key3' WHERE id = 42;
    ```

1. [Reactivate the transfer](../../../data-transfer/operations/transfer.md#activate) and wait for its status to change to **{{ ui-key.yacloud.data-transfer.label_connector-status-DONE }}**.
1. Check the changes in the `x_tab` table of the {{ PG }} target:

    ```sql
    SELECT id, name FROM db1.public.x_tab;
    ```

    ```text
    ┌─id─┬─name──┐
    │ 42 │ Key3  │
    │ 40 │ User1 │
    │ 43 │ User4 │
    │ 44 │ User5 │
    └────┴───────┘
    ```

## Delete the resources you created {#clear-out}

Some resources are not free of charge. To avoid paying for them, delete the resources you no longer need:

* Make sure the transfer has the **{{ ui-key.yacloud.data-transfer.label_connector-status-DONE }}** status and [delete](../../../data-transfer/operations/transfer.md#delete) it.
* [Delete both the source endpoint and the target endpoint](../../../data-transfer/operations/endpoint/index.md#delete).
* Delete the clusters:

    {% list tabs group=instructions %}

    - Manually {#manual}

        * [{{ mpg-name }}](../../../managed-postgresql/operations/cluster-delete.md).
        * [{{ mgp-name }}](../../../managed-greenplum/operations/cluster-delete.md).

    - {{ TF }} {#tf}

        {% include [terraform-clear-out](../../../_includes/mdb/terraform/clear-out.md) %}

    {% endlist %}
