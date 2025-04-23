---
title: Установка Velero
description: Следуя данной инструкции, вы сможете установить Velero.
---

# Установка Velero


[Velero](https://velero.io/) — это инструмент для резервного копирования, восстановления и миграции объектов [кластера {{ managed-k8s-name }}](../../concepts/index.md#kubernetes-cluster), в том числе [постоянных томов](../../concepts/volume.md#persistent-volume). Velero позволяет:
* Защитить данные от потери с помощью гибкой системы резервного копирования.
* Сократить время на восстановление кластера {{ managed-k8s-name }} в случае его недоступности.
* Перенести данные с одного кластера {{ managed-k8s-name }} на другой.

С помощью драйвера {{ CSI }} инструмент Velero [создает резервные копии](../../tutorials/kubernetes-backup.md) и восстанавливает постоянные тома из моментальных [снимков дисков](../../../compute/concepts/snapshot.md) {{ yandex-cloud }}.

## Перед началом работы {#before-you-begin}

1. {% include [cli-install](../../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../../_includes/default-catalogue.md) %}

1. [Создайте сервисный аккаунт](../../../iam/operations/sa/create.md), необходимый для доступа к [{{ objstorage-full-name }}](../../../storage/):

   ```bash
   yc iam service-account create --name <имя_сервисного_аккаунта>
   ```

1. [Назначьте сервисному аккаунту роль](../../../iam/operations/sa/assign-role-for-sa.md) `storage.editor`:

   ```bash
   yc resource-manager folder add-access-binding <идентификатор_каталога> \
     --role storage.editor \
     --subject serviceAccount:<идентификатор_сервисного_аккаунта>
   ```

1. [Создайте статический ключ доступа](../../../iam/operations/authentication/manage-access-keys.md#create-access-key) для [сервисного аккаунта](../../../iam/concepts/users/service-accounts.md).

   * Если установка Velero будет выполняться в [консоли управления с помощью {{ marketplace-full-name }}](#marketplace-install), создайте статический ключ в формате JSON и сохраните его в файл `sa-key.json`:

     ```bash
     yc iam access-key create \
       --service-account-name=<имя_сервисного_аккаунта> \
       --format=json > sa-key.json
     ```

   * Если установка Velero будет выполняться с помощью [Helm-чарта](#helm-install), выполните команду и сохраните полученные идентификатор ключа (`key_id`) и секретный ключ (`secret`):

     ```bash
     yc iam access-key create \
       --service-account-name=<имя_сервисного_аккаунта>
     ```

1. [Создайте бакет {{ objstorage-name }}](../../../storage/operations/buckets/create.md).

1. {% include [check-sg-prerequsites](../../../_includes/managed-kubernetes/security-groups/check-sg-prerequsites-lvl3.md) %}

    {% include [sg-common-warning](../../../_includes/managed-kubernetes/security-groups/sg-common-warning.md) %}

1. Убедитесь, что квот на количество снимков дисков и их объем достаточно для создания резервной копии данных. Для этого можно использовать [сервис проверки квот](../../../quota-manager/operations/read-quotas.md).

## Установка с помощью {{ marketplace-full-name }} {#marketplace-install}

1. Перейдите на [страницу каталога]({{ link-console-main }}) и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-kubernetes }}**.
1. Нажмите на имя нужного кластера {{ managed-k8s-name }} и выберите вкладку ![image](../../../_assets/console-icons/shopping-cart.svg) **{{ ui-key.yacloud.k8s.cluster.switch_marketplace }}**.
1. В разделе **{{ ui-key.yacloud.marketplace-v2.label_available-products }}** выберите [Velero](/marketplace/products/yc/velero-yc-csi) и нажмите кнопку **{{ ui-key.yacloud.marketplace-v2.button_k8s-product-use }}**.
1. Задайте настройки приложения:
   * **Пространство имен** — создайте новое [пространство имен](../../concepts/index.md#namespace) `velero`. Приложение использует его по умолчанию. Если вы оставите пространство имен по умолчанию, Velero может работать некорректно.

     {% note info %}

     Если создать пространство с другим именем, при работе придется задавать его в каждой команде в параметре `--namespace <пространство_имен_приложения_Velero>`.

     {% endnote %}

   * **Название приложения** — укажите название приложения.
   * **Статический ключ для доступа к {{ objstorage-name }}** — скопируйте содержимое файла `sa-key.json` или создайте новый [ключ доступа](../../../iam/concepts/authorization/access-key.md) для сервисного аккаунта. Сервисный аккаунт должен иметь роль `storage.editor`.
   * **Имя бакета {{ objstorage-name }}** — укажите имя бакета {{ objstorage-name }}.
1. Нажмите кнопку **{{ ui-key.yacloud.k8s.cluster.marketplace.button_install }}**.
1. Дождитесь перехода приложения в статус `Deployed`.

## Установка с помощью Helm-чарта {#helm-install}

1. {% include [Установка Helm](../../../_includes/managed-kubernetes/helm-install.md) %}
1. {% include [Настройка kubectl](../../../_includes/managed-kubernetes/kubectl-install.md) %}
1. Для установки [Helm-чарта](https://helm.sh/docs/topics/charts/) с Velero выполните команду, указав в ней параметры ресурсов, созданных [ранее](#before-you-begin):

   ```bash
   helm pull oci://{{ mkt-k8s-key.yc_velero-yc-csi.helmChart.name }} \
        --version {{ mkt-k8s-key.yc_velero-yc-csi.helmChart.tag }} \
        --untar && \
   helm install \
        --namespace velero \
        --create-namespace \
        --set configuration.backupStorageLocation.bucket=<имя_бакета> \
        --set serviceaccountawskeyvalue_generated.accessKeyID=<идентификатор_ключа> \
        --set serviceaccountawskeyvalue_generated.secretAccessKey=<секретный_ключ> \
        velero ./velero/
   ```

   {% include [Support OCI](../../../_includes/managed-kubernetes/note-helm-experimental-oci.md) %}

## См. также {#see-also}

* [Документация Velero](https://velero.io/docs/v1.11/examples/)
* [Резервное копирование кластера {{ managed-k8s-name }} в {{ objstorage-name }}](../../tutorials/kubernetes-backup.md)
