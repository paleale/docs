The `name` label contains the metric name.

Labels shared by all {{ backup-name }} metrics:

Label | Value
----|----
service | Service ID: `baas`
resource_id | [VM](../../../compute/concepts/vm.md) ID in [{{ compute-name }}](../../../compute/) or [{{ baremetal-name }} server](../../../baremetal/concepts/servers.md) ID in [{{ baremetal-full-name }}](../../../baremetal/).
resource_name | VM or {{ baremetal-name }} server name.
resource_type | Resource type, which can take either of these two values:<br/><ul><li>`vm`: {{ compute-name }} VM or {{ baremetal-name }} server.</li><li>`backup`: [Backup](../../../backup/concepts/backup.md).</li>

{% include [baremetal-note](../../../_includes/backup/baremetal-note.md) %}

Service metrics:

Metric name<br/>Type, unit | Description
--- | ---
`protected`<br/>`DGAUGE`, count | Number of VMs and {{ baremetal-name }} servers using {{ backup-name }}
`used_space`<br/>`DGAUGE`, bytes | Storage used by backups