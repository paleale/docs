| Услуга | Цена до 01.05.2025<br>за ГБ в месяц,<br>вкл. НДС | Цена после 01.05.2025<br>за ГБ в месяц,<br>вкл. НДС |
| ----- | ---: | ---: |
| Хранилище на сетевых HDD-дисках                        | {{ sku|RUB|mdb.cluster.network-hdd.mongodb|month|string }}               | {% calc [currency=RUB] round(({{ sku|RUB|mdb.cluster.network-hdd.mongodb|month|number }} × 1.08) × 100) / 100 %} |
| Хранилище на нереплицируемых SSD-дисках^*^             | {{ sku|RUB|mdb.cluster.network-ssd-nonreplicated.mongodb|month|string }} | {% calc [currency=RUB] round(({{ sku|RUB|mdb.cluster.network-ssd-nonreplicated.mongodb|month|number }} × 1.08) × 100) / 100 %} |
| Хранилище на сетевых SSD-дисках                        | {{ sku|RUB|mdb.cluster.network-nvme.mongodb|month|string }}              | {% calc [currency=RUB] round(({{ sku|RUB|mdb.cluster.network-nvme.mongodb|month|number }} × 1.08) × 100) / 100 %} |
| Сверхбыстрое сетевое хранилище с тремя репликами (SSD) | {{ sku|RUB|mdb.cluster.network-ssd-io-m3.mongodb|month|string }}         | {% calc [currency=RUB] round(({{ sku|RUB|mdb.cluster.network-ssd-io-m3.mongodb|month|number }} × 1.08) × 100) / 100 %} |
| Хранилище на локальных SSD-дисках^*^                   | {{ sku|RUB|mdb.cluster.local-nvme.mongodb|month|string }}                | {% calc [currency=RUB] round(({{ sku|RUB|mdb.cluster.local-nvme.mongodb|month|number }} × 1.08) × 100) / 100 %} |
| Резервные копии сверх размера хранилища                | {{ sku|RUB|mdb.cluster.mongodb.backup|month|string }}                    | {% calc [currency=RUB] round(({{ sku|RUB|mdb.cluster.mongodb.backup|month|number }} × 1.08) × 100) / 100 %} |

{% include [storage-limitations-mmg](../../_includes/mdb/mmg/storage-limitations-note.md) %}
