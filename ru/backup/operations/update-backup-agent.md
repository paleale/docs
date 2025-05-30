---
title: Как обновить или восстановить агент {{ backup-full-name }} на виртуальной машине
description: Следуя данной инструкции, вы сможете обновить агент {{ backup-name }} или восстановить его работоспособность на ВМ.
---

# Обновить или восстановить агент {{ backup-full-name }} на виртуальной машине

В некоторых ситуациях, чтобы обеспечить бесперебойное автоматическое резервное копирование [виртуальных машин](../../compute/concepts/vm.md) {{ compute-full-name }}, может понадобиться обновить [агент {{ backup-name }}](../concepts/agent.md) или восстановить нарушенную работоспособность агента.

## Обновление агента {{ backup-name }} {#update-agent}

Обновление агента {{ backup-name }} может потребоваться при технических обновлениях на стороне [провайдера резервного копирования](../concepts/index.md#providers). О подобных случаях {{ yandex-cloud }} заблаговременно предупреждает клиентов.

{% note info %}

Обновление агента {{ backup-name }} не влияет на данные в существующих резервных копиях.

{% endnote %}

Чтобы обновить агент {{ backup-name }} на ВМ:

{% list tabs group=operating_system %}

- Linux {#linux}

  1. [Подключитесь](../../compute/operations/vm-connect/ssh.md#vm-connect) к ВМ по SSH.
  1. В терминале выполните команду:

      ```bash
      curl \
        --output backup_agent_linux_installer.bin \
        https://{{ s3-storage-host }}/backup-distributions/backup_agent_linux_installer.bin && \
      sudo bash ./backup_agent_linux_installer.bin -a
      ```

      Результат:

      ```text
      ...
      Congratulations!
      Cyber Backup Agent has been successfully installed in the system.
      ```

      Обновление агента {{ backup-name }} может занимать около 15 минут.

  1. Отключитесь от ВМ.


- Windows {#windows}

  1. [Подключитесь](../../compute/operations/vm-connect/rdp.md) к ВМ по RDP.
  1. Запустите [Windows PowerShell](https://learn.microsoft.com/ru-ru/powershell/).
  1. Выполните команды:

      ```powershell
      Invoke-WebRequest `
        "https://{{ s3-storage-host }}/backup-distributions/backup_agent_windows_installer.exe" `
        -OutFile ".\backup_agent_windows_installer.exe"
      Invoke-Expression .\backup_agent_windows_installer.exe
      ```

  1. В открывшемся окне нажмите **Repair**.
  1. Дождитесь сообщения `The installation was successfully repaired` и нажмите **CLOSE**.
      
      Обновление агента {{ backup-name }} может занимать около 15 минут.
  1. Отключитесь от ВМ.

{% endlist %}

Если по какой-либо причине обновить агент {{ backup-name }} не удалось, [обратитесь]({{ link-console-support }}) в техническую поддержку.

## Восстановление работоспособности агента {{ backup-name }} {#restore-agent}

{% include [update-kernel-headers-description](../../_includes/backup/operations/update-kernel-headers-description.md) %}

{% list tabs group=operating_system %}

- Debian/Ubuntu {#ubuntu}

  {% include [update-kernel-headers-ubuntu](../../_includes/backup/operations/update-kernel-headers-ubuntu.md) %}

- CentOS {#centos}

  {% include [update-kernel-headers-centos](../../_includes/backup/operations/update-kernel-headers-centos.md) %}

{% endlist %}

Если по какой-либо причине восстановить работу агента {{ backup-name }} не удалось, [обратитесь]({{ link-console-support }}) в техническую поддержку.