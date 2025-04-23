---
title: Management event reference {{ at-full-name }}
description: This section provides descriptions of management events collected in {{ yandex-cloud }} services.
---

# Management event reference

The value of the `event_type` field in a management event audit log is determined by the event source service.

The general value format is as follows:

```text
{{ at-event-prefix }}.audit.<service_name>.<event_name>
```

On this page, you will find events for the following services:

{% include [cp-events-service-list](../../_includes/audit-trails/cp-events-service-list.md) %}

## {{ api-gw-full-name }} {#api-gateway}

Service name: `serverless.apigateway`.

{% include [api-gw-events](../../_includes/audit-trails/events/api-gw-events.md) %}

## {{ alb-full-name }} {#alb}

Service name: `apploadbalancer`.

{% include [alb-events](../../_includes/audit-trails/events/alb-events.md) %}

## {{ at-full-name }} {#audit-trails}

Service name: `audittrails`.

Event name | Description
--- | ---
`CreateTrail` | Creating a trail
`DeleteTrail` | Deleting a trail
`SetTrailAccessBindings` | Assigning access permissions for a trail
`UpdateTrail` | Editing a trail
`UpdateTrailAccessBindings` | Updating access permissions for a trail

## {{ baremetal-full-name }} {#baremetal}

Service name: `baremetal`.

{% include [baremetal-events](../../_includes/audit-trails/events/baremetal-events.md) %}

## {{ certificate-manager-full-name }} {#certificate-manager}

Service name: `certificatemanager`.

{% include [cm-events](../../_includes/audit-trails/events/cm-events.md) %}

## {{ cloud-apps-full-name }} {#cloud-apps}

Service name: `cloudapps`.

{% include [cloud-apps-events](../../_includes/audit-trails/events/cloud-apps-events.md) %}

## {{ backup-full-name }} {#backup}

Service name: `backup`.

{% include [backup-events](../../_includes/audit-trails/events/backup-events.md) %}

## {{ billing-name }} {#billing}

Service name: `billing`.

{% include [billing-events](../../_includes/audit-trails/events/billing-events.md) %}

## {{ cdn-full-name }} {#cdn}

Service name: `cdn`.

{% include [cdn-events](../../_includes/audit-trails/events/cdn-events.md) %}

## {{ cloud-desktop-full-name }} {#clouddesktop}

Service name: `clouddesktop`.

{% include [clouddesktop-events](../../_includes/audit-trails/events/clouddesktop-events.md) %}

## {{ dns-full-name }} {#dns}

Service name: `dns`.

{% include [dns-events](../../_includes/audit-trails/events/dns-events.md) %}

## {{ sf-full-name }} {#functions}

Service name: `serverless`.

{% include [functions-events](../../_includes/audit-trails/events/functions-events.md) %}

## {{ cloud-logging-full-name }} {#cloud-logging}

Service name: `logging`.

{% include [logging-events](../../_includes/audit-trails/events/logging-events.md) %}

## {{ marketplace-full-name }} {#marketplace}

Service name: `marketplace.licensemanager`.

{% include [marketplace-events](../../_includes/audit-trails/events/marketplace-events.md) %}

## {{ org-full-name }} {#organization}

Service name: `organizationmanager`.

{% include [org-events](../../_includes/audit-trails/events/org-events.md) %}

## {{ postbox-full-name }} {#postbox}

Service name: `postbox`.

{% include [postbox-events](../../_includes/audit-trails/events/postbox-events.md) %}

## {{ cloud-registry-full-name }} {#cloud-registry}

Service name: `cloudregistry`.

{% include [cloudregistry-events](../../_includes/audit-trails/events/cloudregistry-events.md) %}

## {{ video-full-name }} {#video}

Service name: `video`.

{% include [video-events](../../_includes/audit-trails/events/video-events.md) %}

## {{ compute-full-name }} {#compute}

Service name: `compute`.

{% include [compute-events](../../_includes/audit-trails/events/compute-events.md) %}

## {{ container-registry-full-name }} {#container-registry}

Service name: `containerregistry`.

{% include [container-registry-events](../../_includes/audit-trails/events/container-registry-events.md) %}

## {{ dataproc-full-name }} {#dataproc}

Service name: `dataproc`.

{% include [dataproc-events](../../_includes/audit-trails/events/data-processing-events.md) %}

## {{ data-transfer-full-name }} {#datatransfer}

Service name: `datatransfer`.

{% include [datatransfer-events](../../_includes/audit-trails/events/datatransfer-events.md) %}

## {{ datalens-full-name }} {#datalens}

Service name: `datalens`.

{% include [datalens-events](../../_includes/audit-trails/events/datalens-events.md) %}

## {{ ml-platform-full-name }} {#datasphere}

Service name: `datasphere`.

{% include [ds-events](../../_includes/audit-trails/events/ds-events.md) %}

## {{ iam-full-name }} {#iam}

Service name: `iam`.

{% include [iam-events](../../_includes/audit-trails/events/iam-events.md) %}

## {{ iot-full-name }} {#iot}

Service name: `iot`.

{% include [iot-events](../../_includes/audit-trails/events/iot-events.md) %}

## {{ kms-full-name }} {#kms}

Service name: `kms`.

{% include [kms-events](../../_includes/audit-trails/events/kms-events.md) %}

## {{ load-testing-full-name }} {#loadtesting}

Service name: `loadtesting`.

{% include [load-testing-events](../../_includes/audit-trails/events/load-testing-events.md) %}

## {{ lockbox-full-name }} {#lockbox}

Service name: `lockbox`.

{% include [lockbox-events](../../_includes/audit-trails/events/lockbox-events.md) %}

## {{ maf-full-name }} {#managed-service-for-airflow}

Service name: `airflow`.

{% include [airflow-events](../../_includes/audit-trails/events/managed-airflow-events.md) %}

## {{ mkf-full-name }} {#managed-service-for-kafka}

Service name: `mdb.kafka`.

{% include [managed-kafka-events](../../_includes/audit-trails/events/managed-kafka-events.md) %}

## {{ mch-full-name }} {#managed-service-for-clickhouse}

Service name: `mdb.clickhouse`.

{% include [mch-events](../../_includes/audit-trails/events/mch-events.md) %}


## {{ mgl-full-name }} {#managed-service-for-gitlab}

Service name: `gitlab`.

{% include [mgl-events](../../_includes/audit-trails/events/mgl-events.md) %}

## {{ mgp-full-name }} {#managed-service-for-greenplum}

Service name: `mdb.greenplum`.

{% include [mgp-events](../../_includes/audit-trails/events/managed-greenplum-events.md) %}

## {{ managed-k8s-full-name }} {#managed-service-for-kubernetes}

Service name: `k8s`.

{% include [managed-k8s-events](../../_includes/audit-trails/events/managed-k8s-events.md) %}

## {{ mmg-full-name }} {#managed-service-for-mongodb}

Service name: `mdb.mongodb`.

{% include [mongodb-events](../../_includes/audit-trails/events/mongodb-events.md) %}

## {{ mmy-full-name }} {#managed-service-for-mysql}

Service name: `mdb.mysql`.

{% include [mysql-events](../../_includes/audit-trails/events/mysql-events.md) %}

## {{ mos-full-name }} {#managed-service-for-opensearch}

Service name: `mdb.opensearch`.

{% include [mos-events](../../_includes/audit-trails/events/mos-events.md) %}

## {{ mpg-full-name }} {#managed-service-for-postgresql}

Service name: `mdb.postgresql`.

{% include [mpg-events](../../_includes/audit-trails/events/managed-postgresql-events.md) %}

## {{ mrd-full-name }} {#managed-service-for-redis}

Service name: `mdb.redis`.

{% include [mrd-events](../../_includes/audit-trails/events/mrd-events.md) %}

## {{ ydb-full-name }} {#ydb}

Service name: `ydb`.

{% include [ydb-events](../../_includes/audit-trails/events/ydb-events.md) %}

## {{ metadata-hub-full-name }} {#metadata-hub}

### {{ metastore-full-name }} {#hive-metastore}

Service name: `metastore`.

{% include [hive-metastore-events](../../_includes/audit-trails/events/hive-metastore-events.md) %}

## {{ network-load-balancer-full-name }} {#network-load-balancer}

Service name: `loadbalancer`.

{% include [network-load-balancer-events](../../_includes/audit-trails/events/network-load-balancer-events.md) %}

## {{ objstorage-full-name }} {#objstorage}

Service name: `storage`.

{% include [storage-events](../../_includes/audit-trails/events/storage-events.md) %}

## {{ yq-full-name }} {#yq}

Service name: `yq`.

{% include [yq-events](../../_includes/audit-trails/events/yq-events.md) %}

## {{ resmgr-full-name }} {#resmgr}

Service name: `resourcemanager`.

{% include [resmgr-events](../../_includes/audit-trails/events/resmgr-events.md) %}

## {{ search-api-full-name }} {#searchapi}

Service name: `searchapi`.

{% include [searchapi-events](../../_includes/audit-trails/events/searchapi-events.md) %}

## {{ serverless-containers-full-name }} {#serverless-containers}

Service name: `serverless.containers`.

{% include [serverless-containers-events](../../_includes/audit-trails/events/serverless-containers-events.md) %}

## {{ captcha-full-name }} {#smartcaptcha}

Service name: `smartcaptcha`.

{% include [captcha-events](../../_includes/audit-trails/events/captcha-events.md) %}

## {{ sws-full-name }} {#smartwebsecurity}

Service name: `smartwebsecurity`.

{% include [sws-events](../../_includes/audit-trails/events/sws-events.md) %}

## {{ speechsense-full-name }} {#speechsense}

Service name: `speechsense`.

{% include [org-events](../../_includes/audit-trails/events/speechsense-events.md) %}

## {{ vpc-full-name }} {#vpc}

Service name: `network`.

{% include [vpc-events](../../_includes/audit-trails/events/vpc-events.md) %}

## {{ websql-full-name }} {#websql}

Service name: `websql`.

{% include [websql-events](../../_includes/audit-trails/events/websql-events.md) %}

{% include [clickhouse-disclaimer](../../_includes/clickhouse-disclaimer.md) %}
