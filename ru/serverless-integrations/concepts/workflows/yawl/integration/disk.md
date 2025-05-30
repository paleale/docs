---
title: Интеграционный шаг Disk
description: В статье описаны поля для интеграционного шага Disk.
---

# Disk

Взаимодействие с файлами на Яндекс Диске. Поля `upload` и `download` — взаимоисключающие, можно использовать только одно из них.

Имя поля | Тип | Обязательное | Значение по умолчанию | Поддерживается [шаблонизация](../../templating.md) | Описание
--- | --- | --- | --- | --- | ---
`oauthToken` | `string` | Да | Нет | Да | [OAuth-токен](../../../../../iam/concepts/authorization/oauth-token.md), который будет использоваться для авторизации при обращении к Яндекс Диску.
`path` | `string` | Да | Нет | Да | Путь к файлу для записи или скачивания.
`upload` | [DiskUpload](#DiskUpload) | Нет | Нет | Нет | Конфигурация действия `upload` — запись файла на Яндекс Диск.
`download` | [DiskDownload](#DiskDownload) | Нет | Нет | Нет | Конфигурация действия `download` — скачивание файла с Яндекс Диска.


## Объект DiskUpload {#DiskUpload}

Имя поля | Тип | Обязательное | Значение по умолчанию | Поддерживается [шаблонизация](../../templating.md) | Описание
--- | --- | --- | --- | --- | ---
`content` | `string` | Да | Нет | Да | Записываемый контент.
`contentType` | `BINARY`\|<br/>`JSON`\|<br/>`TEXT` | Нет | `TEXT` | Нет | Определяет, как будет интерпретировано переданное в `content` содержимое:<ul><li>`BINARY` — набор байт в виде [base64](https://{{ lang }}.wikipedia.org/wiki/Base64)-encoded-строки.</li><li>`JSON` — текст, содержащий [JSON](https://ru.wikipedia.org/wiki/JSON), будет преобразован в JSON-структуру.</li><li>`TEXT` — текст.</li></ul>


## Объект DiskDownload {#DiskDownload}

Имя поля | Тип | Обязательное | Значение по умолчанию | Поддерживается [шаблонизация](../../templating.md) | Описание
--- | --- | --- | --- | --- | ---
`contentType` | `TEXT`\| <br/>`BINARY`\| <br/>`JSON`\| <br/>`CSV`\| <br/>`EXCEL` | Нет | `BINARY` | Нет | Определяет, как будет интерпретирован контент:<ul><li>`TEXT` — текст.</li><li>`BINARY` — набор байт в виде [base64](https://{{ lang }}.wikipedia.org/wiki/Base64)-encoded-строки.</li><li>`JSON` — текст, содержащий [JSON](https://{{ lang }}.wikipedia.org/wiki/JSON), будет преобразован в JSON-структуру.</li><li>`CSV ` — текст будет преобразован в массив массивов строк, путем деления строки по следующим символам: запятая (`,`) и перенос строки.</li><li>`EXCEL` — текст будет преобразован в массив страниц, где каждая страница — массив массивов строк. Поддерживаемые форматы: `XLAM`, `XLSM`, `XLSX`, `XLTM`, `XLTX`.</li></ul>
