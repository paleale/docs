---
title: Установка External Secrets Operator с поддержкой {{ lockbox-full-name }}
description: Следуя данной инструкции, вы сможете установить External Secrets Operator с поддержкой {{ lockbox-name }}.
---

# Установка External Secrets Operator с поддержкой {{ lockbox-name }}


[External Secrets Operator](/marketplace/products/yc/external-secrets) — оператор {{ k8s }}, который интегрирует внешние системы управления секретами, такие как [{{ lockbox-name }}](../../../lockbox/), AWS Secrets Manager, Azure Key Vault, HashiCorp Vault, Google Secrets Manager и другие. Оператор считывает информацию из внешних [API](../../../glossary/rest-api.md) и автоматически вводит значения в {{ k8s }} Secret.

External Secrets Operator с поддержкой {{ lockbox-name }} позволяет настроить синхронизацию [секретов {{ lockbox-name }}](../../../lockbox/concepts/secret.md) с [секретами](../../concepts/encryption.md) [кластера {{ managed-k8s-name }}](../../concepts/index.md#kubernetes-cluster).

## Перед началом работы {#before-you-begin}

1. {% include [cli-install](../../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../../_includes/default-catalogue.md) %}

1. [Создайте сервисный аккаунт](../../../iam/operations/sa/create.md), необходимый для работы External Secrets Operator.
1. Назначьте [сервисному аккаунту](../../../iam/concepts/users/service-accounts.md) необходимую [роль](../../../lockbox/security/index.md#service-roles):
   * [Для ранее созданного секрета](../../../lockbox/operations/secret-access.md).
   * [Для всех секретов](../../../iam/operations/sa/assign-role-for-sa.md) [каталога](../../../resource-manager/concepts/resources-hierarchy.md#folder) или [облака](../../../resource-manager/concepts/resources-hierarchy.md#cloud).
1. Создайте для сервисного аккаунта [авторизованный ключ](../../../iam/concepts/authorization/key.md) и сохраните его в файл `sa-key.json`:

   ```bash
   yc iam key create \
     --service-account-name <имя_сервисного_аккаунта> \
     --output sa-key.json
   ```

1. {% include [check-sg-prerequsites](../../../_includes/managed-kubernetes/security-groups/check-sg-prerequsites-lvl3.md) %}

    {% include [sg-common-warning](../../../_includes/managed-kubernetes/security-groups/sg-common-warning.md) %}

## Установка External Secrets Operator с помощью {{ marketplace-full-name }} {#marketplace-install}

1. Перейдите на [страницу каталога]({{ link-console-main }}) и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-kubernetes }}**.
1. Нажмите на имя нужного кластера {{ managed-k8s-name }} и выберите вкладку ![image](../../../_assets/console-icons/shopping-cart.svg) **{{ ui-key.yacloud.k8s.cluster.switch_marketplace }}**.
1. В разделе **{{ ui-key.yacloud.marketplace-v2.label_available-products }}** выберите [External Secrets Operator с поддержкой {{ lockbox-name }}](/marketplace/products/yc/external-secrets) и нажмите кнопку **{{ ui-key.yacloud.marketplace-v2.button_k8s-product-use }}**.
1. Задайте настройки приложения:
   * **Пространство имен** — создайте новое [пространство имен](../../concepts/index.md#namespace) (например, `external-secrets-operator-space`). Если вы оставите пространство имен по умолчанию, External Secrets Operator может работать некорректно.
   * **Название приложения** — укажите название приложения.
   * **Ключ сервисной учетной записи** — вставьте содержимое файла `sa-key.json`.
1. Нажмите кнопку **{{ ui-key.yacloud.k8s.cluster.marketplace.button_install }}**.
1. Дождитесь перехода приложения в статус `Deployed`.

## Установка с помощью Helm-чарта {#helm-install}

1. {% include [Установка Helm](../../../_includes/managed-kubernetes/helm-install.md) %}
1. {% include [Install and configure kubectl](../../../_includes/managed-kubernetes/kubectl-install.md) %}
1. Для установки [Helm-чарта](https://helm.sh/docs/topics/charts/) с приложением External Secrets Operator выполните команду:

   ```bash
   helm pull oci://{{ mkt-k8s-key.yc_external-secrets.helmChart.name }} \
     --version {{ mkt-k8s-key.yc_external-secrets.helmChart.tag }} \
     --untar && \
   helm install \
     --namespace <пространство_имен> \
     --create-namespace \
     --set-file auth.json=<путь_к_файлу_sa-key.json> \
     external-secrets ./external-secrets/
   ```

   Эта команда создаст новое пространство имен, необходимое для работы External Secrets Operator.

   Если вы укажете в параметре `namespace` пространство имен по умолчанию, External Secrets Operator может работать некорректно. Рекомендуем указывать значение, отличное от всех существующих пространств имен (например, `external-secrets-operator-space`).

   {% include [Support OCI](../../../_includes/managed-kubernetes/note-helm-experimental-oci.md) %}

## Примеры использования {#examples}

* [{#T}](../../tutorials/kubernetes-lockbox-secrets.md).

## См. также {#see-also}

* [Описание External Secrets Operator](https://external-secrets.io/v0.8.1/provider/yandex-lockbox/).
* [Документация {{ lockbox-name }}](../../../lockbox/).
