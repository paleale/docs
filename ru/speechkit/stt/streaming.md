# Потоковый режим распознавания речи

Потоковый режим позволяет одновременно отправлять аудио на распознавание и получать результаты распознавания в рамках одного соединения. Кроме того, в потоковом режиме вы можете получать промежуточные результаты распознавания, когда говорящий еще не закончил фразу. После паузы {{ speechkit-name }} вернет финальные результаты и начнет распознавание новой фразы.

> В таком режиме распознавания работают голосовые помощники и умные колонки. Когда вы активируете помощника, он начинает передавать речь на сервер для распознавания. Сервер обрабатывает данные и возвращает промежуточные и финальные результаты распознавания каждой фразы. Промежуточные результаты используются, чтобы показать ход распознавания, а после финальных результатов помощник выполняет действие, например включает музыку или звонит другому человеку.

{% note warning %}

Потоковый режим предназначен для распознавания аудио в режиме реального времени. Для распознавания уже записанного аудиофайла используйте [синхронный](request.md) или [асинхронный](transcribation.md) режим распознавания аудиозаписей.

{% endnote %}


## Ограничения потокового распознавания {#session-restrictions}


Потоковое распознавание {{ speechkit-name }} имеет ряд ограничений, которые нужно учитывать при создании приложения. Полный список действующих в {{ speechkit-name }} ограничений см. в разделе [{#T}](../concepts/limits.md).

|  | Потоковое распознавание |
|---|---------|
| **Сценарии использования** | Телефонные ассистенты и роботы </br> Виртуальные ассистенты |
| **Входные данные** | Голос в режиме реального времени |
| **Принцип работы** | Обмен сообщениями с сервером в рамках одного соединения |
| **Поддерживаемые API** | [gRPC v2](api/streaming-api.md) </br> [gRPC v3](../stt-v3/api-ref/grpc/index.md) |
| **Максимальная длительность аудиоданных** | {{ stt-streaming-audioLength }} |
| **Максимальный объем переданных данных** | {{ stt-streaming-fileSize }} |
| **Количество распознаваемых каналов** | 1 |

## Использование сервиса {#service-use}

Чтобы использовать сервис, создайте приложение, которое будет отправлять фрагменты аудио и обрабатывать ответ с результатами распознавания.

### Код интерфейса клиентского приложения {#create-client-app}

{{ speechkit-name }} имеет две версии API потокового распознавания: [API v3](../stt-v3/api-ref/grpc/) и [API v2](api/streaming-api.md). Для новых проектов мы рекомендуем использовать API v3.

Чтобы приложение могло обращаться к сервису, склонируйте репозиторий [{{ yandex-cloud }} API](https://github.com/yandex-cloud/cloudapi/) и сгенерируйте код интерфейса клиента для используемого языка программирования из файла спецификации [API v2](https://github.com/yandex-cloud/cloudapi/blob/master/yandex/cloud/ai/stt/v2/stt_service.proto) или [API v3](https://github.com/yandex-cloud/cloudapi/blob/master/yandex/cloud/ai/stt/v3/stt_service.proto).

Примеры клиентского приложения:

* [{#T}](api/streaming-examples-v3.md).
* [{#T}](api/microphone-streaming.md).
* [{#T}](api/streaming-examples.md).

Помимо этого в [документации gRPC](https://grpc.io/docs/tutorials/) вы можете найти подробные инструкции по генерации интерфейсов и реализации клиентских приложений для различных языков программирования.

{% note warning %}

При запросе результатов операции gRPC-клиенты по умолчанию ограничивают максимальный размер сообщения, который они могут принять в качестве ответа, — не более 4 МБ. Если ответ с результатами распознавания будет больше этого размера, то вы получите ошибку.

{% endnote %}

Чтобы получить ответ целиком, повысьте ограничение на максимальный размер сообщения:
* для Go используйте функцию [MaxCallRecvMsgSize](https://pkg.go.dev/google.golang.org/grpc#MaxCallRecvMsgSize);
* для C++ в [методе call](https://grpc.github.io/grpc/cpp/classgrpc_1_1internal_1_1_call.html#af04fabbdb53dea98da54c387364faf63) задайте значение `max_receive_message_size`.

### Аутентификация в сервисе {#auth}

В каждом запросе приложение должно передавать IAM-токен или API-ключ для аутентификации в сервисе, а также [идентификатор каталога](../../resource-manager/operations/folder/get-id.md), на который у аккаунта есть роль `{{ roles-speechkit-stt }}` или выше. Подробнее о необходимых разрешениях см. в разделе [Управление доступом](../security/index.md).

Для аутентификации приложения удобнее всего использовать сервисный аккаунт. При аутентификации с сервисным аккаунтом не передавайте идентификатор каталога в запросах — {{ speechkit-name }} использует каталог, в котором был создан сервисный аккаунт.

[Подробнее об аутентификации в {{ speechkit-name }}](../concepts/auth.md).

### Запрос распознавания {#requests}

Для распознавания речи приложение сначала должно отправить сообщение с настройками распознавания:

* для API v3 — сообщение [RecognizeStreaming](../stt-v3/api-ref/grpc/Recognizer/recognizeStreaming) с типом `session_options`.
* для API v2 — сообщение `StreamingRecognitionRequest` с типом [RecognitionConfig](api/streaming-api#specification-msg).

После настройки сессии сервер будет ожидать сообщений с аудиофрагментами (chunks) — отправляйте сообщение `RecognizeStreaming` с типом [session_options](../stt-v3/api-ref/grpc/Recognizer/recognizeStreaming) или сообщение `StreamingRecognitionRequest` с типом [audio_content](api/streaming-api#audio-msg) в API v2. При отправке сообщений учитывайте следующие рекомендации:

* Не отправляйте аудиофрагменты слишком часто или редко. Время между отправкой сообщений в сервис должно примерно совпадать с длительностью отправляемых аудиофрагментов, но не должно превышать 5 секунд. Например, каждые 400 мс отправляйте на распознавание 400 мс аудио.
* Максимальная длительность переданного аудио за всю сессию — {{ stt-streaming-audioLength }}.
* Максимальный размер переданных аудиоданных — {{ stt-streaming-fileSize }}.

Если в течение 5 секунд в сервис не отправлялись сообщения или достигнут лимит по длительности или размеру данных, сессия обрывается. Чтобы продолжить распознавание речи, надо заново установить соединение и отправить новое сообщение с настройками распознавания.

Не дожидаясь окончания потока сообщений с аудиофрагментами, {{ speechkit-name }} будет возвращать промежуточные результаты распознавания.

### Результат распознавания {#results}

В каждом сообщении с результатами распознавания ([StreamingResponse](../stt-v3/api-ref/grpc/Recognizer/recognizeStreaming#speechkit.stt.v3.StreamingResponse) или [StreamingRecognitionResponse](api/streaming-api.md#response)) сервер {{ speechkit-name }} возвращает один или несколько фрагментов речи, которые он успел распознать за этот промежуток (`chunks`). Для каждого фрагмента речи указывается список вариантов распознанного текста (`alternatives`).

Сервер {{ speechkit-name }} возвращает результаты распознавания с указанием их типа:

* `partial` — для промежуточных результатов;
* `final` — для окончательных результатов;
* `final_refinement` — для [нормализованных](normalization.md) окончательных результатов.

   При включенной нормализации приходят результаты `final` и `final_refinement`.

В API v2, если распознавание еще не завершилось, результаты содержат параметр `final` со значением `False`.

Распознавание речи завершается, и приходят окончательные результаты, когда наступает [EOU (End-of-Utterance)](eou.md). Он является признаком конца фразы. EOU происходит в следующих случаях:

* Завершилась gRPC-сессия.
* Была распознана тишина в последнем фрагменте речи. Тишину можно передать с помощью одного из [двух параметров](../stt-v3/api-ref/grpc/Recognizer/recognizeStreaming.md#speechkit.stt.v3.StreamingRequest):

   * `chunk` — звук, который распознается как тишина.
   * `silence_chunk` — длительность тишины в миллисекундах. Параметр позволяет уменьшить размер пакета с аудио и не передавать в нем тишину, которую не нужно распознавать.

## Примеры использования {#examples}

* [{#T}](api/streaming-examples-v3.md)
* [{#T}](api/microphone-streaming.md)
* [{#T}](api/stt-language-labels-example.md)
* [{#T}](api/streaming-examples.md)

#### См. также {#see-also}

* [{#T}](../formats.md)
* [{#T}](models.md)
* [{#T}](../concepts/auth.md)
* [{#T}](api/streaming-api.md)
* [Справочник API v3](../stt-v3/api-ref/grpc/index.md)