---
id: 5uezmoku6s27kzcnux11iew
title: KafkaConnect API
desc: ''
updated: 1704901957031
created: 1642057112445
---
# Kafka Connect

## Standalone Mode

## Distributed Mode

В распределённом режиме коннекторы создаются через REST API. Для использования статических конфигураций коннекторов их нужно добавлять в контейнер и запускать. Вот тут https://stackoverflow.com/questions/62715790/creating-kafka-connector-after-distributed-kafka-connect-is-started-in-docker-en описана подобная проблема и способ её решения.

Такой способ подойдёт при использовании K8S, Docker Swarm или при базовой установке Kafka Connect на выделенных хостах.


* [Docker Tips and Tricks with Kafka Connect, ksqlDB, and Kafka](https://rmoff.net/2018/12/15/docker-tips-and-tricks-with-kafka-connect-ksqldb-and-kafka/)

## Introduction to Kafka Connectors from Baeldung

<https://www.baeldung.com/kafka-connectors-guide>

> **Kafka Connect** is a framework for connecting Kafka with external systems such as databases, key-value stores, search indexes, and file systems, using so-called Connectors.

## How to install Kafka Connect connector plugins

[How to install Kafka Connect connector plugins](https://www.youtube.com/watch?v=18gDPSOH3wU)

## Data Ingestion into Azure Data Explorer using Kafka Connect

[Data Ingestion into Azure Data Explorer using Kafka Connect](https://strimzi.io/blog/2020/09/25/data-explorer-kafka-connect/)

## Kafka Connector to HDFS

### Kafka-Connect-HDFS with HDP

[How to implement kafka-connect-hdfs with HDP provided kafka](https://community.cloudera.com/t5/Support-Questions/How-to-implement-kafka-connect-hdfs-with-hdp-provided-kafka/td-p/208761)

From a non-Hadoop machine, install Java+Maven+Git.

```console
git clone https://github.com/confluentinc/kafka-connect-hdfs
cd kafka-connect-hdfs
git fetch --all --tags --prune
git checkout tags/v4.1.2  # This is a Confluent Release number, which corresponds to a Kafka release number
mvn clean install -DskipTests
```


This should generate some files under the target folder in that directory.

So, using the 4.1.2 example, I would ZIP up the "target/kafka-connect-hdfs-4.1.2-package/share/java/" folder that was built, then **copy this file and extract it into all HDP servers that I want to run Kafka Connect on**. For example, `/opt/kafka-connect-hdfs/share/java`.

From there, you would find your *"connect-distributed.properties"* file and add a line for

`plugin.path=/opt/kafka-connect-hdfs/share/java`

Now, run something like this (I don't know the full location of the property files)

`connect-distributed /usr/hdp/current/kafka/.../connect-distributed.properties`

Once that starts, you can attempt to hit `http://connect-server:8083/connector-plugins` , and you should see an item for `io.confluent.connect.hdfs.HdfsSinkConnector`.

If successful, continue to read the HDFS Connector documentation, then POST the JSON configuration body to the Connect Server endpoint. (or use Landoop's Connect UI tool)

### Kafka Connect in HDP

<https://community.cloudera.com/t5/Support-Questions/kafka-connect/td-p/212463>


> If you are running Kafka 0.10 or newer, connect-distributed.sh exists somewhere under `/usr/hdp/current/kafka` already.
> You can run that process on multiple machines to create a Kafka Connect cluster.

> **Kafka Connect Setup:**
>
> 1. Download the Confluent-Kafka tar for Confluent: https://www.confluent.io/download/
> 2. Untar the package and copy the '`/share`' folder under '`/usr/hdp/hdp_version_/kafka/`' folder
> 3. update the `CLASSPPATH` with jars files location, in my case its '`/usr/hdp/2.6.4.0-91/kafka/share/> java`'
> 4. Make appropriate changes to '`connect-distributed`' & '`connect-standalone`' property files under `/etc/kafka/hdp_version/0/`
> 5. I added '`quickstart-hdfs.properties`' under '`/etc/kafka/hdp_version/0/`' which includes topic names,topics dirs,flush size etc.
> 6. Run a test job with these changes and worked for me.
>

```
name=hdfs-sink
connector.class=io.confluent.connect.hdfs.HdfsSinkConnector
tasks.max=1
#topics=Topic1, Topic2, Topic3, Topic4
hdfs.url=hdfs://Hostname:8020
flush.size=1
topics.dir=/user/xyz
format.class=io.confluent.connect.hdfs.avro.AvroFormat
#format.class=io.confluent.connect.hdfs.parquet.ParquetFormat
#format.class=io.confluent.connect.hdfs.json.JsonFormat
#format.class=io.confluent.connect.hdfs.string.StringFormat
#partitioner.class=io.confluent.connect.storage.partitioner.TimeBasedPartitioner
#flush.size=100
rotate.interval.ms=30000
avro.codec=snappy
hive.integration=true
hive.metastore.uris=thrift://Hive_metastore_host:9083
schema.compatibility=BACKWARD
```

## [Building Realtime Data Pipelines with Kafka Connect and Spark Streaming](https://databricks.com/session/building-realtime-data-pipelines-with-kafka-connect-and-spark-streaming)

![qownnotes-media-U50795](../../media/qownnotes-media-U50795-1907057852.png)


## [Automatically restarting failed Kafka Connect tasks](https://rmoff.net/2019/06/06/automatically-restarting-failed-kafka-connect-tasks/)


## Transformations (SMT)

* https://docs.confluent.io/platform/current/connect/transforms/index.html

### Rename topic

* https://docs.confluent.io/platform/current/connect/transforms/regexrouter.html


**Remove a topic prefix**

This configuration snippet shows how to remove the prefix `soe-` from the beginning of a topic.

```
"transforms": "dropPrefix",
"transforms.dropPrefix.type": "org.apache.kafka.connect.transforms.RegexRouter",
"transforms.dropPrefix.regex": "soe-(.*)",
"transforms.dropPrefix.replacement": "$1"
```

Before: `soe-Order`
After: `Order`

**Add a topic prefix**

This configuration snippet shows how to add the prefix `acme_` to the beginning of a topic.

```
"transforms": "AddPrefix",
"transforms.AddPrefix.type": "org.apache.kafka.connect.transforms.RegexRouter",
"transforms.AddPrefix.regex": ".*",
"transforms.AddPrefix.replacement": "acme_$0"
```

Before: `Order`
After: `acme_Order`

**Remove part of a topic name**

This configuration snippet shows how to remove a string in a topic name.

```
"transforms=RemoveString",
"transforms.RemoveString.type": "org.apache.kafka.connect.transforms.RegexRouter",
"transforms.RemoveString.regex": "(.*)Stream_(.*)",
"transforms.RemoveString.replacement": "$1$2"
```

Before: `Order_Stream_Data`
After: `Order_Data`

### Drop Key or Value

* https://docs.confluent.io/cloud/current/connectors/transforms/drop.html

Drop the key from the message, using the default behavior for schemas, which nullifies the keys if they are not already null.

```
"transforms": "dropKeyExample", "dropValueAndForceOptionalSchemaExample",
"transforms.dropKeyExample.type": "io.confluent.connect.transforms.Drop$Key"
```

Drop the value from the message. If the schema for the value isn’t already optional, forcefully overwrite it to become optional.

```
"transforms.dropValueAndForceOptionalSchemaExample.type": "io.confluent.connect.transforms.Drop$Value",
"transforms.dropValueAndForceOptionalSchemaExample.schema.behavior": "force_optional"
```

### ReplaceField

* https://docs.confluent.io/cloud/current/connectors/transforms/replacefield.html

#### Rename a Field

```
"transforms": "RenameField",
"transforms.RenameField.type": "org.apache.kafka.connect.transforms.ReplaceField$Value",
"transforms.RenameField.renames": "foo:c1,bar:c2"
```

#### Drop a Field

This configuration snippet shows how to use ReplaceField to exclude a field and remove it.

```
"transforms": "ReplaceField",
"transforms.ReplaceField.type": "org.apache.kafka.connect.transforms.ReplaceField$Value",
"transforms.ReplaceField.exclude": "c2"
```

### TimestampConverter

* [TimestampConverter | Confluent Documentation](https://docs.confluent.io/platform/current/connect/transforms/timestampconverter.html#timestampconverter)
* https://rmoff.net/2020/12/17/twelve-days-of-smt-day-8-timestampconverter/

```
"transforms": "tsConverter",
"transforms.tsConverter.type": "org.apache.kafka.connect.transforms.TimestampConverter$Value",
"transforms.tsConverter.field": "ordertime",
"transforms.tsConverter.format": "yyyy-MM-dd",
"transforms.tsConverter.target.type": "string"

"transforms": "TimestampConverter",
"transforms.TimestampConverter.type": "org.apache.kafka.connect.transforms.TimestampConverter$Value",
"transforms.TimestampConverter.format": "yyyy-MM-dd",
"transforms.TimestampConverter.target.type": "string"
```

### Aiven Transformations for Apache Kafka® Connect

* https://github.com/aiven/transforms-for-apache-kafka-connect

## Error handling and Dead Letter Queues

* [Kafka Connect Deep Dive – Error Handling and Dead Letter Queues | Confluent](https://www.confluent.io/blog/kafka-connect-deep-dive-error-handling-dead-letter-queues/)

Kafka Connect can be configured to send messages that it cannot process (such as a deserialization error as seen in “fail fast” above) to a dead letter queue, which is a separate Kafka topic. Valid messages are processed as normal, and the pipeline keeps on running. Invalid messages can then be inspected from the dead letter queue, and ignored or fixed and reprocessed as required.

Пример параметров для контроля ошибок в потоке:

```json
        "_comment": "Error handling",
        "errors.tolerance": "all",
        "errors.deadletterqueue.topic.name": "deadletterqueue",
        "errors.deadletterqueue.topic.replication.factor": 1,
        "errors.deadletterqueue.context.headers.enable": "true",
        "errors.log.enable": "true",
        "errors.log.include.messages": "true",
        "errors.retry.delay.max.ms": 60000,
        "errors.retry.timeout": 300000,
```

