---
title: Управление политикой доступа к бакету {{ objstorage-full-name }}
description: Следуя данной инструкции, вы научитесь управлять политикой доступа к бакету в {{ objstorage-name }}.
---

# Управление политикой доступа (bucket policy)

{% include [full-overview](../../../_includes/storage/security/full-overview.md) %}

[Политики доступа (bucket policy)](../../concepts/policy.md) устанавливают права на действия с [бакетами](../../concepts/bucket.md), [объектами](../../concepts/object.md) и группами объектов.

Образцы политик доступа для решения конкретных задач см. в подразделе [Примеры конфигураций](../../concepts/policy.md#config-examples).


{% note warning %}

{% include [s3-with-policy-access](../../../_includes/storage/s3-with-policy-access.md) %}

{% endnote %}


## Применить или изменить политику {#apply-policy}

Минимально необходимая роль для применения или изменения политики доступа — `storage.configurer`. См. [описание роли](../../../storage/security/index.md#storage-configurer).

{% note info %}

Если ранее для бакета была настроена политика доступа, то после применения изменений она будет полностью перезаписана.

{% endnote %}

Для применения или изменения политики доступа к бакету:

{% list tabs group=instructions %}

- Консоль управления {#console}

  1. В [консоли управления]({{ link-console-main }}) в списке сервисов выберите **{{ ui-key.yacloud.iam.folder.dashboard.label_storage }}** и перейдите в бакет, в котором нужно настроить политику доступа.
  1. На панели слева выберите ![image](../../../_assets/console-icons/persons-lock.svg) **{{ ui-key.yacloud.storage.bucket.switch_security }}** и перейдите на вкладку **{{ ui-key.yacloud.storage.bucket.switch_policy }}**.
  1. Нажмите кнопку **{{ ui-key.yacloud.storage.bucket.policy.button_policy-edit }}**.
  1. Введите идентификатор политики доступа.
  1. Настройте правило:
     1. Введите идентификатор правила.
     1. Настройте параметры правила:
        * **{{ ui-key.yacloud.storage.bucket.policy.field_effect }}** — разрешить или запретить.
        * **{{ ui-key.yacloud.storage.bucket.policy.field_principal-type }}** — включить или исключить пользователей.
        * **{{ ui-key.yacloud.storage.bucket.policy.field_user }}** — выберите всех пользователей или задайте набор конкретных субъектов.

            Чтобы задать набор конкретных субъектов:
            
            * Выберите опцию `{{ ui-key.yacloud.storage.bucket.policy.label_select-users }}`.

            * {% include [select-subject-console](../../../_includes/storage/select-subject-console.md) %}

                Вы можете выбрать одновременно нескольких субъектов, для этого выбирайте их поочередно.

        * **{{ ui-key.yacloud.storage.bucket.policy.field_action }}**, для которого создается правило. Вы также можете выбрать опцию **Все действия**.
        * **{{ ui-key.yacloud.storage.bucket.policy.field_resource }}** — по умолчанию указан выбранный бакет. Чтобы добавить другие ресурсы в правило, нажмите кнопку **{{ ui-key.yacloud.storage.bucket.policy.button_add-resource }}**.

            {% note info %}

            {% include [policy-bucket-objects](../../../_includes/storage/policy-bucket-objects.md) %}

            {% endnote %}

     1. При необходимости добавьте [условие](../../s3/api-ref/policy/conditions.md) для правила:
        * Выберите **{{ ui-key.yacloud.storage.bucket.policy.field_key }}** из списка.
        * Выберите **{{ ui-key.yacloud.storage.bucket.policy.field_operator }}** из списка. Чтобы оператор действовал в существующих полях, выберите опцию **{{ ui-key.yacloud.storage.bucket.policy.label_if-exists }}**. Тогда, если поля не существует, условие будет считаться выполненным.
        * Введите **{{ ui-key.yacloud.storage.bucket.policy.field_value }}**.
        * Нажмите кнопку **{{ ui-key.yacloud.storage.bucket.policy.button_add-value }}**, чтобы добавить дополнительное значение в условие.

        {% include [conditions-combining-and](../../../_includes/storage/conditions-combining-and.md) %}

        {% include [conditions-combining-or](../../../_includes/storage/conditions-combining-or.md) %}

  1. При необходимости добавьте правила и настройте их.
  1. Нажмите кнопку **{{ ui-key.yacloud.common.save }}**.

- {{ yandex-cloud }} CLI {#cli}

  {% include [cli-install](../../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../../_includes/default-catalogue.md) %}

  1. Посмотрите описание команды CLI для редактирования ACL бакета:

     ```bash
     yc storage bucket update --help
     ```

  1. Опишите конфигурацию политики доступа в виде [схемы данных](../../s3/api-ref/policy/scheme.md) в формате JSON:

     ```json
     {
       "Version": "2012-10-17",
       "Statement": {
         "Effect": "Allow",
         "Principal": "*",
         "Action": "s3:GetObject",
         "Resource": "arn:aws:s3:::<имя_бакета>/*",
         "Condition": {
           "Bool": {
             "aws:SecureTransport": "true"
           }
         }
       }
     }
     ```

     Где:

     * `Version` — версия описания политик доступа. Необязательный параметр.
     * `Statement` — правила политики доступа:
       * `Effect` — запрет или разрешение запрошенного действия. Возможные значения: `Allow` и `Deny`.
       * `Principal` — идентификатор субъекта запрошенного разрешения. Получателем может быть [пользователь](../../../iam/operations/users/get.md), [сервисный аккаунт](../../../iam/operations/sa/get-id.md) или [группа пользователей](../../../organization/operations/manage-groups.md). Возможные значения: `*`, `<идентификатор_субъекта>`. Необязательный параметр.

         
         Идентификаторы можно получить следующими способами:
         * [Пользователь](../../../iam/operations/users/get.md).
         * [Сервисный аккаунт](../../../iam/operations/sa/get-id.md).
         * Группа пользователей — перейдите на вкладку [**{{ ui-key.yacloud_org.pages.groups }}**]({{ link-org-cloud-center }}/groups) в интерфейсе {{ cloud-center }}.


       * `Action` — [действие](../../s3/api-ref/policy/actions.md), которое будет разрешено при срабатывании политики. Возможные значения: `s3:GetObject`, `s3:PutObject` и `*` если необходимо применять политику ко всем действиям.
       * `Resource` — ресурс, к которому будет применяться правило.
       * `Condition` — [условие](../../s3/api-ref/policy/conditions.md), которое будет проверяться. Необязательный параметр.

         {% include [conditions-combining-and](../../../_includes/storage/conditions-combining-and.md) %}

         {% include [conditions-combining-or](../../../_includes/storage/conditions-combining-or.md) %}

  1. Выполните команду:

     ```bash
     yc storage bucket update \
       --name <имя_бакета> \
       --policy-from-file <путь_к_файлу_с_политиками>
     ```

     Результат:

     ```text
     name: my-bucket
     folder_id: csgeoelk7fl1********
     default_storage_class: STANDARD
     versioning: VERSIONING_SUSPENDED
     max_size: "10737418240"
     policy:
         Statement:
           Action: s3:GetObject
           Condition:
             Bool:
               aws:SecureTransport: "true"
             Effect: Allow
             Principal: '*'
             Resource: arn:aws:s3:::my-bucket
           Version: "2012-10-17"
     acl: {}
     created_at: "2022-12-14T08:42:16.273717Z"
     ```

- AWS CLI {#aws-cli}

  {% note info %}

  Для управления политикой доступа с помощью AWS CLI сервисному аккаунту должна быть назначена [роль](../../security/index.md#storage-admin) `storage.admin`.

  {% endnote %}

  Если у вас еще нет AWS CLI, [установите и сконфигурируйте его](../../tools/aws-cli.md).

  1. Опишите конфигурацию политики доступа в виде [схемы данных](../../s3/api-ref/policy/scheme.md) формата JSON:

     ```json
     {
       "Version": "2012-10-17",
       "Statement": {
         "Effect": "Allow",
         "Principal": "*",
         "Action": "s3:GetObject",
         "Resource": "arn:aws:s3:::<имя_бакета>/*",
         "Condition": {
           "Bool": {
             "aws:SecureTransport": "true"
           }
         }
       }
     }
     ```

     Где:

     * `Version` — версия описания политик доступа. Необязательный параметр.
     * `Statement` — правила политики доступа:
       * `Effect` — запрет или разрешение запрошенного действия. Возможные значения: `Allow` и `Deny`.
       * `Principal` — идентификатор субъекта запрошенного разрешения. Получателем может быть [пользователь](../../../iam/operations/users/get.md), [сервисный аккаунт](../../../iam/operations/sa/get-id.md) или [группа пользователей](../../../organization/operations/manage-groups.md). Возможные значения: `*`, `<идентификатор_субъекта>`. Необязательный параметр.

         
         Идентификаторы можно получить следующими способами:
         * [Пользователь](../../../iam/operations/users/get.md).
         * [Сервисный аккаунт](../../../iam/operations/sa/get-id.md).
         * Группа пользователей — перейдите на вкладку [**{{ ui-key.yacloud_org.pages.groups }}**]({{ link-org-cloud-center }}/groups) в интерфейсе {{ cloud-center }}.


       * `Action` — [действие](../../s3/api-ref/policy/actions.md), которое будет разрешено при срабатывании политики. Возможные значения: `s3:GetObject`, `s3:PutObject` и `*` если необходимо применять политику ко всем действиям.
       * `Resource` — ресурс, к которому будет применяться правило.
       * `Condition` — [условие](../../s3/api-ref/policy/conditions.md), которое будет проверяться. Необязательный параметр.

         {% include [conditions-combining-and](../../../_includes/storage/conditions-combining-and.md) %}

         {% include [conditions-combining-or](../../../_includes/storage/conditions-combining-or.md) %}

     Сохраните готовую конфигурацию в файле `policy.json`.
  1. Выполните команду:

     ```bash
     aws s3api put-bucket-policy \
       --endpoint https://{{ s3-storage-host }} \
       --bucket <имя_бакета> \
       --policy file://policy.json
     ```

- {{ TF }} {#tf}

  {% include [terraform-role](../../../_includes/storage/terraform-role.md) %}

  {% include [terraform-install](../../../_includes/terraform-install.md) %}

  Получите [статические ключи доступа](../../../iam/operations/authentication/manage-access-keys.md#create-access-key) — секретный ключ и идентификатор ключа, используемые для аутентификации в {{ objstorage-name }}.

  {% include [terraform-iamtoken-note](../../../_includes/storage/terraform-iamtoken-note.md) %}

  1. Опишите в конфигурационном файле параметры ресурсов, которые необходимо создать:

     ```hcl

     resource "yandex_iam_service_account" "sa" {
       name = "<имя_сервисного_аккаунта>"
     }

     // Назначение роли сервисному аккаунту
     resource "yandex_resourcemanager_folder_iam_member" "sa-admin" {
       folder_id = "<идентификатор_каталога>"
       role      = "storage.admin"
       member    = "serviceAccount:${yandex_iam_service_account.sa.id}"
     }

     // Создание статического ключа доступа
     resource "yandex_iam_service_account_static_access_key" "sa-static-key" {
       service_account_id = yandex_iam_service_account.sa.id
       description        = "static access key for object storage"
     }

     resource "yandex_storage_bucket" "b" {
       access_key = "yandex_iam_service_account_static_access_key.sa-static-key.access_key"
       secret_key = "yandex_iam_service_account_static_access_key.sa-static-key.secret_key"
       bucket     = "my-policy-bucket"
       policy     = <<POLICY
      {
       "Version": "2012-10-17",
       "Statement": [
         {
           "Effect": "Allow",
           "Principal": "*",
           "Action": "s3:*",
           "Resource": [
             "arn:aws:s3:::my-policy-bucket/*",
             "arn:aws:s3:::my-policy-bucket"
           ]
         },
         {
           "Effect": "Deny",
           "Principal": "*",
           "Action": "s3:PutObject",
           "Resource": [
             "arn:aws:s3:::my-policy-bucket/*",
             "arn:aws:s3:::my-policy-bucket"
           ]
         }
       ]
     }
     POLICY
     }
     ```

     Где:

     * `access_key` — идентификатор статического ключа доступа.
     * `secret_key` — значение секретного ключа доступа.
     * `bucket` — имя бакета. Обязательный параметр.
     * `policy` — имя политики. Обязательный параметр.

     Настройки политики:

     * `Version` — версия описания политик доступа. Необязательный параметр.
     * `Statement` — правила политики доступа:
       * `Effect` — запрет или разрешение запрошенного действия. Возможные значения: `Allow` и `Deny`.
       * `Principal` — идентификатор субъекта запрошенного разрешения. Получателем может быть [пользователь](../../../iam/operations/users/get.md), [сервисный аккаунт](../../../iam/operations/sa/get-id.md) или [группа пользователей](../../../organization/operations/manage-groups.md). Возможные значения: `*`, `<идентификатор_субъекта>`. Необязательный параметр.

         
         Идентификаторы можно получить следующими способами:
         * [Пользователь](../../../iam/operations/users/get.md).
         * [Сервисный аккаунт](../../../iam/operations/sa/get-id.md).
         * Группа пользователей — перейдите на вкладку [**{{ ui-key.yacloud_org.pages.groups }}**]({{ link-org-cloud-center }}/groups) в интерфейсе {{ cloud-center }}.


       * `Action` — [действие](../../s3/api-ref/policy/actions.md), которое будет разрешено при срабатывании политики. Возможные значения: `s3:GetObject`, `s3:PutObject` и `*` если необходимо применять политику ко всем действиям.
       * `Resource` — ресурс, к которому будет применяться правило.
       * `Condition` — [условие](../../s3/api-ref/policy/conditions.md), которое будет проверяться. Необязательный параметр.

         {% include [conditions-combining-and](../../../_includes/storage/conditions-combining-and.md) %}

         {% include [conditions-combining-or](../../../_includes/storage/conditions-combining-or.md) %}

     Более подробную информацию о ресурсах, которые вы можете создать с помощью {{ TF }}, см. в [документации провайдера]({{ tf-provider-link }}/).
  1. Проверьте корректность конфигурационных файлов.
     1. В командной строке перейдите в папку, где вы создали конфигурационный файл.
     1. Выполните проверку с помощью команды:

        ```bash
        terraform plan
        ```

     Если конфигурация описана верно, в терминале отобразится список создаваемых ресурсов и их параметров. Если в конфигурации есть ошибки, {{ TF }} на них укажет.
  1. Разверните облачные ресурсы.
     1. Если в конфигурации нет ошибок, выполните команду:

        ```bash
        terraform apply
        ```

     1. Подтвердите создание ресурсов.

     После этого в указанном каталоге будут созданы все требуемые ресурсы. Проверить появление ресурсов и их настройки можно в [консоли управления]({{ link-console-main }}).

- API {#api}

  Чтобы управлять политикой доступа, воспользуйтесь методом REST API [update](../../api-ref/Bucket/update.md) для ресурса [Bucket](../../api-ref/Bucket/index.md), вызовом gRPC API [BucketService/Update](../../api-ref/grpc/Bucket/update.md) или методом S3 API [PutBucketPolicy](../../s3/api-ref/policy/put.md). Если ранее для бакета уже была настроена политика доступа, то после применения новой политики она будет полностью перезаписана.

{% endlist %}

{% include [storage-note-empty-policy](../../_includes_service/storage-note-empty-policy.md) %}

## Просмотреть политику {#view-policy}

Минимально необходимая роль для просмотра политики доступа — `storage.configViewer`. См. [описание роли](../../../storage/security/#storage-config-viewer).

Чтобы просмотреть примененную к бакету политику доступа:

{% list tabs group=instructions %}

- Консоль управления {#console}

  1. В [консоли управления]({{ link-console-main }}) в списке сервисов выберите **{{ ui-key.yacloud.iam.folder.dashboard.label_storage }}**.
  1. Выберите бакет в списке.
  1. В меню слева выберите **{{ ui-key.yacloud.storage.bucket.switch_security }}** и перейдите на вкладку **{{ ui-key.yacloud.storage.bucket.switch_policy }}**.

- AWS CLI {#aws-cli}

  Выполните следующую команду:

  ```bash
  aws --endpoint https://{{ s3-storage-host }} s3api get-bucket-policy \
    --bucket <имя_бакета> \
    --output text
  ```

  Результат:

  ```json
  {
    "Policy": "{\"Version\":\"2012-10-17\",\"Statement\":{\"Effect\":\"Allow\",\"Principal\":\"*\",\"Action\":\"s3:GetObject\",\"Resource\":\"arn:aws:s3:::<имя_бакета>/*\",\"Condition\":{\"Bool\":{\"aws:SecureTransport\":\"true\"}}}}"
  }
  ```

  Подробнее о параметрах читайте в описании [схемы данных](../../s3/api-ref/policy/scheme.md).

- API {#api}

  Воспользуйтесь методом S3 API [GetBucketPolicy](../../s3/api-ref/policy/get.md).

{% endlist %}

## Удалить политику {#delete-policy}

Минимально необходимая роль для удаления политики доступа — `storage.configurer`. См. [описание роли](../../../storage/security/#storage-configurer).

Чтобы удалить политику доступа:

{% list tabs group=instructions %}

- Консоль управления {#console}

  1. В [консоли управления]({{ link-console-main }}) в списке сервисов выберите **{{ ui-key.yacloud.iam.folder.dashboard.label_storage }}**.
  1. Выберите бакет в списке.
  1. В меню слева выберите **{{ ui-key.yacloud.storage.bucket.switch_security }}** и перейдите на вкладку **{{ ui-key.yacloud.storage.bucket.switch_policy }}**.
  1. Нажмите значок ![options](../../../_assets/console-icons/ellipsis.svg) и выберите **{{ ui-key.yacloud.storage.bucket.policy.button_policy-delete }}**.
  1. Нажмите кнопку **{{ ui-key.yacloud.common.delete }}**.

- AWS CLI {#aws-cli}

  Выполните следующую команду:

  ```bash
  aws --endpoint https://{{ s3-storage-host }} s3api delete-bucket-policy \
    --bucket <имя_бакета>
  ```

- {{ TF }} {#tf}

  {% include [terraform-install](../../../_includes/terraform-install.md) %}

  Если вы применили политику доступа к бакету при помощи {{ TF }}, вы можете удалить ее:
  1. Найдите в конфигурационном файле параметры созданной ранее политики доступа, которую необходимо удалить:

     ```hcl
     resource "yandex_storage_bucket" "b" {
       bucket = "my-policy-bucket"
       policy = <<POLICY
      {
       "Version": "2012-10-17",
       "Statement": [
         {
           "Effect": "Allow",
           "Principal": "*",
           "Action": "s3:*",
           "Resource": [
             "arn:aws:s3:::my-policy-bucket/*",
             "arn:aws:s3:::my-policy-bucket"
           ]
         },
         {
           "Effect": "Deny",
           "Principal": "*",
           "Action": "s3:PutObject",
           "Resource": [
             "arn:aws:s3:::my-policy-bucket/*",
             "arn:aws:s3:::my-policy-bucket"
           ]
         }
       ]
     }
     POLICY
     }
     ```

  1. Удалите поле `policy` с описанием параметров политики доступа из конфигурационного файла.
  1. Проверьте корректность конфигурационных файлов.
     1. В командной строке перейдите в папку, где вы редактировали конфигурационный файл.
     1. Выполните проверку с помощью команды:

        ```bash
        terraform plan
        ```

     Если конфигурация описана верно, в терминале отобразится список создаваемых ресурсов и их параметров без удаляемого описания политики доступа. Если в конфигурации есть ошибки, {{ TF }} на них укажет.
  1. Удалите политику доступа.
     1. Если в конфигурации нет ошибок, выполните команду:

        ```bash
        terraform apply
        ```

     1. Введите слово `yes` и нажмите **Enter**.

     После этого в указанном каталоге будет удалена политика доступа к бакету. Проверить отсутствие политики доступа можно в [консоли управления]({{ link-console-main }}).

- API {#api}

  Воспользуйтесь методом S3 API [DeleteBucketPolicy](../../s3/api-ref/policy/delete.md).

{% endlist %}

## См. также {#see-also}

* [{#T}](../../concepts/policy.md#config-examples)