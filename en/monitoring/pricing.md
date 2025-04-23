---
title: '{{ monitoring-full-name }} pricing policy'
description: This article explains the {{ monitoring-name }} pricing policy.
editable: false
---

# {{ monitoring-full-name }} pricing policy

{% include [without-use-calculator](../_includes/pricing/without-use-calculator.md) %}

{% include [link-to-price-list](../_includes/pricing/link-to-price-list.md) %}

## What goes into the cost of {{ monitoring-short-name }} {#rules}

At present, you will be charged for writing custom metrics via the [{{ monitoring-short-name }} API](api-ref/index.md) and writing any metrics via the [{{ prometheus-name }} Remote API](operations/prometheus/index.md), as well as reading any metrics via the [{{ monitoring-short-name }} API](api-ref/index.md).

Reading metrics via the {{ prometheus-name }} Remote API is not yet billable.

Pricing features:
* After writing or reading the first 50 million metric values via the {{ monitoring-short-name }} API, the writing fee is reduced. See .
* There is no charge for writing {{ yandex-cloud }} resource metrics collected automatically.
* There is no charge for reading metrics via the {{ monitoring-short-name }} interface and {{ yandex-cloud }} console.
* There is no charge for {{ monitoring-short-name }} incoming and outgoing traffic.

### Cost calculation example {#example}






{% include [usd-example](../_pricing_examples/monitoring/usd-example.md) %}



## Prices for the Russia region {#prices}



{% note warning %}

Starting May 1, 2025, the prices for {{ monitoring-full-name }} resources in the Russia region will increase. For new USD prices, see our [price list](https://yandex.cloud/en/price-list?currency=USD&installationCode=ru&services=dn2675dm5m59q3pira4b).

{% endnote %}




{% include [pricing-diff-regions](../_includes/pricing-diff-regions.md) %}


### {{ monitoring-short-name }} API {#monitoring-api}




The minimum billing unit is 1 metric value. The cost is rounded to the nearest cent.

{% include [usd-api-example](../_pricing_examples/monitoring/usd-api-example.md) %}

{% include [usd.md](../_pricing/monitoring/usd.md) %}


### {{ prometheus-name }} Remote API {#prometheus-remote-api}




{% include [usd-prometheus.md](../_pricing/monitoring/usd-prometheus.md) %}


{% include [trademark](../_includes/monitoring/trademark.md) %}
