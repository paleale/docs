---
title: Technical support pricing
description: This article discusses the support pricing policy.
editable: false
---

# Technical support pricing


{% note tip %}




To calculate the cost of using the service, use the [calculator](https://yandex.cloud/en/prices?state=a1e4dbe0c722#calculator) on the {{ yandex-cloud }} website or see the pricing data in this section.


{% endnote %}



{% include [link-to-price-list](../_includes/pricing/link-to-price-list.md) %}


## Prices for the Russia region {#prices}


{% note warning %}

Starting May 1, 2025, the prices for technical support in the Russia region will increase. For new USD prices, see our [price list](https://yandex.cloud/en/price-list?currency=USD&installationCode=ru&services=dn2m1lagd9bf5oopu2r0).

{% endnote %}




{% include [pricing-diff-regions](../_includes/pricing-diff-regions.md) %}


{% include [currency-choice](../_includes/pricing/currency-choice.md) %}

The cost depends on the service plan selected. The plan you choose covers your organization and can only be changed by its owner or administrator. You can use one billing account to pay for technical support of multiple organizations that may have different support service plans activated. For the services available under various plans, see [Requesting technical support](overview.md).




Service plan | Basic           | Business                       | Premium 
--- |-------------------|------------------------------|--------
 **Cost** | Free of charge | {{ sku|USD|support.organization.business.fixed_consumption.v1|string }} per month from the billing account selected at the time of service plan activation and 5% of the organization's resource consumption cost, regardless of which billing account the organization's resources are linked to. | Upon request


{% note info %}

* All prices are shown without VAT. The cost of support is calculated based on the [cost of paid resources consumed](../billing/pricing.md). If a billing account is awarded a [grant](../billing/concepts/bonus-account.md), it will be counted towards payment for the support plan.

{% endnote %}

### Basic {#base}

The basic service plan is provided to all {{ yandex-cloud }} users at no separate charge. It is suitable for personal and research projects.

### Business {#business}

This plan is good for business projects requiring 24/7 support.
The price is calculated based on the amount of resources consumed over the current reporting period (calendar month). To calculate the cost of using the service, use [our calculator](/prices#calculator) or see the calculation methods in the sections below.

#### Service plan cost {#business-price}




{% include [usd.md](../_pricing/support/usd-business-2023.md) %}


#### Example of calculating the cost for an organization whose resources are paid from a single billing account {#business-example-one-ba}




{% include [usd-support-one-ba](../_pricing_examples/support/usd-one-ba.md) %}


#### Example of calculating the cost for an organization whose resources are paid from two billing accounts {#business-example-two-ba}

If an organization with an activated support service plan uses resources paid from different billing accounts, the percentage part of the plan will be charged to each billing account according to the cost of consumed resources. The fixed part will be paid by only one account: the one specified when selecting the _Business_ service plan.




{% include [usd-support-two-ba](../_pricing_examples/support/usd-two-ba.md) %}



### Premium {#premium}

The [Premium plan](/support) covers all services of other plans and can be further enhanced to best suit your requirements. It may include troubleshooting recommendations for interactions of {{ yandex-cloud }} services with third-party software, consulting sessions with a dedicated support engineer based on your {{ yandex-cloud }} usage scenario, and services of a personal technical manager.

For a cost estimate for the _Premium_ plan, contact your {{ yandex-cloud }} manager or [technical support]({{ link-console-support }}).


## How to change your service plan {#change-service-plan}

{% include [change-tariff](../_includes/support/change-pricing.md) %}