---
id: 1nusce2jthl302ad6mz3ro4
title: Docker
desc: ''
updated: 1672405660149
created: 1625817223134
---

## Install Docker

* https://docs.docker.com/engine/install

### Ubuntu

* https://docs.docker.com/engine/install/ubuntu/
* [How To Install and Use Docker on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-20-04)

```console
codename=`lsb_release -c | cut -d ':' -f 2 | tr -d '\t '`
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/docker.gpg
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $codename stable"
sudo apt update
sudo apt install docker-ce
```

Посмотреть доступную версию можно так

```console
$ apt-cache policy docker-ce
```

В Ubuntu Docker пакетируется в пакет с именем *docker.io*.

После установки, должен работать сервис:

```console
sudo systemctl status docker
```

Так же часто требуется утилита *docker-compose*:

```console
$ sudo apt install docker-compose
```

## Install Docker-Compose

https://github.com/docker/compose

* `pip install docker-compose`
* https://github.com/docker/compose/releases
* `sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose`

## Создание 

https://www.mirantis.com/blog/multi-container-pods-and-container-communication-in-kubernetes/


## Использование

см. [Docker](./docker.md)
