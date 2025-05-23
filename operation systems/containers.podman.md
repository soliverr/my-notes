---
id: 02p4jnl3reu4eyrvsrmx1nr
title: podman
desc: ''
updated: 1680017273174
created: 1617608671228
---

[podman](https://github.com/containers/podman)

## Installation

* [How to Install and Use Podman on Ubuntu 20.04](https://www.vultr.com/docs/how-to-install-and-use-podman-on-ubuntu-20-04)

Сб орки вот тут:
https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/

Текущая версия дистрибутива:

```console
$ lsb_release --all
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 20.04.2 LTS
Release:	20.04
Codename:	focal
```

Добавить источник:

```console
PODMAN_VERSION_ID=20.04
$ sudo sh -c "echo 'deb http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_${PODMAN_VERSION_ID}/ /' > /etc/apt/sources.list.d/podman_stable.list"
```

Получить ключ архива:

```console
$ curl -fsSL https://download.opensuse.org/repositories/devel:kubic:libcontainers:stable/xUbuntu_${PODMAN_VERSION_ID}/Release.key | sudo dd of=/usr/share/keyrings/podman-keyring.gpg && sudo chmod go+r /usr/share/keyrings/podman-keyring.gpg
```

Обновить кеш пакетов:

```console
$ sudo apt-get update
```

Установить `podman`:

```
$ sudo apt-get --yes install podman
```

Проверить версию `podman`:

```
$ podman info
```

## Полезные команды

### Список контейнеров

```sh
$ podman ps [-a]
```

### Shell в контейнере

```sh
$ podman exec -it <name> bash
```

### Aliases for Bash and Zsh

```
alias podls='podman image ls'
alias podps='podman ps'
alias podrunit='podman run -it --rm'
```

## Проверка работы

```sh
$ podman run quay.io/podman/hello
$ podman run hello-world
```


## Ошибки в работе

### Error processing tar file(exit status 1): operation not permitted

При загрузке образа возникла вот такая ошибка:

```sh
$ podman run -d --name podman-centos7 docker.io/pycontribs/centos:7 /usr/sbin/init 
ERRO[0000] error unmounting /home/oliver/.local/share/containers/storage/overlay/c7c1790daed0ac0a10be119023c95ba966d611e62795d845cdcbfb70e4ecd266/merged: invalid argument 
Error: error mounting storage for container 018664d29ebbb435f1dfe3ed3cec438f5673ace2baa30c263c010b4584bfaeef: error creating overlay mount to /home/oliver/.local/share/containers/storage/overlay/c7c1790daed0ac0a10be119023c95ba966d611e62795d845cdcbfb70e4ecd266/merged, mount_data=",lowerdir=/home/oliver/.local/share/containers/storage/overlay/l/CM7PIRA5JPXFVRGE4VJX7YPTRX:/home/oliver/.local/share/containers/storage/overlay/l/ENLJRAF7NP2ITRPLY24HWYVPAB,upperdir=/home/oliver/.local/share/containers/storage/overlay/c7c1790daed0ac0a10be119023c95ba966d611e62795d845cdcbfb70e4ecd266/diff,workdir=/home/oliver/.local/share/containers/storage/overlay/c7c1790daed0ac0a10be119023c95ba966d611e62795d845cdcbfb70e4ecd266/work,userxattr": invalid argument
```

При попытке удалить и создать контейнер заново, так же возникла  ошибка:

```sh
$ podman pull docker.io/pycontribs/centos:7

Error processing tar file(exit status 1): operation not permitted
```

Помогло вот это решение: https://github.com/containers/buildah/issues/1745

```sh
$ mkdir ~/.config/containers
$ cp /etc/config/containers/storage.conf ~/.config/containers
$ vim ~/.config/containers/storage.conf
```

Раскомментировать строку `mount_program = "/usr/bin/fuse-overlayfs"` в секции `[storage.options.overlay]`.

Строки указания расположения образов в секции `[storage]` закомментировать.

### Ошибка запуска в Docker-контейнере

```sh
# podman ps
WARN[0000] "/" is not a shared mount, this could cause issues or missing mounts with rootless containers 
cannot clone: Operation not permitted
Error: cannot re-exec process
```



* https://github.com/containers/buildah/issues/3726

> podman/buildah expect the following:
> 
> ```sh
$ findmnt -o PROPAGATION /
PROPAGATION
shared
```

* https://www.redhat.com/sysadmin/podman-inside-container

> Rootless Podman with Docker
> 
> ```sh
# docker run --user podman --privileged quay.io/podman/stable podman run ubi8 echo hello
Resolved "ubi8" as an alias (/etc/containers/registries.conf.d/shortnames.conf)
...
hello
```
>
> Rootful Podman in Docker without --privileged
```sh
# docker run --cap-add=sys_admin --cap-add mknod --device=/dev/fuse --security-opt seccomp=unconfined --security-opt label=disable quay.io/podman/stable podman run ubi8-minimal echo hello
hello
```

см. [Gitlab](./devops.gitlab.md)

## Podman Compose

Можно имитировать работу с Docker Compose: [Using PodMan with docker-compose files](https://faun.pub/using-podman-with-docker-compose-files-b606f964b65?gi=ab3e87fd16bd)

* https://github.com/containers/podman-compose

### Installation

**Stable**:

```sh
pip3 install podman-compose
```

**Development**

```sh
pip3 install https://github.com/containers/podman-compose/archive/devel.tar.gz
```

**System wide**:

```
curl -o /usr/local/bin/podman-compose https://raw.githubusercontent.com/containers/podman-compose/devel/podman_compose.py
chmod +x /usr/local/bin/podman-compose
```

**Inside your home**:

```sh
curl -o ~/.local/bin/podman-compose https://raw.githubusercontent.com/containers/podman-compose/devel/podman_compose.py
chmod +x ~/.local/bin/podman-compose
```

### Пример использования

_docker-compose.yaml_: 

```
---
version: '3.3'

services:
  db:
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: somewordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    ports:
      - "8000:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress

volumes:
  db_data: {}
```

**docker-compose**:

```sh
$ docker-compose -f docker-compose.yaml up
...

$ docker ps
CONTAINER ID   IMAGE              COMMAND                  CREATED          STATUS          PORTS                                   NAMES
5c93d526727f   wordpress:latest   "docker-entrypoint.s…"   13 seconds ago   Up 12 seconds   0.0.0.0:8000->80/tcp, :::8000->80/tcp   tmp_wordpress_1
024092cc9a65   mysql:5.7          "docker-entrypoint.s…"   14 seconds ago   Up 13 seconds   3306/tcp, 33060/tcp                     tmp_db_1
```

**podman-compose**:

```sh
$ podman-compose -f docker-compose.yaml up
...
Error: short-name "mysql:5.7" did not resolve to an alias and no unqualified-search registries are defined in "/etc/containers/registries.conf"
```

#### Error 'short-name did not resolve'

[Podman error on Ubuntu – short-name did not resolve to an alias and no unqualified-search registries](https://www.desigeek.com/blog/amit/2022/03/25/podman-error-on-ubuntu-short-name-did-not-resolve-to-an-alias-and-no-unqualified-search-registries/): 

> This is because, shortnames it seems arent resolved by default – atleast not on the the Ubuntu (ARM) version. To fix this, the following needs to be added to the _/etc/containers/registries.conf_ file:
>
> `unqualified-search-registries=["docker.io"]`


Из проекта *Fedora*:

```
unqualified-search-registries = ["registry.fedoraproject.org", "registry.access.redhat.com", "docker.io", "quay.io"]
```


После исправления ошибки всё заработало.

```sh
$ podman-compose -f docker-compose.yaml up
...

$ podman ps
CONTAINER ID  IMAGE                               COMMAND               CREATED         STATUS             PORTS                 NAMES
21432b8bc289  docker.io/library/mysql:5.7         mysqld                46 seconds ago  Up 20 seconds ago                        tmp_db_1
2958740a5fb8  docker.io/library/wordpress:latest  apache2-foregroun...  20 seconds ago  Up 18 seconds ago  0.0.0.0:8000->80/tcp  tmp_wordpress_1
```

## Использование Pods

[Podman: Managing pods and containers in a local container runtime](https://developers.redhat.com/blog/2019/01/15/podman-managing-containers-pods)

