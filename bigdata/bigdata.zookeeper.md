---
id: iernh199xxclexq7ne3dfer
title: Zookeeper
desc: ''
updated: 1658818217325
created: 1638974467146
---

# ZooKeeper в Docker Swarm или K8S

При миграции сервиса ZooKeeper в Docker Stack или Docker Service возникает проблема с Docker Ingress: в кластере Swarm/K8s порт 2181 будет опубликован один раз на ведущем сервере кластера. Доступ к контейнерам может быть осуществлён только через Network Overlay, то есть, при репликации сервиса - узлы ZooKeeper в локальной сети другдруга не увидят по заданным именам в корпоративном DNS.

Кроме того, хотя и возможно воспользоваться определением одного сервиса для репликации контейнеров через указание шаблона hostname: "{{.Node.Hostname"}}, и заранее заданный myid, такие контейнеры друг друга не видят, из-за автоматических настроек Ingress, которые по умолчанию перенаправляют порт 2181 на первую реплику сервиса.

То есть, работать можно только определяя свою сеть Network Overlay и свои имена хостов в этой сети.

Но такая конфигурация не позволяет "подружить" сервера ZooKeeper в контейнере и без контейнера, а также делает узким местом центральный сервер Swarm (Ingress), при падении которого доступ в кластерный сервис прекратится.

Получается, что нужно ещё больше усложнить инфраструктуру - обеспечив доступ к локальному DNS сервиса Docker и к сетям Docker для сервисов, работающих на узлах кластера Hadoop.

Остаётся рабочий вариант - запуск ZooKeeper в контейнере через Ansible.

При этом в Portainer можно клонировать, включать, выключать такие контейнеры.

* https://docs.docker.com/config/containers/container-networking/
* https://habr.com/ru/post/315500/
* https://kerneltalks.com/networking/how-docker-container-dns-works/
* https://github.com/itsaur/zookeeper-docker-swarm
* https://github.com/31z4/zookeeper-docker

```shell
docker run --name zookeeper-4 --publish 2181:2181 \
           --volume /etc/zookeeper/3.6:/conf \
           --volume /var/lib/zookeeper/3.6:/data \
           --volume /var/log/zookeeper/3.6:/datalog \
           --volume /var/log/zookeeper/3.6:/logs \
           --user 800:800 \
           --env "ZOO_SERVERS=server.1=c-t-hdp-zkeeper-1.srv.bia-tech.ru:2888:3888;2181 server.2=c-t-hdp-zkeeper-2.srv.bia-tech.ru:2888:3888;2181 server.3=c-t-hdp-zkeeper-3.srv.bia-tech.ru:2888:3888;2181 server.4=c-t-hdp-kafka-3.srv.bia-tech.ru:2888:3888;2181" \
           --env "ZOO_LOG4J_PROP=INFO,CONSOLE,ROLLINGFILE" \
           --env "JVMFLAGS=-Xmx1024m" \
           --hostname=`hostname -f` \
           --detach \
           zookeeper
```


## Вариант решения проблемы масштабируемости сервиса в Docker Swarm или Kubernetes:

* заранее выделить пул хостов для развёртывания
* передавать в контейнер/pod текущее значение фактора репликации сервиса
* реализовать поддержку динамического конфигурирования сервиса
* автоматически задавать текущий список серверов из пула на основе фактора репликации сервиса
* реализовать контроль и динамическое обновление списка серверов в ансамбле:
  * на основе динамических записей DNS в каждом контейнере: `dig tasks.$SERVICE_NAME +short`
  * новый добавленный контейнер должен сам добавить себя в список во все текущие контейнеры сервиса
  * использовать команду `zkCli.sh -reconfig [-remove <list>] [-add <list>]`

# Основные параметры конфигурации

* https://zookeeper.apache.org/doc/
* https://zookeeper.apache.org/doc/r3.5.3-beta/zookeeperReconfig.html

## Клиентский порт

> **Specifying the client port**
>
> Starting with 3.5.0 the `clientPort` and `clientPortAddress` configuration parameters should no longer be used. Instead, this information is now part of the server keyword specification, which becomes as follows:
>
> `server.<positive id> = <address1>:<port1>:<port2>[:role];[<client port address>:]<client port>`
>
> The client port specification is to the right of the semicolon. The client port address is optional, and if not specified it defaults to "0.0.0.0". As usual, role is also optional, it can be `participant` or `observer` (`participant` by default).

## Динамическая конфигурация сервиса

> **Dynamic configuration**
> 
> Dynamic configuration parameters are stored in a separate file on the server (which we call the dynamic configuration file). This file is linked from the static config file using the new dynamicConfigFile keyword.
>
> For now, the following configuration keywords are considered part of the dynamic configuration: **server, group and weight**.
>
> **Once created, the dynamic configuration file should not be manually altered**. Changed are only made through the new reconfiguration commands outlined below. _Note that changing the config of an offline cluster could result in an inconsistency with respect to configuration information stored in the ZooKeeper log (and the special configuration znode, populated from the log) and is therefore highly discouraged_.

## Режим standAlone

> **The standaloneEnabled flag**
>
>  `standaloneEnabled=false`
> 
> With this setting it is possible to start a ZooKeeper ensemble containing a single participant and to dynamically grow it by adding more servers. Similarly, it is possible to shrink an ensemble so that just a single participant remains, by removing servers.
> 
> Since running the Distributed mode allows more flexibility, we recommend setting the flag to false. We expect that the legacy Standalone mode will be 
deprecated in the future.

## Модификация состава кластера

> **Modifying the current dynamic configuration**
> 
> `reconfig -remove 3 -add server.5=125.23.63.23:1234:1235;1236`
> `reconfig -remove 3,4 -add server.5=localhost:2111:2112;2113,6=localhost:2114:2115:observer;2116`
> 
> The second mode of reconfiguration is non-incremental, whereby a client gives a complete specification of the new dynamic system configuration. The new configuration can either be given in place or read from a file:
>
> `reconfig -file newconfig.cfg //newconfig.cfg is a dynamic config file, see Dynamic configuration file`
>
> `reconfig -members server.1=125.23.63.23:2780:2783:participant;2791,server.2=125.23.63.24:2781:2784:participant;2792,server.3=125.23.63.25:2782:2785:participant;2793`

### API

* Reconfiguration API
* Get Configuration API

### Security

* Access Control: The dynamic configuration is stored in a special znode `ZooDefs.CONFIG_NODE = /zookeeper/config`. 

```shell 
[zk: localhost:2181(CONNECTED) 8] getAcl /zookeeper/config
'world,'anyone
: r
```

* Authentication: See [ZooKeeper and SASL](https://cwiki.apache.org/confluence/display/ZOOKEEPER/Zookeeper+and+SASL) for more details on this topic.
* Disable ACL check: ZooKeeper supports `skipACL` option such that ACL check will be completely skipped, if `skipACL` is set to `"yes"`. In such cases any unauthenticated users can use reconfig API. 

### Reconfig

See: [reconfig command](https://github.com/apache/zookeeper/blob/master/zookeeper-docs/src/main/resources/markdown/zookeeperCLI.md#reconfig)

Pre-requisites:
  1. set reconfigEnabled=true in the zoo.cfg
  1. add a super user or `skipAcl`,otherwise will get `“Insufficient permission”`. e.g. `addauth digest zookeeper:admin`.

# Утилиты для ZooKeeper

## ZooNavigator

* https://zoonavigator.elkozmon.com/en/latest/
  * https://github.com/elkozmon/zoonavigator
  
### Docker

```sh
docker run \
  -d \
  -p 9000:9000 \
  -e HTTP_PORT=9000 \
  --name zoonavigator \
  --restart unless-stopped \
  elkozmon/zoonavigator:latest
```

### Snap

```sh
sudo snap install zoonavigator
```

После установки сервис доступен на http://localhost:9000.
