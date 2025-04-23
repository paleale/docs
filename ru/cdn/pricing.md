---
title: Правила тарификации для {{ cdn-full-name }}
description: В статье содержатся правила тарификации сервиса {{ cdn-full-name }}.
editable: false
---

# Правила тарификации для {{ cdn-full-name }}



{% include [without-use-calculator](../_includes/pricing/without-use-calculator.md) %}

{% include [link-to-price-list](../_includes/pricing/link-to-price-list.md) %}

При расчете стоимости использования {{ cdn-name }} учитываются:
* Объем исходящего трафика с CDN-серверов, в том числе запрошенного с пользовательских ресурсов сервисов {{ yandex-cloud }}, например с [виртуальных машин](../compute/concepts/vm.md) {{ compute-full-name}}. Входящий трафик, поступающий на CDN-серверы от сервисов и ресурсов {{ yandex-cloud }} и из интернета, не тарифицируется.
* Платные функции, включенные для ресурсов: [экранирование источников](concepts/origins-shielding.md) и [выгрузка логов](concepts/logs.md).

## Цены для региона Россия {#prices}


{% note warning %}

С 1 мая 2025 года увеличатся цены на ресурсы {{ cdn-full-name }} в регионе Россия. Новые цены можно посмотреть на сайте:

* [Цены в рублях](https://yandex.cloud/ru/price-list?installationCode=ru&currency=RUB&services=dn2rse5n40m8h0bu8jqa)
* [Цены в тенге](https://yandex.cloud/ru/price-list?installationCode=ru&currency=KZT&services=dn2rse5n40m8h0bu8jqa)

{% endnote %}



{% include [pricing-diff-regions](../_includes/pricing-diff-regions.md) %}

### Исходящий трафик {#traffic}


{% list tabs group=pricing %}

- Цены в рублях {#prices-rub}

  {% include notitle [rub.md](../_pricing/cdn/rub.md) %}

- Цены в тенге {#prices-kzt}

  {% include notitle [kzt.md](../_pricing/cdn/kzt.md) %}

{% endlist %}



### Платные функции {#paid-features}

Тарификация помесячная. Если функция подключена или отключена в любой день месяца, в последний день будет списана полная цена функции в месяц.


{% list tabs group=pricing %}

- Цены в рублях {#prices-rub}

  {% include notitle [rub-paid-features.md](../_pricing/cdn/rub-paid-features.md) %}

- Цены в тенге {#prices-kzt}

  {% include notitle [kzt-paid-features.md](../_pricing/cdn/kzt-paid-features.md) %}

{% endlist %}



