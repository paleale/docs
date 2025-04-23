---
title: Использование SDK для функции на Java в {{ sf-full-name }}. Обзор
description: Вы можете управлять функцией {{ sf-name }} на Java с помощью SDK.
---

# Использование SDK для функции на Java

[Среда выполнения](../../concepts/runtime/index.md) не содержит предустановленной библиотеки для работы с [API {{ yandex-cloud }}](../../../api-design-guide/). Для ее использования требуется добавить [зависимость](dependencies.md) в приложение на Java. Исходный код библиотеки находится на [GitHub](https://github.com/yandex-cloud/java-sdk). Доступность модулей библиотеки зависит от версии языка:

* `java-sdk-functions` — только для Java 11.
* `java-sdk-serverless` — для Java 17 и выше.

Другие модули доступны для любой версии Java.

[SDK (Software Development Kit)](https://ru.wikipedia.org/wiki/SDK) позволяет управлять ресурсами {{ yandex-cloud }} от имени [сервисного аккаунта](../../operations/function-sa.md), который указан в параметрах функции.

## Пример {#example}

Следующая функция получает на вход идентификатор каталога (`folderId`), авторизуется в SDK, получает список всех виртуальных машин {{ compute-name }} в заданном каталоге и запускает остановленные машины. В качестве результата возвращает сообщение-отчет с количеством запущенных машин.

{% note warning %}

Функцию необходимо вызывать с помощью [{{ yandex-cloud }} CLI](../../concepts/function-invoke.md) или с помощью HTTP-запроса с параметром `?integration=raw`.

{% endnote %}

```java
import yandex.cloud.api.compute.v1.InstanceOuterClass;
import yandex.cloud.api.compute.v1.InstanceServiceGrpc;
import yandex.cloud.api.compute.v1.InstanceServiceOuterClass;
import yandex.cloud.sdk.ServiceFactory;
import yandex.cloud.sdk.auth.Auth;

import java.util.function.Function;

public class Handler implements Function<String, String> {
  @Override
  public String apply(String folderId) {
    // Авторизация в SDK через сервисный аккаунт
    var defaultComputeEngine = Auth.computeEngineBuilder().build();
    var factory = ServiceFactory.builder()
            .credentialProvider(defaultComputeEngine)
            .build();
    var instanceService = factory.create(InstanceServiceGrpc.InstanceServiceBlockingStub.class, InstanceServiceGrpc::newBlockingStub);
    var listInstancesRequest = InstanceServiceOuterClass.ListInstancesRequest.newBuilder().setFolderId(folderId).build();
    // Получение списка ВМ по заданному в запросе folderId
    var listInstancesResponse = instanceService.list(listInstancesRequest);
    var instances = listInstancesResponse.getInstancesList();
    var count = 0;
    for (var instance : instances) {
      if (instance.getStatus() != InstanceOuterClass.Instance.Status.RUNNING) {
        var startInstanceRequest = InstanceServiceOuterClass.StartInstanceRequest.newBuilder().setInstanceId(instance.getId()).build();
        // Запуск ВМ с указанными в запросе идентификаторами ВМ
        var startInstanceResponse = instanceService.start(startInstanceRequest);
        if (!startInstanceResponse.hasError()) {
          count++;
        }
      }
    }
    return String.format("Started %d instances", count);
  }
}
```