# Репозиторий в {{ container-registry-name }}

_Репозиторий_ — набор Docker-образов с одинаковым именем. Обычно в одном репозитории находятся несколько версий одного Docker-образа. Для работы с версиями внутри репозитория используются теги и хеш. Подробнее читайте в разделе [Docker-образ](docker-image.md).

Репозиторий определяется комбинацией `<идентификатор_реестра>/<имя_Docker-образа>`.

* В командах Docker CLI необходимо использовать полное имя, включающее в себя адрес {{ container-registry-name }}:

  ```bash
  docker push {{ registry }}/<идентификатор_реестра>/<имя_Docker-образа>
  ```

* В командах {{ yandex-cloud }} CLI необходимо использовать имя репозитория без адреса {{ container-registry-name }}:

  ```bash
  yc container image list --repository-name=<идентификатор_реестра>/<имя_Docker-образа>
  ```

Для репозитория можно задать [политику автоматического удаления Docker-образов](lifecycle-policy.md), в соответствии с которой Docker-образы будут удаляться автоматически.

## Примеры использования {#examples}

* [{#T}](../tutorials/run-docker-on-vm/index.md)
* [{#T}](../tutorials/sign-cr-with-cosign.md)
* [{#T}](../tutorials/fault-tolerance.md)
* [{#T}](../tutorials/grpc-node.md)
* [{#T}](../tutorials/deploy-app-container.md)
