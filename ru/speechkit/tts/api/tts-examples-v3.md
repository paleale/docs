# Синтез речи в API v3

С помощью {{ speechkit-short-name }} [API v3](../../tts-v3/api-ref/grpc/) можно синтезировать речь из текста в [TTS-разметке](../markup/tts-markup.md) в файл формата [WAV](https://ru.wikipedia.org/wiki/WAV).

В примере заданы следующие параметры синтеза:

* [формат](../../formats.md) синтезированного аудиофайла — LPCM с частотой дискретизации 22 050 Гц, контейнер [WAV](https://ru.wikipedia.org/wiki/WAV) (значение по умолчанию);
* [нормализация громкости](../index.md#volume) — LUFS (значение по умолчанию).

Преобразование и запись результата выполняются с помощью пакетов `grpcio-tools` и `pydub` и утилиты [FFmpeg](https://ffmpeg.org/).

Аутентификация происходит от имени сервисного аккаунта с помощью [API-ключа](../../../iam/concepts/authorization/api-key.md) или [IAM-токена](../../../iam/concepts/authorization/iam-token.md). Подробнее об аутентификации в API {{ speechkit-name }} см. [{#T}](../../concepts/auth.md).

Чтобы реализовать пример:

1. [Создайте](../../../iam/operations/sa/create.md) сервисный аккаунт для работы с API {{ speechkit-short-name }}.
1. [Назначьте](../../../iam/operations/sa/assign-role-for-sa.md) сервисному аккаунту роль `{{ roles-speechkit-tts }}` или выше на каталог, в котором он был создан.
1. Получите [API-ключ](../../../iam/operations/authentication/manage-api-keys.md#create-api-key) или [IAM-токен](../../../iam/operations/iam-token/create-for-sa.md) для сервисного аккаунта.
1. Создайте клиентское приложение:

    {% list tabs group=programming_language %}

    - Python 3 {#python}

      1. Склонируйте репозиторий [{{ yandex-cloud }} API](https://github.com/yandex-cloud/cloudapi):

          ```bash
          git clone https://github.com/yandex-cloud/cloudapi
          ```

      1. Установите пакеты `grpcio-tools` и `pydub` с помощью менеджера пакетов [pip](https://pip.pypa.io/en/stable/):

          ```bash
          pip install grpcio-tools && \
          pip install pydub
          ```

          Пакет `grpcio-tools` нужен для генерации кода интерфейса клиента API v3 синтеза. Пакет `pydub` нужен для обработки полученных аудиофайлов.

      1. [Скачайте](https://www.ffmpeg.org/download.html) утилиту FFmpeg для корректной работы пакета `pydub`. Добавьте путь к папке, в которой находится исполняемый файл, в переменную `PATH`. Для этого выполните команду:

          ```bash
          export PATH=$PATH:<путь_к_папке_с_исполняемым_файлом_FFmpeg>
          ```

      1. Перейдите в папку со склонированным репозиторием {{ yandex-cloud }} API, создайте папку `output` и сгенерируйте в ней код интерфейса клиента:

          ```bash
          cd <путь_к_папке_cloudapi>
          mkdir output
          python3 -m grpc_tools.protoc -I . -I third_party/googleapis \
            --python_out=output \
            --grpc_python_out=output \
            google/api/http.proto \
            google/api/annotations.proto \
            yandex/cloud/api/operation.proto \
            google/rpc/status.proto \
            yandex/cloud/operation/operation.proto \
            yandex/cloud/validation.proto \
            yandex/cloud/ai/tts/v3/tts_service.proto \
            yandex/cloud/ai/tts/v3/tts.proto
          ```

          В результате в папке `output` будут созданы файлы с интерфейсом клиента: `tts_pb2.py`, `tts_pb2_grpc.py`, `tts_service_pb2.py`, `tts_service_pb2_grpc.py` и файлы зависимостей.

      1. Создайте файл в корне папки `output`, например `test.py`, и добавьте в него следующий код:

          ```python
          import io
          import grpc
          import pydub
          import argparse

          import yandex.cloud.ai.tts.v3.tts_pb2 as tts_pb2
          import yandex.cloud.ai.tts.v3.tts_service_pb2_grpc as tts_service_pb2_grpc

          # Задайте настройки синтеза.
          # Вместо iam_token передавайте api_key при аутентификации с API-ключом
          #def synthesize(api_key, text) -> pydub.AudioSegment:
          def synthesize(iam_token, text) -> pydub.AudioSegment:
              request = tts_pb2.UtteranceSynthesisRequest(
                  text=text,
                  output_audio_spec=tts_pb2.AudioFormatOptions(
                      container_audio=tts_pb2.ContainerAudio(
                          container_audio_type=tts_pb2.ContainerAudio.WAV
                      )
                  ),
                  # Параметры синтеза
                  hints=[
                    tts_pb2.Hints(voice= 'alexander'), # (Опционально) Задайте голос. Значение по умолчанию marina
                    tts_pb2.Hints(role = 'good'), # (Опционально) Укажите амплуа, только если голос их имеет
                    tts_pb2.Hints(speed=1.1), # (Опционально) Задайте скорость синтеза
                  ],

                  loudness_normalization_type=tts_pb2.UtteranceSynthesisRequest.LUFS
              )

              # Установите соединение с сервером.
              cred = grpc.ssl_channel_credentials()
              channel = grpc.secure_channel('{{ api-host-sk-tts }}:443', cred)
              stub = tts_service_pb2_grpc.SynthesizerStub(channel)

              # Отправьте данные для синтеза.
              it = stub.UtteranceSynthesis(request, metadata=(

              # Параметры для аутентификации с IAM-токеном
                  ('authorization', f'Bearer {iam_token}'),
              # Параметры для аутентификации с API-ключом от имени сервисного аккаунта
              #   ('authorization', f'Api-Key {api_key}'),
              ))

              # Соберите аудиозапись по порциям.
              try:
                  audio = io.BytesIO()
                  for response in it:
                      audio.write(response.audio_chunk.data)
                  audio.seek(0)
                  return pydub.AudioSegment.from_wav(audio)
              except grpc._channel._Rendezvous as err:
                  print(f'Error code {err._state.code}, message: {err._state.details}')
                  raise err


          if __name__ == '__main__':
              parser = argparse.ArgumentParser()
              parser.add_argument('--token', required=True, help='IAM token or API key')
              parser.add_argument('--text', required=True, help='Text for synthesis')
              parser.add_argument('--output', required=True, help='Output file')
              args = parser.parse_args()

              audio = synthesize(args.token, args.text)
              with open(args.output, 'wb') as fp:
                  audio.export(fp, format='wav')
          ```

      1. Выполните созданный в предыдущем пункте файл:

          ```bash
          export IAM_TOKEN=<IAM-токен_сервисного_аккаунта>
          export TEXT='Я Яндекс Спичк+ит. Я могу превратить любой текст в речь. Теперь и в+ы — можете!'
          python3 output/test.py \
            --token ${IAM_TOKEN} \
            --output speech.wav \
            --text ${TEXT}
          ```

          Где:

          * `IAM_TOKEN` — [IAM-токен сервисного аккаунта](../../../iam/concepts/authorization/iam-token.md). Если для аутентификации от имени сервисного аккаунта вы используйте API-ключ, измените код Python-скрипта и вызов программы.
          * `TEXT` — текст в [TTS-разметке](../markup/tts-markup.md), который нужно синтезировать.
          * `--output` — имя файла для записи аудио.

          В результате в папке `cloudapi` будет создан файл `speech.wav` с синтезированной речью.

    - Java {#java}

      1. Установите зависимости:

          ```bash
          sudo apt update && sudo apt install --yes default-jdk maven
          ```

      1. Склонируйте [репозиторий](https://github.com/yandex-cloud-examples/yc-speechkit-tts-java) с конфигурацией для приложения на Java:

          ```bash
          git clone https://github.com/yandex-cloud-examples/yc-speechkit-tts-java
          ```

      1. Перейдите в папку репозитория:

          ```bash
          cd yc-speechkit-tts-java
          ```

      1. Скомпилируйте проект в этой папке:

          ```bash
          mvn clean install
          ```

      1. Перейдите в созданную папку `target`:

          ```bash
          cd target
          ```

      1. Задайте [API-ключ](../../../iam/concepts/authorization/api-key.md) сервисного аккаунта и текст, который надо синтезировать:

          ```bash
          export API_KEY=<API-ключ> && \
          export TEXT='Я Яндекс Спичк+ит. Я могу превратить любой текст в речь. Теперь и в+ы — можете!'
          ```

      1. Запустите Java-скрипт для синтеза речи:

          ```bash
          java -cp speechkit_examples-1.0-SNAPSHOT.jar yandex.cloud.speechkit.examples.TtsV3Client ${TEXT}
          ```

          В результате в папке `target` появится аудиофайл `result.wav`. В него записана речь из переменной окружения `TEXT`.

    {% endlist %}

#### См. также {#see-also}

* [Подробнее про API v3](../../tts-v3/api-ref/grpc/)
* [{#T}](../../concepts/auth.md)
* [{#T}](../../sdk/python/synthesis.md)
