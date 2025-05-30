# Asynchronous replication of data from {{ mmy-name }} to {{ mch-name }} using {{ data-transfer-full-name }}


With {{ data-transfer-name }}, you can migrate your database from a {{ MY }} source cluster to {{ CH }}.

To transfer data:

1. [Prepare the source cluster](#prepare-source).
1. [Set up and activate your transfer](#prepare-transfer).
1. [Test your transfer](#verify-transfer).
1. [Select the data from {{ CH }}](#working-with-data-ch).

If you no longer need the resources you created, [delete them](#clear-out).


## Required paid resources {#paid-resources}

The support cost includes:

* {{ mmy-name }} cluster fee: Using computing resources allocated to hosts and disk space (see [{{ mmy-name }} pricing](../../managed-mysql/pricing.md)).
* {{ mch-name }} cluster fee: Using computing resources allocated to hosts (including ZooKeeper hosts) and disk space (see [{{ mch-name }} pricing](../../managed-clickhouse/pricing.md)).
* Fee for using public IP addresses if public access is enabled for cluster hosts (see [{{ vpc-name }} pricing](../../vpc/pricing.md)).
* Transfer fee: Using computing resources and the number of transferred data rows (see [{{ data-transfer-name }} pricing](../../data-transfer/pricing.md)).


## Getting started {#before-you-begin}

Set up your infrastructure:

{% list tabs group=instructions %}

- Manually {#manual}

    1. [Create a {{ mmy-name }} source cluster](../../managed-mysql/operations/cluster-create.md) in any suitable configuration. To connect to the cluster from the user's local machine rather than doing so from the {{ yandex-cloud }} cloud network, enable public access to the cluster when creating it.

    1. [Create a {{ mch-name }} target cluster](../../managed-clickhouse/operations/cluster-create.md) of any suitable configuration with the following settings:

        * Number of {{ CH }} hosts: At least two, which is required to enable replication in the cluster.
        * Database name: Same as in the source cluster.
        * To connect to the cluster from the user's local machine rather than doing so from the {{ yandex-cloud }} cloud network, enable public access to the cluster when creating it.

    
    1. If you are using security groups in your clusters, configure them so that you can connect to the clusters from the internet:

        * [{{ mmy-name }}](../../managed-mysql/operations/connect.md#configuring-security-groups).
        * [{{ mch-name }}](../../managed-clickhouse/operations/connect/index.md#configuring-security-groups).


- {{ TF }} {#tf}

    1. {% include [terraform-install-without-setting](../../_includes/mdb/terraform/install-without-setting.md) %}
    1. {% include [terraform-authentication](../../_includes/mdb/terraform/authentication.md) %}
    1. {% include [terraform-setting](../../_includes/mdb/terraform/setting.md) %}
    1. {% include [terraform-configure-provider](../../_includes/mdb/terraform/configure-provider.md) %}

    1. Download the [data-transfer-mmy-mch.tf](https://github.com/yandex-cloud-examples/yc-data-transfer-from-mysql-to-clickhouse/blob/main/data-transfer-mmy-mch.tf) configuration file to the same working directory.

        This file describes:

        * [Network](../../vpc/concepts/network.md#network).
        * [Subnet](../../vpc/concepts/network.md#subnet).
        * [Security group](../../vpc/concepts/security-groups.md) and the rule required to connect to a {{ mmy-name }} cluster.
        * {{ mmy-name }} source cluster.
        * {{ mch-name }} target cluster.
        * Source endpoint.
        * Target endpoint.
        * Transfer.

    1. Specify the following in the `data-transfer-mmy-mch.tf` file:

        * The {{ mmy-name }} source cluster parameters that will be used as the [source endpoint parameters](../../data-transfer/operations/endpoint/target/mysql.md#managed-service):

            * `source_mysql_version`: {{ MY }} version.
            * `source_db_name`: {{ MY }} database name that will be used as the {{ mch-name }} database name.
            * `source_user` and `source_password`: Name and user password of the database owner.

        * The {{ mch-name }} target cluster parameters that will be used as the [target endpoint parameters](../../data-transfer/operations/endpoint/target/clickhouse.md#managed-service):

            * `target_user` and `target_password`: Name and user password of the database owner.

    1. Make sure the {{ TF }} configuration files are correct using this command:

        ```bash
        terraform validate
        ```

        If there are any errors in the configuration files, {{ TF }} will point them out.

    1. Create the required infrastructure:

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

        {% include [explore-resources](../../_includes/mdb/terraform/explore-resources.md) %}

{% endlist %}

## Prepare the source cluster {#prepare-source}

1. If you created the infrastructure manually, [prepare the source cluster](../../data-transfer/operations/prepare.md#source-my).

1. [Connect to the {{ mmy-name }} source cluster](../../managed-mysql/operations/connect.md).

1. Add test data to the database.

    1. Create a table named `x_tab`:

    ```sql
    CREATE TABLE x_tab
    (
        id INT,
        name TEXT,
        PRIMARY KEY (id)
    );
    ```

    1. Populate the table with data:

    ```sql
    INSERT INTO x_tab (id, name) VALUES
        (40, 'User1'),
        (41, 'User2'),
        (42, 'User3'),
        (43, 'User4'),
        (44, 'User5');
    ```

## Set up and activate the transfer {#prepare-transfer}

{% list tabs group=instructions %}

- Manually {#manual}

    1. [Create a source endpoint](../../data-transfer/operations/endpoint/index.md#create):

        * **{{ ui-key.yacloud.data-transfer.forms.label-database_type }}**: `{{ MY }}`
        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.mysql.console.form.mysql.MysqlSource.title }}** → **{{ ui-key.yc-data-transfer.data-transfer.console.form.mysql.console.form.mysql.MysqlSource.connection.title }}**: `{{ ui-key.yc-data-transfer.data-transfer.console.form.mysql.console.form.mysql.MysqlConnectionType.mdb_cluster_id.title }}`

            Select a source cluster from the list and specify its connection settings.

    1. [Create a target endpoint](../../data-transfer/operations/endpoint/index.md#create):

        * **{{ ui-key.yacloud.data-transfer.forms.label-database_type }}**: `ClickHouse`
        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseTarget.title }}** → **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseTarget.connection.title }}**: `{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseManaged.mdb_cluster_id.title }}`

            Select a target cluster from the list and specify its connection settings.

    1. [Create a transfer](../../data-transfer/operations/transfer.md#create) of the **_{{ ui-key.yc-data-transfer.data-transfer.console.form.transfer.console.form.transfer.TransferType.snapshot_and_increment.title }}_** type that will use the endpoints you created.
    1. [Activate](../../data-transfer/operations/transfer.md#activate) your transfer.

- {{ TF }} {#tf}

    1. In the `data-transfer-mmy-mch.tf` file, set the `transfer_enabled` variable to `1`.

    1. Make sure the {{ TF }} configuration files are correct using this command:

        ```bash
        terraform validate
        ```

        If there are any errors in the configuration files, {{ TF }} will point them out.

    1. Create the required infrastructure:

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

        Once created, your transfer will be activated automatically.

{% endlist %}

## Test your transfer {#verify-transfer}

1. Wait until the transfer status switches to **{{ ui-key.yacloud.data-transfer.label_connector-status-RUNNING }}**.

1. Make sure the data from the source {{ mmy-name }} cluster has been moved to the {{ mch-name }} database:

    1. [Connect to the cluster](../../managed-clickhouse/operations/connect/clients.md#clickhouse-client) using `clickhouse-client`.

    1. Run this request:

        ```sql
        SELECT * FROM <{{ CH }}_database_name>.x_tab
        ```

        Result:

        ```text
        ┌─id─┬─name──┬─__data_transfer_commit_time─┬─__data_transfer_delete_time─┐
        │ 40 │ User1 │         1661952756538347180 │                           0 │
        │ 41 │ User2 │         1661952756538347180 │                           0 │
        │ 42 │ User3 │         1661952756538347180 │                           0 │
        │ 43 │ User4 │         1661952756538347180 │                           0 │
        │ 44 │ User5 │         1661952756538347180 │                           0 │
        └────┴───────┴─────────────────────────────┴─────────────────────────────┘
        ```

        The table also contains [columns with timestamps](#working-with-data-ch): `__data_transfer_commit_time` and `__data_transfer_delete_time`.

1. In the `x_tab` table of the source {{ MY }} database, delete the row where `id` equals `41` and update the one where `id` equals `42`:

    1. [Connect to the {{ mmy-name }} source cluster](../../managed-mysql/operations/connect.md).

    1. Run the following requests:

        ```sql
        DELETE FROM x_tab WHERE id = 41;
        UPDATE x_tab SET name = 'Key3' WHERE id = 42;
        ```

1. Check the changes in the `x_tab` table of the {{ CH }} target:

    ```sql
    SELECT * FROM <{{ CH }}_database_name>.x_tab WHERE id in (41,42);
    ```

    Result:

    ```text
    ┌─id─┬─name──┬─__data_transfer_commit_time─┬─__data_transfer_delete_time─┐
    │ 41 │ User2 │         1661952756538347180 │                           0 │
    │ 42 │ User3 │         1661952756538347180 │                           0 │
    └────┴───────┴─────────────────────────────┴─────────────────────────────┘
    ┌─id─┬─name─┬─__data_transfer_commit_time─┬─__data_transfer_delete_time─┐
    │ 41 │ ᴺᵁᴸᴸ │         1661953256000000000 │         1661953256000000000 │
    └────┴──────┴─────────────────────────────┴─────────────────────────────┘
    ┌─id─┬─name─┬─__data_transfer_commit_time─┬─__data_transfer_delete_time─┐
    │ 42 │ Key3 │         1661953280000000000 │                           0 │
    └────┴──────┴─────────────────────────────┴─────────────────────────────┘
    ```

## Select the data from {{ CH }} {#working-with-data-ch}

For table recovery, the {{ CH }} target with [replication](../../managed-clickhouse/concepts/replication.md) enabled uses the [ReplicatedReplacingMergeTree]({{ ch.docs }}/engines/table-engines/mergetree-family/replication/) and [ReplacingMergeTree]({{ ch.docs }}/engines/table-engines/mergetree-family/replacingmergetree/) engines. The following columns are added automatically to each table:

* `__data_transfer_commit_time`: Time when the was row updated to this value, in `TIMESTAMP` format.
* `__data_transfer_delete_time`: Row deletion time, in `TIMESTAMP` format, if the row was deleted in the source. If the row was not deleted, the value is set to `0`.

    The `__data_transfer_commit_time` column is required for the ReplicatedReplacedMergeTree engine to work. If a record is deleted or updated, a new row is inserted with a value in this column. A query by a single primary key returns multiple records with different `__data_transfer_commit_time` values.

With the **{{ ui-key.yacloud.data-transfer.label_connector-status-RUNNING }}** transfer status, the source data can be added or deleted. To ensure standard behavior of SQL commands when a primary key points to a single record, provide a clause to filter data by `__data_transfer_delete_time` when querying tables transferred to {{ CH }}. Here is an example of a query to the `x_tab` table:

```sql
SELECT * FROM <{{ CH }}_database_name>.x_tab FINAL
WHERE __data_transfer_delete_time = 0;
```

To simplify the `SELECT` queries, create a view with filtering by `__data_transfer_delete_time` and use it for querying. Here is an example of a query to the `x_tab` table:

```sql
CREATE VIEW x_tab_view AS SELECT * FROM <{{ CH }}_database_name>.x_tab FINAL
WHERE __data_transfer_delete_time == 0;
```

## Delete the resources you created {#clear-out}

{% include [note before delete resources](../../_includes/mdb/note-before-delete-resources.md) %}

Some resources are not free of charge. To avoid paying for them, delete the resources you no longer need:

{% list tabs group=instructions %}

- Manually {#manual}

    1. [Delete the transfer](../../data-transfer/operations/transfer.md#delete-transfer).
    1. [Delete the endpoints](../../data-transfer/operations/endpoint/index.md#delete) for both the source and target.
    1. [Delete the {{ mmy-name }} cluster](../../managed-mysql/operations/cluster-delete.md).
    1. [Delete the {{ mch-name }} cluster](../../managed-clickhouse/operations/cluster-delete.md).

- {{ TF }} {#tf}

    {% include [terraform-clear-out](../../_includes/mdb/terraform/clear-out.md) %}

{% endlist %}
