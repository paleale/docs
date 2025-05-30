# Тестирующие потоки

[Генератор нагрузки](load-generator.md) создает параллельные _тестирующие потоки_. Действия внутри потоков выполняются последовательно: после создания запроса ожидается ответ. Поток, внутри которого ожидается ответ, недоступен для новых запросов. Чтобы обеспечить нужную нагрузку, генератор использует следующие потоки.

Отслеживать занятость потоков можно по графику в [отчете](reports.md).

Максимальное количество запросов в секунду зависит от количества тестирующих потоков и общего времени ответов на эти запросы в эту секунду:

   ```text
   RPS_max = количество тестирующих потоков / (общее время ответов в эту секунду)
   ```

   Где `RPS_max` — максимальное количество запросов в секунду.

По умолчанию Phantom создает 1000 тестирующих потоков.

Pandora создает 1000 тестирующих потоков, если при настройке конфигурации через форму не было заполнено поле **Тестирующие потоки**. Но если настройка выполнялась через файл конфигурации и количество тестирующих потоков не было указано, то Pandora завершит работу с ошибкой.

Если нужно задать количество потоков, настройте их конфигурацию в [консоли управления]({{ link-console-main }}).

Посчитать необходимое количество тестирующих потоков можно по формуле:

   ```text
   Количество тестирующих потоков = RPS × (среднее время ответа в секундах)
   ```

   Где `RPS` — количество запросов в секунду.
   
> Например, чтобы обеспечить нагрузку в 200 запросов в секунду при среднем времени ответа 50 мс, нужно создать 10 тестирующих потоков.

## Пример файла конфигурации {#config_example}

Пример настройки тестирующих потоков для генерации 200 запросов в секунду в течение 5 минут:

{% list tabs group=load_generator %}

- Pandora {#pandora}

    ```yaml
	pandora:
      enabled: true
      package: yandextank.plugins.Pandora
      config_content:
        pools:
          - id: HTTP
            gun:
              type: http
              target: localhost:80
            ammo:
              file: /tmp/ammofile
              type: uri
            result:
              type: phout
              destination: ./phout.log
            startup:
              type: once
              times: 10         # количество потоков
            rps:
              - duration: 300s  # время теста
                type: const     # тип нагрузки
                ops: 200        # количество запросов в секунду
        log:
          level: error
        monitoring:
          expvar:
            enabled: true
            port: 1234
	```

- Phantom {#phantom}

    ```yaml
	phantom:
      enabled: true
      package: yandextank.plugins.Phantom
      address: localhost:80
      ammo_type: uri
      instances: 10             # количество потоков
      load_profile:
        load_type: rps
        schedule: const(200,5m) # расписание нагрузки: 200 запросов в секунду в течение 5 минут
      uris: ['/']
	```

{% endlist %}

## Примеры использования {#examples}

* [{#T}](../tutorials/loadtesting-grpc.md)
* [{#T}](../tutorials/loadtesting-https-pandora.md)
* [{#T}](../tutorials/loadtesting-https-phantom.md)
* [{#T}](../tutorials/loadtesting-results-compare.md)
* [{#T}](../tutorials/loadtesting-http-scenario-pandora.md)
