---
title: '{{ cdn-full-name }} pricing policy'
description: This article describes the {{ cdn-full-name }} pricing policy.
editable: false
---

# {{ cdn-full-name }} pricing policy



{% include [without-use-calculator](../_includes/pricing/without-use-calculator.md) %}

{% include [link-to-price-list](../_includes/pricing/link-to-price-list.md) %}

The cost of using {{ cdn-name }} is based on:
* Amount of outgoing traffic from CDN servers, including traffic requested from user resources on {{ yandex-cloud }}, e.g., {{ compute-full-name }} [virtual machines](../compute/concepts/vm.md). Incoming traffic received by CDN servers from the {{ yandex-cloud }} services and resources or from the Internet, is not charged.
* Paid features enabled for resources, such as [origin shielding](concepts/origins-shielding.md) and [log export](concepts/logs.md).

## Prices for the Russia region {#prices}



{% note warning %}

Starting May 1, 2025, the prices for {{ cdn-full-name }} resources in the Russia region will increase. For new USD prices, see our [price list](https://yandex.cloud/en/price-list?installationCode=ru&currency=USD&services=dn2rse5n40m8h0bu8jqa).

{% endnote %}


{% include [pricing-diff-regions](../_includes/pricing-diff-regions.md) %}

### Egress traffic {#traffic}



{% include notitle [usd.md](../_pricing/cdn/usd.md) %}


### Paid features {#paid-features}

Billing occurs on a monthly basis. If a function is enabled or disabled on any day of a month, the full monthly price will be charged for the month on the last day.



{% include notitle [usd-paid-features.md](../_pricing/cdn/usd-paid-features.md) %}


