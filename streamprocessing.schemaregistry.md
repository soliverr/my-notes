---
id: n8jsr97kalfcv4sumpa41wl
title: Schemaregistry
desc: ''
updated: 1658996698375
created: 1642057028666
---
Schema Registry
==============

## Schema Registry Tutorial in Debezuim

Примеры использования Schema Registry есть в проекте [Debezium Examples](https://github.com/debezium/debezium-examples/) в подпапке *tutorial*.

В примерах есть настройка на использование двух вариантов сервиса:

* Confluent Schema Registry
* Apicurio Schema Registry


### Confluent Schema Registry

[Using MySQL and the Avro message format](https://github.com/debezium/debezium-examples/tree/master/tutorial#using-mysql-and-the-avro-message-format):

> To use Avro-style messages instead of JSON, Avro can be configured one of two ways, in the Kafka Connect worker configuration or in the connector configuration. Using Avro in conjunction with the schema registry allows for much more compact messages.

Предлагается два файла для тестирования:
* docker-compose-mysql-avro-connector.yaml
* docker-compose-mysql-avro-worker.yaml

Разница в том, что в *worker*  сразу задаются параметры сериализации для Avro и подключение к Confluent Schema Registry. В конфигурации *connector* привязки нет и параметры работы задаются при создании коннектора:
* register-mysql-apicurio.json
* register-mysql-avro.json

Вот эти параметры контейнеров:

```
KEY_CONVERTER=io.confluent.connect.avro.AvroConverter
VALUE_CONVERTER=io.confluent.connect.avro.AvroConverter
CONNECT_KEY_CONVERTER_SCHEMA_REGISTRY_URL=http://schema-registry:8081
CONNECT_VALUE_CONVERTER_SCHEMA_REGISTRY_URL=http://schema-registry:8081
```

За основу для эксперимента беру файл  *[docker-compose-mysql-avro-connector.yaml](https://github.com/debezium/debezium-examples/blob/master/tutorial/docker-compose-mysql-avro-connector.yaml)*:

```yaml
version: '2'
services:
  zookeeper:
    image: debezium/zookeeper:${DEBEZIUM_VERSION}
    ports:
     - 2181:2181
     - 2888:2888
     - 3888:3888
  kafka:
    image: debezium/kafka:${DEBEZIUM_VERSION}
    ports:
     - 9092:9092
    links:
     - zookeeper
    environment:
     - ZOOKEEPER_CONNECT=zookeeper:2181
  mysql:
    image: debezium/example-mysql:${DEBEZIUM_VERSION}
    ports:
     - 3306:3306
    environment:
     - MYSQL_ROOT_PASSWORD=debezium
     - MYSQL_USER=mysqluser
     - MYSQL_PASSWORD=mysqlpw
  schema-registry:
    image: confluentinc/cp-schema-registry
    ports:
     - 8181:8181
     - 8081:8081
    environment:
     - SCHEMA_REGISTRY_KAFKASTORE_CONNECTION_URL=zookeeper:2181
     - SCHEMA_REGISTRY_HOST_NAME=schema-registry
     - SCHEMA_REGISTRY_LISTENERS=http://schema-registry:8081
    links:
     - zookeeper
  connect:
    image: debezium/connect:${DEBEZIUM_VERSION}
    ports:
     - 8083:8083
    links:
     - kafka
     - mysql
     - schema-registry
    environment:
     - BOOTSTRAP_SERVERS=kafka:9092
     - GROUP_ID=1
     - CONFIG_STORAGE_TOPIC=my_connect_configs
     - OFFSET_STORAGE_TOPIC=my_connect_offsets
     - STATUS_STORAGE_TOPIC=my_connect_statuses
     - INTERNAL_KEY_CONVERTER=org.apache.kafka.connect.json.JsonConverter
     - INTERNAL_VALUE_CONVERTER=org.apache.kafka.connect.json.JsonConverter
```

Здесь используется сервис *Confluent Schema Registry*.

Запуск тестового окружения:

```console
export DEBEZIUM_VERSION=1.4
docker-compose -f docker-compose-mysql-avro-connector.yaml up --detach
```

То, что сервис запустился, можно проверить, вызвав API:

```console
$ curl -i -X GET -H "Accept:application/json" localhost:8081/schemas
```

> Configuring Avro at the Debezium Connector involves specifying the converter and schema registry as a part of the connectors configuration. To do this, follow the same steps above for MySQL but instead using the *docker-compose-mysql-avro-connector.yaml* and *register-mysql-avro.json* configuration files.

Конфигурация для регистрации сервиса Kafka Connect *[register-mysql-avro.json](https://github.com/debezium/debezium-examples/blob/master/tutorial/register-mysql-avro.json)*

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
        "database.include": "inventory",
        "database.history.kafka.bootstrap.servers": "kafka:9092",
        "database.history.kafka.topic": "schema-changes.inventory",
        "key.converter": "io.confluent.connect.avro.AvroConverter",
        "value.converter": "io.confluent.connect.avro.AvroConverter",
        "key.converter.schema.registry.url": "http://schema-registry:8081",
        "value.converter.schema.registry.url": "http://schema-registry:8081"
    }
}
```

Данные пошли в AVRO-виде. Получение данных:

```console
$ kafkacat -b localhost:9092  -t dbserver1.inventory.addresses -s avro -r http://localhost:8081
```


### Apicurio Schema Registry

[Using Debezium With the Apicurio API and Schema Registry](https://debezium.io/blog/2020/04/09/using-debezium-with-apicurio-api-schema-registry/):

> *Apicurio also provides API compatibility layers for schema registries from IBM and Confluent*. This is a very useful feature, as it enables the use of 3rd-party tools like kafkacat, even if they are not aware of Apicurio’s native API.

Для запуска тестовой среды используется файл *[docker-compose-mysql-apicurio.yaml](https://github.com/debezium/debezium-examples/blob/master/tutorial/docker-compose-mysql-apicurio.yaml)*:

```yaml
version: '2'
services:
  zookeeper:
    image: debezium/zookeeper:${DEBEZIUM_VERSION}
    ports:
     - 2181:2181
     - 2888:2888
     - 3888:3888
  kafka:
    image: debezium/kafka:${DEBEZIUM_VERSION}
    ports:
     - 9092:9092
    links:
     - zookeeper
    environment:
     - ZOOKEEPER_CONNECT=zookeeper:2181
  mysql:
    image: debezium/example-mysql:${DEBEZIUM_VERSION}
    ports:
     - 3306:3306
    environment:
     - MYSQL_ROOT_PASSWORD=debezium
     - MYSQL_USER=mysqluser
     - MYSQL_PASSWORD=mysqlpw
  apicurio:
    image: apicurio/apicurio-registry-mem:1.3.0.Final
    ports:
     - 8080:8080
  connect:
    image: debezium/connect:${DEBEZIUM_VERSION}
    ports:
     - 8083:8083
    links:
     - kafka
     - mysql
     - apicurio
    environment:
     - BOOTSTRAP_SERVERS=kafka:9092
     - GROUP_ID=1
     - CONFIG_STORAGE_TOPIC=my_connect_configs
     - OFFSET_STORAGE_TOPIC=my_connect_offsets
     - STATUS_STORAGE_TOPIC=my_connect_statuses
     - ENABLE_APICURIO_CONVERTERS=true
```

Проверка работоспособности Apicurio:

```console
$ curl -i -X GET -H "Accept:application/json" localhost:8080/api/artifacts
```

Документация по API [Apicurio Registry REST API](https://www.apicur.io/registry/docs/apicurio-registry/1.3.3.Final/assets-attachments/registry-rest-api.htm)

Для настройки KafkaConnect можно использовать два файла:

* [register-mysql-apicurio-converter-avro.json](https://github.com/debezium/debezium-examples/blob/master/tutorial/register-mysql-apicurio-converter-avro.json)
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
        "database.include": "inventory",
        "database.history.kafka.bootstrap.servers": "kafka:9092",
        "database.history.kafka.topic": "schema-changes.inventory",
        "key.converter": "io.apicurio.registry.utils.converter.AvroConverter",
        "key.converter.apicurio.registry.url": "http://apicurio:8080/api",
        "key.converter.apicurio.registry.global-id": "io.apicurio.registry.utils.serde.strategy.AutoRegisterIdStrategy",
        "value.converter": "io.apicurio.registry.utils.converter.AvroConverter",
        "value.converter.apicurio.registry.url": "http://apicurio:8080/api",
        "value.converter.apicurio.registry.global-id": "io.apicurio.registry.utils.serde.strategy.AutoRegisterIdStrategy"
    }
}
```
* [register-mysql-apicurio-converter-json.json](https://github.com/debezium/debezium-examples/blob/master/tutorial/register-mysql-apicurio-converter-json.json)
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
        "database.include": "inventory",
        "database.history.kafka.bootstrap.servers": "kafka:9092",
        "database.history.kafka.topic": "schema-changes.inventory",
        "key.converter": "io.apicurio.registry.utils.converter.ExtJsonConverter",
        "key.converter.apicurio.registry.url": "http://apicurio:8080/api",
        "key.converter.apicurio.registry.global-id": "io.apicurio.registry.utils.serde.strategy.AutoRegisterIdStrategy",
        "value.converter.apicurio.registry.url": "http://apicurio:8080/api",
        "value.converter": "io.apicurio.registry.utils.converter.ExtJsonConverter",
        "value.converter.apicurio.registry.global-id": "io.apicurio.registry.utils.serde.strategy.AutoRegisterIdStrategy"
    }
}
```

`key.converter.apicurio.registry.global-id` и  `value.converter.apicurio.registry.global-id` могут быть:
* io.apicurio.registry.utils.serde.strategy.AutoRegisterIdStrategy - создаёт новую версию схемы каждый раз
* io.apicurio.registry.utils.serde.strategy.GetOrCreateIdStrategy - использует существующую схему при наличии


#### register-mysql-apicurio-converter-json.json

При использоввании JSON-конвертера данные приходят в виде JSON, схема приходит в виде идентификатора:

```console
$ kafkacat -b localhost:9092 -C -t dbserver1.inventory.addresses

{"schemaId":33,"payload":{"id":10}}{"schemaId":34,"payload":{"before":null,"after":{"id":10,"customer_id":1001,"street":"3183 Moore Avenue","city":"Euless","state":"Texas","zi
p":"76036","type":"SHIPPING"},"source":{"version":"1.4.1.Final","connector":"mysql","name":"dbserver1","ts_ms":0,"snapshot":"true","db":"inventory","table":"addresses","server
_id":0,"gtid":null,"file":"mysql-bin.000003","pos":154,"row":0,"thread":null,"query":null},"op":"c","ts_ms":1614340913613,"transaction":null}}
```

Схему можно попросить так:
* для ключа
    * по идентификатору: `curl -i -X GET -H "Accept:application/json" localhost:8080/api/ids/33`
    * по символическому названию: `curl -i -X GET -H "Accept:application/json" localhost:8080/api/artifacts/dbserver1.inventory.addresses-key`
* для значения
    * по идентификатору: `curl -i -X GET -H "Accept:application/json" localhost:8080/api/ids/34`
    * по символическому названию: `curl -i -X GET -H "Accept:application/json" localhost:8080/api/artifacts/dbserver1.inventory.addresses-value`

#### register-mysql-apicurio-converter-avro.json

При использовании AVRO-коннектора все данные приходят в формате AVRO.

```console
$ # kafkacat -b localhost:9092 -C -t dbserver1.inventory.addresses -s avro -r http://localhost:8080/api/ccompat -e

% ERROR: Failed to format message in dbserver1.inventory.addresses [0] at offset 0: Avro/Schema-registry key deserialization: REST request failed (code 404): {"message":"No artifact with ID '0' was found.","error_code":404} : terminating
```

Вот в этой статье нашёл, как конфигурировать Apicurio [Debezium Serialization With Apache Avro and Apicurio Service Registry](https://dzone.com/articles/debezium-serialization-with-apache-avro-and-apicur)

```json
  "key.converter": "io.apicurio.registry.utils.converter.AvroConverter",
  "key.converter.apicurio.registry.url": "http://registry:8080/api",
  "key.converter.apicurio.registry.global-id": "io.apicurio.registry.utils.serde.strategy.AutoRegisterIdStrategy",
  "key.converter.apicurio.registry.as-confluent": "true",
  "value.converter": "io.apicurio.registry.utils.converter.AvroConverter",
  "value.converter.apicurio.registry.url": "http://registry:8080/api",
  "value.converter.apicurio.registry.global-id": "io.apicurio.registry.utils.serde.strategy.AutoRegisterIdStrategy",
  "value.converter.apicurio.registry.as-confluent": "true"
```

После этого kafkacat заработал:

```console
$ kafkacat -b localhost:9092 -t dbserver1.inventory.addresses -s avro -r http://localhost:8080/api/ccompat

% Auto-selecting Consumer mode (use -P or -C to override)
%3|1614342784.045|FAIL|rdkafka#consumer-1| [thrd:localhost:9092/bootstrap]: localhost:9092/bootstrap: Connect to ipv6#[::1]:9092 failed: Connection refused (after 0ms in state
 CONNECT)
% ERROR: Local: Broker transport failure: localhost:9092/bootstrap: Connect to ipv6#[::1]:9092 failed: Connection refused (after 0ms in state CONNECT)
% ERROR: Local: All broker connections are down: 1/1 brokers are down : terminating
[root@c-t-hdp-debezium-app-01 ~]# kafkacat -b localhost:9092 -C -t dbserver1.inventory.addresses -s avro -r http://localhost:8080/api/ccompat
{"id": 10}{"before": null, "after": {"Value": {"id": 10, "customer_id": 1001, "street": "3183 Moore Avenue", "city": "Euless", "state": "Texas", "zip": "76036", "type": "SHIPP
ING"}}, "source": {"version": "1.4.1.Final", "connector": "mysql", "name": "dbserver1", "ts_ms": 0, "snapshot": {"string": "true"}, "db": "inventory", "table": {"string": "add
resses"}, "server_id": 0, "gtid": null, "file": "mysql-bin.000003", "pos": 154, "row": 0, "thread": null, "query": null}, "op": "c", "ts_ms": {"long": 1614342768735}, "transac
tion": null}
```

Запросы через API Apicurio также работают.

#### Web Console

Вот тут доступна WebConsole: http://c-t-hdp-debezium-app-01:8080/ui

**Похоже, выгодней использовать Apicurio.**

## Confluent Schema Registry

[Confluence Schema Management](https://docs.confluent.io/platform/current/schema-registry/index.html)

[Kafka Schema](./Kafka.md#Confluent-Schema-Registry)

Спросил в канале Slack:

> Can someone point me out, what are the main advantages of a Confluent Schema Registry versus an Apicurio schema regisrty?
>
> Robert Yokota:
>
> The main advantage of Confluent Schema Registry is that *it comes with out of the box integration* with Kafka Connect, Confluent REST Proxy, Kafka Streams, ksqlDB, Confluent Schema Validation, and Confluent Control Center, as described here:  https://www.confluent.io/blog/confluent-platform-now-supports-protobuf-json-schema-custom-formats/
>
> It also has support for schema references as described here: https://www.confluent.io/blog/multiple-event-types-in-the-same-kafka-topic/

### API

* https://docs.confluent.io/platform/current/schema-registry/develop/using.html

Спросил в [канале Slack Confluent #schema-registry](https://confluentcommunity.slack.com/archives/C49ST5555/p1615375957089400) про то как передаётся идентификатор схемы.

Robin Moffatt:

> each message will have the schema id as part of the 'magic byte' at the front of each message but I'm not sure how you'd get that out without writing your own code and calling the deserialiser
>
> you can use the Schema Registry API to poke around and see [Confluent Schema Registry REST API cheatsheet](https://rmoff.net/2019/01/17/confluent-schema-registry-rest-api-cheatsheet/)
>
> [Wire Format](https://docs.confluent.io/platform/current/schema-registry/serdes-develop/index.html#wire-format)

```sh
curl --silent -X GET http://localhost:8081/subjects | jq .
```

### [On-Premises Schema Registry Tutorial](https://docs.confluent.io/platform/current/schema-registry/schema_registry_onprem_tutorial.html#schema-registry-onprem-tutorial)

### [How to programmatically get schema from confluent schema registry in Python](https://stackoverflow.com/questions/60467878/how-to-programmatically-get-schema-from-confluent-schema-registry-in-python)

* [Confluent's Python Client for Apache Kafka](https://github.com/confluentinc/confluent-kafka-python)
* [SchemaRegistryClient](https://marcosschroh.github.io/python-schema-registry-client/client/)

## Apicurio Schema Registry

[Apicurio Registry](https://www.apicur.io/registry/)

Похоже, там RedHat управляет этим проектом.

[Using MySQL and Apicurio Registry](https://github.com/debezium/debezium-examples/tree/master/tutorial#using-mysql-and-apicurio-registry):

> Apicurio Registry is an open-source API and schema registry that amongst other things can be used to store schemas of Kafka records. It provides
>
> * its own native Avro converter and Protobuf serializer
> * a JSON converter that exports its schema into the registry
> * a compatibility layer with other schema registries such as IBM's or Confluent's; it can be used with the Confluent Avro converter.


Основная документация вот тут <https://github.com/Apicurio/apicurio-registry>

Apicurio Registry supports 4 persistence implementations:

* In-Memory
* Kafka (Topics vs. KV-Store / Streams)
* SQL
* Infinispan (POC / WIP)

### [SQL](https://github.com/Apicurio/apicurio-registry#sql)


* In the dev mode, the application expects a H2 server running at jdbc:h2:tcp://localhost:9123/mem:registry.
* In the prod mode, you have to provide connection configuration for a PostgreSQL server as follows:



| Option          | Command argument              | Env. variable                |
|-----------------|-------------------------------|------------------------------|
| Data Source URL | -Dquarkus.datasource.jdbc.url | REGISTRY_DATASOURCE_URL      |
| DS Username     | -Dquarkus.datasource.username | REGISTRY_DATASOURCE_USERNAME |
| DS Password     | -Dquarkus.datasource.password | REGISTRY_DATASOURCE_PASSWORD |

To see additional options, visit:

* [Data Source config](https://quarkus.io/guides/datasource)
* [Data Source options](https://quarkus.io/guides/datasource-guide#configuration-reference)
* [Hibernate options](https://quarkus.io/guides/hibernate-orm-guide#properties-to-refine-your-hibernate-orm-configuration)

### [Docker containers]https://github.com/Apicurio/apicurio-registry#docker-containers)

Публикуются несколько вариантов контейнеров для всех имплементаций хранилища.

* https://hub.docker.com/r/apicurio/apicurio-registry-sql

### Сборка

Нужно будет собирвать проект. Процесс сборки можно посмотреть в GitHub https://github.com/Apicurio/apicurio-registry/blob/master/.github/workflows
