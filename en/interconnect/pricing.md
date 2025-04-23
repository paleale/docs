---
title: '{{ interconnect-full-name }} pricing policy'
description: This article explains the {{ interconnect-name }} pricing policy.
editable: false
---

# {{ interconnect-name }} pricing policy



{% note warning %}

Starting May 1, 2025, the prices for {{ interconnect-full-name }} resources in the Russia region will increase. For new USD prices, see our [price list](https://yandex.cloud/en/price-list?currency=USD&installationCode=ru&services=dn2iqlgecrio4statr88).

{% endnote %}




The service is provided if technically feasible.

Automatic billing for the service starts with the earliest of the two events:
* The [BGP session](./concepts/priv-con.md#bgp-peering) of any [private connection](./concepts/priv-con.md) in the trunk goes `Active`.
* More than 90 days elapses since the [trunk](./concepts/trunk.md) was created (port was reserved) on the {{ yandex-cloud }} equipment. The state of the trunk physical port (ports) is irrelevant.


## Data size {#data}



{% include [usd-data](../_pricing/interconnect/usd-data.md) %}


## Other services {#other-services}



{% include [usd-other-services](../_pricing/interconnect/usd-other-services.md) %}


