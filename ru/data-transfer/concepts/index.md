---
title: Взаимосвязь ресурсов сервиса {{ data-transfer-full-name }}
description: '{{ data-transfer-full-name }} позволяет легко переносить данные между базами данных. Сервис позволяет сократить время на процесс миграции, минимизировать простой при переключении на новую базу данных или иметь постоянную реплику базы.'
---

# Взаимосвязь ресурсов в {{ data-transfer-name }}

{{ data-transfer-full-name }} помогает переносить данные между СУБД, объектными хранилищами или брокерами сообщений. Сервис позволяет сократить время на процесс миграции и минимизировать простой при переключении на новую базу данных.

{{ data-transfer-full-name }} настраивается через стандартные интерфейсы {{ yandex-cloud }}.

Сервис подходит для создания постоянной реплики базы. Перенос схемы базы данных из источника на приемник автоматизирован.

## Эндпоинт {#endpoint}

_Эндпоинт_ — это конфигурация для подключения к сервису-_источнику_ или _приемнику_ данных. Кроме настроек подключения, эндпоинт может содержать информацию о том, какие данные будут участвовать в трансфере и как они должны быть обработаны в процессе переноса.

В качестве источника или приемника данных могут выступать:

| Сервис                                                                                                                                |                                Источник                                |               Приемник               |
|---------------------------------------------------------------------------------------------------------------------------------------|:----------------------------------------------------------------------:|:------------------------------------:|
| Топик {{ KF }} — собственный или в составе [сервиса {{ mkf-short-name }}](../../managed-kafka/)                                       |                  ![yes](../../_assets/common/yes.svg)                  | ![yes](../../_assets/common/yes.svg) |
| Поток сообщений AWS CloudTrail                                                                                                        |                  ![yes](../../_assets/common/yes.svg)                  |  ![no](../../_assets/common/no.svg)  |
| Собственная база данных BigQuery                                                                                                      |                  ![yes](../../_assets/common/yes.svg)                  |  ![no](../../_assets/common/no.svg)  |
| База данных {{ CH }} — собственная или в составе [сервиса {{ mch-short-name }}](../../managed-clickhouse/)                            |                  ![yes](../../_assets/common/yes.svg)                  | ![yes](../../_assets/common/yes.svg) |
| Собственная база данных {{ ES }}                                                                                                      |                  ![yes](../../_assets/common/yes.svg)                  | ![yes](../../_assets/common/yes.svg) |
| База данных {{ GP }} — собственная или в составе [сервиса {{ mgp-short-name }}](../../managed-greenplum/)                             |                  ![yes](../../_assets/common/yes.svg)                  | ![yes](../../_assets/common/yes.svg) |
| База данных {{ MG }} — собственная или в составе [сервиса {{ mmg-short-name }}](../../managed-mongodb/)                               |                  ![yes](../../_assets/common/yes.svg)                  | ![yes](../../_assets/common/yes.svg) |
| База данных {{ MY }} — собственная или в составе [сервиса {{ mmy-short-name }}](../../managed-mysql/)                                 |                  ![yes](../../_assets/common/yes.svg)                  | ![yes](../../_assets/common/yes.svg) |
| Собственная база данных Oracle                                                                                                        |                  ![yes](../../_assets/common/yes.svg)                  |  ![no](../../_assets/common/no.svg)  |
| База данных {{ PG }} — собственная или в составе [сервиса {{ mpg-short-name }}](../../managed-postgresql/)                            |                  ![yes](../../_assets/common/yes.svg)                  | ![yes](../../_assets/common/yes.svg) |
| База данных {{ OS }} — собственная или в составе [сервиса {{ mos-short-name }}](../../managed-opensearch/)                            |                  ![yes](../../_assets/common/yes.svg)                  | ![yes](../../_assets/common/yes.svg) |
| [S3-совместимый бакет](../../glossary/s3.md) |                  ![yes](../../_assets/common/yes.svg)                  |  ![no](../../_assets/common/no.svg)  |
| Поток данных [{{ yds-full-name }}](../../data-streams/)                                                                               |                  ![yes](../../_assets/common/yes.svg)                  | ![yes](../../_assets/common/yes.svg) |
| База данных {{ ydb-name }} — в составе [сервиса {{ ydb-name }}](../../ydb/)                                                           |                  ![yes](../../_assets/common/yes.svg)                  | ![yes](../../_assets/common/yes.svg) |
| Бакет [{{ objstorage-full-name }}](../../storage/)                                                                                    |                  ![yes](../../_assets/common/yes.svg)                  | ![yes](../../_assets/common/yes.svg) |
| 

### Статусы эндпоинтов {#statuses}

В связи с [переходом](../release-notes/2501.md) сервиса {{ data-transfer-name }} на [асинхронные операции](../../api-design-guide/concepts/async.md) с эндпоинтами, введены статусы эндпоинтов:

* **{{ ui-key.yacloud.data-transfer.label_endpoint-status-READY }}** — присваивается, когда эндпоинт готов к использованию.
* **{{ ui-key.yacloud.data-transfer.label_endpoint-status-CREATING }}** — присваивается в момент начала операции по созданию эндпоинта. По окончании операции эндпоинту присваивается статус **{{ ui-key.yacloud.data-transfer.label_endpoint-status-READY }}**.
* **{{ ui-key.yacloud.data-transfer.label_endpoint-status-UPDATING }}** — присваивается в момент начала операции по изменению эндпоинта. По окончании операции эндпоинту присваивается статус **{{ ui-key.yacloud.data-transfer.label_endpoint-status-READY }}**.
* **{{ ui-key.yacloud.data-transfer.label_endpoint-status-DELETING }}** — присваивается в момент начала операции по удалению эндпоинта.

{% note info %}

Все новые эндпоинты создаются асинхронными. Старые эндпоинты остаются синхронными и могут иметь только статус **{{ ui-key.yacloud.data-transfer.label_endpoint-status-READY }}**.

{% endnote %}

## Трансфер {#transfer}

_Трансфер_ — это процесс переноса данных между сервисом-источником и сервисом-приемником. Он должен находиться в одном каталоге с используемыми эндпоинтами.

Если для эндпоинтов заданы подсети, то эти подсети должны быть размещены в одной [зоне доступности](../../overview/concepts/geo-scope.md). Иначе активация трансфера с такими эндпоинтами завершится с ошибкой.

### Воркер {#worker}

_Воркер_ — это служебный процесс, который запускает перенос данных в рамках трансфера. Для каждого воркера выделяется отдельная виртуальная машина. Можно указать, какие вычислительные ресурсы использовать для этой виртуальной машины:

{% include [vm-computing-resources](../../_includes/data-transfer/vm-computing-resources.md) %}

При [параллельном копировании](sharded.md) или параллельной репликации (для источников {{ DS }}, {{ ydb-short-name }} и {{ KF }}) пользователь выбирает количество воркеров, работающих одновременно.

Количество vCPU и объем RAM влияют на [стоимость ресурсов {{ data-transfer-name }}](../pricing.md). Для экономного потребления и уменьшения стоимости передачи данных рекомендуется оптимизировать загрузку воркеров, уменьшая их количество и увеличивая загрузку каждого воркера. Вы также можете изменить конфигурацию воркера в [настройках трансфера](../operations/transfer.md#update) для пар источник-приемник, которые [тарифицируются](../pricing.md) и находятся на стадии [GA](../../overview/concepts/launch-stages.md).

### Типы трансферов {#transfer-type}

{% include [include](../../_includes/data-transfer/transfer-types.md) %}

Подробнее о различиях между типами трансферов читайте в разделе [{#T}](./transfer-lifecycle.md).

### Совместимость источников и приемников {#connectivity-matrix}

{% include [include](../../_includes/data-transfer/connectivity-marix.md) %}

{% include [clickhouse-disclaimer](../../_includes/clickhouse-disclaimer.md) %}

## Примеры использования {#examples}

* [{#T}](../tutorials/index.md)
* [{#T}](../operations/index.md)