---
title: Правила тарификации для {{ mrd-full-name }}
description: В статье содержатся правила тарификации сервиса {{ mrd-name }}.
editable: false
---

# Правила тарификации для {{ mrd-name }}

В этом разделе описаны [правила](#rules), по которым тарифицируется использование сервиса {{ mrd-name }}, и представлены [актуальные цены](#prices) на предоставляемые им ресурсы.

{% note tip %}


Чтобы рассчитать стоимость использования сервиса, воспользуйтесь [калькулятором](https://yandex.cloud/ru/prices?state=26441efb181e#calculator) на сайте {{ yandex-cloud }} или ознакомьтесь с тарифами в этом разделе.





{% endnote %}

{% include [link-to-price-list](../_includes/pricing/link-to-price-list.md) %}

{% include [currency-choice](../_includes/pricing/currency-choice.md) %}

{% include [pricing-status.md](../_includes/mdb/pricing-status.md) %}

## Из чего складывается стоимость использования {{ mrd-short-name }} {#rules}

Расчет стоимости использования {{ mrd-name }} учитывает:

{% include [pricing-rules](../_includes/mdb/pricing-rules.md) %}

{% include [pricing-gb-size](../_includes/pricing-gb-size.md) %}

### Использование хостов БД {#rules-hosts-uptime}

Стоимость начисляется за каждый час работы хоста в соответствии с выделенными для него вычислительными ресурсами. Поддерживаемые конфигурации ресурсов приведены в разделе [Классы хостов](concepts/instance-types.md), цены за использование vCPU и RAM — в разделе [Цены](#prices).

Минимальная единица тарификации — минута (например, стоимость 1,5 минут работы хоста равна стоимости 2 минут). Время, когда хост {{ VLK }} не может выполнять свои основные функции, не тарифицируется.

### Использование дискового пространства {#rules-storage}

Оплачивается:

* Размер хранилища, выделенный для кластеров БД.

    * Хранилище на нереплицируемых SSD-дисках (`network-ssd-nonreplicated`) можно заказывать только для кластеров на платформах Intel Cascade Lake и Intel Ice Lake с тремя или более хостами, с шагом 93 ГБ.

    * Хранилище на локальных SSD-дисках (`local-ssd`) можно заказывать только для кластеров с тремя хостами и более:
                * для платформ **Intel Broadwell** и **Intel Cascade Lake** — с шагом 100 ГБ;
        * для платформы **Intel Ice Lake** — с шагом {{ local-ssd-v3-step }}.

    Подробнее об ограничениях хранилища, связанных с платформой, см. в разделе [Типы дисков](./concepts/storage.md).

* Размер, занимаемый резервными копиями баз данных сверх запрошенного размера хранилища для кластера.

    * Резервные копии хранятся бесплатно, пока сумма размера БД и всех резервных копий остается меньше выбранного размера хранилища.
    * Запрошенный размер хранилища выделяется для каждого хоста, поэтому количество хостов кластера не влияет на бесплатный объем резервных копий.

Цена указывается за 1 месяц использования и формируется из расчета 720 часов в месяц. Минимальная единица тарификации — 1 ГБ в минуту (например, стоимость хранения 1 ГБ в течение 1,5 минут равна стоимости хранения в течение 2 минут).

### Пример расчета стоимости кластера {#example}

Стоимость использования кластера со следующими параметрами в течение 30 дней:

* **Хосты {{ VLK }}**: 3 хоста класса `hm3-c2-m8`: Intel Ice Lake, 2 × 100% vCPU, 8 ГБ RAM.
* **{{ ui-key.yacloud.mdb.forms.section_storage }}**: 100 ГБ на сетевых SSD-дисках.

Расчет стоимости для хостов {{ VLK }}:


{% list tabs group=pricing %}

- Расчет в рублях {#prices-rub}

  {% include [rub-redis-host](../_pricing_examples/managed-redis/rub-host.md) %}

- Расчет в тенге {#prices-kzt}

  {% include [kzt-redis-host](../_pricing_examples/managed-redis/kzt-host.md) %}

{% endlist %}




Расчет стоимости хранилища и итоговой стоимости:


{% list tabs group=pricing %}

- Расчет в рублях {#prices-rub}

  {% include [rub-redis-storage](../_pricing_examples/managed-redis/rub-storage.md) %}

- Расчет в тенге {#prices-kzt}

  {% include [kzt-redis-storage](../_pricing_examples/managed-redis/kzt-storage.md) %}

{% endlist %}





## Скидка за резервируемый объем ресурсов (CVoS) {#cvos}

{% include [cvos](../_includes/mdb/cvos.md) %}

Сервис {{ mrd-name }} предоставляет CVoS двух видов: на vCPU и RAM для хостов, которые вы планируете использовать в кластерах БД. В консоли управления вы можете увидеть потенциальную экономию для текущего потребления ресурсов при переводе их на схему CVoS, а также предварительно рассчитать месячные платежи для нужного количества ядер процессора и оперативной памяти.

{% note info %}

По схеме CVoS можно заказать только ресурсы определенного вида: для недоступных видов ресурсов в колонках CVoS в разделе [Цены](#prices) стоят прочерки. Размер хранилища и интернет-трафика заказать таким образом пока невозможно.

{% endnote %}

## Цены для региона Россия {#prices}


{% note warning %}

С 1 мая 2025 года увеличатся цены на ресурсы {{ mrd-full-name }} в регионе Россия. Новые цены можно посмотреть на сайте:

* [Цены в рублях](https://yandex.cloud/ru/price-list?installationCode=ru&currency=RUB&services=dn2hb3vlkb6qfih0pgv6)
* [Цены в тенге](https://yandex.cloud/ru/price-list?installationCode=ru&currency=KZT&services=dn2hb3vlkb6qfih0pgv6)

{% endnote %}






{% include [pricing-diff-regions](../_includes/pricing-diff-regions.md) %}



Все цены указаны с включением НДС.



{% include [pricing-month-term](../_includes/mdb/pricing-month-term.md) %}

### Вычислительные ресурсы хостов {#prices-hosts}


{% include [Доступ к Compute Optimized по запросу](../_includes/mdb/note-compute-optimized-request.md) %}



#### Цены в час {#host-price-per-hour}


{% list tabs group=pricing %}

- Цены в рублях {#prices-rub}

  {% include [rub-host-hourly](../_pricing/managed-redis/rub-host-hourly.md) %}

- Цены в тенге {#prices-kzt}

  {% include [kzt-host-hourly](../_pricing/managed-redis/kzt-host-hourly.md) %}

{% endlist %}



#### Цены в месяц {#host-price-per-month}


{% list tabs group=pricing %}

- Цены в рублях {#prices-rub}

  {% include [rub-host-monthly](../_pricing/managed-redis/rub-host-monthly.md) %}

- Цены в тенге {#prices-kzt}

  {% include [kzt-host-monthly](../_pricing/managed-redis/kzt-host-monthly.md) %}

{% endlist %}





### Хранилище и резервные копии {#prices-storage}


{% note info %}

Доступ к сверхбыстрому сетевому хранилищу с тремя репликами (SSD) предоставляется по запросу. Обратитесь в [техническую поддержку]({{ link-console-support }}) или к вашему аккаунт-менеджеру.

{% endnote %}



{% list tabs group=pricing %}

- Цены в рублях {#prices-rub}

  {% include [rub-storage.md](../_pricing/managed-redis/rub-storage.md) %}

- Цены в тенге {#prices-kzt}

  {% include [kzt-storage.md](../_pricing/managed-redis/kzt-storage.md) %}

{% endlist %}




{% include [storage-limitations-mrd](../_includes/mdb/mrd/storage-limitations-note.md) %}

{% include [egress-traffic-pricing](../_includes/egress-traffic-pricing.md) %}
