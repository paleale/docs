---
title: Как настроить эндпоинт-приемник {{ KF }} в {{ data-transfer-full-name }}
description: Из статьи вы узнаете, как задать настройки при создании или изменении эндпоинта-приемника {{ KF }} в {{ data-transfer-full-name }}.
---

# Передача данных в эндпоинт-приемник {{ KF }}

С помощью сервиса {{ data-transfer-full-name }} вы можете переносить данные в очередь {{ KF }} и реализовывать различные сценарии обработки и трансформации данных. Для реализации трансфера:

1. [Ознакомьтесь с возможными сценариями передачи данных](#scenarios).
1. [Настройте один из поддерживаемых источников данных](#supported-sources).
1. [Настройте эндпоинт-приемник](#endpoint-settings) в {{ data-transfer-full-name }}.
1. [Создайте](../../transfer.md#create) и [запустите](../../transfer.md#activate) трансфер.
1. Выполняйте необходимые действия по работе с базой и [контролируйте трансфер](../../monitoring.md).
1. При возникновении проблем, [воспользуйтесь готовыми решениями](../../../../data-transfer/troubleshooting/index.md) по их устранению.

## Сценарии передачи данных в {{ KF }} {#scenarios}

1. {% include [migration](../../../../_includes/data-transfer/scenario-captions/migration.md) %}

    Отдельной задачей миграции является зеркалирование данных между очередями:
    * [Зеркалирование {{ KF }}](../../../tutorials/mkf-to-mkf.md)

1. {% include [cdc](../../../../_includes/data-transfer/scenario-captions/cdc.md) %}
  
    * [Захват изменений из {{ MY }} и поставка в {{ KF }}](../../../tutorials/cdc-mmy.md);
    * [Захват изменений {{ ydb-short-name }} и поставка в {{ KF }}](../../../tutorials/cdc-ydb.md);
    * [Захват изменений из {{ PG }} и поставка в {{ KF }}](../../../tutorials/cdc-mpg.md).

1. {% include [queue](../../../../_includes/data-transfer/scenario-captions/queue.md) %}
    * [Поставка данных из очереди {{ DS }} в {{ KF }}](../../../tutorials/yds-to-kafka.md)

Подробное описание возможных сценариев передачи данных в {{ data-transfer-full-name }} см. в разделе [Практические руководства](../../../tutorials/index.md).

## Настройка источника данных {#supported-sources}

Настройте один из поддерживаемых источников данных:

* [{{ PG }}](../source/postgresql.md);
* [{{ MY }}](../source/mysql.md);
* [{{ KF }}](../source/kafka.md);
* [{{ AB }}](../../../transfer-matrix.md#airbyte);
* [{{ DS }}](../source/data-streams.md);
* [{{ ydb-name }}](../source/ydb.md);
* [{{ ES }}](../source/elasticsearch.md);
* [{{ OS }}](../source/opensearch.md).

Полный список поддерживаемых источников и приемников в {{ data-transfer-full-name }} см. в разделе [Доступные трансферы](../../../transfer-matrix.md).

## Настройка эндпоинта-приемника {{ KF }} {#endpoint-settings}

При [создании](../index.md#create) или [изменении](../index.md#update) эндпоинта вы можете задать:

* Настройки подключения к [кластеру {{ mkf-full-name }}](#managed-service) или [пользовательской инсталляции](#on-premise) и [настройки сериализации](#serializer), в т. ч. на базе виртуальных машин {{ compute-full-name }}. Это обязательные параметры.
* [Настройки топика Apache Kafka](#kafka-settings).

### Кластер {{ mkf-name }} {#managed-service}


{% note warning %}

Для создания или редактирования эндпоинта управляемой базы данных вам потребуется [роль `{{ roles.mkf.viewer }}`](../../../../managed-kafka/security/index.md#mkf-viewer) или примитивная [роль `viewer`](../../../../iam/roles-reference.md#viewer), выданная на каталог кластера этой управляемой базы данных.

{% endnote %}


Подключение с указанием идентификатора кластера в {{ yandex-cloud }}.

{% list tabs group=instructions %}

- Консоль управления {#console}

    {% include [Managed Kafka UI](../../../../_includes/data-transfer/necessary-settings/ui/managed-kafka-target.md) %}

- {{ TF }} {#tf}

    * Тип эндпоинта — `kafka_target`.

    {% include [Managed Kafka Terraform](../../../../_includes/data-transfer/necessary-settings/terraform/managed-kafka-target.md) %}

    Пример структуры конфигурационного файла:

    
    ```hcl
    resource "yandex_datatransfer_endpoint" "<имя_эндпоинта_в_{{ TF }}>" {
      name = "<имя_эндпоинта>"
      settings {
        kafka_target {
          security_groups = ["<список_идентификаторов_групп_безопасности>"]
          connection {
            cluster_id = "<идентификатор_кластера>"
          }
          auth {
            <метод_аутентификации>
          }
          <настройки_топика>
          <настройки_сериализации>
        }
      }
    }
    ```


    Подробнее см. в [документации провайдера {{ TF }}]({{ tf-provider-dt-endpoint }}).

- API {#api}

    {% include [Managed Kafka API](../../../../_includes/data-transfer/necessary-settings/api/managed-kafka-target.md) %}

{% endlist %}

### Пользовательская инсталляция {#on-premise}

Подключение к кластеру {{ KF }} с явным указанием сетевых адресов и портов хостов-брокеров.

{% list tabs group=instructions %}

- Консоль управления {#console}

    {% include [On premise Kafka UI](../../../../_includes/data-transfer/necessary-settings/ui/on-premise-kafka-target.md) %}

- {{ TF }} {#tf}

    * Тип эндпоинта — `kafka_target`.

    {% include [On-premise Kafka Terraform](../../../../_includes/data-transfer/necessary-settings/terraform/on-premise-kafka-target.md) %}

    Пример структуры конфигурационного файла:

    
    ```hcl
    resource "yandex_datatransfer_endpoint" "<имя_эндпоинта_в_{{ TF }}>" {
      name = "<имя_эндпоинта>"
      settings {
        kafka_target {
          security_groups = ["<список_идентификаторов_групп_безопасности>"]
          connection {
            on_premise {
              broker_urls = ["<список_IP-адресов_или_FQDN_хостов-брокеров>"]
              subnet_id  = "<идентификатор_подсети_c_хостами-брокерами>"
            }
          }
          auth = {
            <метод_аутентификации>
          }
          <настройки_топика>
          <настройки_сериализации>
        }
      }
    }
    ```


    Подробнее см. в [документации провайдера {{ TF }}]({{ tf-provider-dt-endpoint }}).

- API {#api}

    {% include [On-premise Kafka API](../../../../_includes/data-transfer/necessary-settings/api/on-premise-kafka-target.md) %}

{% endlist %}

### Настройки топика {{ KF }} {#kafka-settings}

{% list tabs group=instructions %}

- Консоль управления {#console}

    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.kafka.console.form.kafka.KafkaTargetConnection.topic_settings.title }}**:

        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.kafka.console.form.kafka.KafkaTargetTopic.topic_name.title }}** — укажите имя топика, в который будут отправляться сообщения. Выберите **{{ ui-key.yc-data-transfer.data-transfer.console.form.kafka.console.form.kafka.KafkaTargetTopic.save_tx_order.title }}**, чтобы не разбивать поток событий на независимые очереди по таблицам.

        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.kafka.console.form.kafka.KafkaTargetTopicSettings.topic_prefix.title }}** — укажите префикс топика, аналог настройки `Debezium database.server.name`. Сообщения будут отправляться в топик с именем `<префикс_топика>.<схема>.<имя_таблицы>`.

- {{ TF }} {#tf}

    Укажите в блоке `topic_settings` одну из опций отправки сообщений в топик:

    * `topic` — укажите параметры в этом блоке, чтобы отправлять все сообщения в один топик:
        * `topic_name` — имя топика, в который будут отправляться сообщения.
        * `save_tx_order` — опция, позволяющая сохранять порядок транзакций. Укажите значение `true`, чтобы не разбивать поток событий на независимые очереди по таблицам.

    * `topic_prefix` — укажите префикс, чтобы отправлять сообщения в разные топики с заданным префиксом.

        {% include [kafka-topic-prefix-explanation](../../../../_includes/data-transfer/kafka-topic-prefix-explanation.md) %}

- API {#api}

    Укажите в поле `topicSettings` одну из опций отправки сообщений в топик:

    * `topic` — укажите параметры в этом поле, чтобы отправлять все сообщения в один топик:
        * `topicName` — имя топика, в который будут отправляться сообщения.
        * `saveTxOrder` — опция, позволяющая сохранять порядок транзакций. Укажите значение `true`, чтобы не разбивать поток событий на независимые очереди по таблицам.

    * `topicPrefix` — укажите префикс, чтобы отправлять сообщения в разные топики с заданным префиксом.

        {% include [kafka-topic-prefix-explanation](../../../../_includes/data-transfer/kafka-topic-prefix-explanation.md) %}

{% endlist %}

{{ data-transfer-full-name }} поддерживает CDC-режим для трансферов из баз данных {{ PG }}, {{ MY }} и {{ ydb-short-name }} в {{ KF }} и {{ yds-full-name }}. При этом данные в приемник попадают в формате Debezium. Подробнее о CDC-режиме см. в разделе [Захват изменения данных](../../../concepts/cdc.md).

{% include [CDC-YDB](../../../../_includes/data-transfer/note-ydb-cdc.md) %}

### Настройки сериализации {#serializer}

{% list tabs group=instructions %}

- Консоль управления {#console}

    {% include [serializer](../../../../_includes/data-transfer/serializer.md) %}

- {{ TF }} {#tf}

    {% include [serializer](../../../../_includes/data-transfer/serializers/terraform.md)  %}

- API {#api}

    {% include [serializer](../../../../_includes/data-transfer/serializers/api.md)  %}

{% endlist %}

### Дополнительные настройки {#additional-settings}

{% list tabs group=instructions %}

- Консоль управления {#console}

    Вы можете указать [параметры конфигурации топика](https://docs.confluent.io/platform/current/installation/configuration/topic-configs.html), которые будут применяться при создании новых топиков.

    Укажите параметр и одно из его допустимых значений: например, `cleanup.policy` и `compact`.

{% endlist %}

После настройки источника и приемника данных [создайте и запустите трансфер](../../transfer.md#create).
