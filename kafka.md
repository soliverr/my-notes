---
id: qz9eznwqqu4zzij1ph5n0by
title: Kafka
desc: ''
updated: 1666175709874
created: 1642057144877
tags: cdc debezium kafka kafka-docker streaming
---

Kafka
=====

[Apache Kafka](https://kafka.apache.org/) -  is an open-source distributed event streaming platform used by thousands of companies for high-performance data pipelines, streaming analytics, data integration, and mission-critical applications.

<https://qimia.io/en/blog/Developing-Streaming-Applications-Kafka>:

> Kafka has been created by LinkedIn and is currently an open-source project maintained by Confluent.

---

## Ссылки

* [Проектирование событийно-ориентированных систем. Бен Стопфорд](https://www.itsumma.ru/press)
>  Перевод книги:  Ben Stopford. DESIGNING EVENT-DRIVEN SYSTEMS
* [KafkaThe Definitive Guide](https://book.huihoo.com/pdf/confluent-kafka-definitive-guide-complete.pdf)
* https://cwiki.apache.org/confluence/display/KAFKA/Index
* [Mirror on GitHub](https://github.com/apache/kafka)

---

## The basics of Kafka

**Topics** — categories for messages. They are multi-subscriber and can have multiple consumers. Each topic has a partitioned log. A Topic looks like this:

![media-r33029](../../media/media-r33029-1085348390.png)

**Producer** — publish data to the appropriate topics.

**Consumer** — applications that subscribe to Topics. Consumer instances can be in separate processes or machines to ensure high-availability.

**Consumer Groups** — contain 2 or more consumers instances. The messages are load balanced across consumer instances in a consumer Group

**Brokers** — responsible for taking data from the producers and sending it to the consumers.

**Partition** — an ordered, immutable, append-to log. Records are each assigned an offset value that identifies their position in the Partition. *The partitions are distributed and replicated over a Kafka cluster*. While each Topic Partition must fit on a server, they enable Topics to scale beyond a single server. **The Broker with the partition with the highest offset is the leader**. *The rest of the brokers are **followers***. The leader handles the read and write requests for the Partitions. The following brokers replicate the leader. If the leader becomes unavailable, a follower is selected to take the failed Leader’s position.

![media-w33029](../../media/media-w33029-407438182.png)

**Parallelism** — Parallelism is the simultaneous execution of multiple processes. *Each partition is consumed by exactly one consumer in a consumer group*. However, *consumers can consume records in parallel*.  If a consumer stops, Kafka can spread the partitions across the other consumers in the consumer group. In this way, **partitions are a unit of parallelism**.

**Ordering** —Since each partition is read and consumed by exactly one consumer in a consumer Group, *Kafka is able to guarantee ordering*. This is opposed to tradition queues where multiple consumers read and consume from the same queue and the ordering of records can be lost. I recommend reading this blog to learn more about [parallelism and ordering within Kafka](https://dzone.com/articles/kafka-topic-architecture-replication-failover-and).

**Processing** — the Stream API enables real-time processing of data streams. With it you can perform aggregations or joins streams together.

## [Apache Kafka Quickstart](https://kafka.apache.org/quickstart)


## Official Documentation

<https://kafka.apache.org/documentation.html>

An **event** records the fact that "something happened" in the world or in your business. It is also called record or message in the documentation. When you read or write data to Kafka, you do this in the form of events. *Conceptually, an event has a key, value, timestamp, and optional metadata headers*.

Events are organized and durably stored in **topics**. Very simplified, a topic is similar to a folder in a filesystem, and the events are the files in that folder. ***Topics in Kafka are always multi-producer and multi-subscriber***: a topic can have zero, one, or many producers that write events to it, as well as zero, one, or many consumers that subscribe to these events. *Events in a topic can be read as often as needed*—unlike traditional messaging systems, events are not deleted after consumption. Instead, ***you define for how long Kafka should retain your events through a per-topic configuration setting***, after which old events will be discarded. Kafka's performance is effectively constant with respect to data size, so storing data for a long time is perfectly fine.

Topics are **partitioned**, meaning a topic is spread over a number of "buckets" located on different Kafka brokers. *This distributed placement of your data is very important for scalability because it allows client applications to both read and write the data from/to many brokers at the same time*. When a new event is published to a topic, it is actually appended to one of the topic's partitions. Events with the same event key (e.g., a customer or vehicle ID) are written to the same partition, and Kafka guarantees that any consumer of a given topic-partition will always read that partition's events in exactly the same order as they were written.

![media-h97732](../../media/media-h97732-808334014.png)

To make your data fault-tolerant and highly-available, every topic can be **replicated**, even across geo-regions or datacenters, so that there are always multiple brokers that have a copy of the data just in case things go wrong, you want to do maintenance on the brokers, and so on. ***A common production setting is a replication factor of 3, i.e., there will always be three copies of your data***. This replication is performed at the level of topic-partitions.

### [Kafka APIs](https://kafka.apache.org/documentation/#api)

In addition to command line tooling for management and administration tasks, Kafka has five core APIs for Java and Scala:

* *The Admin API* to manage and inspect topics, brokers, and other Kafka objects.
* *The Producer API* to publish (write) a stream of events to one or more Kafka topics.
* *The Consumer API* to subscribe to (read) one or more topics and to process the stream of events produced to them.
* *The Kafka Streams API* to *implement stream processing applications and microservices*. It provides higher-level functions to process event streams, including transformations, stateful operations like aggregations and joins, windowing, processing based on event-time, and more. Input is read from one or more topics in order to generate output to one or more topics, effectively transforming the input streams to output streams.
* *The Kafka Connect API* to *build and run reusable data import/export connectors* that consume (read) or produce (write) streams of events from and to external systems and applications so they can integrate with Kafka. For example, a connector to a relational database like PostgreSQL might capture every change to a set of tables. However, in practice, you typically don't need to implement your own connectors because the Kafka community already provides hundreds of ready-to-use connectors.

Документация по API в формате Javadoc: https://kafka.apache.org/27/javadoc/index.html?org/apache/kafka/connect

### Stream Processing

Many users of Kafka process data in processing pipelines consisting of multiple stages, where raw input data is consumed from Kafka topics and then aggregated, enriched, or otherwise transformed into new topics for further consumption or follow-up processing. For example, a processing pipeline for recommending news articles might crawl article content from RSS feeds and publish it to an "articles" topic; further processing might normalize or deduplicate this content and publish the cleansed article content to a new topic; a final processing stage might attempt to recommend this content to users. Such processing pipelines create graphs of real-time data flows based on the individual topics. Starting in 0.10.0.0, a light-weight but powerful stream processing library called Kafka Streams is available in Apache Kafka to perform such data processing as described above. Apart from Kafka Streams, alternative open source stream processing tools include Apache Storm and Apache Samza.

### [Kafka Ecosystem](https://cwiki.apache.org/confluence/display/KAFKA/Ecosystem)

## Kafka Tools

### Kafka Cluster Manager

* [CMAK](https://github.com/yahoo/CMAK)
* [AKHQ](https://github.com/tchiotludo/akhq)
* [Kafdrop](https://github.com/obsidiandynamics/kafdrop)
* [Kafka UI](https://github.com/provectus/kafka-ui)
* [Kafka Desktop Administration App](https://www.conduktor.io)

### CLI and UI

* [Kafka Tool UI](ttps://www.kafkatool.com/)
* [KafkaCat](https://github.com/edenhill/kafkacat)
    * [librdkafka](https://github.com/edenhill/librdkafka)

#### KafkaCat

Установка в Ubuntu/Debian:

```console
$ sudo apt install kafkacat
```

Оказалось, эта сборка идёт без поддержки Avro:

```console
$ kafkacat -b localhost:9092 -C -t dbserver1.inventory.addresses -e -s avro -r http://localhost:8085
% ERROR: This build of kafkacat lacks Avro/Schema-Registry support
```

Для сборки вручную в дереве проекта есть скрипт  `bootsrap.sh`

```console
$ sudo apt install gcc g++ autoconf automake libtool make cmake pkg-config
$ sudo apt install libsasl2-dev libzstd-dev
$ sudo apt install libcurl4-openssl-dev
$ ./bootstrap.sh
$ sudo make install
```

TODO: Надо найти скипты упаковки в deb-пакет.

#### KCat

KafkaCat переименовался в KCat: https://github.com/edenhill/kcat

```sh
# List brokers and topics in cluster
$ docker run -it --network=host edenhill/kcat:1.7.1 -b YOUR_BROKER -L
```

Для Ubuntu устанавливать пакет `kafkacat`

### Python

* [Kafka-Python](https://github.com/dpkp/kafka-python) — An open-source community-based library.
* [PyKafka](https://github.com/Parsely/pykafka) — This library is maintained by Parsly and it’s claimed to be a Pythonic API. Unlike Kafka-Python you can’t create dynamic topics.
* [Confluent Python Kafka](https://github.com/confluentinc/confluent-kafka-python):- It is offered by Confluent as a thin wrapper around librdkafka, hence it’s performance is better than the two.
* [Kafka in Python](Kafka%20in%20Python.md)

### Scala

* [Kafka in Scala](Kafka%20in%20Scala.md)

### Stress Tests

* https://github.com/msfidelis/kafka-stress


wget https://github.com/msfidelis/kafka-stress/releases/download/v0.0.7/kafka-stress_0.0.7_linux_amd64 -O kafka-stress 

```
./kafka-stress --bootstrap-servers kafka-headless:9092 --events 30000 --size 5120 --topic kafka-stress --test-mode producer
./kafka-stress --verbose --bootstrap-servers kafka-headless:9092 --events 30000 --topic kafka-stress --topic kafka-stress --test-mode consumer --consumers 6
```


## Kafka in Docker

### kafka-docker

<https://github.com/wurstmeister/kafka-docker>

### Strimzi: Kafka on Kubernetes in a few minutes

Kafka in Kubernetes

* [Kafka on Kubernetes in a few minutes](https://strimzi.io/)
* [Using Strimzi](https://strimzi.io/docs/operators/latest/using.html)
* [Kafka Connect on Kubernetes The Easy Way!](https://dzone.com/articles/kafka-connect-on-kubernetes-the-easy-way)

### Quick Starts

[Quick Starts](https://strimzi.io/quickstarts/)

## Lenses

[Lenses](./streamprocessing.lenses.md)

## Confluent

Коммерческая платформа потоковой обработки.

[Confluent]](./Confluent.md)


## Apache Gobblin

*

## Kafka Connect

* [Kafka Connect](https://docs.confluent.io/platform/current/connect/index.html)
* [How to Build a Scalable ETL Pipeline with Kafka Connect](https://www.confluent.io/blog/how-to-build-a-scalable-etl-pipeline-with-kafka-connect/)

Более подробная информация по архитектуре и конфигурированию Kafka Connect дана в документации платформы [Confluent](./Confluent.mb).

### Ingest data with Debezium

* [Deploying Debezium using the new KafkaConnector resource](https://strimzi.io/blog/2020/01/27/deploying-debezium-with-kafkaconnector-resource/)
* [Streaming Data from MySQL into Kafka with Kafka Connect and Debezium](https://rmoff.net/2018/03/24/streaming-data-from-mysql-into-kafka-with-kafka-connect-and-debezium/)
* [How to Create MySQL CDC Kafka Pipeline: Easy Steps](https://hevodata.com/learn/mysql-cdc-kafka/)\
* [How to do an initial snapshot load with debezium mysql connector kafka](https://stackoverflow.com/questions/61091091/how-to-do-an-initial-snapshot-load-with-debezium-mysql-connector-kafka)

### Integration with Hive

* [How can I send data from kafka to hive](https://stackoverflow.com/questions/50346798/how-can-i-send-data-from-kafka-to-hive)
* [Introducing Hive-Kafka integration for real-time Kafka SQL queries](https://blog.cloudera.com/introducing-hive-kafka-sql/)
* [Integrating Apache Hive with Kafka, Spark, and BI](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/integrating-hive/content/hive-kafka-integration.html)
* [Ingest bulk data from multiple Kafka topics into Hive or HDFS using streaming mapping](https://docs.informatica.com/data-engineering/data-engineering-streaming/h2l/ingest-bulk-data-from-multiple-kafka-topics-into-hive-or-hdfs-us/ingest-bulk-data-from-multiple-kafka-topics-into-hive-or-hdfs-us/bulk-data-ingestion-solution/overview.html)
* [kafka-handler on github](https://github.com/apache/hive/tree/master/kafka-handler)
* [Hive Streaming](../Apache%20Hive%2FHive%20Streaming.md)


### [Running Kafka Connect](https://kafka.apache.org/documentation.html#connect_running)

*Kafka Connect currently supports two modes of execution: standalone (single process) and distributed.*

You can start a **standalone process** with the following command:

```
> bin/connect-standalone.sh config/connect-standalone.properties connector1.properties [connector2.properties ...]
```
The provided example should work well with a local cluster running with the default configuration provided by *config/server.properties*.

* `bootstrap.servers` - List of Kafka servers used to bootstrap connections to Kafka
* `key.converter` - Converter class used to convert between Kafka Connect format and the serialized form that is written to Kafka. This controls the format of the keys in messages written to or read from Kafka, and since this is independent of connectors it allows any connector to work with any serialization format. Examples of common formats include JSON and Avro.
* `value.converter` - Converter class used to convert between Kafka Connect format and the serialized form that is written to Kafka. This controls the format of the values in messages written to or read from Kafka, and since this is independent of connectors it allows any connector to work with any serialization format. Examples of common formats include JSON and Avro.

**The important configuration options specific to standalone mode are:**

* `offset.storage.file.filename` - File to store offset data in

For configuration of the producers used by Kafka source tasks and the consumers used by Kafka sink tasks, the same parameters can be used but need to be prefixed with `producer.` and `consumer.`

Starting with 2.3.0, client configuration overrides can be configured individually per connector by using the prefixes `producer.override.` and `consumer.override.`

**Distributed mode** handles automatic balancing of work, allows you to scale up (or down) dynamically, and offers fault tolerance both in the active tasks and for configuration and offset commit data. Execution is very similar to standalone mode:

```
> bin/connect-distributed.sh config/connect-distributed.properties
```
*The difference is* in the class which is started and the configuration parameters which change how the Kafka Connect process decides where to store configurations, how to assign work, and where to store offsets and task statues. **In the distributed mode, Kafka Connect stores the offsets, configs and task statuses in Kafka topics**. *It is recommended to manually create the topics for offset, configs and statuses* in order to achieve the desired the number of partitions and replication factors. If the topics are not yet created when starting Kafka Connect, the topics will be auto created with default number of partitions and replication factor, which may not be best suited for its usage.

In particular, the following configuration parameters, in addition to the common settings mentioned above, are critical to set before starting your cluster:


* `group.id` (default *connect-cluster*) - unique name for the cluster, used in forming the Connect cluster group; note that this must not conflict with consumer group IDs
* `config.storage.topic` (default *connect-configs*) - topic to use for storing connector and task configurations; note that this should be a single partition, highly replicated, compacted topic. You may need to manually create the topic to ensure the correct configuration as auto created topics may have multiple partitions or be automatically configured for deletion rather than compaction
* `offset.storage.topic` (default *connect-offsets*) - topic to use for storing offsets; this topic should have many partitions, be replicated, and be configured for compaction
* `status.storage.topic` (default *connect-status*) - topic to use for storing statuses; this topic can have multiple partitions, and should be replicated and configured for compaction

***Note that in distributed mode the connector configurations are not passed on the command line. Instead, use the REST API described below to create, modify, and destroy connectors.***

### [Configuring Connectors](https://kafka.apache.org/documentation.html#connect_configuring)

Connector configurations are simple key-value mappings. ***For standalone mode** these are defined in a properties file and passed to the Connect process on the command line. **In distributed mode**, they will be included in the JSON payload for the request that creates (or modifies) the connector.*

There are a few common options:

* `name` - Unique name for the connector. Attempting to register again with the same name will fail.
* `connector.class` - The Java class for the connector
* `tasks.max` - The maximum number of tasks that should be created for this connector. *The connector may create fewer tasks if it cannot achieve this level of parallelism*.
* `key.converter` - (optional) Override the default key converter set by the worker.
* `value.converter` - (optional) Override the default value converter set by the worker.

The `connector.class` config supports several formats: the full name or alias of the class for this connector. If the connector is `org.apache.kafka.connect.file.FileStreamSinkConnector`, you can either specify this full name or use `FileStreamSink` or `FileStreamSinkConnector` to make the configuration a bit shorter.

*Sink connectors* also have a few additional options to control their input. Each sink connector must set one of the following:

* `topics` - A comma-separated list of topics to use as input for this connector
*  `topics.regex` - A Java regular expression of topics to use as input for this connector

### [Trasformation](https://kafka.apache.org/documentation.html#connect_transforms)

Connectors can be configured with transformations to make lightweight message-at-a-time modifications. They can be convenient for data massaging and event routing.

A transformation chain can be specified in the connector configuration.

* `transforms` - List of aliases for the transformation, specifying the order in which the transformations will be applied.
* `transforms.$alias.type` - Fully qualified class name for the transformation.
* `transforms.$alias.$transformationSpecificConfig` -  Configuration properties for the transformation

For example, lets take the built-in file source connector and use a transformation to add a static field. After adding the transformations, *connect-file-source.properties* file looks as following:

```
name=local-file-source
connector.class=FileStreamSource
tasks.max=1
file=test.txt
topic=connect-test
transforms=MakeMap, InsertSource
transforms.MakeMap.type=org.apache.kafka.connect.transforms.HoistField$Value
transforms.MakeMap.field=line
transforms.InsertSource.type=org.apache.kafka.connect.transforms.InsertField$Value
transforms.InsertSource.static.field=data_source
transforms.InsertSource.static.value=test-file-source
```

#### [Included transformations](https://kafka.apache.org/documentation.html#connect_included_transformation)

* `InsertField` - Add a field using either static data or record metadata
* `ReplaceField` - Filter or rename fields
* `MaskField` - Replace field with valid null value for the type (0, empty string, etc) or custom replacement (non-empty string or numeric value only)
* `ValueToKey` - Replace the record key with a new key formed from a subset of fields in the record value
* `HoistField` - Wrap the entire event as a single field inside a Struct or a Map
* `ExtractField` - Extract a specific field from Struct and Map and include only this field in results
* `SetSchemaMetadata` - modify the schema name or version
* `TimestampRouter` - Modify the topic of a record based on original topic and timestamp. Useful when using a sink that needs to write to different tables or indexes based on timestamps
* `RegexRouter` - modify the topic of a record based on original topic, replacement string and a regular expression
* `Filter` - Removes messages from all further processing. This is used with a predicate to selectively filter certain messages.

### [Predicates](https://kafka.apache.org/documentation.html#connect_predicates)

Transformations can be configured with predicates so that *the transformation is applied only to messages which satisfy some condition*. In particular, when combined with the `Filter` transformation predicates can be used to selectively filter out certain messages.

Predicates are specified *in the connector configuration*.

* `predicates` - Set of aliases for the predicates to be applied to some of the transformations.
* `predicates.$alias.type` - Fully qualified class name for the predicate.
* `predicates.$alias.$predicateSpecificConfig` - Configuration properties for the predicate.

All transformations have the implicit config properties `predicate` and `negate`. A predicular predicate is associated with a transformation by setting the transformation's `predicate` config to the predicate's alias. The predicate's value can be reversed using the `negate` configuration property.

For example, suppose you have a source connector which produces messages to many different topics and you want to:

* filter out the messages in the 'foo' topic entirely
* apply the `ExtractField` transformation with the field name 'other_field' to records in all topics except the topic 'bar'

```
transforms=Filter
transforms.Filter.type=org.apache.kafka.connect.transforms.Filter
transforms.Filter.predicate=IsFoo

predicates=IsFoo
predicates.IsFoo.type=org.apache.kafka.connect.predicates.TopicNameMatches
predicates.IsFoo.pattern=foo
```

```
transforms=Filter,Extract
transforms.Filter.type=org.apache.kafka.connect.transforms.Filter
transforms.Filter.predicate=IsFoo

transforms.Extract.type=org.apache.kafka.connect.transforms.ExtractField$Key
transforms.Extract.field=other_field
transforms.Extract.predicate=IsBar
transforms.Extract.negate=true

predicates=IsFoo,IsBar
predicates.IsFoo.type=org.apache.kafka.connect.predicates.TopicNameMatches
predicates.IsFoo.pattern=foo

predicates.IsBar.type=org.apache.kafka.connect.predicates.TopicNameMatches
predicates.IsBar.pattern=bar
```
#### Included predicates

* `TopicNameMatches` - matches records in a topic with a name matching a particular Java regular expression.
* `HasHeaderKey` - matches records which have a header with the given key.
* `RecordIsTombstone` - matches tombstone records, that is records with a null value.

### [Kafka Connect REST API](https://kafka.apache.org/documentation.html#connect_rest)

The REST API server can be configured using the listeners configuration option. This field should contain a list of `listeners` in the following format: `protocol://host:port,protocol2://host2:port2`. Currently supported protocols are http and https. For example:

`listeners=http://localhost:8080,https://localhost:8443`

By default, if no `listeners` are specified, the REST server runs on port 8083 using the HTTP protocol. When using HTTPS, the configuration has to include the SSL configuration. By default, it will use the `ssl.*` settings.

The REST API is used not only by users to monitor / manage Kafka Connect. *It is also used for the Kafka Connect cross-cluster communication*. Requests received on the follower nodes REST API will be forwarded to the leader node REST API. In case the URI under which is given host reachable is different from the URI which it listens on, the configuration options `rest.advertised.host.name`, r`est.advertised.port` and `rest.advertised.listener` can be used to change the URI which will be used by the follower nodes to connect with the leader.

### [Error Reporting in Connect](https://kafka.apache.org/documentation.html#connect_errorreporting)

To report errors within a connector's converter, transforms, or within the sink connector itself to the log:

* `errors.log.enable=true`
* `errors.log.include.messages=true`

To report errors within a connector's converter, transforms, or within the sink connector itself to a dead letter queue topic:

* `errors.deadletterqueue.topic.name`
* `errors.deadletterqueue.context.headers.enable=true`

Так же:

* `errors.retry.timeout` - *0 - disable retries on failure*
* `errors.retry.delay.max.ms`
* `errors.tolerance` - *`none` - Fail on first error, `all` - Tolerate all errors*



### [Kafka Connect HDFS](https://www.confluent.io/hub/confluentinc/kafka-connect-hdfs)

* [kafka-connect-hdfs on github](https://github.com/confluentinc/kafka-connect-hdfs)


*The HDFS connector allows you to export data from Kafka topics to HDFS files in a variety of formats and integrates with Hive to make data immediately available for querying with HiveQL. The connector periodically polls data from Kafka and writes them to HDFS.*

The data from each Kafka topic is partitioned by the provided partitioner and divided into chunks. Each chunk of data is represented as an HDFS file with topic, Kafka partition, start and end offsets of this data chunk in the filename.

If no partitioner is specified in the configuration, the default partitioner which preserves the Kafka partitioning is used. The size of each data chunk is determined by the number of records written to HDFS, the time written to HDFS and schema compatibility.

The HDFS connector integrates with Hive and when it is enabled, the connector automatically creates an external Hive partitioned table for each Kafka topic and updates the table according to the available data in HDFS.


## [Подключение к Kafka через Spark Structure Streaming](http://blog.skahin.ru/2020/02/kafka-spark-structure-streaming.html)

**Использование стрима в HiveQL**:

Стриминг в Orc таблицу имеет минус: огромное число мелких файлов, которые отрицательно сказываются на производительности Hive/SparkSQL.

Есть 3 выхода:
1. Периодически конкатенировать файлы из стрим таблицы в основную
    - (-) дополнительная программа, которая следит за датами файлов и объединяет старые
2. Делать батч процессинг
    + (+) единая программа, которая запускает процессинг и сжимает файлы в 1
    - (-) нужно хранить или определять offset вручную
3. Периодический стрим доступных данных (опция Trigger.Once() )
    + (+) единая программа
    + (+) offset хранит spark в hdfs и при запуске стартует с точки прошлого останова
    - (-) дополнительное плановое задание, которое стартует стрим с некоторой периодичностью
    -
*Автор для себя выбрал вариант 3, как наиболее простой в реализации*.

## Kafka and InfluxDB

[InfluxDB and Kafka: How Companies are Integrating the Two](https://www.influxdata.com/blog/influxdb-and-kafka-how-companies-are-integrating-the-two/)

![media-N33029](../../media/media-N33029-468883886.png)

Wayfair uses Kafka as a Message Queue for application metrics. In their architecture, Kafka is sandwiched between Telegraf agents. An Output Kafka Telegraf agent pipes metrics from their application to Kafka and then the Kafka-Consumer Telegraf agent collects those metrics from Kafka and sends them to InfluxDB. This model enables Wayfair to connect to multiple data centers, inject processing hooks ad hoc, and gain multi-day tolerance against severe outages.

![media-I33029](../../media/media-I33029-570273610.png)

## Scalability of Kafka Messaging using Consumer Groups

<https://blog.cloudera.com/scalability-of-kafka-messaging-using-consumer-groups/>

## Schema Registry

### [HortonWorks Schema Registry](https://github.com/hortonworks/registry)

Похоже, живой проект. [Документация проекта](https://registry-project.readthedocs.io/en/latest/).

Имеет WebGUI:

![media-s97732](../../media/media-s97732-1071494184.png)

### [Confluent Schema Registry](https://github.com/confluentinc/schema-registry)

* [Exercises for the Confluent Schema Registry Workshop](https://github.com/confluentinc/schema-registry-workshop)
* [Docker images for Schema Registry](https://github.com/confluentinc/schema-registry-images)

Похоже, активно развивающийся проект.

[Confluence Schema Management](https://docs.confluent.io/platform/current/schema-registry/index.html)

![media-G97732](../../media/media-G97732-71081792.png)

Schema Registry is a distributed storage layer for schemas which uses Kafka as its underlying storage mechanism. Some key design decisions:

* Assigns globally unique ID to each registered schema. Allocated IDs are guaranteed to be monotonically increasing but not necessarily consecutive.
* Kafka provides the durable backend, and functions as a write-ahead changelog for the state of Schema Registry and the schemas it contains.
* Schema Registry is designed to be distributed, with single-primary architecture, and ZooKeeper/Kafka coordinates primary election (based on the configuration).

### [Apicurio Schema Registry](https://github.com/Apicurio/apicurio-registry)

<https://www.apicur.io>

Нашёл вот здесь: [Using Debezium With the Apicurio API and Schema Registry](https://debezium.io/blog/2020/04/09/using-debezium-with-apicurio-api-schema-registry/)


Change events streamed from a database by Debezium are (in developer parlance) strongly typed. This means that event consumers should be aware of the types of data conveyed in the events. This problem of passing along message type data can be solved in multiple ways:

1. the message structure is passed out-of-band to the consumer, which is able to process the data stored in it
1. the message contains metadata (the schema) that is embedded within the message
1. the message contains a reference to a registry which contains the associated metadata

Apicurio enables Debezium and consumers to exchange messages whose schema is stored in the registry and pass only a reference to the schema in the messages themselves. A the structure of captured source tables and thus message schemas evolve, the registry creates new versions of the schemas, too, so not only current but also historical schemas are available.

Apicurio provides multiple serialization formats out-of-the-box:

* JSON with externalized schema support
* [Apache Avro](https://avro.apache.org/)
* [Protocol Buffers](https://developers.google.com/protocol-buffers)

Every serializer and deserializer knows how to automatically interact with the Apicurio API so the consumer is isolated from it as an implementation detail. The only information necessary is the location of the registry.

Apicurio also provides API compatibility layers for schema registries from IBM and Confluent. This is a very useful feature, as it enables the use of 3rd-party tools like [kafkacat](#KafkaCat), even if they are not aware of Apicurio’s native API.

<https://debezium.io/documentation/reference/configuration/avro.html#about-the-registry>

> The Apicurio Registry project also provides a JSON converter. This converter combines the advantage of less verbose messages with human-readable JSON. Messages do not contain the schema information themselves, but only a schema ID.

#### [KafkaCat](https://github.com/edenhill/kafkacat)

kafkacat is a generic non-JVM producer and consumer for Apache Kafka >=0.8, think of it as a netcat for Kafka.

In **producer** mode kafkacat reads messages from stdin, delimited with a configurable delimiter (-D, defaults to newline), and produces them to the provided Kafka cluster (-b), topic (-t) and partition (-p).

In **consumer** mode kafkacat reads messages from a topic and partition and prints them to stdout using the configured message delimiter.

There's also support for the Kafka >=0.9 high-level balanced consumer, use the -G <group> switch and provide a list of topics to join the group.

kafkacat also features a Metadata list (-L) mode to display the current state of the Kafka cluster and its topics and partitions.

Supports Avro message deserialization using **the Confluent Schema-Registry**, and generic primitive deserializers (see examples below).

kafkacat is fast and lightweight; statically linked it is no more than 150Kb.

#### [Apicurio Service Registry Example - Avro](https://github.com/hguerrero/amq-examples/tree/master/registry-example-avro#apicurio-service-registry-example---avro)

This project shows how to use the Apicurio Service Registry to manage Apache Kafka data schemas. This example uses the Avro data format messages to and from a local cluster.


### [RedHat Integration Service Registry](https://www.redhat.com/en/products/integration)

[Replacing Confluent Schema Registry with Red Hat integration service registry](https://developers.redhat.com/blog/2019/12/17/replacing-confluent-schema-registry-with-red-hat-integration-service-registry/)

> Сделано на базе Apicurio

[First look at the new Apicurio Registry UI and Operator](https://developers.redhat.com/blog/2020/06/11/first-look-at-the-new-apicurio-registry-ui-and-operator/)

## Hortonworks Streams Messaging Manager

[Hortonworks : Monitoring Kafka Streams Microservices with Hortonworks Streams Messaging Manager (SMM)](https://www.marketscreener.com/news/latest/Hortonworks-Monitoring-Kafka-Streams-Microservices-with-Hortonworks-Streams-Messaging-Manager-SMM--27771136/)

Похоже был такой продукт в составе HDP.

## Kafka Streams from Hortonworks

Похоже, Hortonwork пытались сделать свою платформу для потоковой аналитики.

### [Democratizing Analytics within Kafka with Three New Access Patterns](https://blog.cloudera.com/democratizing-analytics-within-kafka-three-new-access-patterns/)

>     November 20, 2018

> ![media-d97732](../../media/media-d97732-1771985256.png)

> HDP/HDF supports two stream processing engines: *Spark Structured Streaming and Streaming Analytics Manager (SAM) with Storm*.

> ![media-w97732](../../media/media-w97732-1661645163.png)

> ![media-m97732](../../media/media-m97732-1203486682.png)

> ![media-f97732](../../media/media-f97732-343692475.png)

### [Kafka Streams – Is it the right Stream Processing engine for you?](https://blog.cloudera.com/kafka-streams-is-it-the-right-stream-processing-engine-for-you/)

>     December 07, 2018

> ![media-z97732](../../media/media-z97732-875002579.png)

### [Building Secure and Governed Microservices with Kafka Streams](https://blog.cloudera.com/building-secure-and-governed-microservices-with-kafka-streams/)

>     December 07, 2018

> ![media-C97732](../../media/media-C97732-1878824577.png)

> ![media-i97732](../../media/media-i97732-635436159.png)

> ![media-f97732](../../media/media-f97732-268015058.png)

> ![media-b97732](../../media/media-b97732-1315400485.png)

> One of the key benefits of using Kafka Streams over other streaming engines is that the stream processing apps / microservices don’t need a cluster. Rather, each microservice can be run as a standalone app (e.g: jvm container). You can then spin multiple instances of each to scale up the microservice. Kafka will treat this as a single consumer group with multiple instances. Kafka streams takes care of consumer partition reassignments for scalability.

> You can see how to start these three microservices [here](https://github.com/georgevetticaden/kafka-streams-trucking-ref-app/blob/hdf-3-3/src/main/resources/scripts/startAllMicroServices.sh).

> Пример стримингового приложения для анализа движения грузовиков: <https://github.com/georgevetticaden/kafka-streams-trucking-ref-app>
