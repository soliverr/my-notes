---
id: 91gnoscijg9zeoduknvqbis
title: Debezium
desc: ''
updated: 1678468854279
created: 1630322116561
---

# Introduction 

[Debezium](https://debezium.io/) - **Stream changes from your database.**

> Debezium is an open source distributed platform for change data capture. Start it up, point it at your databases, and your apps can start responding to all of the inserts, updates, and deletes that other apps commit to your databases. Debezium is durable and fast, so your apps can respond quickly and never miss an event, even when things go wrong.

* [Debezium on GitHub](https://github.com/debezium/debezium)
* [Debezium Images](https://github.com/debezium/docker-images)
* [Debezium Examples](https://github.com/debezium/debezium/debexium-examples)
* [Debezium FAQ](https://debezium.io/documentation/faq/)
* [Streaming Data from MySQL into Apache Kafka](https://sandeepkattepogu.medium.com/streaming-data-from-mysql-into-apache-kafka-db9b5468667e)

# Install

## Локальная устновка

Добавить плагины Debezium в каталог Kafka Connect. Я устанавливаю [Kafka](kafka.md) и Debezium с помощью [Ansible](devops.ansible.md).

# Tutorial 

* [Debezium Tutorial](https://debezium.io/documentation/reference/tutorial.html)

## Запуск ZooKeeper

```
$ docker run -it --rm --name zookeeper -p 2181:2181 -p 2888:2888 -p 3888:3888 debezium/zookeeper:1.4
```

или

```
$ sudo podman pod create --name=dbz --publish "9092,3306,8083"
$ sudo podman run -it --rm --name zookeeper --pod dbz debezium/zookeeper:1.4
```

## Запуск Kafka

```
$ docker run -it --rm --name kafka -p 9092:9092 --link zookeeper:zookeeper debezium/kafka:1.4
```

или

```
$ sudo podman run -it --rm --name kafka --pod dbz debezium/kafka:1.4
```

## Запуск MySQL

```
$ docker run -it --rm --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=debezium -e MYSQL_USER=mysqluser -e MYSQL_PASSWORD=mysqlpw debezium/example-mysql:1.4
```

или

```
$ sudo podman run -it --rm --name mysql --pod dbz -e MYSQL_ROOT_PASSWORD=debezium -e MYSQL_USER=mysqluser -e MYSQL_PASSWORD=mysqlpw debezium/example-mysql:1.4
```

### Клиент MySQL

Клиента MySQL тоже можно запустить в контейнере

```
$ docker run -it --rm --name mysqlterm --link mysql --rm mysql:5.7 sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD"'
```

или

```
$ sudo podman run -it --rm --name mysqlterm --pod dbz --rm mysql:5.7 sh -c 'exec mysql -h 0.0.0.0 -uroot -pdebezium'
```

Тестовая БД **inventory**:

`mysql> use inventory;`

## Запуск Kafka Connect

```
$ docker run -it --rm --name connect -p 8083:8083 -e GROUP_ID=1 -e CONFIG_STORAGE_TOPIC=my_connect_configs -e OFFSET_STORAGE_TOPIC=my_connect_offsets -e STATUS_STORAGE_TOPIC=my_connect_statuses --link zookeeper:zookeeper --link kafka:kafka --link mysql:mysql debezium/connect:1.4
```

или

```
$ sudo podman run -it --rm --name connect --pod dbz -e GROUP_ID=1 -e CONFIG_STORAGE_TOPIC=my_connect_configs -e OFFSET_STORAGE_TOPIC=my_connect_offsets -e STATUS_STORAGE_TOPIC=my_connect_statuses debezium/connect:1.4
```

*Имена конфигурационных топиков необходимо конфигурировать для соответсвующей подсистемы.*

### Проверка работоспособности Debezium Kafka Connect

#### Версия Kafka Connect
```
$ curl -H "Accept:application/json" localhost:8083/

{"version":"2.6.1","commit":"6b2021cd52659cef","kafka_cluster_id":"iudjbahxRJq_4Dg2NiyYzQ"}
```

На порту 8083 слушает сервис *ConnectDistributed*:

`org.apache.kafka.connect.cli.ConnectDistributed /kafka/config/connect-distributed.properties`


#### Список коннекторов

```
$ curl -H "Accept:application/json" localhost:8083/connectors/
```

## Запуск KafkaCat

Можно использовать следующие Docker-образы:
* <https://hub.docker.com/r/solsson/kafkacat> - хорошая документация
* <https://hub.docker.com/r/edenhill/kafkacat> - образ от разработчика
* <https://hub.docker.com/r/confluentinc/cp-kafkacat/> - от платформы Confluent

Есть сборки пакетов под Ubuntu, OpenSUSE.

```
docker pull solsson/kafkacat
```

### Примеры использования

**Список топиков**

```
$ docker run -it --rm --network=host solsson/kafkacat -b localhost:9092 -L
```

## Деплой коннектора MySQL

Параметры конфигурации: <https://debezium.io/documentation/reference/connectors/mysql.html#mysql-connector-properties>

**Для Docker:**

```
$ curl -i -X POST -H "Accept:application/json" -H "Content-Type:application/json" localhost:8083/connectors/ -d '{ "name": "inventory-connector", "config": { "connector.class": "io.debezium.connector.mysql.MySqlConnector", "tasks.max": "1", "database.hostname": "mysql", "database.port": "3306", "database.user": "debezium", "database.password": "dbz", "database.server.id": "184054", "database.server.name": "dbserver1", "database.include.list": "inventory", "database.history.kafka.bootstrap.servers": "kafka:9092", "database.history.kafka.topic": "dbhistory.inventory" } }'
```

**Для Podman:**

```
$ curl -i -X POST -H "Accept:application/json" -H "Content-Type:application/json" localhost:8083/connectors/ -d '{ "name": "inventory-connector", "config": { "connector.class": "io.debezium.connector.mysql.MySqlConnector", "tasks.max": "1", "database.hostname": "0.0.0.0", "database.port": "3306", "database.user": "debezium", "database.password": "dbz", "database.server.id": "184054", "database.server.name": "dbserver1", "database.include.list": "inventory", "database.history.kafka.bootstrap.servers": "0.0.0.0:9092", "database.history.kafka.topic": "dbhistory.inventory" } }'
```

### Проверка состояния

```
$ curl -H "Accept:application/json" localhost:8083/connectors/
["inventory-connector"]

$ curl -i -X GET -H "Accept:application/json" localhost:8083/connectors/inventory-connector

HTTP/1.1 200 OK
Date: Thu, 06 Feb 2020 22:12:03 GMT
Content-Type: application/json
Content-Length: 531
Server: Jetty(9.4.20.v20190813)

{
  "name": "inventory-connector",
  ...
  "tasks": [
    {
      "connector": "inventory-connector",
      "task": 0
    }
  ]
}
```

### Пример создания и обновления коннектора из файла конфигурации JSON

*inventory_connector_1.json* :

```json
{
  "name": "inventory-connector",
  "config": {
    "connector.class": "io.debezium.connector.mysql.MySqlConnector",
    "tasks.max": "1",
    "database.hostname": "mysql",
    "database.port": "3306",
    "database.user": "debezium",
    "database.password": "dbz",
    "database.server.id": "184054",
    "database.server.name": "dbserver1",
    "database.include.list": "inventory",
    "database.history.kafka.bootstrap.servers": "kafka:9092",
    "database.history.kafka.topic": "dbhistory.inventory",
    "snapshot.mode": "initial",
    "table.include.list":"inventory.addresses"
  }
}
```
Фильтровать создание снапшотов можно параметром: `snapshot.include.collection.list`.

Создание коннектора, обслуживающего одну таблицу БД:

```
curl -i -X POST -H "Accept:application/json" -H "Content-Type:application/json" -d @inventory_connector_1.json localhost:8083/connectors/
```

Похоже, когда Debezium находит первичный ключ, он формирует вот такие записи:

```json
{
  "schema": {
    "type": "struct",
    "fields": [
      {
        "type": "int32",
        "optional": false,
        "field": "id"
      }
    ],
    "optional": false,
    "name": "dbserver1.inventory.addresses.Key"
  },
  "payload": {
    "id": 10
  }
}
```

```
mysql> create table test (id int, dsc text);
mysql> insert into test values (1, 'test1');
mysql> commit;
```

Создание таблиц можно мониторить в топике `dbhistory.inventory`.


*inventory_connector_2.json* :

```json
{
    "connector.class": "io.debezium.connector.mysql.MySqlConnector",
    "tasks.max": "1",
    "database.hostname": "mysql",
    "database.port": "3306",
    "database.user": "debezium",
    "database.password": "dbz",
    "database.server.id": "184054",
    "database.server.name": "dbserver1",
    "database.include.list": "inventory",
    "database.history.kafka.bootstrap.servers": "kafka:9092",
    "database.history.kafka.topic": "dbhistory.inventory",
    "snapshot.mode": "initial",
    "table.include.list": "inventory.addresses, inventory.test.*"
}
```

Обновление коннектора, таблица без индекса и PK:

```
curl -i -X PUT -H "Accept:application/json" -H "Content-Type:application/json" -d @inventory_connector_2.json localhost:8083/connectors/inventory-connector/config
```

В сообщении приходят только данные, без поля **Key**.

Для таблиц без первичного ключа или уникального индекса можно использовать параметр [`message.key.columns`](https://debezium.io/documentation/reference/1.4/connectors/mysql.html#mysql-property-message-key-columns) для задания маппинга полей в ключевое поле **Key**

```
mysql> create table test_uniq_1( id int unique, dsc text);
mysql> create table test_uniq_2( id1 int, id2 int, dsc text, unique(id1, id2));

mysql> insert into test_uniq_1 values (1, 'test1');
mysql> commit;

mysql> insert into test_uniq_2 values (1, 1, 'test1');
mysql> commit;
```

Здесь получил записи о ключевых полях

```json
...
   "optional": false,
    "name": "dbserver1.inventory.test_uniq_1.Key"
  },
  "payload": {
    "id": 1
  }

...

   "optional": false,
    "name": "dbserver1.inventory.test_uniq_2.Key"
  },
  "payload": {
    "id1": 1,
    "id2": 1
  }
```

При удаление строки  генерируется *tombstone event*.

Это параметр [`tombstones.on.delete`](https://debezium.io/documentation/reference/1.4/connectors/mysql.html#mysql-property-tombstones-on-delete)

```
mysql>  delete from test_uniq_1 where id = 1;
mysql> commit;
```

```
{"schema":{"type":"struct","fields":[{"type":"int32","optional":true,"field":"id"}],"optional":false,"name":"dbserver1.inventory.test_uniq_1.Key"},"payload":{"id":1}}{"schema":{"type":"struct","fields":[{"type":"struct","fields":[{"type":"int32","optional":true,"field":"id"},{"type":"string","optional":true,"field":"dsc"}],"optional":true,"name":"dbserver1.inventory.test_uniq_1.Value","field":"before"},{"type":"struct","fields":[{"type":"int32","optional":true,"field":"id"},{"type":"string","optional":true,"field":"dsc"}],"optional":true,"name":"dbserver1.inventory.test_uniq_1.Value","field":"after"},{"type":"struct","fields":[{"type":"string","optional":false,"field":"version"},{"type":"string","optional":false,"field":"connector"},{"type":"string","optional":false,"field":"name"},{"type":"int64","optional":false,"field":"ts_ms"},{"type":"string","optional":true,"name":"io.debezium.data.Enum","version":1,"parameters":{"allowed":"true,last,false"},"default":"false","field":"snapshot"},{"type":"string","optional":false,"field":"db"},{"type":"string","optional":true,"field":"table"},{"type":"int64","optional":false,"field":"server_id"},{"type":"string","optional":true,"field":"gtid"},{"type":"string","optional":false,"field":"file"},{"type":"int64","optional":false,"field":"pos"},{"type":"int32","optional":false,"field":"row"},{"type":"int64","optional":true,"field":"thread"},{"type":"string","optional":true,"field":"query"}],"optional":false,"name":"io.debezium.connector.mysql.Source","field":"source"},{"type":"string","optional":false,"field":"op"},{"type":"int64","optional":true,"field":"ts_ms"},{"type":"struct","fields":[{"type":"string","optional":false,"field":"id"},{"type":"int64","optional":false,"field":"total_order"},{"type":"int64","optional":false,"field":"data_collection_order"}],"optional":true,"field":"transaction"}],"optional":false,"name":"dbserver1.inventory.test_uniq_1.Envelope"},"payload":{"before":{"id":1,"dsc":"test1"},"after":null,"source":{"version":"1.4.1.Final","connector":"mysql","name":"dbserver1","ts_ms":1613647243000,"snapshot":"false","db":"inventory","table":"test_uniq_1","server_id":223344,"gtid":null,"file":"mysql-bin.000003","pos":2083,"row":0,"thread":7,"query":null},"op":"d","ts_ms":1613647243311,"transaction":null}}
{"schema":{"type":"struct","fields":[{"type":"int32","optional":true,"field":"id"}],"optional":false,"name":"dbserver1.inventory.test_uniq_1.Key"},"payload":{"id":1}}
```

Второе сообщение без указания строки - это *tombstone event*.

Для таблицы без ключа *tombstone event* не пришёл, пришло полностью пустое сообщение без содержимого: перевод строки в консоли.


Работа с искусственным ключом:

`"message.key.columns": "inventory.test:id"`

```json
"name":"dbserver1.inventory.test.Key"},"payload":{"id":3}}

{"schema":{"type":"struct","fields":[{"type":"int32","optional":true,"field":"id"}],"optional":false,"name":"dbserver1.inventory.test.Key"},"payload":{"id":2}}{"schema":{"type":"struct","fields":[{"type":"struct","fields":[{"type":"int32","optional":true,"field":"id"},{"type":"string","optional":true,"field":"dsc"}],"optional":true,"name":"dbserver1.inventory.test.Value","field":"before"},{"type":"struct","fields":[{"type":"int32","optional":true,"field":"id"},{"type":"string","optional":true,"field":"dsc"}],"optional":true,"name":"dbserver1.inventory.test.Value","field":"after"},{"type":"struct","fields":[{"type":"string","optional":false,"field":"version"},{"type":"string","optional":false,"field":"connector"},{"type":"string","optional":false,"field":"name"},{"type":"int64","optional":false,"field":"ts_ms"},{"type":"string","optional":true,"name":"io.debezium.data.Enum","version":1,"parameters":{"allowed":"true,last,false"},"default":"false","field":"snapshot"},{"type":"string","optional":false,"field":"db"},{"type":"string","optional":true,"field":"table"},{"type":"int64","optional":false,"field":"server_id"},{"type":"string","optional":true,"field":"gtid"},{"type":"string","optional":false,"field":"file"},{"type":"int64","optional":false,"field":"pos"},{"type":"int32","optional":false,"field":"row"},{"type":"int64","optional":true,"field":"thread"},{"type":"string","optional":true,"field":"query"}],"optional":false,"name":"io.debezium.connector.mysql.Source","field":"source"},{"type":"string","optional":false,"field":"op"},{"type":"int64","optional":true,"field":"ts_ms"},{"type":"struct","fields":[{"type":"string","optional":false,"field":"id"},{"type":"int64","optional":false,"field":"total_order"},{"type":"int64","optional":false,"field":"data_collection_order"}],"optional":true,"field":"transaction"}],"optional":false,"name":"dbserver1.inventory.test.Envelope"},"payload":{"before":{"id":2,"dsc":"test2"},"after":null,"source":{"version":"1.4.1.Final","connector":"mysql","name":"dbserver1","ts_ms":1613647904000,"snapshot":"false","db":"inventory","table":"test","server_id":223344,"gtid":null,"file":"mysql-bin.000003","pos":3179,"row":0,"thread":7,"query":null},"op":"d","ts_ms":1613647904944,"transaction":null}}
{"schema":{"type":"struct","fields":[{"type":"int32","optional":true,"field":"id"}],"optional":false,"name":"dbserver1.inventory.test.Key"},"payload":{"id":2}}
```
То есть, появилось поле **Key** и стал генерироваться *tombstone event* просле удаления строки.

##  Удаление коннектора

```
$ curl -i -X DELETE localhost:8083/connectors/inventory-connector
```

Можно удалить коннектор вручную: [Manually delete a connector from Kafka Connect](https://rmoff.net/2019/06/23/manually-delete-a-connector-from-kafka-connect/)

## Snapshot

Operation type for snapshot events

The MySql connector emits snapshot events using the **"r"** operation type (READ). In case you want the connector to emit snapshot events as "c" events (CREATE, as done incorrectly in earlier versions), this can be achieved using a Simple Message Transforms (SMT). Configure the Debezium ReadToInsertEvent SMT by adding the SMT configuration details to your connector’s configuration.

An example of the configuration is this:

```
transforms=snapshotasinsert,...
transforms.snapshotasinsert.type=io.debezium.connector.mysql.transforms.ReadToInsertEvent
```

## [Learnings from using Kafka Connect - Debezium - PostgreSQL](https://www.sderosiaux.com/articles/2020/01/06/learnings-from-using-kafka-connect-debezium-postgresql/)


# Avro Serialization

* [Avro Serialization](https://debezium.io/documentation/reference/configuration/avro.html)
* [Serializing Debezium events with Avro](https://debezium.io/blog/2016/09/19/Serializing-Debezium-events-with-Avro/) (September 19, 2016 by Randall Hauch)

If you want records to be serialized with JSON, consider setting the following connector configuration properties to `false`:

* `key.converter.schemas.enable`
* `value.converter.schemas.enable`

*Setting these properties to `false` excludes the verbose schema information from each record*.

Alternatively, you can serialize the record keys and values by using Apache Avro. The Avro binary format is compact and efficient. Avro schemas make it possible to ensure that each record has the correct structure. *Avro’s schema evolution mechanism enables schemas to evolve*. This is essential for Debezium connectors, which dynamically generate each record’s schema to match the structure of the database table that was changed. Over time, change event records written to the same Kafka topic might have different versions of the same schema. Avro serialization makes it easier for change event record consumers to adapt to a changing record schema.

To use Apache Avro serialization, you must deploy a schema registry that manages Avro message schemas and their versions. Available options include ***the Apicurio API and Schema Registry as well as the Confluent Schema Registry***. Both are described here.

> *The Apicurio Registry project also provides a JSON converter. This converter combines the advantage of less verbose messages with human-readable JSON. Messages do not contain the schema information themselves, but only a schema ID.*

## Confluent Schema Registry

* [Avro Serialization :: Debezium Documentation](https://debezium.io/documentation/reference/2.1/configuration/avro.html#confluent-schema-registry)
* [Deploying Confluent Schema Registry with Debezium containers](https://debezium.io/documentation/reference/1.1/configuration/avro.html#deploying-confluent-schema-registry-with-debezium-containers)


_Beginning with Debezium 2.0.0, Confluent Schema Registry support is not included in the Debezium containers_. To enable the Confluent Schema Registry for a Debezium container, install the following Confluent Avro converter JAR files into the Connect plugin directory: 
* `kafka-connect-avro-converter` 
* `kafka-connect-avro-data` 
* `kafka-avro-serializer` 
* `kafka-schema-serializer` 
* `kafka-schema-registry-client` 
* `common-config` 
* `common-utils` 

You can download the preceding files from the [Confluent Maven repository](https://packages.confluent.io/maven/).

## APICurio Schema Registry

* [Debezium serialization with Apache Avro and Apicurio Registry](https://developers.redhat.com/blog/2020/12/11/debezium-serialization-with-apache-avro-and-apicurio-registry/) (By Hugo Guerrero December 11, 2020)

# Проблема инициализации коннектора

Основная проблема в том, что бы при инициализации коннектора не производить снапшот всех доступных таблиц, а делать это постепенно.

Коннектор при снапшоте обрабатывает все найденные таблицы:

```console
2021-03-09 14:27:26,773 INFO   MySQL|dbserver1|snapshot  Step 0: disabling autocommit, enabling repeatable read transactions, and setting lock wait timeout to 10   [io.debezium.connector.mysql.SnapshotReader]
2021-03-09 14:27:26,775 INFO   MySQL|dbserver1|snapshot  Step 1: flush and obtain global read lock to prevent writes to database   [io.debezium.connector.mysql.SnapshotReader]
2021-03-09 14:27:26,776 INFO   MySQL|dbserver1|snapshot  Step 2: start transaction with consistent snapshot   [io.debezium.connector.mysql.SnapshotReader]
2021-03-09 14:27:26,776 INFO   MySQL|dbserver1|snapshot  Step 3: read binlog position of MySQL primary server   [io.debezium.connector.mysql.SnapshotReader]
2021-03-09 14:27:26,777 INFO   MySQL|dbserver1|snapshot          using binlog 'mysql-bin.000003' at position '154' and gtid ''   [io.debezium.connector.mysql.SnapshotReader]
2021-03-09 14:27:26,777 INFO   MySQL|dbserver1|snapshot  Step 4: read list of available databases   [io.debezium.connector.mysql.SnapshotReader]
2021-03-09 14:27:26,778 INFO   MySQL|dbserver1|snapshot          list of available databases is: [information_schema, inventory, mysql, performance_schema, sys]   [io.debezium.connector.mysql.SnapshotReader]
2021-03-09 14:27:26,778 INFO   MySQL|dbserver1|snapshot  Step 5: read list of available tables in each database   [io.debezium.connector.mysql.SnapshotReader]
2021-03-09 14:27:26,778 WARN   MySQL|dbserver1|snapshot  Using configuration property "database.blacklist" is deprecated and will be removed in future versions. Please use "database.exclude.list" instead.   [io.debezium.config.Configuration]
2021-03-09 14:27:26,778 WARN   MySQL|dbserver1|snapshot  Using configuration property "table.blacklist" is deprecated and will be removed in future versions. Please use "table.exclude.list" instead.   [io.debezium.config.Configuration]
2021-03-09 14:27:26,778 WARN   MySQL|dbserver1|snapshot  Using configuration property "column.blacklist" is deprecated and will be removed in future versions. Please use "column.exclude.list" instead.   [io.debezium.config.Configuration]
2021-03-09 14:27:26,780 INFO   MySQL|dbserver1|snapshot          including 'inventory.addresses' among known tables   [io.debezium.connector.mysql.SnapshotReader]
2021-03-09 14:27:26,780 INFO   MySQL|dbserver1|snapshot          including 'inventory.addresses' for further processing   [io.debezium.connector.mysql.SnapshotReader]
2021-03-09 14:27:26,780 INFO   MySQL|dbserver1|snapshot          including 'inventory.customers' among known tables   [io.debezium.connector.mysql.SnapshotReader]
2021-03-09 14:27:26,780 INFO   MySQL|dbserver1|snapshot          'inventory.customers' is filtered out of capturing   [io.debezium.connector.mysql.SnapshotReader]
2021-03-09 14:27:26,780 INFO   MySQL|dbserver1|snapshot          including 'inventory.geom' among known tables   [io.debezium.connector.mysql.SnapshotReader]
2021-03-09 14:27:26,780 INFO   MySQL|dbserver1|snapshot          'inventory.geom' is filtered out of capturing   [io.debezium.connector.mysql.SnapshotReader]
2021-03-09 14:27:26,780 INFO   MySQL|dbserver1|snapshot          including 'inventory.orders' among known tables   [io.debezium.connector.mysql.SnapshotReader]
2021-03-09 14:27:26,780 INFO   MySQL|dbserver1|snapshot          'inventory.orders' is filtered out of capturing   [io.debezium.connector.mysql.SnapshotReader]
2021-03-09 14:27:26,780 INFO   MySQL|dbserver1|snapshot          including 'inventory.products' among known tables   [io.debezium.connector.mysql.SnapshotReader]
2021-03-09 14:27:26,780 INFO   MySQL|dbserver1|snapshot          'inventory.products' is filtered out of capturing   [io.debezium.connector.mysql.SnapshotReader]
2021-03-09 14:27:26,780 INFO   MySQL|dbserver1|snapshot          including 'inventory.products_on_hand' among known tables   [io.debezium.connector.mysql.SnapshotReader]
2021-03-09 14:27:26,780 INFO   MySQL|dbserver1|snapshot          'inventory.products_on_hand' is filtered out of capturing   [io.debezium.connector.mysql.SnapshotReader]
2021-03-09 14:27:26,781 INFO   MySQL|dbserver1|snapshot          including 'mysql.columns_priv' among known tables   [io.debezium.connector.mysql.SnapshotReader]

```


Нашёл в исходниках  *debezium/debezium/debezium-connector-mysql/src/main/java/io/debezium/connector/mysql/MySqlConnectorConfig.java*:

```java
public static final Field SNAPSHOT_NEW_TABLES = Field.create("snapshot.new.tables")
            .withDisplayName("Snapshot newly added tables")
            .withEnum(SnapshotNewTables.class, SnapshotNewTables.OFF)
            .withWidth(Width.SHORT)
            .withImportance(Importance.LOW)
            .withDescription("BETA FEATURE: On connector restart, the connector will check if there have been any new tables added to the configuration, "
                    + "and snapshot them. There is presently only two options:"
                    + "'off': Default behavior. Do not snapshot new tables."
                    + "'parallel': The snapshot of the new tables will occur in parallel to the continued binlog reading of the old tables. When the snapshot "
                    + "completes, an independent binlog reader will begin reading the events for the new tables until it catches up to present time. At this "
                    + "point, both old and new binlog readers will be momentarily halted and new binlog reader will start that will read the binlog for all "
                    + "configured tables. The parallel binlog reader will have a configured server id of 10000 + the primary binlog reader's server id.");

```

**Этот параметр влияет на обработку созданных таблиц в БД после инициализации коннектора!**

Для постепенного добавления таблиц в поток, необходимо создавать временный коннектор для прокачивания данных, как описано тут [Include new tables with data to existing Debezium connector](https://stackoverflow.com/questions/64490534/include-new-tables-with-data-to-existing-debezium-connector)

# Monitoring

* https://debezium.io/documentation/reference/stable/operations/monitoring.html
* https://docs.confluent.io/kafka-connectors/self-managed/monitoring.html
* https://github.com/prometheus/jmx_exporter/tree/main/example_configs
  * [jmx KafkaConnect Rules](https://github.com/prometheus/jmx_exporter/blob/main/example_configs/kafka-connect.yml)


Получается, что мы просто не все метрики экспортируем. Изменил конфиг на DEV для debezium-consumer-partners, появилась нужная нам метрика kafka_connect_connector_status{connector="preprod.partners",status="running",task="0",} 1.0 - тут видно, что таск 0 в состянии running.

Доступ к списку метрик: `curl http://debezium:9071`.
