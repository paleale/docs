---
title: Как подключить виртуальную машину на Windows Server к {{ backup-full-name }}
description: Следуя данной инструкции, вы сможете подключить виртуальную машину на Windows Server к {{ backup-name }}.
---

# Подключить виртуальную машину на Windows Server к {{ backup-name }}

Вы можете создавать резервные копии [виртуальных машин](../../compute/concepts/vm.md) {{ compute-name }} c [поддерживаемыми операционными системами на базе Windows](../concepts/vm-connection.md#windows).

{% include [requirements](../../_includes/backup/requirements.md) %}

{% include [vm-prereqs-note](../../_includes/backup/vm-prereqs-note.md) %}

Если вы [удалили](delete-vm.md) ВМ из {{ backup-name }} и хотите подключить ее к сервису заново, воспользуйтесь инструкцией ниже.

Чтобы подключить виртуальную машину на Windows к {{ backup-name }}:

1. [Создайте](../../iam/operations/sa/create.md) сервисный аккаунт с [ролью](../security/index.md#backup-editor) `backup.editor`.
1. [Подключите](../../compute/operations/vm-control/vm-connect-sa.md) к ВМ сервисный аккаунт, созданный ранее.
1. [Настройте](../concepts/vm-connection.md#vm-network-access) сетевой доступ для ВМ.
1. [Подключитесь к ВМ по RDP](../../compute/operations/vm-connect/rdp.md).
1. Запустите Windows PowerShell.

    {% include [ps-note](../../_includes/backup/ps-note.md) %}

1. Выполните следующую команду:

   ```powershell
   . { iwr -useb https://{{ s3-storage-host }}/backup-distributions/agent_installer.ps1 } | iex
   ```
   
   Результат:

   ```text
   ...
   Backup agent installation complete after 190 s!
   ```

После этого ВМ можно привязать к [политике резервного копирования](../concepts/policy.md).


#### См. также {#see-also}

* [{#T}](create-vm.md)
* [Привязать виртуальную машину к политике резервного копирования](./policy-vm/update.md#update-vm-list)
* [{#T}](./backup-vm/recover.md)
* [{#T}](./backup-vm/delete.md)
* [{#T}](./policy-vm/create.md)
