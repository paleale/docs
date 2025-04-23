---
title: Правила тарификации для {{ search-api-full-name }}
description: В статье содержатся правила тарификации сервиса {{ search-api-name }}.
editable: false
---

# Правила тарификации для {{ search-api-name }}



{% include [without-use-calculator](../_includes/pricing/without-use-calculator.md) %}

{% include [link-to-price-list](../_includes/pricing/link-to-price-list.md) %}

## Из чего складывается стоимость использования {{ search-api-name }} {#rules}

Стоимость использования {{ search-api-name }} рассчитывается, исходя из количества инициированных поисковых запросов за календарный месяц ([Отчетный период](../billing/concepts/reporting-period.md)).

## Цены для региона Россия {#prices}


{% include [pricing-diff-regions](../_includes/pricing-diff-regions.md) %}

Уровень нетарифицированного потребления (free tier) для каждого пользователя составляет 1000 синхронных запросов в месяц через [API v1](concepts/index.md#api-v1) в ночные часы^1^ с 00:00:00 по 07:59:59. Запросы, превышающие эти значения, оплачиваются по тарифам, приведенным ниже. Уровень нетарифицированного потребления не распространяется на запросы, выполненные через [API v2](concepts/index.md#api-v2).


{% list tabs group=pricing %}

- Цены в рублях {#prices-rub}

  {% include notitle [rub.md](../_pricing/search-api/rub.md) %}

- Цены в тенге {#prices-kzt}

  {% include notitle[kzt](../_pricing/search-api/kzt.md) %}

{% endlist %}



Для пользователей сервиса действуют квоты. Подробнее об ограничениях см. в разделе [Квоты и лимиты](concepts/limits.md). Для изменения значений квот обратитесь в [техническую поддержку]({{ link-console-support }}) или к вашему аккаунт-менеджеру.

^1^ Время указано в часовом поясе [UTC+3](https://ru.wikipedia.org/wiki/UTC%2B3:00).
