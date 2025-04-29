---
title: Начало работы с {{ mpg-full-name }}
description: В этой инструкции вы научитесь создавать кластер {{ PG }} и подключаться к нему.
---

# Как начать работать с {{ mpg-name }}

Чтобы начать работу с сервисом:
* [Создайте кластер БД](#cluster-create).
* [Подключитесь к БД](#connect).


## Перед началом работы {#before-you-begin}

1. Перейдите в [консоль управления]({{ link-console-main }}), затем войдите в {{ yandex-cloud }} или зарегистрируйтесь, если вы еще не зарегистрированы.

1. Если у вас еще нет каталога, создайте его:

   {% include [create-folder](../_includes/create-folder.md) %}

1. [Назначьте](../iam/operations/roles/grant.md) вашему аккаунту в {{ yandex-cloud }} роль [{{ roles-vpc-user }}](../vpc/security/index.md#vpc-user) и роль [{{ roles.mpg.editor }} или выше](security/index.md#roles-list). Эти роли позволяют создать кластер.

    {% include [note-managing-roles](../_includes/mdb/note-managing-roles.md) %}

1. Подключаться к [кластерам](../glossary/cluster.md) БД можно как изнутри, так и извне {{ yandex-cloud }}:

   * Чтобы подключиться изнутри {{ yandex-cloud }}, создайте виртуальную машину в той же облачной сети, что и кластер БД (на основе [Linux](../compute/quickstart/quick-create-linux.md)).

   * Чтобы подключиться к кластеру из интернета, запросите публичный доступ к хостам при создании кластера.

   {% note info %}

   Следующий шаг предполагает, что подключение к кластеру производится с ВМ на основе [Linux](../compute/quickstart/quick-create-linux.md).

   {% endnote %}

1. [Подключитесь](../compute/operations/vm-connect/ssh.md) к ВМ по [SSH](../glossary/ssh-keygen.md).

1. Установите необходимые зависимости и клиент {{ PG }}:

   ```bash
   sudo apt update && sudo apt install -y postgresql-client
   ```


## Создайте кластер {#cluster-create}

1. В консоли управления выберите каталог, в котором нужно создать кластер БД.
1. Выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-postgresql }}**.
1. Нажмите кнопку **{{ ui-key.yacloud.mdb.clusters.button_create }}**.
1. Задайте параметры кластера и нажмите кнопку **{{ ui-key.yacloud.mdb.forms.button_create }}**. Процесс подробно рассмотрен в разделе [Создание кластера](operations/cluster-create.md).
1. Дождитесь, когда кластер будет готов к работе: его статус на панели {{ mpg-short-name }} сменится на **Running**, а состояние — на **Alive**. Это может занять некоторое время.

## Подключитесь к БД {#connect}


1. Если вы используете группы безопасности для облачной сети, [настройте их](operations/connect.md#configuring-security-groups) так, чтобы был разрешен весь необходимый трафик между кластером и хостом, с которого выполняется подключение.


1. Для подключения к серверу БД получите SSL-сертификат:

    {% include [install-certificate](../_includes/mdb/mpg/install-certificate.md) %}

1. Используйте для подключения команду `psql`:

    {% include [default-connstring](../_includes/mdb/mpg/default-connstring.md) %}


## Что дальше {#whats-next}

* Изучите [концепции сервиса](concepts/index.md).
* Узнайте подробнее о [создании кластера](operations/cluster-create.md) и [подключении к БД](operations/connect.md).
* Ознакомьтесь с [вопросами и ответами](qa/general.md).
