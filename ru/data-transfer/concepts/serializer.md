---
title: Сериализация в {{ data-transfer-full-name }}
description: Из статьи вы узнаете, что такое сериализация и какие типы сериализации можно использовать в зависимости от поставки.
---

# Сериализация

Сериализация — процесс преобразования объекта данных в битовую последовательность во время передачи в приемники, которые работают с <q>сырыми</q> данными. К таким приемникам относятся:

* [{{ objstorage-name }}](#serializer-s3)
* [Очереди сообщений {{ KF }} и {{ yds-full-name }}](#serializer-message-queue)

Сериализацию можно настроить во время [создания](../operations/endpoint/index.md#create) или [изменения](../operations/endpoint/index.md#update) эндпоинта-приемника.

## Сериализация при поставке в {{ objstorage-name }} {#serializer-s3}

При поставке в {{ objstorage-name }} вы можете выбрать **{{ ui-key.yc-data-transfer.data-transfer.console.form.object_storage.console.form.object_storage.ObjectStorageTarget.output_format.title }}**: `{{ ui-key.yc-data-transfer.data-transfer.console.form.object_storage.console.form.object_storage.ObjectStorageSerializationFormatUI.OBJECT_STORAGE_SERIALIZATION_FORMAT_JSON.title }}`, `{{ ui-key.yc-data-transfer.data-transfer.console.form.object_storage.console.form.object_storage.ObjectStorageSerializationFormatUI.OBJECT_STORAGE_SERIALIZATION_FORMAT_CSV.title }}`, `PARQUET` или `{{ ui-key.yc-data-transfer.data-transfer.console.form.object_storage.console.form.object_storage.ObjectStorageSerializationFormatUI.OBJECT_STORAGE_SERIALIZATION_FORMAT_RAW.title }}`. Для `{{ ui-key.yc-data-transfer.data-transfer.console.form.object_storage.console.form.object_storage.ObjectStorageSerializationFormatUI.OBJECT_STORAGE_SERIALIZATION_FORMAT_JSON.title }}` доступна настройка **{{ ui-key.yc-data-transfer.data-transfer.console.form.object_storage.console.form.object_storage.ObjectStorageTarget.any_as_string.title }}**.

Выходной формат данных зависит не только от выбора настройки **{{ ui-key.yc-data-transfer.data-transfer.console.form.object_storage.console.form.object_storage.ObjectStorageTarget.output_format.title }}**, но и от типа и настроек правил конвертации эндпоинта-источника.

Ниже представлены различия выходных данных при отсутствии правил конвертации для эндпоинта-источника.

{% note info %}

Примеры выходных данных для формата `PARQUET` не приводятся, так как это бинарный формат.

{% endnote %}

### {{ yds-full-name }} {#yds}

Входные данные — два сообщения:

```text
Text string
{"device_id":"iv9,"speed":"5"}
```

Выходные данные:

{% list tabs %}

- {{ ui-key.yc-data-transfer.data-transfer.console.form.object_storage.console.form.object_storage.ObjectStorageSerializationFormatUI.OBJECT_STORAGE_SERIALIZATION_FORMAT_JSON.title }}

    ```text
    <имя_потока>,<ключ_сегмента>,<порядковый_номер_сообщения>,<дата_и_время_записи_данных>,Text string
    <имя_потока>,<ключ_сегмента>,<порядковый_номер_сообщения>,<дата_и_время_записи_данных>,"{""device_id"":""iv9"",""speed"":5}"
    ```

- {{ ui-key.yc-data-transfer.data-transfer.console.form.object_storage.console.form.object_storage.ObjectStorageSerializationFormatUI.OBJECT_STORAGE_SERIALIZATION_FORMAT_CSV.title }}

    ```text
    {"data":"Text string","partition":<ключ_сегмента>,"seq_no":<порядковый_номер_сообщения>,"topic":"<имя_потока>","write_time":"<дата_и_время_записи_данных>"}
    {"data":"{\"device_id\":\"iv9\",\"speed\":5}","partition":<ключ_сегмента>,"seq_no":<порядковый_номер_сообщения>,"topic":"<имя_потока>","write_time":"<дата_и_время_записи_данных>"}
    ```

- {{ ui-key.yc-data-transfer.data-transfer.console.form.object_storage.console.form.object_storage.ObjectStorageSerializationFormatUI.OBJECT_STORAGE_SERIALIZATION_FORMAT_RAW.title }}

    ```text
    Text string
    {"device_id":"iv9,"speed":"5"}
    ```

{% endlist %}

### {{ mpg-name }} {#pg}

Входные данные — таблица:

| device_id | speed |
| --------- | ----- |
| iv9       | 5     |
| rhi       | 10    |

Выходные данные:

{% list tabs %}

- {{ ui-key.yc-data-transfer.data-transfer.console.form.object_storage.console.form.object_storage.ObjectStorageSerializationFormatUI.OBJECT_STORAGE_SERIALIZATION_FORMAT_JSON.title }}

    ```text
    {"device_id":"iv9","speed":5}
    {"device_id":"rhi","speed":10}
    ```

- {{ ui-key.yc-data-transfer.data-transfer.console.form.object_storage.console.form.object_storage.ObjectStorageSerializationFormatUI.OBJECT_STORAGE_SERIALIZATION_FORMAT_CSV.title }}

    ```text
    iv9,5,
    rhi,10,
    ````

- {{ ui-key.yc-data-transfer.data-transfer.console.form.object_storage.console.form.object_storage.ObjectStorageSerializationFormatUI.OBJECT_STORAGE_SERIALIZATION_FORMAT_RAW.title }}

    Не поддерживается.

{% endlist %}

## Сериализация при поставке в очереди сообщений {#serializer-message-queue}

При поставке в очередь сообщений вы можете использовать сериализацию двух типов:

* [{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.Serializer.serializer_auto.title }}](#auto)
* [{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.Serializer.serializer_debezium.title }}](#debezium)

### {{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.Serializer.serializer_auto.title }} {#auto}

Автоматический выбор настроек сериализации в зависимости от типа источника.

### {{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.Serializer.serializer_debezium.title }} {#debezium}

Сериализация [Debezium](https://debezium.io/) с возможностью настройки ее параметров:

* **dt.add.original.type.info** — определяет, добавлять ли информацию об исходных типах данных, чтобы восстановить их тип после переноса.

    Исключение — типы данных даты и времени {{ PG }} `with time zone`. Информацию о часовом поясе восстановить невозможно.

    Значение по умолчанию — `false`.

* **dt.batching.max.size** — максимальный размер пакета сообщений в байтах.

    Значение по умолчанию — `0` байт (пакетирование отключено). Рекомендуемое значение — `1048576` байт (1 МБ). Ненулевое значение включает пакетирование.

    Настройка актуальна, если используется JSON-сериализация со {{ schema-registry-name }} (см. параметры **key.converter** и **value.converter**). При использовании {{ schema-registry-name }} сообщения, приходящие в очередь, могут стать очень маленькими. Пакетирование в таком случае позволяет увеличить пропускную способность очередей.

    При включенном пакетировании в одно сообщение очереди помещаются последовательно несколько логических сообщений в формате [Confluent wire format](https://docs.confluent.io/cloud/current/sr/fundamentals/serdes-develop/index.html#wire-format). Данные, сериализованные таким образом, можно однозначно декодировать.

    При включенном пакетировании сообщения накапливаются в буфере. Если новое сообщение увеличивает размер пакета сверх установленного значения `dt.batching.max.size`, то текущий буфер сохраняется, а новое сообщение добавляется в пустой буфер. Если одно логическое сообщение из источника превышает значение `dt.batching.max.size`, то будет сформирован пакет из одного такого сообщения. Пакетирование происходит до компрессии в клиенте очереди.

    Включать пакетирование может быть полезно, чтобы оптимизировать тяжелую поставку в очередь, из которой сообщения считываются трансфером.

    {% note warning %}

    Пакетированные сообщения могут быть декодированы только трансфером.

    {% endnote %}

* **dt.mysql.timezone** — часовой пояс для типов данных даты и времени {{ MY }} в формате [IANA](https://www.iana.org/time-zones).

    Значение по умолчанию — `UTC`.

* **dt.unknown.types.policy** — политика поведения при обработке пользовательских типов данных.

    Возможные значения:

    * `skip` — не прерывать трансфер и игнорировать пользовательские типы данных.
    * `to_string` — не прерывать трансфер и преобразовать пользовательские типы данных в текст.
    * `fail` — прерывать трансфер с ошибкой.

    Значение по умолчанию — `skip`.

* **decimal.handling.mode** — режим обработки действительных чисел.

    Возможные значения:

    * `precise` — точное преобразование с помощью метода `java.math.BigDecimal`.
    * `double` — преобразование в тип данных `double`. Может привести к потере точности.
    * `string` — преобразование в текст.

    Значение по умолчанию — `precise`.

* **interval.handling.mode** — режим обработки временных интервалов.

    Возможные значения:

    * `numeric` — приблизительное преобразование в микросекунды.
    * `string` — точное преобразование с использованием строкового шаблона `P<years>Y<months>M<days>DT<hours>H<minutes>M<seconds>S`.

    Значение по умолчанию — `numeric`.

* **key.converter** и **value.converter** — конвертеры для ключей и значений.

    Возможные значения:

    * `org.apache.kafka.connect.json.JsonConverter` — JSON, стандартный для [Debezium](https://debezium.io/documentation/reference/index.html).
    * `io.confluent.connect.json.JsonSchemaConverter` — [Confluent Schema Registry](https://docs.confluent.io/platform/current/schema-registry/index.html).

    Значение по умолчанию — `org.apache.kafka.connect.json.JsonConverter`.

* **key.converter.schemas.enable** и **value.converter.schemas.enable** — определяют, добавлять ли описание схемы в каждое сообщение для ключей и значений при использовании конвертера `org.apache.kafka.connect.json.JsonConverter`.

    Значение по умолчанию — `true`.

* **key.converter.schema.registry.url** и **value.converter.schema.registry.url** — определяют, добавлять ли описание схемы в каждое сообщение для ключей и значений при использовании конвертера `io.confluent.connect.json.JsonSchemaConverter`.

    Возможные значения: 

    * пустая строка (значение по умолчанию) — не добавлять описание схемы;
    * строковое значение URL, определяющее путь к сервису реестра схем. 

* **key.converter.dt.json.generate.closed.content.schema** и **value.converter.dt.json.generate.closed.content.schema** — определяют, будет ли схема производителя данных для ключа и значения генерироваться в закрытой контентной модели. Это нужно для проверки совместимости через конвертацию открытой модели потребителя в закрытую и поиск аналогичной схемы среди зарегистрированных для производителя схем. 

    Значение по умолчанию — `false`.

    Чтобы сохранять полную транзитивную совместимость при добавлении и удалении опциональных полей в схеме ключа:

    1. Выберите в пространстве имен {{ schema-registry-name }} политику проверки совместимости `Optional-friendly`.
    1. В настройках сериализации эндпоинта-приемника {{ mkf-name }} [задайте настройку](../operations/endpoint/target/kafka.md#serializer) **key.converter.dt.json.generate.closed.content.schema** — `true`.

    Чтобы сохранять полную транзитивную совместимость при добавлении и удалении опциональных полей в схеме значения:

    1. Выберите в пространстве имен {{ schema-registry-name }} политику проверки совместимости `Optional-friendly`.
    1. В настройках сериализации эндпоинта-приемника [задайте настройку](../operations/endpoint/target/kafka.md#serializer) **value.converter.dt.json.generate.closed.content.schema** — `true`.    

* **key.converter.basic.auth.user.info** и **value.converter.basic.auth.user.info** — имя пользователя и пароль для авторизации в Confluent Schema Registry для ключей и значений при использовании конвертера `io.confluent.connect.json.JsonSchemaConverter`.

    Формат значения: `<имя_пользователя>:<пароль>`.

* **key.converter.ssl.ca** и **value.converter.ssl.ca** — содержимое SSL-сертификата Confluent Schema Registry для ключей и значений при использовании конвертера `io.confluent.connect.json.JsonSchemaConverter`.

    Если значение настройки не указано, SSL-сертификат не проверяется.

* **unavailable.value.placeholder** — значение, которое устанавливается вместо данных, если их тип не поддерживается.

    Значение по умолчанию — `__debezium_unavailable_value`.

## Примеры использования {#examples}

* [{#T}](../tutorials/mkf-to-mch.md)
* [{#T}](../tutorials/mkf-to-mpg.md)
* [{#T}](../tutorials/yds-to-clickhouse.md)
* [{#T}](../tutorials/yds-to-objstorage.md)
* [{#T}](../tutorials/data-ingestion.md)

