{% note warning %}

Оцените риск включения доступа через серийную консоль, учитывая следующие факторы:

* ВМ будет доступна для управления из интернета даже в случае отсутствия внешнего IP-адреса. 
  Получить доступ к серийной консоли ВМ из консоли управления {{ yandex-cloud }} сможет пользователь, успешно аутентифицированный в консоли управления {{ yandex-cloud }} при наличии должных прав на ВМ. Доступ к серийной консоли ВМ из клиентского приложения [SSH](../../compute/operations/vm-connect/ssh.md) (например, Putty) или [CLI](../../cli/) также возможен путем аутентификации через SSH-ключ. В связи с этим необходимо тщательно контролировать SSH-ключ и завершать веб-сессию для снижения рисков ее перехвата.

* Сессия будет доступна одновременно всем пользователям, имеющим право доступа к серийной консоли.
  Действия одного пользователя будут видны другим пользователям, если в это время они смотрят вывод серийной консоли.

* Незавершенная сессия может быть использована другим пользователем.

Мы рекомендуем включать серийную консоль только в случае крайней необходимости, выдавать такой доступ узкому кругу лиц и использовать стойкие пароли для доступа к ВМ.

Не забывайте [отключать доступ](../../compute/operations/serial-console/disable.md) после работы с серийной консолью.

{% endnote %}