---
title: Посмотреть потребление клиентов
description: Следуя данной инструкции, вы сможете посмотреть детальную информацию о том, как клиенты используют сервисы.
---

# Посмотреть потребление клиентов

Вы можете посмотреть детальную информацию о том, как клиенты используют сервисы:

* через консоль управления;
* на партнерском портале.

{% list tabs group=instructions %}

- Консоль управления {#console}

  Чтобы посмотреть сведения по использованию сервисов {{ yandex-cloud }} в виде графиков и таблиц:

  1. В [консоли управления]({{ link-console-main }}) нажмите ![image](../../_assets/console-icons/dots-9.svg) **Все сервисы**.
  1. Выберите сервис ![image](../../_assets/console-icons/credit-card.svg) [**{{ billing-name }}**]({{ link-console-billing }}).
  1. Нажмите на имя нужного аккаунта и выберите ![image](../../_assets/console-icons/layout-cells-large.svg) **Детализация**.

     Подробнее о настройках страницы **Детализация** см. в [документации сервиса {{ billing-name }}](../../billing/operations/check-charges.md).

- Партнерский портал {#partner}

  Войдите на [партнерский портал]({{ link-cloud-partners }}) с Яндекс ID, к которому привязан аккаунт партнера в {{ yandex-cloud }}. Вам будут доступны несколько способов проверить потребление клиентов:

  * Раздел **Дашборд**

    1. На панели слева выберите ![icon](../../_assets/console-icons/layout-header-side-content.svg) **Дашборд**.
    1. Выберите аккаунт клиента в списке и нажмите на него.
    1. Перейдите на вкладку **Потребление услуг**.

  * Раздел **Вознаграждения**

    1. На панели слева выберите ![icon](../../_assets/console-icons/medal.svg) **Вознаграждения**.
    1. Укажите период, за который необходимо отобразить статистику. Общая сумма потребления всеми клиентами отобразится на диаграмме в разбивке по месяцам.

       {% note info %}

       Максимальный период отображения статистики — 92 дня.

       {% endnote %}

       Вы можете скачать детальную статистику по клиентам в виде файла `.csv`, нажав кнопку **Скачать CSV**.

{% endlist %}

Вы также можете [посмотреть сведения по использованию сервисов](../../billing/operations/dashboard.md) в {{ datalens-full-name }}.
