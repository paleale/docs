- {% include [Backup time](../../../_includes/mdb/console/backup-time.md) %}

- **{{ ui-key.yacloud.mdb.forms.backup-retain-period }}**: Retention period for automatic backups. If an automatic backup expires, it is deleted. The default is {{ mpg-backup-retention }} days. For more information, see [Backups](../../../managed-postgresql/concepts/backup.md).

    Changing the retention period affects both new automatic backups and existing backups. For example, the initial retention period was 7 days. The remaining lifetime for a backup with this period is 1 day. When the retention period increases to 9 days, the remaining lifetime for this backup is 3 days.

    Automatic cluster backups are stored for a specified number of days whereas manually created ones are stored indefinitely. After a cluster is deleted, all backups persist for 7 days.

- **{{ ui-key.yacloud.mdb.forms.maintenance-window-type }}**: [Maintenance window](../../../managed-postgresql/concepts/maintenance.md) settings:

    {% include [Maintenance window](../console/maintenance-window-description.md) %}

- **{{ ui-key.yacloud.mdb.forms.additional-field-datalens }}**: This option allows you to analyze cluster data in [{{ datalens-full-name }}](../../../datalens/concepts/index.md).


- **{{ ui-key.yacloud.mdb.forms.additional-field-websql-service }}**: Enables you to [run SQL queries](../../../managed-postgresql/operations/web-sql-query.md) against cluster databases from the {{ yandex-cloud }} management console using {{ websql-full-name }}.




- **{{ ui-key.yacloud.mdb.forms.additional-field-yandex-query_ru }}**: Enables you to run YQL queries against cluster databases from [{{ yq-full-name }}](../../../query/concepts/index.md).

- **{{ ui-key.yacloud.mdb.forms.additional-field-serverless }}**: Enable this option to allow cluster access from [{{ sf-full-name }}](../../../functions/concepts/index.md). For more information about setting up access, see the [{{ sf-name }}](../../../functions/operations/database-connection.md) documentation.



- **{{ ui-key.yacloud.mdb.forms.field_diagnostics-enabled }}**: Allows you to use the [Performance diagnostics](../../../managed-postgresql/operations/performance-diagnostics.md) tool in a cluster. If this option is enabled, also set the **{{ ui-key.yacloud.mdb.forms.field_diagnostics-sessions-interval }}** and **{{ ui-key.yacloud.mdb.forms.field_diagnostics-statements-interval }}** using the sliders. Both are measured in seconds.


- **Autofailover**: If this option is enabled, the replication source for all replica hosts will automatically switch to the new master host when the master changes. To learn more, see [Replication](../../../managed-postgresql/concepts/replication.md).

    If the master host [is deleted](../../../managed-postgresql/operations/hosts.md#remove), a new master will be selected automatically regardless of the value of this option.

    {% note alert %}

    If the **Autofailover** option is disabled, run the selection of a new master or assign this role to one of the replicas [manually](../../../managed-postgresql/operations/update.md#start-manual-failover) if the master host fails.

    {% endnote %}


- **{{ ui-key.yacloud.postgresql.cluster.additional-field-pooling_mode }}**: Select one of the [connection pooler modes](../../../managed-postgresql/concepts/pooling.md).

- **{{ ui-key.yacloud.mdb.forms.label_deletion-protection }}**: Protection of the cluster, its databases, and users against deletion.

    By default, the parameter inherits its value from the cluster when creating users and databases. You can also set the value manually; for more information, see the [User management](../../../managed-postgresql/operations/cluster-users.md) and [Database management](../../../managed-postgresql/operations/databases.md) sections.
    
    If the parameter is changed on a running cluster, only users and databases with the **Same as cluster** protection will inherit the new value.

    {% include [deletion-protection-limits](../deletion-protection-limits-db.md) %}
