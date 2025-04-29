---
title: Основные понятия {{ metadata-hub-full-name }}
description: Из статьи вы узнаете, что такое подключение и реестр схем.
---

# О сервисе {{ metadata-hub-full-name }}

{% include notitle [preview](../../_includes/note-preview.md) %}

{{ metadata-hub-full-name }} — сервис, который предоставляет возможности по управлению данными в {{ yandex-cloud }}:

* автоматическое создание и управление параметрами подключений к базам данных;
* хранение, получение схем и проверка эволюции схем обмена данными;
* создание и управление кластерами {{ metastore-full-name }};
* поиск и визуализация метаинформации о хранилищах данных и связях между ними.

## Управление подключениями {#connection-manager}

{% include [connection](../../_includes/metadata-hub/connection-definition.md) %}

## Управление табличными метаданными {#metastore}

{% include [connection](../../_includes/metadata-hub/metastore-definition.md) %}


### Примеры использования {#examples-metastore}

* [{#T}](../tutorials/metastore-import.md)
* [{#T}](../tutorials/sharing-tables.md)

## Реестр схем данных {#schema-registry}

{% include [connection](../../_includes/metadata-hub/schema-registry-definition.md) %}

Реестр схем позволяет вам определять схемы для ваших форматов и версий данных и регистрировать их в реестре. После регистрации схему можно использовать совместно в различных системах и приложениях. Когда поставщик отправляет данные получателю сообщений, схема данных включается в заголовок сообщения, а реестр схемы гарантирует, что схема действительна и совместима с ожидаемой схемой для субъекта.

### Примеры использования {#examples-schema-registry}

* [{#T}](../tutorials/managed-schema-registry.md)
* [{#T}](../tutorials/schema-registry-cdc-debezium-kafka.md)

