---
title: Как создать кластер {{ PG }}
description: Следуя данной инструкции, вы сможете создать кластер {{ PG }} с одним или несколькими хостами базы данных.
---

# Создание кластера {{ PG }}


[Кластер](../../glossary/cluster.md) {{ PG }} — это один или несколько [хостов базы данных](../concepts/index.md), между которыми можно настроить [репликацию](../concepts/replication.md). Репликация работает по умолчанию в любом кластере из более чем одного хоста: хост-мастер принимает запросы на запись и дублирует изменения в репликах. Транзакция подтверждается, если данные записаны на [диск](../concepts/storage.md) и на хосте-мастере, и на определенном числе реплик, достаточном для формирования кворума.

{% note info %}

* Количество хостов, которые можно создать вместе с кластером {{ PG }}, зависит от выбранного [типа диска](../concepts/storage.md#storage-type-selection) и [класса хостов](../concepts/instance-types.md#available-flavors).
* Доступные типы диска зависят от выбранного [класса хостов](../concepts/instance-types.md#available-flavors).
* Если хранилище БД заполнится на 97%, кластер перейдет в режим только чтения. Рассчитывайте и увеличивайте необходимый размер хранилища заранее или [настройте автоматическое увеличение его размера](storage-space.md#disk-size-autoscale).

{% endnote %}

По умолчанию {{ mpg-name }} выставляет максимально возможное количество подключений к каждому хосту кластера {{ PG }}. Этот максимум не может быть больше значения настройки [Max connections](../concepts/settings-list.md#setting-max-connections).

{% include [note-pg-user-connections.md](../../_includes/mdb/note-pg-user-connections.md) %}


## Создать кластер {#create-cluster}


Для создания кластера {{ mpg-name }} нужна роль [{{ roles-vpc-user }}](../../vpc/security/index.md#vpc-user) и роль [{{ roles.mpg.editor }} или выше](../security/index.md#roles-list). О том, как назначить роль, см. [документацию {{ iam-name }}](../../iam/operations/roles/grant.md).


### {{ connection-manager-name }} {#conn-man}

Подключениями к БД кластера управляет сервис {{ connection-manager-name }}.

Вместе с кластером автоматически создаются:

* [Подключение {{ connection-manager-name }}](../../metadata-hub/concepts/connection-manager.md) с информацией о соединении с БД.

* [Секрет {{ lockbox-name }}](../../metadata-hub/concepts/secret.md), в котором хранится пароль пользователя — владельца БД. Хранение паролей в сервисе {{ lockbox-name }} обеспечивает их безопасность.

Подключение и секрет создаются для каждого нового пользователя БД. Чтобы увидеть все подключения, на странице кластера выберите вкладку **{{ ui-key.yacloud.connection-manager.label_connections }}**.

Для просмотра информации о подключении требуется роль `connection-manager.viewer`. Вы можете [настраивать доступ к подключениям в {{ connection-manager-name }}](../../metadata-hub/operations/connection-access.md).

Использование сервиса {{ connection-manager-name }} и секретов, созданных с его помощью, не тарифицируется.

{% list tabs group=instructions %}

- Консоль управления {#console}

  
  <iframe width="640" height="360" src="https://runtime.strm.yandex.ru/player/video/vplvhanaqkgdchwzk7xu?autoplay=0&mute=0" allow="autoplay; fullscreen; picture-in-picture; encrypted-media" frameborder="0" scrolling="no"></iframe>

  [Смотреть видео на YouTube](https://www.youtube.com/watch?v=UByUvah7lDU&list=PL1x4ET76A10bW1KU3twrdm7hH376z8G5R&index=6&pp=iAQB).



  Чтобы создать кластер {{ mpg-name }}:

  1. В [консоли управления]({{ link-console-main }}) выберите [каталог](../../resource-manager/concepts/resources-hierarchy.md#folder), в котором нужно создать кластер БД.
  1. Выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-postgresql }}**.
  1. Нажмите кнопку **{{ ui-key.yacloud.mdb.clusters.button_create }}**.
  1. Введите имя кластера в поле **{{ ui-key.yacloud.mdb.forms.base_field_name }}**. Имя кластера должно быть уникальным в рамках каталога.
  1. Выберите окружение, в котором нужно создать кластер (после создания кластера окружение изменить невозможно):
     * `PRODUCTION` — для стабильных версий ваших приложений.
     * `PRESTABLE` — для тестирования. Prestable-окружение аналогично Production-окружению и на него также распространяется SLA, но при этом на нем раньше появляются новые функциональные возможности, улучшения и исправления ошибок. В Prestable-окружении вы можете протестировать совместимость новых версий с вашим приложением.
  1. Выберите версию СУБД.
  1. Выберите класс хостов — он определяет технические характеристики [виртуальных машин](../../compute/concepts/vm.md), на которых будут развернуты хосты БД. Все доступные варианты перечислены в разделе [Классы хостов](../concepts/instance-types.md). При изменении класса хостов для кластера меняются характеристики всех уже созданных хостов.
  1. В блоке **{{ ui-key.yacloud.mdb.forms.section_disk }}**:

     * Выберите тип диска.

     
       {% include [storages-step-settings](../../_includes/mdb/settings-storages.md) %}


     * Выберите размер хранилища, который будет использоваться для данных и резервных копий. Подробнее о том, как занимают пространство резервные копии, см. раздел [Резервные копии](../concepts/backup.md).

  1. (Опционально) В блоке **{{ ui-key.yacloud.mdb.cluster.section_disk-scaling }}** укажите желаемые настройки:

      * В поле **{{ ui-key.yacloud.mdb.cluster.field_thresholds }}** задайте соответствующие условия, чтобы:

          * Размер хранилища увеличился в следующее [окно обслуживания](../concepts/maintenance.md#maintenance-window), когда хранилище окажется заполнено более чем на указанную долю (%).
          * Размер хранилища увеличился незамедлительно, когда хранилище окажется заполнено более чем на указанную долю (%).

          Можно задать оба условия, но порог для незамедлительного увеличения должен быть выше порога для увеличения в окно обслуживания.

      * В поле **{{ ui-key.yacloud.mdb.cluster.field_diskSizeLimit }}** укажите максимальный размер хранилища, который может быть установлен при автоматическом увеличении размера хранилища.

      {% include [storage-resize-steps](../../_includes/mdb/mpg/storage-resize-steps.md) %}

      
      {% include [warn-storage-resize](../../_includes/mdb/mpg/warn-storage-resize.md) %}


      {% include [settings-dependence-on-storage](../../_includes/mdb/mpg/settings-dependence-on-storage.md) %}

      Если настроено увеличение хранилища в окно обслуживания, настройте расписание окна обслуживания.

  1. В блоке **{{ ui-key.yacloud.mdb.forms.section_database }}** укажите атрибуты БД:

     * Имя БД. Это имя должно быть уникальным в рамках каталога.

       {% include [database-name-limit](../../_includes/mdb/mpg/note-info-db-name-limits.md) %}

     * Имя пользователя — владельца БД. По умолчанию новому пользователю выделяется 50 подключений к каждому хосту кластера. Изменить допустимое количество подключений можно с помощью настройки [Conn limit](../concepts/settings-list.md#setting-conn-limit).
       
       {% include [username-limits](../../_includes/mdb/mpg/note-info-user-name-and-pass-limits.md) %}

     * Пароль:

       * **{{ ui-key.yacloud.component.password-input.label_button-enter-manually }}** — выберите, чтобы ввести свой пароль. Длина пароля — от 8 до 128 символов.

       * **{{ ui-key.yacloud.component.password-input.label_button-generate }}** — выберите, чтобы сгенерировать пароль с помощью сервиса {{ connection-manager-name }}.

       Чтобы увидеть пароль, после создания кластера выберите вкладку **{{ ui-key.yacloud.postgresql.cluster.switch_users }}** и нажмите **{{ ui-key.yacloud.mdb.cluster.users.label_go-to-password }}** в строке нужного пользователя. Откроется страница секрета {{ lockbox-name }}, в котором хранится пароль. Для просмотра паролей требуется роль `lockbox.payloadViewer`.
     
     * Локаль сортировки и локаль набора символов. Эти настройки определяют правила, по которым производится сортировка строк (`LC_COLLATE`) и классификация символов (`LC_CTYPE`). В {{ mpg-name }} настройки локали действуют на уровне отдельно взятой БД.

       {% include [postgresql-locale](../../_includes/mdb/mpg-locale-settings.md) %}

  
  1. В блоке **{{ ui-key.yacloud.mdb.forms.section_network }}** выберите:
     * [Облачную сеть](../../vpc/concepts/network.md#network) для размещения кластера. Если сети нет, нажмите **{{ ui-key.yacloud.component.vpc.network-select.button_create-network }}** и создайте ее:

       1. В открывшемся окне укажите имя сети и выберите каталог, в котором она будет создана.
       1. (Опционально) Выберите опцию **{{ ui-key.yacloud.vpc.networks.create.field_is-default }}**, чтобы автоматически создать подсети во всех зонах доступности.
       1. Нажмите кнопку **{{ ui-key.yacloud.vpc.networks.create.button_create }}**.

       {% include [network-cannot-be-changed](../../_includes/mdb/mpg/network-cannot-be-changed.md) %}

     * [Группы безопасности](../../vpc/concepts/security-groups.md) для сетевого трафика кластера. Может потребоваться дополнительная [настройка групп безопасности](connect.md#configuring-security-groups) для того, чтобы можно было подключаться к кластеру.


  1. В блоке **{{ ui-key.yacloud.mdb.forms.section_host }}** выберите параметры хостов БД, создаваемых вместе с кластером. По умолчанию каждый хост создается в отдельной [подсети](../../vpc/concepts/network.md#subnet). Чтобы выбрать для хоста конкретную подсеть, в строке этого хоста нажмите значок ![image](../../_assets/console-icons/pencil.svg).

     При настройке параметров хостов обратите внимание, что если в блоке **{{ ui-key.yacloud.mdb.forms.section_disk }}** выбран `local-ssd` или `network-ssd-nonreplicated`, необходимо добавить не менее трех хостов в кластер.

     
     Чтобы к хосту можно было подключаться из интернета, включите настройку **{{ ui-key.yacloud.mdb.hosts.dialog.field_public_ip }}**.


  1. При необходимости задайте дополнительные настройки кластера:

     {% include [Additional cluster settings](../../_includes/mdb/mpg/extra-settings-web-console.md) %}

  1. При необходимости задайте [настройки СУБД уровня кластера](../concepts/settings-list.md#dbms-cluster-settings).
  
     {% note info %}

     Некоторые настройки {{ PG }} [зависят от выбранного класса хостов или от размера хранилища](../concepts/settings-list.md#settings-instance-dependent).

     {% endnote %}

  1. Нажмите кнопку **{{ ui-key.yacloud.mdb.forms.button_create }}**.

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  Чтобы создать кластер {{ mpg-name }}:

  
  1. Проверьте, есть ли в [каталоге](../../resource-manager/concepts/resources-hierarchy.md#folder) [подсети](../../vpc/concepts/network.md#subnet) для хостов кластера:

     ```bash
     yc vpc subnet list
     ```

     Если ни одной подсети в каталоге нет, [создайте нужные подсети](../../vpc/operations/subnet-create.md) в [сервисе {{ vpc-full-name }}](../../vpc/).


  1. Посмотрите описание команды CLI для создания кластера:

     ```bash
     {{ yc-mdb-pg }} cluster create --help
     ```

  1. Укажите параметры кластера в команде создания (в примере приведены не все доступные параметры):

     
     ```bash
     {{ yc-mdb-pg }} cluster create \
       --name <имя_кластера> \
       --environment <окружение> \
       --network-name <имя_сети> \
       --host zone-id=<зона_доступности>,`
                `subnet-id=<идентификатор_подсети>,`
                `assign-public-ip=<доступ_к_хосту_из_интернета> \
       --resource-preset <класс_хоста> \
       --user name=<имя_пользователя>,password=<пароль_пользователя> \
       --database name=<имя_БД>,owner=<имя_владельца_БД> \
       --disk-size <размер_хранилища_ГБ> \
       --disk-type <network-hdd|network-ssd|network-ssd-nonreplicated|local-ssd> \
       --security-group-ids <список_идентификаторов_групп_безопасности> \
       --connection-pooling-mode=<режим_работы_менеджера_подключений> \
       --deletion-protection
     ```

     
     Где:

     * `environment` — окружение: `prestable` или `production`.
     * `disk-type` — тип диска.

     
     * `assign-public-ip` — доступ к хосту из интернета: `true` или `false`.


     * `deletion-protection` — защита от удаления кластера, его баз данных и пользователей.

       По умолчанию при создании пользователей и БД значение параметра наследуется от кластера. Значение также можно задать вручную, подробнее см. в разделах [Управление пользователями](cluster-users.md) и [Управление БД](databases.md).

       Если параметр изменен на работающем кластере, новое значение унаследуют только пользователи и БД с защитой **Как у кластера**.

       {% include [Ограничения защиты от удаления кластера](../../_includes/mdb/deletion-protection-limits-data.md) %}

     
     Идентификатор подсети `subnet-id` необходимо указывать, если в выбранной [зоне доступности](../../overview/concepts/geo-scope.md) создано две и больше подсетей.

     {% include [network-cannot-be-changed](../../_includes/mdb/mpg/network-cannot-be-changed.md) %}



     {% include [database-name-limit](../../_includes/mdb/mpg/note-info-db-name-limits.md) %}

     Длина пароля — от 8 до 128 символов.

     {% note info %}

     Пароль также можно сгенерировать с помощью сервиса {{ connection-manager-name }}. Для этого измените команду и задайте параметры пользователя таким образом:
     
     ```bash
       --user name=<имя_пользователя>,generate-password=true
     ```

     Чтобы увидеть пароль, в [консоли управления]({{ link-console-main }}) выберите созданный кластер, перейдите на вкладку **{{ ui-key.yacloud.postgresql.cluster.switch_users }}** и нажмите **{{ ui-key.yacloud.mdb.cluster.users.label_go-to-password }}** в строке нужного пользователя. Откроется страница секрета {{ lockbox-name }}, в котором хранится пароль. Для просмотра паролей требуется роль `lockbox.payloadViewer`.

     {% endnote %}

     Доступные [режимы работы менеджера подключений](../concepts/pooling.md): `SESSION`, `TRANSACTION` или `STATEMENT`.

     Также вы можете указать дополнительную опцию `replication-source` в параметре `--host` для того, чтобы [вручную управлять потоками репликации](../concepts/replication.md#replication-manual).

     
     Чтобы разрешить доступ к кластеру из сервиса [{{ sf-full-name }}](../../functions/), передайте параметр `--serverless-access`. Подробнее о настройке доступа см. в документации [{{ sf-name }}](../../functions/operations/database-connection.md).

     Чтобы разрешить доступ к кластеру из сервиса [{{ yq-full-name }}](../../query/index.yaml), передайте параметр `--yandexquery-access=true`. Функциональность находится на стадии [Preview](../../overview/concepts/launch-stages.md) и предоставляется по запросу.


     {% note info %}

     По умолчанию при создании кластера устанавливается режим [технического обслуживания](../concepts/maintenance.md) `anytime` — в любое время. Вы можете установить конкретное время обслуживания при [изменении настроек кластера](update.md#change-additional-settings).

     {% endnote %}

- {{ TF }} {#tf}

  {% include [terraform-definition](../../_tutorials/_tutorials_includes/terraform-definition.md) %}

  {% include [terraform-install](../../_includes/terraform-install.md) %}

  Чтобы создать кластер {{ mpg-name }}:
  1. Опишите в конфигурационном файле параметры ресурсов, которые необходимо создать:
     * Кластер БД — описание кластера и его хостов.
     * База данных — описание БД кластера.
     * Пользователь — описание пользователя кластера.

     * {% include [Terraform network description](../../_includes/mdb/terraform/network.md) %}

     * {% include [Terraform subnet description](../../_includes/mdb/terraform/subnet.md) %}

     {% include [network-cannot-be-changed](../../_includes/mdb/mpg/network-cannot-be-changed.md) %}

     Пример структуры конфигурационного файла:

     
     ```hcl
     resource "yandex_mdb_postgresql_cluster" "<имя_кластера>" {
       name                = "<имя_кластера>"
       environment         = "<окружение>"
       network_id          = "<идентификатор_сети>"
       security_group_ids  = [ "<список_идентификаторов_групп_безопасности>" ]
       deletion_protection = <защита_от_удаления>

       config {
         version = "<версия_{{ PG }}>"
         resources {
           resource_preset_id = "<класс_хоста>"
           disk_type_id       = "<тип_диска>"
           disk_size          = <размер_хранилища_ГБ>
         }
         pooler_config {
           pool_discard = <параметр_Odyssey>
           pooling_mode = "<режим_работы>"
         }
         ...
       }

       host {
         zone             = "<зона_доступности>"
         name             = "<имя_хоста>"
         subnet_id        = "<идентификатор_подсети>"
         assign_public_ip = <доступ_к_хосту_из_интернета>
       }
     }

     resource "yandex_mdb_postgresql_database" "<имя_БД>" {
       cluster_id = "<идентификатор_кластера>"
       name       = "<имя_БД>"
       owner      = "<имя_владельца_БД>"
       depends_on = [
         yandex_mdb_postgresql_user.<имя_пользователя>
       ]
     }

     resource "yandex_mdb_postgresql_user" "<имя_пользователя>" {
       cluster_id = "<идентификатор_кластера>"
       name       = "<имя_пользователя>"
       password   = "<пароль_пользователя>"
     }

     resource "yandex_vpc_network" "<имя_сети>" { name = "<имя_сети>" }

     resource "yandex_vpc_subnet" "<имя_подсети>" {
       name           = "<имя_подсети>"
       zone           = "<зона_доступности>"
       network_id     = "<идентификатор_сети>"
       v4_cidr_blocks = ["<диапазон>"]
     }
     ```


     Где:

     * `environment` — окружение: `PRESTABLE` или `PRODUCTION`.

     
     * `assign_public_ip` — доступ к хосту из интернета: `true` или `false`.


     * `deletion_protection` — защита от удаления кластера, его баз данных и пользователей: `true` или `false`.

       По умолчанию при создании пользователей и БД значение параметра наследуется от кластера. Значение также можно задать вручную, подробнее см. в разделах [Управление пользователями](cluster-users.md) и [Управление БД](databases.md).

       Если параметр изменен на работающем кластере, новое значение унаследуют только пользователи и БД с защитой **Как у кластера**.

       {% include [Ограничения защиты от удаления кластера](../../_includes/mdb/deletion-protection-limits-data.md) %}

     * `version` — версия {{ PG }}: {{ pg.versions.tf.str }}.
     * `pool_discard`  — параметр Odyssey `pool_discard`: `true` или `false`.
     * `pooling_mode` — режим работы: `SESSION`, `TRANSACTION` или `STATEMENT`.

     {% include [database-name-limit](../../_includes/mdb/mpg/note-info-db-name-limits.md) %}

     Длина пароля — от 8 до 128 символов.

     {% note info %}

     Пароль также можно сгенерировать с помощью сервиса {{ connection-manager-name }}. Для этого вместо `password = "<пароль_пользователя>"` укажите `generate_password = true`.

     Чтобы увидеть пароль, в [консоли управления]({{ link-console-main }}) выберите созданный кластер, перейдите на вкладку **{{ ui-key.yacloud.postgresql.cluster.switch_users }}** и нажмите **{{ ui-key.yacloud.mdb.cluster.users.label_go-to-password }}** в строке нужного пользователя. Откроется страница секрета {{ lockbox-name }}, в котором хранится пароль. Для просмотра паролей требуется роль `lockbox.payloadViewer`.

     {% endnote %}

     {% include [Maintenance window](../../_includes/mdb/mpg/terraform/maintenance-window.md) %}

     {% include [Performance diagnostics](../../_includes/mdb/mpg/terraform/performance-diagnostics.md) %}

     Полный список доступных для изменения полей конфигурации кластера {{ mpg-name }} см. в [документации провайдера {{ TF }}]({{ tf-provider-mpg }}).
  1. Проверьте корректность настроек.

     {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

  1. Создайте кластер.

     {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

     {% include [Terraform timeouts](../../_includes/mdb/mpg/terraform/timeouts.md) %}

- REST API {#api}

  1. [Получите IAM-токен для аутентификации в API](../api-ref/authentication.md) и поместите токен в переменную среды окружения:

     {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

  1. Создайте файл `body.json` и добавьте в него следующее содержимое:

     
     ```json
     {
       "folderId": "<идентификатор_каталога>",
       "name": "<имя_кластера>",
       "environment": "<окружение>",
       "networkId": "<идентификатор_сети>",
       "securityGroupIds": [
         "<идентификатор_группы_безопасности_1>",
         "<идентификатор_группы_безопасности_2>",
         ...
         "<идентификатор_группы_безопасности_N>"
       ],
       "deletionProtection": <защита_от_удаления:_true_или_false>,
       "configSpec": {
         "version": "<версия_{{ PG }}>",
         "resources": {
           "resourcePresetId": "<класс_хостов>",
           "diskSize": "<размер_хранилища_в_байтах>",
           "diskTypeId": "<тип_диска>"
         },
         "access": {
           "dataLens": <доступ_к_{{ datalens-name }}:_true_или_false>,
           "webSql": <доступ_к_{{ websql-name }}:_true_или_false>,
           "serverless": <доступ_к_Cloud_Functions:_true_или_false>,
           "dataTransfer": <доступ_к_Data_Transfer:_true_или_false>,
           "yandexQuery": <доступ_к_{{ yq-name }}:_true_или_false>
         },
         "performanceDiagnostics": {
           "enabled": <активация_сбора_статистики:_true_или_false>,
           "sessionsSamplingInterval": "<интервал_сбора_сессий>",
           "statementsSamplingInterval": "<интервал_сбора_запросов>"
         }
       },
       "databaseSpecs": [
         {
           "name": "<имя_БД>",
           "owner": "<имя_владельца_БД>"
         },
         { <аналогичный_набор_настроек_для_БД_2> },
         { ... },
         { <аналогичный_набор_настроек_для_БД_N> }
       ],
       "userSpecs": [
         {
           "name": "<имя_пользователя>",
           "password": "<пароль_пользователя>",
           "permissions": [
             {
               "databaseName": "<имя_БД>"
             }
           ],
           "login": <разрешение_для_пользователя_на_подключение_к_БД:_true_или_false>
         },
         { <аналогичный_набор_настроек_для_пользователя_2> },
         { ... },
         { <аналогичный_набор_настроек_для_пользователя_N> }
       ],
       "hostSpecs": [
         {
           "zoneId": "<зона_доступности>",
           "subnetId": "<идентификатор_подсети>",
           "assignPublicIp": <публичный_адрес_хоста:_true_или_false>
         },
         { <аналогичный_набор_настроек_для_хоста_2> },
         { ... },
         { <аналогичный_набор_настроек_для_хоста_N> }
       ]
     }
     ```


     Где:

     * `folderId` — идентификатор каталога. Его можно запросить со [списком каталогов в облаке](../../resource-manager/operations/folder/get-id.md).
     * `name` — имя кластера.
     * `environment` — окружение кластера: `PRODUCTION` или `PRESTABLE`.
     * `networkId` — идентификатор [сети](../../vpc/concepts/network.md#network), в которой будет размещен кластер.

       {% include [network-cannot-be-changed](../../_includes/mdb/mpg/network-cannot-be-changed.md) %}

     
     * `securityGroupIds` — идентификаторы [групп безопасности](../concepts/network.md#security-groups).


     * `deletionProtection` — защита от удаления кластера, его баз данных и пользователей.

       По умолчанию при создании пользователей и БД значение параметра наследуется от кластера. Значение также можно задать вручную, подробнее см. в разделах [Управление пользователями](cluster-users.md) и [Управление БД](databases.md).

       Если параметр изменен на работающем кластере, новое значение унаследуют только пользователи и БД с защитой **Как у кластера**.

        {% include [Ограничения защиты от удаления кластера](../../_includes/mdb/deletion-protection-limits-data.md) %}

     * `configSpec` — настройки кластера:

       * `version` — версия {{ PG }}.
       * `resources` — ресурсы кластера:

         * `resourcePresetId` — [класс хостов](../concepts/instance-types.md);
         * `diskSize` — размер диска в байтах;
         * `diskTypeId` — [тип диска](../concepts/storage.md).

       
       * `access` — настройки доступа кластера к следующим сервисам {{ yandex-cloud }}:

         * `dataLens` — [{{ datalens-full-name }}](../../datalens/index.yaml);
         * `webSql` — [{{ websql-full-name }}](../../websql/index.yaml);
         * `serverless` — [{{ sf-full-name }}](../../functions/index.yaml);
         * `dataTransfer` — [{{ data-transfer-full-name }}](../../data-transfer/index.yaml);
         * `yandexQuery` — [{{ yq-full-name }}](../../query/index.yaml).


       * `performanceDiagnostics` — настройки для [сбора статистики](performance-diagnostics.md#activate-stats-collector):

         * `enabled` — активация сбора статистики.
         * `sessionsSamplingInterval` — интервал сбора сессий. Возможные значения: от `1` до `86400` секунд.
         * `statementsSamplingInterval` — интервал сбора запросов. Возможные значения: от `60` до `86400` секунд.

     * `databaseSpecs` — настройки баз данных в виде массива элементов. Каждый элемент соответствует отдельной БД и имеет следующую структуру:

       * `name` — имя БД.
       * `owner` — имя владельца БД. Должно совпадать с именем одного из пользователей, указанных в запросе.

     * `userSpecs` — настройки пользователей в виде массива элементов. Каждый элемент соответствует отдельному пользователю и имеет следующую структуру:

       * `name` — имя пользователя.
       * `password` — пароль пользователя. Длина пароля — от 8 до 128 символов.
       
          Пароль также можно сгенерировать с помощью сервиса {{ connection-manager-name }}. Для этого вместо `"password": "<пароль_пользователя>"` укажите `"generatePassword": true`.

          Чтобы увидеть пароль, в [консоли управления]({{ link-console-main }}) выберите созданный кластер, перейдите на вкладку **{{ ui-key.yacloud.postgresql.cluster.switch_users }}** и нажмите **{{ ui-key.yacloud.mdb.cluster.users.label_go-to-password }}** в строке нужного пользователя. Откроется страница секрета {{ lockbox-name }}, в котором хранится пароль. Для просмотра паролей требуется роль `lockbox.payloadViewer`.

       * `permissions.databaseName` — имя базы данных, к которой пользователь получает доступ.
       * `login` — разрешение для пользователя на подключение к БД.

     * `hostSpecs` — настройки хостов кластера в виде массива элементов. Каждый элемент соответствует отдельному хосту и имеет следующую структуру:

       * `zoneId` — [зона доступности](../../overview/concepts/geo-scope.md);
       * `subnetId` — идентификатор [подсети](../../vpc/concepts/network.md#subnet);
       * `assignPublicIp` — разрешение на [подключение](connect.md) к хосту из интернета.

  1. Воспользуйтесь методом [Cluster.Create](../api-ref/Cluster/create.md) и выполните запрос, например, с помощью {{ api-examples.rest.tool }}:

     ```bash
     curl \
       --request POST \
       --header "Authorization: Bearer $IAM_TOKEN" \
       --header "Content-Type: application/json" \
       --url 'https://{{ api-host-mdb }}/managed-postgresql/v1/clusters' \
       --data "@body.json"
     ```

  1. Убедитесь, что запрос был выполнен успешно, изучив [ответ сервера](../api-ref/Cluster/create.md#yandex.cloud.operation.Operation).

- gRPC API {#grpc-api}

  1. [Получите IAM-токен для аутентификации в API](../api-ref/authentication.md) и поместите токен в переменную среды окружения:

     {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

  1. {% include [grpc-api-setup-repo](../../_includes/mdb/grpc-api-setup-repo.md) %}
  1. Создайте файл `body.json` и добавьте в него следующее содержимое:

     
     ```json
     {
       "folder_id": "<идентификатор_каталога>",
       "name": "<имя_кластера>",
       "environment": "<окружение>",
       "network_id": "<идентификатор_сети>",
       "security_group_ids": [
         "<идентификатор_группы_безопасности_1>",
         "<идентификатор_группы_безопасности_2>",
         ...
         "<идентификатор_группы_безопасности_N>"
       ],
       "deletion_protection": <защита_от_удаления:_true_или_false>,
       "config_spec": {
         "version": "<версия_{{ PG }}>",
         "resources": {
           "resource_preset_id": "<класс_хостов>",
           "disk_size": "<размер_хранилища_в_байтах>",
           "disk_type_id": "<тип_диска>"
         },
         "access": {
           "data_lens": <доступ_к_{{ datalens-name }}:_true_или_false>,
           "web_sql": <доступ_к_{{ websql-name }}:_true_или_false>,
           "serverless": <доступ_к_Cloud_Functions:_true_или_false>,
           "data_transfer": <доступ_к_Data_Transfer:_true_или_false>,
           "yandex_query": <доступ_к_{{ yq-name }}:_true_или_false>
         },
         "performance_diagnostics": {
           "enabled": <активация_сбора_статистики:_true_или_false>,
           "sessions_sampling_interval": "<интервал_сбора_сессий>",
           "statements_sampling_interval": "<интервал_сбора_запросов>"
         }
       },
       "database_specs": [
         {
           "name": "<имя_БД>",
           "owner": "<имя_владельца_БД>"
         },
         { <аналогичный_набор_настроек_для_БД_2> },
         { ... },
         { <аналогичный_набор_настроек_для_БД_N> }
       ],
       "user_specs": [
         {
           "name": "<имя_пользователя>",
           "password": "<пароль_пользователя>",
           "permissions": [
             {
               "database_name": "<имя_БД>"
             }
           ],
           "login": <разрешение_для_пользователя_на_подключение_к_БД:_true_или_false>
         },
         { <аналогичный_набор_настроек_для_пользователя_2> },
         { ... },
         { <аналогичный_набор_настроек_для_пользователя_N> }
       ],
       "host_specs": [
         {
           "zone_id": "<зона_доступности>",
           "subnet_id": "<идентификатор_подсети>",
           "assign_public_ip": <публичный_адрес_хоста:_true_или_false>
         },
         { <аналогичный_набор_настроек_для_хоста_2> },
         { ... },
         { <аналогичный_набор_настроек_для_хоста_N> }
       ]
     }
     ```


     Где:

     * `folder_id` — идентификатор каталога. Его можно запросить со [списком каталогов в облаке](../../resource-manager/operations/folder/get-id.md).
     * `name` — имя кластера.
     * `environment` — окружение кластера: `PRODUCTION` или `PRESTABLE`.
     * `network_id` — идентификатор [сети](../../vpc/concepts/network.md#network), в которой будет размещен кластер.

       {% include [network-cannot-be-changed](../../_includes/mdb/mpg/network-cannot-be-changed.md) %}

     
     * `security_group_ids` — идентификаторы [групп безопасности](../concepts/network.md#security-groups).


     * `deletion_protection` — защита от удаления кластера, его баз данных и пользователей.

        По умолчанию при создании пользователей и БД значение параметра наследуется от кластера. Значение также можно задать вручную, подробнее см. в разделах [Управление пользователями](cluster-users.md) и [Управление БД](databases.md).

        Если параметр изменен на работающем кластере, новое значение унаследуют только пользователи и БД с защитой **Как у кластера**.

        {% include [Ограничения защиты от удаления кластера](../../_includes/mdb/deletion-protection-limits-data.md) %}

     * `config_spec` — настройки кластера:

       * `version` — версия {{ PG }}.
       * `resources` — ресурсы кластера:

         * `resource_preset_id` — [класс хостов](../concepts/instance-types.md);
         * `disk_size` — размер диска в байтах;
         * `disk_type_id` — [тип диска](../concepts/storage.md).

       
       * `access` — настройки доступа кластера к следующим сервисам {{ yandex-cloud }}:

         * `data_lens` — [{{ datalens-full-name }}](../../datalens/index.yaml);
         * `web_sql` — [{{ websql-full-name }}](../../websql/index.yaml);
         * `serverless` — [{{ sf-full-name }}](../../functions/index.yaml);
         * `data_transfer` — [{{ data-transfer-full-name }}](../../data-transfer/index.yaml);
         * `yandex_query` — [{{ yq-full-name }}](../../query/index.yaml).


       * `performance_diagnostics` — настройки для [сбора статистики](performance-diagnostics.md#activate-stats-collector):

         * `enabled` — активация сбора статистики.
         * `sessions_sampling_interval` — интервал сбора сессий. Возможные значения: от `1` до `86400` секунд.
         * `statements_sampling_interval` — интервал сбора запросов. Возможные значения: от `60` до `86400` секунд.

     * `database_specs` — настройки баз данных в виде массива элементов. Каждый элемент соответствует отдельной БД и имеет следующую структуру:

       * `name` — имя БД.
       * `owner` — имя владельца БД. Должно совпадать с именем одного из пользователей, указанных в запросе.

     * `user_specs` — настройки пользователей в виде массива элементов. Каждый элемент соответствует отдельному пользователю и имеет следующую структуру:

       * `name` — имя пользователя.
       * `password` — пароль пользователя. Длина пароля — от 8 до 128 символов.
       
          Пароль также можно сгенерировать с помощью сервиса {{ connection-manager-name }}. Для этого вместо `"password": "<пароль_пользователя>"` укажите `"generate_password": true`.

          Чтобы увидеть пароль, в [консоли управления]({{ link-console-main }}) выберите созданный кластер, перейдите на вкладку **{{ ui-key.yacloud.postgresql.cluster.switch_users }}** и нажмите **{{ ui-key.yacloud.mdb.cluster.users.label_go-to-password }}** в строке нужного пользователя. Откроется страница секрета {{ lockbox-name }}, в котором хранится пароль. Для просмотра паролей требуется роль `lockbox.payloadViewer`.

       * `permissions.database_name` — имя базы данных, к которой пользователь получает доступ.
       * `login` — разрешение для пользователя на подключение к БД.

     * `host_specs` — настройки хостов кластера в виде массива элементов. Каждый элемент соответствует отдельному хосту и имеет следующую структуру:

       * `zone_id` — [зона доступности](../../overview/concepts/geo-scope.md);
       * `subnet_id` — идентификатор [подсети](../../vpc/concepts/network.md#subnet);
       * `assign_public_ip` — разрешение на [подключение](connect.md) к хосту из интернета.

  1. Воспользуйтесь вызовом [ClusterService.Create](../api-ref/grpc/Cluster/create.md) и выполните запрос, например, с помощью {{ api-examples.grpc.tool }}:

     ```bash
     grpcurl \
       -format json \
       -import-path ~/cloudapi/ \
       -import-path ~/cloudapi/third_party/googleapis/ \
       -proto ~/cloudapi/yandex/cloud/mdb/postgresql/v1/cluster_service.proto \
       -rpc-header "Authorization: Bearer $IAM_TOKEN" \
       -d @ \
       {{ api-host-mdb }}:{{ port-https }} \
       yandex.cloud.mdb.postgresql.v1.ClusterService.Create \
       < body.json
     ```

  1. Убедитесь, что запрос был выполнен успешно, изучив [ответ сервера](../api-ref/grpc/Cluster/create.md#yandex.cloud.mdb.postgresql.v1.Cluster).

{% endlist %}


{% note warning %}

Если вы указали идентификаторы групп безопасности при создании кластера, для подключения к нему может потребоваться дополнительная [настройка групп безопасности](connect.md#configuring-security-groups).

{% endnote %}


## Создать копию кластера {#duplicate}

Вы можете создать кластер {{ PG }}, который будет обладать настройками созданного ранее кластера. Для этого конфигурация исходного кластера {{ PG }} импортируется в {{ TF }}. В результате вы можете либо создать идентичную копию, либо взять за основу импортированную конфигурацию и внести в нее изменения. Использовать импорт удобно, если исходный кластер {{ PG }} обладает множеством настроек и нужно создать похожий на него кластер.

Чтобы создать копию кластера {{ PG }}:

{% list tabs group=instructions %}

- {{ TF }} {#tf}

    1. {% include [terraform-install-without-setting](../../_includes/mdb/terraform/install-without-setting.md) %}
    1. {% include [terraform-authentication](../../_includes/mdb/terraform/authentication.md) %}
    1. {% include [terraform-setting](../../_includes/mdb/terraform/setting.md) %}
    1. {% include [terraform-configure-provider](../../_includes/mdb/terraform/configure-provider.md) %}

    1. В той же рабочей директории разместите файл с расширением `.tf` и содержимым:

        ```hcl
        resource "yandex_mdb_postgresql_cluster" "old" { }
        ```

    1. Запишите идентификатор первоначального кластера {{ PG }} в переменную окружения:

        ```bash
        export POSTGRESQL_CLUSTER_ID=<идентификатор_кластера>
        ```

        Идентификатор можно запросить вместе со [списком кластеров в каталоге](../../managed-postgresql/operations/cluster-list.md#list-clusters).

    1. Импортируйте настройки первоначального кластера {{ PG }} в конфигурацию {{ TF }}:

        ```bash
        terraform import yandex_mdb_postgresql_cluster.old ${POSTGRESQL_CLUSTER_ID}
        ```

    1. Получите импортированную конфигурацию:

        ```bash
        terraform show
        ```

    1. Скопируйте ее из терминала и вставьте в файл с расширением `.tf`.
    1. Расположите файл в новой директории `imported-cluster`.
    1. Измените скопированную конфигурацию так, чтобы из нее можно было создать новый кластер:

        * Укажите новое имя кластера в строке `resource` и параметре `name`.
        * Удалите параметры `created_at`, `health`, `id` и `status`.
        * В блоках `host` удалите параметры `fqdn` и `role`.
        * Если в блоке `disk_size_autoscaling` указано значение параметра `disk_size_limit = 0`, удалите этот блок.
        * Если в блоке `maintenance_window` указано значение параметра `type = "ANYTIME"`, удалите параметр `hour`.
        * (Опционально) Внесите дополнительные изменения, если вам нужна не идентичная, а кастомизированная копия.

    1. В директории `imported-cluster` [получите данные для аутентификации](../../tutorials/infrastructure-management/terraform-quickstart.md#get-credentials).

    1. В этой же директории [настройте и инициализируйте провайдер](../../tutorials/infrastructure-management/terraform-quickstart.md#configure-provider). Чтобы не создавать конфигурационный файл с настройками провайдера вручную, [скачайте его](https://github.com/yandex-cloud-examples/yc-terraform-provider-settings/blob/main/provider.tf).

    1. Поместите конфигурационный файл в директорию `imported-cluster` и [укажите значения параметров](../../tutorials/infrastructure-management/terraform-quickstart.md#configure-provider). Если данные для аутентификации не были добавлены в переменные окружения, укажите их в конфигурационном файле.

    1. Проверьте корректность файлов конфигурации {{ TF }}:

        ```bash
        terraform validate
        ```

        Если в файлах конфигурации есть ошибки, {{ TF }} на них укажет.

    1. Создайте необходимую инфраструктуру:

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

        {% include [explore-resources](../../_includes/mdb/terraform/explore-resources.md) %}

    {% include [Terraform timeouts](../../_includes/mdb/mpg/terraform/timeouts.md) %}

{% endlist %}

## Примеры {#examples}

### Создание кластера с одним хостом {#creating-a-single-host-cluster}

{% list tabs group=instructions %}

- CLI {#cli}

  Чтобы создать кластер с одним хостом, передайте один параметр `--host`.

  Создайте кластер {{ mpg-name }} с тестовыми характеристиками:

  
  * С именем `mypg`.
  * В окружении `production`.
  * В сети `default`.
  * В группе безопасности `{{ security-group }}`.
  * С одним хостом класса `{{ host-class }}` в подсети `b0rcctk2rvtr********`, в зоне доступности `{{ region-id }}-a`.
  * С хранилищем на сетевых SSD-дисках (`{{ disk-type-example }}`) размером 20 ГБ.
  * С одним пользователем (`user1`), с паролем `user1user1`.
  * С одной БД `db1`, принадлежащей пользователю `user1`.
  * С защитой от случайного удаления кластера, его баз данных и пользователей.


  Выполните следующую команду:

  
  ```bash
  {{ yc-mdb-pg }} cluster create \
     --name mypg \
     --environment production \
     --network-name default \
     --resource-preset {{ host-class }} \
     --host zone-id={{ region-id }}-a,subnet-id=b0rcctk2rvtr******** \
     --disk-type {{ disk-type-example }} \
     --disk-size 20 \
     --user name=user1,password=user1user1 \
     --database name=db1,owner=user1 \
     --security-group-ids {{ security-group }} \
     --deletion-protection
  ```


- {{ TF }} {#tf}

  Создайте кластер {{ mpg-name }} и сеть для него с тестовыми характеристиками:

  * С именем `mypg`.
  * Версии `{{ pg.versions.tf.latest }}`.
  * В окружении `PRESTABLE`.
  * В облаке с идентификатором `{{ tf-cloud-id }}`.
  * В каталоге с идентификатором `{{ tf-folder-id }}`.
  * В новой сети `mynet`.

  
  * В новой группе безопасности `pgsql-sg`, разрешающей подключение к кластеру из интернета через порт `6432`.


  * С одним хостом класса `{{ host-class }}` в новой подсети `mysubnet`, в зоне доступности `{{ region-id }}-a`. Подсеть `mysubnet` будет иметь диапазон `10.5.0.0/24`.
  * С хранилищем на сетевых SSD-дисках (`{{ disk-type-example }}`) размером 20 ГБ.
  * С одним пользователем (`user1`), с паролем `user1user1`.
  * С одной БД `db1`, принадлежащей пользователю `user1`.
  * С защитой от случайного удаления кластера, его баз данных и пользователей.

  Конфигурационный файл для такого кластера выглядит так:

  
  ```hcl
  resource "yandex_mdb_postgresql_cluster" "mypg" {
    name                = "mypg"
    environment         = "PRESTABLE"
    network_id          = yandex_vpc_network.mynet.id
    security_group_ids  = [ yandex_vpc_security_group.pgsql-sg.id ]
    deletion_protection = true

    config {
      version = {{ pg.versions.tf.latest }}
      resources {
        resource_preset_id = "{{ host-class }}"
        disk_type_id       = "{{ disk-type-example }}"
        disk_size          = "20"
      }
    }

    host {
      zone      = "{{ region-id }}-a"
      name      = "mypg-host-a"
      subnet_id = yandex_vpc_subnet.mysubnet.id
    }
  }

  resource "yandex_mdb_postgresql_database" "db1" {
    cluster_id = yandex_mdb_postgresql_cluster.mypg.id
    name       = "db1"
    owner      = "user1"
  }

  resource "yandex_mdb_postgresql_user" "user1" {
    cluster_id = yandex_mdb_postgresql_cluster.mypg.id
    name       = "user1"
    password   = "user1user1"
  }

  resource "yandex_vpc_network" "mynet" {
    name = "mynet"
  }

  resource "yandex_vpc_subnet" "mysubnet" {
    name           = "mysubnet"
    zone           = "{{ region-id }}-a"
    network_id     = yandex_vpc_network.mynet.id
    v4_cidr_blocks = ["10.5.0.0/24"]
  }

  resource "yandex_vpc_security_group" "pgsql-sg" {
    name       = "pgsql-sg"
    network_id = yandex_vpc_network.mynet.id

    ingress {
      description    = "PostgreSQL"
      port           = 6432
      protocol       = "TCP"
      v4_cidr_blocks = [ "0.0.0.0/0" ]
    }
  }
  ```


{% endlist %}
