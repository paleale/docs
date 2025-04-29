1. [Подготовьте облако к работе](#before-you-begin).
1. [Создайте публичный бакет](#create-public-bucket).
1. [Включите веб-сайт для бакета](#turn-on-hosting).
1. [Настройте DNS](#configure-dns).
1. [Загрузите файлы сайта](#upload-files).
1. [Проверьте работу сайта](#test-site).

Если созданные ресурсы вам больше не нужны, [удалите их](#clear-out).

## Подготовьте облако к работе {#before-you-begin}

{% include [before-you-begin](../_tutorials_includes/before-you-begin.md) %}

### Необходимые платные ресурсы {#paid-resources}

{% include [static-paid-resources](../_tutorials_includes/static-paid-resources.md) %}

## Создайте публичный бакет {#create-public-bucket}

Чтобы создать бакет для файлов статического сайта:

{% list tabs group=instructions %}

- Консоль управления {#console}

  1. В [консоли управления]({{ link-console-main }}) {{ yandex-cloud }} выберите [каталог](../../resource-manager/concepts/resources-hierarchy.md#folder), в котором будете создавать бакет.
  1. На странице каталога нажмите кнопку **{{ ui-key.yacloud.iam.folder.dashboard.button_add }}** и выберите **{{ ui-key.yacloud.iam.folder.dashboard.value_storage }}**.
  1. В поле **{{ ui-key.yacloud.common.name }}** введите имя бакета. После настройки бакет будет доступен по двум адресам:

     * `http(s)://<имя_бакета>.{{ s3-web-host }}`
     * `http(s)://{{ s3-web-host }}/<имя_бакета>`

     Имя должно удовлетворять требованиям:

     {% include [bucket-name-reqs](../../_includes/bucket-name-reqs.md) %}

     Если у вас есть зарегистрированное доменное имя (например `example.com`), и вы хотите, чтобы ваш сайт был доступен по адресу `example.com`, в качестве имени бакета укажите `example.com`.

     {% note info %}

     Если вы планируете использовать собственный домен для сайта, имя бакета должно в точности совпадать с именем вашего домена, а домен должен быть второго и более уровня. Подробнее см. в разделе [{#T}](../../storage/operations/hosting/own-domain.md).

     {% endnote %}

  1. В поле **{{ ui-key.yacloud.storage.bucket.settings.field_access-read }}** укажите `{{ ui-key.yacloud.storage.bucket.settings.access_value_public }}`.
  1. Нажмите кнопку **{{ ui-key.yacloud.storage.buckets.create.button_create }}**.

{% endlist %}

## Включите веб-сайт для бакета {#turn-on-hosting}

1. Загрузите и настройте главную страницу и страницу обработки ошибок будущего сайта. Для этого создайте на вашем компьютере файлы:

    * `index.html` с текстом `Hello, world!`. Содержимое файла будет отображаться при обращении к главной странице сайта.
    * `error.html` с текстом `Error!`. Содержимое файла будет отображаться при ответах сайта с ошибками `4xx`.

1. Загрузите созданные файлы в бакет:

    {% list tabs group=instructions %}

    - Консоль управления {#console}

      1. В [консоли управления]({{ link-console-main }}) выберите [каталог](../../resource-manager/concepts/resources-hierarchy.md#folder), в котором расположен бакет.
      1. В списке сервисов выберите **{{ ui-key.yacloud.iam.folder.dashboard.label_storage }}** и в открывшемся окне выберите созданный ранее бакет.
      1. На панели слева выберите **{{ ui-key.yacloud.storage.bucket.switch_files }}**.
      1. Нажмите кнопку **{{ ui-key.yacloud.storage.bucket.button_upload }}**. В открывшемся окне выберите созданные файлы и подтвердите загрузку.
      1. На панели слева выберите **{{ ui-key.yacloud.storage.bucket.switch_settings }}**.
      1. На вкладке **{{ ui-key.yacloud.storage.bucket.switch_website }}**:

          1. Перейдите на вкладку `{{ ui-key.yacloud.storage.bucket.switch_website }}` и выберите `{{ ui-key.yacloud.storage.bucket.website.switch_hosting }}` .
          1. В поле **{{ ui-key.yacloud.storage.bucket.website.field_index }}** укажите `index.html`.
          1. В поле **{{ ui-key.yacloud.storage.bucket.website.field_error }}** укажите `error.html`.
          1. Нажмите кнопку **{{ ui-key.yacloud.storage.bucket.website.button_save }}**.

    {% endlist %}

1. Убедитесь, что главная страница сайта открывается. Для этого подключитесь к сайту через браузер по ссылке вида `http://<имя_бакета>.{{ s3-web-host }}`.
1. Убедитесь, что страница ошибки открывается. Для этого подключитесь к сайту через браузер по ссылке вида `http://<имя_бакета>.{{ s3-web-host }}/error-check`.

По умолчанию сайт доступен только по протоколу HTTP, например `http://example.com.{{ s3-web-host }}`. Чтобы поддержать для сайта протокол HTTPS:
1. Добавьте [собственный сертификат безопасности](../../certificate-manager/operations/import/cert-create.md) или [сертификат от Let's Encrypt](../../certificate-manager/operations/managed/cert-create.md) в сервисе {{ certificate-manager-full-name }}.
1. [Настройте](../../storage/operations/hosting/certificate.md) поддержку HTTPS для бакета. После этого сайт будет доступен по протоколу HTTPS `https://example.com.website.yandexcloud.net`.

    {% include [bucket-https](../../_includes/storage/bucket-https.md) %}

1. Чтобы настроить доступ к сайту по короткому доменному имени `example.com`, [настройте DNS](#configure-dns).

## Настройте DNS {#configure-dns}

Если у вас есть зарегистрированное доменное имя, назначьте его бакету. Для этого создайте в [DNS-запись](../../dns/concepts/resource-record.md) [типа ANAME](../../dns/concepts/resource-record.md#cname).

Вы можете воспользоваться сервисом {{ dns-name }} для управления доменом.

{% note info %}

Имя бакета должно в точности совпадать с именем вашего домена, а домен должен быть второго и более уровня. Подробнее см. в разделе [{#T}](../../storage/operations/hosting/own-domain.md).

{% endnote %}

В инструкции ниже описана настройка DNS для домена второго уровня `example.com`.

### Добавьте зону {#create-dns-zone}

Чтобы создать публичную зону DNS:

{% list tabs group=instructions %}

- Консоль управления {#console}

  1. В [консоли управления]({{ link-console-main }}) выберите каталог, в котором хотите создать публичную зону.
  1. В списке сервисов выберите **{{ ui-key.yacloud.iam.folder.dashboard.label_dns }}**.
  1. Нажмите кнопку **{{ ui-key.yacloud.dns.button_zone-create }}** и в открывшемся окне задайте настройки зоны DNS:

      * **{{ ui-key.yacloud.dns.label_zone }}** — укажите ваше зарегистрированное доменное имя. Например: `example.com.`. Значение поля должно заканчиваться точкой.
      * **{{ ui-key.yacloud.common.type }}** — `{{ ui-key.yacloud.dns.label_public }}`.
      * **{{ ui-key.yacloud.common.name }}** — `example-zone-1`.

  1. Нажмите кнопку **{{ ui-key.yacloud.common.create }}**.

{% endlist %}

### Добавьте ресурсную запись типа ANAME {#create-cname-record}

Создайте в публичной зоне DNS ресурсную запись типа [ANAME](../../dns/concepts/resource-record.md#aname):

{% list tabs group=instructions %}

- Консоль управления {#console}

  1. В [консоли управления]({{ link-console-main }}) выберите каталог, в котором находится публичная зона DNS.
  1. В списке сервисов выберите **{{ ui-key.yacloud.iam.folder.dashboard.label_dns }}** и выберите созданную ранее зону DNS. 
  1. Нажмите кнопку **{{ ui-key.yacloud.dns.button_record-set-create }}** и в открывшемся окне задайте параметры записи:

      * В поле **{{ ui-key.yacloud.common.name }}** выберите `{{ ui-key.yacloud.dns.label_fqdn-equal-to-zone }}`.
      * В поле **{{ ui-key.yacloud.common.type }}** выберите `ANAME`.
      * В поле **{{ ui-key.yacloud.dns.label_records }}** укажите значение ресурсной записи. Например: `example.com.{{ s3-web-host }}`, где `example.com` — имя созданного ранее публичного бакета.

  1. Нажмите кнопку **{{ ui-key.yacloud.common.create }}**.

{% endlist %}

Имя домена в ANAME-записи должно соответствовать имени бакета. Например, если вы создаете ANAME-запись с именем `example.com`, ваш бакет должен иметь имя `example.com`.

### Делегируйте доменное имя {#delegate-domain}

Делегирование — это перенос ответственности с серверов регистратора на ваши серверы. Для домена создаются ресурсные записи [типа NS](../../dns/concepts/resource-record.md#ns) (`ns1.{{ dns-ns-host-sld }}` и `ns2.{{ dns-ns-host-sld }}`).

Чтобы делегировать домен, укажите для него DNS-серверы в личном кабинете регистратора.

Делегирование происходит не сразу. Серверы интернет-провайдеров обычно обновляют записи до 24 часов (86400 секунд). Это обусловлено значением [TTL](https://ru.wikipedia.org/wiki/Time_to_live), в течение которого кешируются записи для доменов.

Проверить делегирование домена можно с помощью [сервиса Whois](https://www.reg.ru/whois/check_site) или утилиты `dig`:

```bash
dig +short NS example.com
```

Результат:

```text
ns2.{{ dns-ns-host-sld }}.
ns1.{{ dns-ns-host-sld }}.
```

## Загрузите файлы сайта {#upload-files}

После настройки и проверки доступности загрузите в бакет остальные файлы, необходимые для работы сайта. Используйте для этого [консоль управления]({{ link-console-main }}), [S3 API](../../storage/s3/api-ref/object/upload.md), [{{ TF }}](../../tutorials/infrastructure-management/terraform-quickstart.md) или другие [инструменты для работы с {{ objstorage-name }}](../../storage/tools/).

## Проверьте работу сайта {#test-site}

{% include [static-test-site](../_tutorials_includes/static-test-site.md) %}

## Как удалить созданные ресурсы {#clear-out}

Чтобы перестать платить за ресурсы:
1. [Удалите загруженные файлы](../../storage/operations/objects/delete.md).
1. [Удалите бакет](../../storage/operations/buckets/delete.md).
1. [Удалите зону DNS](../../dns/operations/zone-delete.md).