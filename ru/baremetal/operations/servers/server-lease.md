---
title: Как арендовать сервер в {{ baremetal-full-name }}
description: Следуя данной инструкции, вы сможете арендовать сервер в {{ baremetal-full-name }}.
---

# Арендовать сервер

{% list tabs group=instructions %}

- Консоль управления {#console}

  1. В [консоли управления]({{ link-console-main }}) выберите каталог, в котором хотите арендовать сервер.
  1. В списке сервисов выберите **{{ ui-key.yacloud.iam.folder.dashboard.label_baremetal }}**.
  1. Нажмите кнопку **{{ ui-key.yacloud.baremetal.label_create-server }}**.
  1. Выберите [зону доступности](../../../overview/concepts/geo-scope.md), в которой будет арендован сервер.
  1. Выберите [пул](../../concepts/servers.md#server-pools), из которого будет арендован сервер.
  1. В блоке **{{ ui-key.yacloud.baremetal.title_section-server-config }}** выберите [конфигурацию сервера](../../concepts/server-configurations.md).
  1. (Опционально) В блоке **{{ ui-key.yacloud.baremetal.title_section-disk }}** настройте разметку дисков:

        1. Нажмите кнопку **{{ ui-key.yacloud.baremetal.action_disk-layout-settings }}**.
        1. Укажите параметры разделов. Чтобы создать новый раздел, нажмите кнопку ![icon](../../../_assets/console-icons/plus.svg) **{{ ui-key.yacloud.baremetal.actions_add-partition }}**.

           {% note info %}

           Чтобы самостоятельно собрать RAID-массивы и настроить разделы дисков, нажмите кнопку **{{ ui-key.yacloud.baremetal.action_destroy-raid }}**.

           {% endnote %}

        1. Нажмите кнопку **{{ ui-key.yacloud.common.save }}**.
  
  1. В блоке **{{ ui-key.yacloud.baremetal.title_section-server-product }}** выберите:
  
      * `{{ ui-key.yacloud.baremetal.field_choose-marketplace-os }}` — чтобы установить на сервер один из доступных публичных образов ОС в {{ marketplace-full-name }}.
      * `{{ ui-key.yacloud.baremetal.field_choose-no-os }}` — чтобы арендовать сервер без операционной системы.
      
          [Установить](./reinstall-os-from-own-image.md) операционную систему из собственного ISO-образа вы сможете позднее.

  1. В блоке **{{ ui-key.yacloud.baremetal.title_section-lease-conditions }}**:

     1. Укажите количество серверов, которое хотите арендовать.
     1. Выберите период, на который арендуете серверы.
  
  1. В блоке **{{ ui-key.yacloud.baremetal.title_section-server-network-settings }}**:

     1. Выберите существующую [приватную подсеть](../../concepts/network.md#private-subnet) или нажмите кнопку **{{ ui-key.yacloud.common.create }}** для создания новой:

        * В открывшемся окне укажите имя и описание подсети.
        * Нажмите кнопку **{{ ui-key.yacloud.baremetal.label_create-subnetwork }}**.

     1. В поле **{{ ui-key.yacloud.baremetal.field_needed-public-ip }}** выберите способ назначения публичного адреса:

        * `{{ ui-key.yacloud.baremetal.label_public-ip-auto }}` — чтобы назначить случайный IP-адрес.
        * `{{ ui-key.yacloud.baremetal.label_public-ip-no }}` — чтобы не назначать публичный IP-адрес.

  1. Если вы устанавливаете на сервер операционную систему из публичного образа в {{ marketplace-name }}, в блоке **{{ ui-key.yacloud.baremetal.title_server-access }}** задайте параметры доступа к серверу:

      {% include [server-lease-access](../../../_includes/baremetal/server-lease-access.md) %}

  1. (Опционально) Включите резервное копирование сервера в [{{ backup-full-name }}](../../../backup/index.yaml):

      1. Раскройте блок **{{ ui-key.yacloud.baremetal.title_section-backup }}**.
      1. Выберите [политику резервного копирования](../../../backup/concepts/policy.md) или [создайте](../../../backup/operations/policy-vm/create.md) новую.
      1. Выберите [сервисный аккаунт](../../../iam/concepts/users/service-accounts.md) с ролями [baremetal.editor](../../security/index.md#baremetal-editor) и [backup.editor](../../../backup/security/index.md#backup-editor) или [создайте](../../../iam/operations/sa/create.md) новый.

      Подробнее см. в инструкции [{#T}](../../../backup/operations/backup-baremetal/lease-server-with-backup.md).

  1. В блоке **{{ ui-key.yacloud.baremetal.title_section-server-info }}**:

     1. В поле **{{ ui-key.yacloud.baremetal.field_name }}** введите имя сервера.
     1. (Опционально) Добавьте **{{ ui-key.yacloud.baremetal.field_description }}** к серверу.
     1. (Опционально) Задайте **{{ ui-key.yacloud.component.label-set.label_labels }}**.
  
  1. Нажмите кнопку **{{ ui-key.yacloud.baremetal.label_create-server }}**.

{% endlist %}

После того как вы арендуете сервер, вы в любой момент сможете установить или переустановить на нем операционную систему из публичного образа в {{ marketplace-name }} или из собственного ISO-образа. Подробнее см. в инструкциях [{#T}](./reinstall-os-from-marketplace.md) и [{#T}](./reinstall-os-from-own-image.md).