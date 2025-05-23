---
id: tw3a84m7vc6r9rtnxkapvj0
title: Confluent
desc: ''
updated: 1674728869692
created: 1642057007257
tags: confluent kafka streaming
---

* [5 Steps to Event Streaming](../../attachments/20200929-EB-Five_Steps_to_Event_-560450247.pdf)
* [Deploying Confluent Platform (Kafka) OSS using Docker](https://medium.com/@robcowart/deploying-confluent-platform-kafka-oss-using-docker-39b65fa6809b)

# Pricing

* [Cloud](https://www.confluent.io/confluent-cloud/pricing)
* [Platform](https://www.confluent.io/product/confluent-platform) по подписке, цена не понятна

# Версии платформы

Список доступных версий можно посмотреть на этой странице: https://packages.confluent.io/archive/

# Разработка

* [Resources for Developers](https://developer.confluent.io/)

# Demo

* [Kafka Event Streaming Applications](https://github.com/confluentinc/cp-demo)
* [Confluent Platform Demo (cp-demo)](https://docs.confluent.io/platform/current/tutorials/cp-demo/docs/index.html?utm_source=github&utm_medium=demo&utm_campaign=ch.cp-demo_type.community_content.cp-demo)

This example and accompanying tutorial show users how to deploy an Apache Kafka® event streaming application using ksqlDB and Kafka Streams for stream processing. All the components in the Confluent Platform have security enabled end-to-end. Run the example with the tutorial.

У Confluent множество активно обновляемых репозитариев. Видимо, начинать надо вот с этого репозитария: https://github.com/confluentinc/examples, либо с этого https://github.com/confluentinc/cp-all-in-one

# Установка

## On-Premises

* [On-Premises Deployment](https://docs.confluent.io/platform/current/installation/installing_cp/overview.html)

## Из бинарных архивов

* [Manual Install using ZIP and TAR Archives](https://docs.confluent.io/platform/current/installation/installing_cp/zip-tar.html#manual-install-using-zip-and-tar-archives)

* `curl -O http://packages.confluent.io/archive/6.1/confluent-6.1.0.tar.gz`
* `curl -O http://packages.confluent.io/archive/6.1/confluent-community-6.1.0.tar.gz`

## Ubuntu/Debian

* [Manual Install using Systemd on Ubuntu and Debian](https://docs.confluent.io/platform/current/installation/installing_cp/deb-ubuntu.html#manual-install-using-systemd-on-ubuntu-and-debian)

Определить версию платформы:

```sh
confluent_version=7.1
```

Установка публичного ключа:

```sh
wget -qO - https://packages.confluent.io/deb/${confluent_version}/archive.key | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/confluent_${confluent_version}.gpg
```

Добавить репозитарии:

```sh
sudo add-apt-repository "deb [arch=amd64] https://packages.confluent.io/deb/${confluent_version} stable main"
# sudo add-apt-repository "deb https://packages.confluent.io/clients/deb $(lsb_release -cs) main"
```

## RHEL/CentOS

[Manual Install using Systemd on RHEL and CentOS](https://docs.confluent.io/platform/current/installation/installing_cp/rhel-centos.html#manual-install-using-systemd-on-rhel-and-centos)

* [Installing and Upgrading the Confluent Schema Registry](https://blog.clairvoyantsoft.com/installing-and-upgrading-the-kafka-schema-registry-2b2944a22244)

```console
$ sudo rpm --import https://packages.confluent.io/rpm/6.1/archive.key
```

*/etc/yum.repos.d/confluent.repo*:

```
[Confluent.dist]
name=Confluent repository (dist)
baseurl=https://packages.confluent.io/rpm/6.1/$releasever
gpgcheck=1
gpgkey=https://packages.confluent.io/rpm/6.1/archive.key
enabled=1

[Confluent]
name=Confluent repository
baseurl=https://packages.confluent.io/rpm/6.1
gpgcheck=1
gpgkey=https://packages.confluent.io/rpm/6.1/archive.key
enabled=1
```

```
$ sudo yum clean all
$ yum search confluent
```

## Docker

* [Install using Docker](https://docs.confluent.io/platform/current/installation/docker/installation.html#install-using-docker)


## Confluent Hub client

* https://docs.confluent.io/kafka-connectors/self-managed/confluent-hub/client.html

```
curl -L -O http://client.hub.confluent.io/confluent-hub-client-latest.tar.gz
```

# [Apache Kafka Quick Start](https://docs.confluent.io/platform/current/quickstart/index.html)

# Kafka Connect

## [Kafka Connect Concepts](https://docs.confluent.io/platform/current/connect/concepts.html)

## [Getting Started with Kafka Connect](https://docs.confluent.io/home/connect/userguide.html)

### [Configuring and Running Workers]https://docs.confluent.io/home/connect/userguide.html#configuring-and-running-workers

### [Connect Internal Topics](https://docs.confluent.io/home/connect/userguide.html#kconnect-internal-topics)

## [Connect REST Interface](https://docs.confluent.io/platform/current/connect/references/restapi.html)

## [Kafka Connect HDFS 2](https://www.confluent.io/hub/confluentinc/kafka-connect-hdfs)

В открытом доступе коннектор для HDFS2: https://github.com/confluentinc/kafka-connect-hdfs

<https://github.com/confluentinc/kafka-connect-hdfs/issues/495#issuecomment-622630426>:

> looks like you are running HDFS3 and not HDFS2 which is what this repo is.

## [Kafka Connect HDFS 3](https://www.confluent.io/hub/confluentinc/kafka-connect-hdfs3-source)

## [Install HDFS 3 Sink Connector](https://docs.confluent.io/kafka-connect-hdfs3-sink/current/index.html#install-the-connector-using-c-hub)

По ссылке на загрузку:

`curl -O https://d1i4a15mxbxib1.cloudfront.net/api/plugins/confluentinc/kafka-connect-hdfs/versions/10.0.1/confluentinc-kafka-connect-hdfs-10.0.1.zip`

Configure an instance of your connector
Once installed, you can then create a connector configuration file with the connector's settings, and deploy that to a Connect worker.

Далее необходимо следовать инструкциям в разделе [#Install-Connect-Plugins].

## [HDFS 3 Sink Connector for Confluent Platform](https://docs.confluent.io/kafka-connect-hdfs3-sink/current/index.html)

The Kafka Connect HDFS 3 connector allows you to export data from Kafka topics to HDFS 3.x files in a variety of formats and integrates with Hive to make data immediately available for querying with HiveQL.

The connector periodically polls data from Apache Kafka® and writes them to HDFS. The data from each Kafka topic is partitioned by the provided partitioner and divided into chunks. Each chunk of data is represented as an HDFS file with topic, Kafka partition, start and end offsets of this data chunk in the file name. If a partitioner is not specified in the configuration, the default partitioner which preserves the Kafka partitioning is used. The size of each data chunk is determined by the number of records written to HDFS, the time written to HDFS, and schema compatibility.

The HDFS connector integrates with Hive and when it is enabled, the connector automatically creates an external Hive partitioned table for each Kafka topic and updates the table according to the available data in HDFS.

## Kafka Connect RabbitMQ

* [Streaming messages from RabbitMQ into Kafka with Kafka Connect](https://rmoff.net/2020/01/08/streaming-messages-from-rabbitmq-into-kafka-with-kafka-connect/) #[[Roam-Highlights]]

### Prerequisites

The following are required to run the Kafka Connect HDFS 3 Sink Connector:

* Kafka Broker: Confluent Platform 3.3.0 or above, or Kafka 0.11.0 or above
* Connect: Confluent Platform 3.3.0 or above, or Kafka 0.11.0 or above
* Java 1.8
* HDFS 3.x cluster
* Hive 3.x

*This connector ships with HDFS 3.x client and Hive 3.x libraries, which are not compatible with HDFS 2.x or Hive 2.x clusters.*

### [HDFS 3 Sink Connector Configuration Properties](https://docs.confluent.io/kafka-connect-hdfs3-sink/current/configuration_options.html#hdfs-3-sink-connector-configuration-properties)

```json
{
  "name": "hdfs3-sink",
  "config": {
    "connector.class": "io.confluent.connect.hdfs3.Hdfs3SinkConnector",
    "tasks.max": "1",
    "topics": "test_hdfs",
    "hdfs.url": "hdfs://localhost:9000",
    "flush.size": "3",
    "key.converter": "org.apache.kafka.connect.storage.StringConverter",
    "value.converter": "io.confluent.connect.avro.AvroConverter",
    "value.converter.schema.registry.url":"http://localhost:8081",
    "confluent.topic.bootstrap.servers": "localhost:9092",
    "confluent.topic.replication.factor": "1"
  }
}
```

```json
{
    "name": "hdfs3-parquet-field",
    "config": {
        "connector.class": "io.confluent.connect.hdfs3.Hdfs3SinkConnector",
        "tasks.max": "1",
        "topics": "parquet_field_hdfs",
        "hdfs.url": "hdfs://localhost:9000",
        "flush.size": "3",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "value.converter": "io.confluent.connect.avro.AvroConverter",
        "value.converter.schema.registry.url":"http://localhost:8081",
        "confluent.topic.bootstrap.servers": "localhost:9092",
        "confluent.topic.replication.factor": "1",

        "format.class":"io.confluent.connect.hdfs3.parquet.ParquetFormat",
        "partitioner.class":"io.confluent.connect.storage.partitioner.FieldPartitioner",
        "partition.field.name":"is_customer"
    }
}
```

#### [Confluent license properties](https://docs.confluent.io/kafka-connect-hdfs3-sink/current/configuration_options.html#confluent-license-properties)

*While it is possible to include license-related properties in the connector configuration, starting with Confluent Platform version 6.0, you can now put license-related properties in the Connect worker configuration instead of in each connector configuration.*

**This connector is proprietary and requires a license. The license information is stored in the _confluent-command topic. If the broker requires SSL for connections, you must include the security-related confluent.topic.* properties as described below.**

#### [License topic configuration](https://docs.confluent.io/kafka-connect-hdfs3-sink/current/configuration_options.html#license-topic-configuration)

The following describes how the default _confluent-command topic is generated under different scenarios:

* *A 30-day trial license** is automatically generated for the `_confluent command` topic if you do not add the `confluent.license` property or leave this property empty (for example, `confluent.license=`).
* Adding a valid license key (for example, `confluent.license=<valid-license-key>`) adds a valid license in the `_confluent-command topic`.


# [Install Connectors](https://docs.confluent.io/home/connect/install.html#install-connectors)

## [Install Connect Plugins](https://docs.confluent.io/home/connect/userguide.html#installing-kconnect-plugins)


# Schema Registry

* https://docs.confluent.io/platform/current/schema-registry/installation/index.html
* [[streamprocessing.schemaregistry]]

# Confluent Tutorial

TODO: Надо найти ссылку на этот проект

```yml
---
version: '2'
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:7.0.1
    hostname: zookeeper
    container_name: zookeeper
    ports:
      - "2181:2181"
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000
    logging:
      options:
        max-size: "10m"
        max-file: "3"

  broker:
    image: confluentinc/cp-kafka:7.0.1
    hostname: broker
    container_name: broker
    depends_on:
      - zookeeper
    ports:
      - "29092:29092"
      - "9092:9092"
      - "9101:9101"
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: 'zookeeper:2181'
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: PLAINTEXT:PLAINTEXT,PLAINTEXT_HOST:PLAINTEXT
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://broker:29092,PLAINTEXT_HOST://localhost:9092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
      KAFKA_TRANSACTION_STATE_LOG_MIN_ISR: 1
      KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR: 1
      KAFKA_GROUP_INITIAL_REBALANCE_DELAY_MS: 0
      KAFKA_JMX_PORT: 9101
      KAFKA_JMX_HOSTNAME: localhost
    logging:
      options:
        max-size: "300m"
        max-file: "3"

  schema-registry:
    image: confluentinc/cp-schema-registry:7.0.1
    hostname: schema-registry
    container_name: schema-registry
    depends_on:
      - broker
    ports:
      - "8081:8081"
    environment:
      SCHEMA_REGISTRY_HOST_NAME: schema-registry
      SCHEMA_REGISTRY_KAFKASTORE_BOOTSTRAP_SERVERS: 'broker:29092'
      SCHEMA_REGISTRY_LISTENERS: http://0.0.0.0:8081
    logging:
      options:
        max-size: "10m"
        max-file: "3"

  rest-proxy:
    image: confluentinc/cp-kafka-rest:7.0.1
    depends_on:
      - broker
      - schema-registry
    ports:
      - 8082:8082
    hostname: rest-proxy
    container_name: rest-proxy
    environment:
      KAFKA_REST_HOST_NAME: rest-proxy
      KAFKA_REST_BOOTSTRAP_SERVERS: 'broker:29092'
      KAFKA_REST_LISTENERS: "http://0.0.0.0:8082"
      KAFKA_REST_SCHEMA_REGISTRY_URL: 'http://schema-registry:8081'
      KAFKA_REST_CONSUMER_INSTANCE_TIMEOUT_MS: 3600000
    logging:
      options:
        max-size: "10m"
        max-file: "3"
  kafka-ui:
    image: provectuslabs/kafka-ui
    container_name: kafka-ui
    ports:
      - "8080:8080"
    restart: always
    environment:
      - KAFKA_CLUSTERS_0_NAME=local
      - KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS=broker:29092
      - KAFKA_CLUSTERS_0_ZOOKEEPER=localhost:2181
    logging:
      options:
        max-size: "10m"
        max-file: "3"
```
