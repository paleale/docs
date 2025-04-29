---
title: Интеграционный шаг ObjectStorage
description: В статье описаны поля для интеграционного шага ObjectStorage.
---


# ObjectStorage

Взаимодействие с объектами {{ objstorage-full-name }}. Поля `put` и `get` — взаимоисключающие, можно выполнить только одно действие над объектом.

Имя поля | Тип | Обязательное | Значение по умолчанию | Поддерживается [шаблонизация](../../templating.md) | Описание
--- | --- | --- | --- | --- | ---
`bucket` | `string` | Да | Нет | Нет | Имя бакета.
`object` | `string` | Да | Нет | Да | Имя объекта.
`put` | [ObjectStoragePut](#ObjectStoragePut) | Нет | Нет | Нет | Конфигурация действия `put` — добавление объекта в бакет.
`get` | [ObjectStorageGet](#ObjectStorageGet) | Нет | Нет | Нет | Конфигурация действия `get` — получение объекта из бакета.

## Объект ObjectStoragePut {#ObjectStoragePut}

Имя поля | Тип | Обязательное | Значение по умолчанию | Поддерживается [шаблонизация](../../templating.md) | Описание
--- | --- | --- | --- | --- | ---
`content` | `string` | Да | Нет | Да | Содержимое объекта.
`contentType` | `BINARY`\|<br/>`JSON`\|<br/>`TEXT` | Нет | `TEXT` | Нет | Определяет, как будет интерпретировано переданное в `content` содержимое:<ul><li>`BINARY` — набор байт в виде [base64](https://{{ lang }}.wikipedia.org/wiki/Base64)-encoded-строки.</li><li>`JSON` — текст, содержащий [JSON](https://ru.wikipedia.org/wiki/JSON), будет преобразован в JSON-структуру.</li><li>`TEXT` — текст.</li></ul>

## Объект ObjectStorageGet {#ObjectStorageGet}

Имя поля | Тип | Обязательное | Значение по умолчанию | Поддерживается [шаблонизация](../../templating.md) | Описание
--- | --- | --- | --- | --- | ---
`contentType` | `BINARY`\|<br/>`JSON`\|<br/>`TEXT`\| <br/>`EXCEL`\| <br/>`CSV` | Нет | `BINARY` | Нет | Определяет, как будет интерпретировано содержимое объекта:<ul><li>`BINARY` — набор байт в виде [base64](https://{{ lang }}.wikipedia.org/wiki/Base64)-encoded-строки.</li><li>`JSON` — текст, содержащий [JSON](https://ru.wikipedia.org/wiki/JSON), будет преобразован в JSON-структуру.</li><li>`TEXT` — текст.</li><li>`EXCEL` — текст будет преобразован в массив страниц, где каждая страница — массив массивов строк. Поддерживаемые форматы: `XLAM`, `XLSM`, `XLSX`, `XLTM`, `XLTX`.</li><li>`CSV` — текст будет преобразован в массив массивов строк, путем деления строки по следующим символам: запятая (`,`) и перенос строки.</li></ul>
