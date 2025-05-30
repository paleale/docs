# Миграция ресурсов в другую зону

Ряду ресурсов {{ compute-name }} и {{ vpc-name }} в CLI добавлена команда `relocate`, которая позволяет переместить ресурс в другую зону. В случае переноса групп ВМ, ресурсов сервисов {{ network-load-balancer-name }} и {{ alb-name }}, управляемых сервисов баз данных, инстансов {{ mgl-name }}, кластеров {{ managed-k8s-name }} и сервисов Serverless воспользуйтесь существующими инструментами.

Если среди сервисов, которые вы используете, есть {{ objstorage-name }}, {{ cdn-name }}, {{ dns-name }} и другие, не указанные ниже — мигрировать их ресурсы не требуется.

## Рекомендуемый порядок миграции {#migration-best-practices} 

1. Во всех сетях [создайте новую подсеть](../../vpc/operations/subnet-create.md) в зоне `{{ region-id }}-d`. 
1. (опционально) Если вы используете сервис {{ interconnect-name }}, обратитесь в [техническую поддержку]({{ link-console-support }}), чтобы настроить работу с новой подсетью. Для завершения настройки подсети необходимо создать и подключить к ней любой ресурс (например, виртуальную машину), чтобы маршрутная информация, связанная с новой подсетью, корректно анонсировалась в сервисе {{ interconnect-name }}.
1. Перенесите ваши ресурсы в новую зону доступности: 
    1. [Виртуальные машины](#compute) (по отдельности или с помощью расширения группы ВМ). 
    1. [Хосты баз данных](#mdb).
    1. (опционально) [Перезапустите](../../data-transfer/operations/transfer.md) привязанные трансферы {{ data-transfer-name }}.
    1. [Мастера и группы узлов {{ managed-k8s-name }}](../../managed-kubernetes/tutorials/migration-to-an-availability-zone.md).
1. Если вы использовали [сетевые](../../network-load-balancer/operations/load-balancer-change-zone.md) и [L7-балансировщики](../../application-load-balancer/operations/application-load-balancer-relocate.md), добавьте перемещенные ресурсы в их целевые группы. Включите прием трафика в новой зоне у L7-балансировщиков.

## Инструменты миграции {#migration-tools}

### {{ compute-name }} {#compute}

Мы рекомендуем мигрировать [виртуальные машины](../../compute/operations/vm-control/vm-change-zone.md) и [диски](../../compute/operations/disk-control/disk-change-zone.md) с помощью [снимков](../../compute/operations/disk-control/create-snapshot.md) или [сервиса {{ backup-name }}](../../backup/).

Так вы сможете сами контролировать ход миграции. Вы определяете, в какой момент выключать ВМ в исходной зоне доступности, и в какой момент она появится в конечной зоне доступности. ВМ в исходной зоне доступности может продолжать работать, пока вы не убедитесь, что созданная из снимка ВМ в новой зоне доступности работает корректно. 

Если снимки не подходят вам в качестве инструмента миграции, ВМ и диски можно мигрировать с помощью команд `yc compute disk relocate` или `yc compute instance relocate`. В таком случае все равно сделайте снимок или создайте резервную копию в {{ backup-name }} перед выполнением команд — в ходе миграции у вашей ВМ изменится сетевое окружение, что может повлиять на ее работоспособность. Если в ходе миграции что-то пойдет не так, у вас будет снимок или резервная копия, из которых вы сможете оперативно восстановить вашу ВМ в исходной зоне доступности и попробовать мигрировать ее еще раз. После выполнения команды `relocate` снимки и копии можно будет удалить. 

{% note warning %}

Сейчас миграция ВМ и дисков с помощью команды `relocate` возможна только в зону `{{ region-id }}-d` из любой другой зоны.

{% endnote %}

Обратите внимание, что ВМ с подключенными [файловыми хранилищами](../../compute/concepts/filesystem.md) мигрировать нельзя.

[Группы ВМ](../../compute/operations/instance-groups/move-group.md) можно расширить, добавляя ВМ в новую зону доступности. ВМ из старой зоны доступности после этого можно удалить.

Миграция [групп ВМ с балансировщиками](../../compute/operations/instance-groups/move-group-with-nlb.md) происходит в зависимости от типа используемого балансировщика. 

В группу с внешним сетевым балансировщиком достаточно добавить ВМ в новой зоне доступности. В группах с внутренним балансировщиком потребуется указать новый обработчик в подсети в новой зоне доступности или мигрировать подсеть в новую зону.

Если в группе есть [L7-балансировщик](../../compute/operations/instance-groups/move-group-with-alb.md), нужно включить прием трафика в новой зоне доступности и добавить ВМ в целевую группу.

### Управляемые сервисы баз данных {#mdb}

В большинстве случаев для миграции хостов управляемых сервисов баз данных нужно создать хост в новой зоне доступности, добавить его в кластер и указать FQDN нового хоста на бэкенде или клиенте. 

См. инструкции по миграции для конкретных сервисов:

* [{{ dataproc-name }}](../../data-proc/operations/migration-to-an-availability-zone.md).
* [{{ dataproc-name }} с файловой системой HDFS](../../data-proc/tutorials/hdfs-cluster-migration.md).
* [{{ mkf-name }}](../../managed-kafka/operations/host-migration.md).
* [{{ mch-name }}](../../managed-clickhouse/operations/host-migration.md).
* [{{ mmg-name }}](../../managed-mongodb/operations/host-migration.md).
* [{{ mmy-name }}](../../managed-mysql/operations/host-migration.md).
* [{{ mos-name }}](../../managed-opensearch/operations/host-migration.md).
* [{{ mpg-name }}](../../managed-postgresql/operations/host-migration.md).
* [{{ mrd-name }}](../../managed-redis/operations/host-migration.md).
* [{{ ydb-name }}](../../ydb/operations/migration-to-an-availability-zone.md).
* {{ mgp-name }} — для миграции нужно восстановить кластер из [резервной копии](../../managed-greenplum/operations/cluster-backups.md).

### {{ data-transfer-name }} {#data-transfer}

Если вы добавили в кластер с настроенным {{ data-transfer-name }} новый хост в зоне `{{ region-id }}-d`, для [продолжения работы трансфера](../../data-transfer/operations/endpoint/migration-to-an-availability-zone.md) нужно:

1. Чтобы в кластере был хотя бы один хост вне зоны `{{ region-id }}-d`.
1. После изменения кластера перезапустить трансферы, находившиеся в состоянии `Running`, либо изменить настройки трансфера или его эндпоинтов — трансфер перезапустится и получит новую топологию кластера.

### {{ managed-k8s-name }} {#k8s}

Чтобы мигрировать кластер {{ managed-k8s-name }} из одной зоны доступности в другую:

* [Перенесите мастер](../../managed-kubernetes/tutorials/migration-to-an-availability-zone.md#transfer-a-master).
* [Перенесите группу узлов и рабочую нагрузку в подах](../../managed-kubernetes/tutorials/migration-to-an-availability-zone.md#transfer-a-node-group).

### {{ network-load-balancer-name }} {#nlb}

При использовании сетевых балансировщиков без групп ВМ нужно [перенести ВМ в новую зону доступности](../../network-load-balancer/operations/load-balancer-change-zone.md) и добавить ее в целевую группу балансировщика.

### {{ alb-name }} {#alb}

Для переноса ВМ, подключенной к L7-балансировщику, нужно включить прием трафика в новой зоне доступности, [перенести ВМ и добавить их в целевую группу](../../application-load-balancer/operations/application-load-balancer-relocate.md).

### {{ vpc-name }} {#vpc}

Миграция подсетей позволяет сохранить адресацию и настроенные IP-адреса обработчиков внутренних балансировщиков. Обратите внимание, что переместить можно только пустые подсети, к которым не подключены никакие ресурсы: ВМ, хосты БД, узлы {{ managed-k8s-name }} и другие.

Подсети можно [мигрировать](../../vpc/operations/subnet-relocate.md) с помощью команды `relocate`.

#### Миграция IP-адресов {#ip-addresses}

Перенос публичных IP-адресов между зонами невозможен. Чтобы сохранить публичный адрес для входящего трафика, [зарезервируйте](../../vpc/operations/get-static-ip.md) этот адрес и затем назначьте его обработчику сетевого балансировщика. Далее можно перенести ВМ и подключить ее к сетевому балансировщику.

Обратите внимание: таким образом можно сохранить IP-адрес только для входящего трафика. Например, если на IP-адрес ВМ выдана лицензия, вы не сможете использовать публичный IP-адрес балансировщика для ее проверки.

Если вам нужен IP-адрес с открытым портом `25` в новой зоне, заранее закажите новый такой адрес через поддержку. 

### {{ api-gw-name }}, {{ sf-name }}, {{ serverless-containers-name }} {#serverless}

Для миграции функций, контейнеров и API-шлюзов нужно создать подсеть в новой зоне доступности.

* [Функции](../../functions/operations/function/migration.md)
* [Контейнеры](../../serverless-containers/operations/migration.md)
* [API-шлюзы](../../api-gateway/operations/api-gw-migration.md)

### {{ mgl-name }} {#gitlab}

Чтобы изменить зону доступности инстанса {{ mgl-name }}, воспользуйтесь инструкцией в разделе [Миграция инстанса в другую зону доступности](../../managed-gitlab/operations/instance/zone-migration.md).

### {{ cloud-desktop-name }} {#cloud-desktop}

Если на рабочем столе нет ценной информации, то достаточно [создать новый рабочий стол](../../cloud-desktop/operations/desktops/create.md) и [удалить старый](../../cloud-desktop/operations/desktops/delete.md). Если ценная информация есть, то из старого рабочего стола нужно [создать образ](../../cloud-desktop/operations/images/create-from-desktop.md) и создать новый рабочий стол из образа.
