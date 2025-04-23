# Поставка данных из {{ mpg-full-name }} в {{ mkf-full-name }} с помощью {{ data-transfer-full-name }}

Вы можете отслеживать изменения данных в _кластере-источнике_ {{ mpg-name }} и отправлять их в _кластер-приемник_ {{ mkf-name }} с помощью технологии Change Data Capture (CDC).

Чтобы настроить CDC с использованием сервиса {{ data-transfer-name }}:

1. [Подготовьте кластер-источник](#prepare-source).
1. [Подготовьте кластер-приемник](#prepare-target).
1. [Подготовьте и активируйте трансфер](#prepare-transfer).
1. [Проверьте работоспособность трансфера](#verify-transfer).

Если созданные ресурсы вам больше не нужны, [удалите их](#clear-out).


## Необходимые платные ресурсы {#paid-resources}

В стоимость поддержки описываемого решения входят:

* Плата за кластер {{ mpg-name }}: использование вычислительных ресурсов, выделенных хостам, и дискового пространства (см. [тарифы {{ mpg-name }}](../../managed-postgresql/pricing.md)).
* Плата за кластер {{ mkf-name }}: использование вычислительных ресурсов, выделенных хостам (в том числе хостам ZooKeeper), и дискового пространства (см. [тарифы {{ KF }}](../../managed-kafka/pricing.md)).
* Плата за использование публичных IP-адресов для хостов кластеров (см. [тарифы {{ vpc-name }}](../../vpc/pricing.md)).
* Плата за трансфер: использование вычислительных ресурсов и количество переданных строк данных (см. [тарифы {{ data-transfer-name }}](../../data-transfer/pricing.md)).


## Перед началом работы {#before-you-begin}

1. [Создайте кластер-источник {{ mpg-name }}](../../managed-postgresql/operations/cluster-create.md) любой подходящей конфигурации со следующими настройками:

    * с базой данных `db1`;
    * с пользователем `pg-user`;
    * с хостами в публичном доступе.

1. [Создайте кластер-приемник {{ mkf-name }}](../../managed-kafka/operations/cluster-create.md) любой подходящей конфигурации с хостами в публичном доступе.


1. Если вы используете группы безопасности, настройте их так, чтобы к кластерам можно было подключаться из интернета:

    * [Инструкция для {{ mpg-name }}](../../managed-postgresql/operations/connect.md#configuring-security-groups).
    * [Инструкция для {{ mkf-name }}](../../managed-kafka/operations/connect/index.md#configuring-security-groups).


1. Установите на локальный компьютер [утилиту](https://github.com/edenhill/kcat) `kcat` (`kafkacat`) и [клиент командной строки PostgreSQL](https://www.postgresql.org/download/). Например, в Ubuntu 20.04 выполните команду:

    ```bash
    sudo apt update && sudo apt install kafkacat postgresql-client --yes
    ```

    Убедитесь, что можете с ее помощью [подключиться к кластеру-источнику {{ mkf-name }} через SSL](../../managed-kafka/operations/connect/clients.md#bash-zsh).

## Подготовьте кластер-источник {#prepare-source}

1. Чтобы сервис {{ data-transfer-name }} мог получать от кластера {{ mpg-name }} уведомления об изменениях в данных, в кластере-источнике необходимо создать публикацию (publication). Чтобы пользователь `pg-user` мог создать публикацию, [назначьте](../../managed-postgresql/operations/grant.md) ему роль `mdb_replication`.

1. [Подключитесь к базе данных](../../managed-postgresql/operations/connect.md) `db1` от имени пользователя `pg-user`.

1. Наполните базу тестовыми данными. В качестве примера используется простая таблица, содержащая информацию с некоторых датчиков автомобиля.

    Создайте таблицу:

    ```sql
    CREATE TABLE public.measurements (
        "device_id" text PRIMARY KEY NOT NULL,
        "datetime" timestamp NOT NULL,
        "latitude" real NOT NULL,
        "longitude" real NOT NULL,
        "altitude" real NOT NULL,
        "speed" real NOT NULL,
        "battery_voltage" real,
        "cabin_temperature" real NOT NULL,
        "fuel_level" real
    );
    ```

    Наполните таблицу данными:

    ```sql
    INSERT INTO public.measurements VALUES
        ('iv9a94th6rzt********', '2020-06-05 17:27:00', 55.70329032, 37.65472196,  427.5,    0, 23.5, 17, NULL),
        ('rhibbh3y08qm********', '2020-06-06 09:49:54', 55.71294467, 37.66542005, 429.13, 55.5, NULL, 18, 32),
        ('iv9a94th678t********', '2020-06-07 15:00:10', 55.70985913, 37.62141918,  417.0, 15.7, 10.3, 17, NULL);
    ```

## Подготовьте кластер-приемник {#prepare-target}

Настройки различаются в зависимости от используемого [способа управления топиками](../../managed-kafka/concepts/topics.md#management). При этом имена топиков для данных конструируются по тому же принципу, что и в [Debezium](https://debezium.io/documentation/reference/connectors/postgresql.html#postgresql-topic-names) — `<префикс_топика>.<имя_схемы>.<имя_таблицы>`. В этом руководстве в качестве примера будет использоваться префикс `cdc`.

{% list tabs group=topic_management %}

- Интерфейсы {{ yandex-cloud }} {#yc}

    Если управление топиками осуществляется с помощью стандартных интерфейсов {{ yandex-cloud }} (Консоль управления, CLI, {{ TF }}, API):

    1. [Создайте топик](../../managed-kafka/operations/cluster-topics.md#create-topic) с именем `cdc.public.measurements`.

        Если необходимо отслеживать изменения в нескольких таблицах, создайте для каждой из них отдельный топик.

    1. [Создайте пользователя](../../managed-kafka/operations/cluster-accounts.md#create-account) с именем `kafka-user` и ролями `ACCESS_ROLE_CONSUMER` и `ACCESS_ROLE_PRODUCER`, действующими на созданные топики.

- Admin API {#api}

    Если для управления топиками используется Kafka Admin API:

    1. Создайте [пользователя-администратора](../../managed-kafka/operations/cluster-accounts.md) с именем `kafka-user`.

    1. Помимо роли `ACCESS_ROLE_ADMIN` назначьте пользователю-администратору роли `ACCESS_ROLE_CONSUMER` и `ACCESS_ROLE_PRODUCER` на топики, имена которых начинаются с префикса `cdc`.

        Необходимые топики будут созданы автоматически при первом событии изменения в отслеживаемых таблицах кластера-источника. Такое решение может быть удобным для отслеживания изменений во множестве таблиц, однако, требует запас свободного места в хранилище кластера. Подробнее см. в разделе [{#T}](../../managed-kafka/concepts/storage.md).

{% endlist %}


## Подготовьте и активируйте трансфер {#prepare-transfer}

1. [Создайте эндпоинты](../../data-transfer/operations/endpoint/index.md#create).

    * Эндпоинт для источника:

        * **{{ ui-key.yacloud.data-transfer.forms.label-database_type }}** — `PostgreSQL`.
        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.postgres.console.form.postgres.PostgresSource.title }}**:
            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.postgres.console.form.postgres.PostgresSource.connection.title }}** — `{{ ui-key.yc-data-transfer.data-transfer.console.form.postgres.console.form.postgres.PostgresConnectionType.mdb_cluster_id.title }}`.
            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.postgres.console.form.postgres.PostgresConnectionType.mdb_cluster_id.title }}** — выберите [созданный ранее](#before-you-begin) кластер {{ mpg-name }}.
            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.Connection.database.title }}** — `db1`.
            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.Connection.user.title }}** — `pg-user`.
            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.Connection.password.title }}** — укажите пароль пользователя `pg-user`.
            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.postgres.console.form.postgres.PostgresTableFilter.include_tables.title }}** — `public.measurements`.

    * Эндпоинт для приемника:

        * **{{ ui-key.yacloud.data-transfer.forms.label-database_type }}** — `Kafka`.
        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.kafka.console.form.kafka.KafkaTarget.title }}**:
            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.kafka.console.form.kafka.KafkaTargetConnection.connection_type.title }}** — `{{ ui-key.yc-data-transfer.data-transfer.console.form.kafka.console.form.kafka.KafkaConnectionType.managed.title }}`.
                * **{{ ui-key.yc-data-transfer.data-transfer.console.form.kafka.console.form.kafka.ManagedKafka.cluster_id.title }}** — выберите кластер-приемник.
                * **{{ ui-key.yc-data-transfer.data-transfer.console.form.kafka.console.form.kafka.ManagedKafka.auth.title }}** — укажите данные созданного ранее пользователя `kafka-user`.

            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.kafka.console.form.kafka.KafkaTargetConnection.topic_settings.title }}** — `{{ ui-key.yc-data-transfer.data-transfer.console.form.kafka.console.form.kafka.KafkaSourceConnection.topic_name.title }}`.
            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.kafka.console.form.kafka.KafkaTargetTopicSettings.topic.title }}** — `cdc.public.measurements`.

            Если необходимо отслеживать изменения в нескольких таблицах, заполните поля следующим образом:

            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.kafka.console.form.kafka.KafkaTargetConnection.topic_settings.title }}** — `{{ ui-key.yc-data-transfer.data-transfer.console.form.kafka.console.form.kafka.KafkaTargetTopicSettings.topic_prefix.title }}`.
            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.kafka.console.form.kafka.KafkaTargetTopicSettings.topic_prefix.title }}** — укажите префикс `cdc`, использованный при формировании имен топиков.

1. [Создайте трансфер](../../data-transfer/operations/transfer.md#create) со следующими настройками:

    * **{{ ui-key.yacloud.data-transfer.label_endpoints }}**:
        * **{{ ui-key.yacloud.data-transfer.forms.label_source-type }}** — созданный ранее эндпоинт для источника.
        * **{{ ui-key.yacloud.data-transfer.forms.label_target-type }}** — созданный ранее эндпоинт для приемника.
    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.transfer.console.form.transfer.Transfer.type.title }}** — **{{ ui-key.yc-data-transfer.data-transfer.console.form.transfer.console.form.transfer.TransferType.increment.title }}**.

1. [Активируйте трансфер](../../data-transfer/operations/transfer.md#activate) и дождитесь его перехода в статус **{{ ui-key.yacloud.data-transfer.label_connector-status-RUNNING }}**.

## Проверьте работоспособность трансфера {#verify-transfer}

1. В отдельном терминале запустите утилиту `kafkacat` в режиме потребителя:

    ```bash
    kafkacat \
        -C \
        -b <FQDN_хоста-брокера_1>:9091,...,<FQDN_хоста-брокера_N>:9091 \
        -t cdc.public.measurements \
        -X security.protocol=SASL_SSL \
        -X sasl.mechanisms=SCRAM-SHA-512 \
        -X sasl.username=kafka-user \
        -X sasl.password=<пароль> \
        -X ssl.ca.location={{ crt-local-dir }}{{ crt-local-file }} \
        -Z \
        -K:
    ```

    FQDN хостов-брокеров можно получить со [списком хостов в кластере {{ mkf-name }}](../../managed-kafka/operations/cluster-hosts.md).

    Будет выведена схема формата данных таблицы `public.measurements` и данные о добавленных в нее ранее строках.

    {% cut "Пример фрагмента сообщения" %}

    ```json
    {
      "payload": {
        "consumer":"dttuhfpp97l3********"
      },
      "schema": {
        "fields": [
          {
            "field": "consumer",
            "optional":false,
            "type":"string"
          }
        ],
        "name": "__data_transfer_stub.public.__consumer_keeper.Key",
        "optional":false,
        "type":"struct"
      }
    }:{
      "payload": {
        "after": {
          "consumer":"dttuhfpp97l3********l",
          "locked_by":"dttuhfpp97l3********-1",
          "locked_till":"2022-05-15T09:55:18Z"
        },
      "before": null,
      "op":"u",
      "source": {
        "connector":"postgresql",
        "db":"db1",
        "lsn":85865797008,
        "name":"__data_transfer_stub",
        "schema":"public",
        "snapshot":"false",
        "table":"__consumer_keeper",
        "ts_ms":1652608518883,
        "txId":245165,
        "version":"1.1.2.Final",
        "xmin":null
      },
    ...
    ```

    {% endcut %}

1. Подключитесь к кластеру-источнику и добавьте данные в таблицу `measurements`:

    ```sql
    INSERT INTO public.measurements VALUES ('iv7b74th678t********', '2020-06-08 17:45:00', 53.70987913, 36.62549834, 378.0, 20.5, 5.3, 20, NULL);
    ```

1. Убедитесь, что в терминале с запущенной утилитой `kafkacat` отобразились сведения о добавленной строке.

## Удалите созданные ресурсы {#clear-out}

Некоторые ресурсы платные. Чтобы за них не списывалась плата, удалите ресурсы, которые вы больше не будете использовать:

1. [Деактивируйте](../../data-transfer/operations/transfer.md#deactivate) и [удалите](../../data-transfer/operations/transfer.md#delete) трансфер.

1. [Удалите эндпоинты](../../data-transfer/operations/endpoint/index.md#delete).

1. Удалите кластеры:

    * [{{ mkf-name }}](../../managed-kafka/operations/cluster-delete.md).
    * [{{ mpg-name }}](../../managed-postgresql/operations/cluster-delete.md).

1. Если для доступа к хостам кластеров использовались статические публичные IP-адреса, освободите и [удалите](../../vpc/operations/address-delete.md) их.


## Дополнительные материалы {#video}

Больше информации о сценариях поставок данных в вебинаре {{ yandex-cloud }}:


<iframe width="640" height="360" src="https://runtime.strm.yandex.ru/player/video/vplvkntkhjbfsn2c7ptv?autoplay=0&mute=0" allow="autoplay; fullscreen; picture-in-picture; encrypted-media" frameborder="0" scrolling="no"></iframe>

[Смотреть видео на YouTube](https://www.youtube.com/watch?v=bzWmmPp6KFg).


