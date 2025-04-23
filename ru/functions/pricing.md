---
title: Правила тарификации для {{ sf-full-name }}
description: В статье содержатся правила тарификации сервиса {{ sf-name }}.
editable: false
---

# Правила тарификации для {{ sf-name }}



{% include [without-use-calculator](../_includes/pricing/without-use-calculator.md) %}

{% include [link-to-price-list](../_includes/pricing/link-to-price-list.md) %}

## Из чего складывается стоимость использования {{ sf-name }} {#rules}

В рамках сервиса {{ sf-name }} тарифицируется количество вызовов функции, вычислительные ресурсы, выделенные для выполнения функции, время простоя [подготовленных экземпляров](concepts/function.md#provisioned-instances) и исходящий трафик.

При тарификации вычислительных ресурсов (ГБ×час) учитывается объем памяти, выделенный для функции, и время выполнения функции:
* Объем памяти, указанный при [создании версии](operations/function/version-manage.md), измеряется в ГБ.
* Время выполнения для каждого вызова функции, измеряется в часах, и округляется в большую сторону до ближайшего значения, кратного 100 мс.

{% note info %}

Тарифицируются только [вызовы функции](concepts/function-invoke.md), которые привели к запуску вашего кода.

{% endnote %}

### Формула расчета стоимости {#price-formula}


{% list tabs group=pricing %}

- Стоимость в рублях {#prices-rub}

  Стоимость в месяц = {{ sku|RUB|serverless.functions.compute.v1|pricingRate.10|string }} × Объем памяти (Гб) × Время обработки вызовов (Часы) + {{ sku|RUB|serverless.functions.invocations.v1|pricingRate.1|string }} × Количество миллионов вызовов

  {% include [not-charged-functions.md](../_includes/pricing/price-formula/not-charged-functions.md) %}

  {% include [free-tier.md](../_includes/pricing/price-formula/free-tier.md) %}

- Стоимость в тенге {#prices-kzt}

  Стоимость в месяц = {{ sku|KZT|serverless.functions.compute.v1|pricingRate.10|string }} × Объем памяти (Гб) × Время обработки вызовов (Часы) + {{ sku|KZT|serverless.functions.invocations.v1|pricingRate.1|string }} × Количество миллионов вызовов

  {% include [not-charged-functions.md](../_includes/pricing/price-formula/not-charged-functions.md) %}

  {% include [free-tier.md](../_includes/pricing/price-formula/free-tier.md) %}

{% endlist %}



### Пример расчета стоимости {#price-example}

{% include [prices-example](../_includes/functions/prices-example.md) %}

## Использование триггеров {#triggers}

Использование [триггеров](concepts/trigger/index.md) не тарифицируется. Вы можете создавать и использовать триггеры в рамках доступных [квот и лимитов](concepts/limits.md).

## Навыки Алисы

Функции {{ sf-name }}, которые используются для навыков Алисы, не тарифицируются и не расходуют [нетарифицируемый объем услуг](../billing/concepts/serverless-free-tier.md#sf), если:
* функцию вызывает [платформа Яндекс Диалоги](https://yandex.ru/dev/dialogs/);
* навык Алисы создан по [инструкции](https://yandex.ru/dev/dialogs/alice/doc/deploy-ycloud-function.html#deploy-ycloud-function__register).

При этом если функция использует другие ресурсы {{ yandex-cloud }}, они тарифицируются. Например, если функция делает запросы к очереди {{ message-queue-name }}, за них взимается плата в соответствии с [тарифами](../message-queue/pricing.md#requests-to-queues).

## Цены для региона Россия {#prices}


{% note warning %}

С 1 мая 2025 года увеличатся цены на ресурсы {{ sf-full-name }} в регионе Россия. Новые цены можно посмотреть на сайте:

* [Цены в рублях](https://yandex.cloud/ru/price-list?installationCode=ru&currency=RUB&services=dn2hj814q5t5pipfkqo4)
* [Цены в тенге](https://yandex.cloud/ru/price-list?installationCode=ru&currency=KZT&services=dn2hj814q5t5pipfkqo4)

{% endnote %}



{% include [pricing-diff-regions](../_includes/pricing-diff-regions.md) %}

### Вызов функции {#invoke}


{% list tabs group=pricing %}

- Цены в рублях {#prices-rub}

  {% include [rub.md](../_pricing/functions/rub-invocations.md) %}

  Оплачивается фактическое количество вызовов. Например, 1000 вызовов сверх нетарифицируемого объема стоит {% calc [currency=RUB] {{ sku|RUB|serverless.functions.invocations.v1|pricingRate.1|number }} × 1000 / 1000000 %}, если 1 миллион запросов стоит {{ sku|RUB|serverless.functions.invocations.v1|pricingRate.1|string }}.

- Цены в тенге {#prices-kzt}

  {% include [kzt.md](../_pricing/functions/kzt-invocations.md) %}

  Оплачивается фактическое количество вызовов. Например, 1000 вызовов сверх нетарифицируемого объема стоит {% calc [currency=KZT] {{ sku|KZT|serverless.functions.invocations.v1|pricingRate.1|number }} × 1000 / 1000000 %}, если 1 миллион запросов стоит {{ sku|KZT|serverless.functions.invocations.v1|pricingRate.1|string }}.

{% endlist %}



### Время выполнения функции {#execution}


{% list tabs group=pricing %}

- Цены в рублях {#prices-rub}

  {% include [rub.md](../_pricing/functions/rub-compute.md) %}

- Цены в тенге {#prices-kzt}

  {% include [kzt.md](../_pricing/functions/kzt-compute.md) %}

{% endlist %}



### Подготовленные экземпляры {#provisioned-instances}


{% list tabs group=pricing %}

- Цены в рублях {#prices-rub}

  {% include [rub.md](../_pricing/functions/rub-compute-provisioned-instances.md) %}

- Цены в тенге {#prices-kzt}

  {% include [kzt.md](../_pricing/functions/kzt-compute-provisioned-instances.md) %}

{% endlist %}



{% include [egress-traffic-pricing](../_includes/egress-traffic-pricing.md) %}

