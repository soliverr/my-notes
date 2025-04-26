---
id: uodymx8mqzq9slnwuhedapr
title: Kafka for Developers
desc: ''
updated: 1644473307078
created: 1642057220788
---
Kafka for Developers
====================

## Ссылки

* https://docs.confluent.io/platform/current/connect/devguide.html
* https://www.confluent.io/blog/write-a-kafka-connect-connector-with-configuration-handling/
* https://docs.confluent.io/platform/current/tutorials/examples/clients/docs/scala.html
* https://github.com/confluentinc/examples
* https://github.com/niqdev/kafka-scala-examples


## Развёртывание кластера Kafka

Развёртывание кластера потоковой обработки для разработки и тестирования. Нашёл следующие обучающие метериалы для развёртывания локального кластера.

### Debezium

Можно воспользоваться вот этим репозитарием: [Debezium Examples](https://github.com/debezium/debezium/debexium-examples). Примеры файлов развёртывания находятся в каталоге *tutorial*

Развёртывание тестовой инфраструктуры CDC для СУБД MySQL. Необходимо задать версию Debezium. На данный момент 04.03.21 19:18 - `1.4`, но уже на подходе 1.5

```console
$ cd ~/src/bigdata/debezium/debezium-examples/tutorial/

$ export DEBEZIUM_VERSION=1.4
$ docker-compose -f docker-compose-mysql.yaml up -d

```

### Confluent

### SuperGloo

На обучающем ресурсе SuperGloo используются скрипты для платформы Confluent: <https://github.com/tmcgrath/docker-for-demos>.

### Loic M DIVAD

Нашёл интересный блог https://blog.loicmdivad.com/ и репозиторий исходных текстов на Scala [DivLoic на GitHub](https://github.com/DivLoic?tab=repositories), которые можно использовать для изучения Kafka API.

### Qimia

Серия обучающих статей от [Qimia](https://qimia.io):

* [Developing Streaming Applications - Akka](https://www.qimia.de/en/blog/Developing-Streaming-Applications-Akka)
* [Developing Streaming Applications - Kafka](https://qimia.io/en/blog/Developing-Streaming-Applications-Kafka)
* [Developing Streaming Applications - Spark](https://qimia.io/en/blog/Developing-Streaming-Applications-Spark-Structured-Streaming)

## Генерация данных

[Kafka Test Data Generation Examples](https://supergloo.com/kafka/kafka-test-data/)

* Консольные скрипты в составе Kafka
* kafkacat
* Confluent ksql-datagen

## Обработка данных в Apache Kafka

* [Kafka Connect](./Kafka Connect.md)

### Варианты реализации

Видимо существует два варианта реализации:
* KafkaConnect API
* Spark Streaming/Spark Structured Streaming API

Нашёл сайт [Supergloo](https://supergloo.com/), посвящённый теме потоковой обработки в Kafka. Соответсвтующий сайт с примерами - https://github.com/tmcgrath

Основной особенностью (преимуществом?) использования API KafkaConnect, является возможность работы с одиночном (standalone) или кластерном (distributed) режимах. При этом, *в кластерном режиме*, KafkaConnect ***не требует** выделенного ведущего сервера*.

[Running Kafka Connect – Standalone vs Distributed Mode Examples](https://supergloo.com/kafka-connect/):

> Running Kafka Connect in Distributed mode runs Connect workers on one or multiple nodes.  *When running on multiple nodes*, the coordination mechanics to work in parallel **does not require an orchestration manager** such as YARN.  Let me stop here because this is an important point.  In other words, when running in a “cluster” of multiple nodes, the need to coordinate “which node is doing what?” is required.  This may or may not be relevant to you.  *For me personally, I came to this after Apache Spark, so no requirement for an orchestration manager interested me.*

### KafkaConsumer

* [Class KafkaConsumer](https://kafka.apache.org/10/javadoc/org/apache/kafka/clients/consumer/KafkaConsumer.html)
* https://docs.confluent.io/platform/current/clients/consumer.html

## Spark Structured Streaming with Kafka

### [Processing Data in Apache Kafka with Structured Streaming in Apache Spark 2.2](https://databricks.com/blog/2017/04/26/processing-data-in-apache-kafka-with-structured-streaming-in-apache-spark-2-2.html)

Structured Streaming provides *a unified batch and streaming API* that enables us to view data published to Kafka as a DataFrame. When processing unbounded data in a streaming fashion, we use the same API and get the same data consistency guarantees as in batch processing. The system ensures end-to-end exactly-once fault-tolerance guarantees, so that *a user does not have to reason about low-level aspects of streaming*.

Existing users of the KafkaConsumer will notice that Structured Streaming provides a more granular version of the configuration option, `auto.offset.reset`. Instead of one option, we split these concerns into two different parameters, one that says what to do when the stream is first starting (*startingOffsets*), and another that handles what to do if the query is not able to pick up from where it left off, because the desired data has already been aged out (*failOnDataLoss*).


        # Construct a streaming <a href="https://databricks.com/glossary/what-are-dataframes">DataFrame</a> that reads from topic1
        df = spark \
             .readStream \
             .format("kafka") \
             .option("kafka.bootstrap.servers", "host1:port1,host2:port2") \
             .option("subscribe", "topic1") \
             .option("startingOffsets", "earliest") \
             .load()

`df.printSchema()` reveals the schema of our DataFrame.

        root
         |-- key: binary (nullable = true)
         |-- value: binary (nullable = true)
         |-- topic: string (nullable = true)
         |-- partition: integer (nullable = true)
         |-- offset: long (nullable = true)
         |-- timestamp: timestamp (nullable = true)
         |-- timestampType: integer (nullable = true)


If the bytes of the Kafka records represent UTF8 strings, we can simply use a cast to convert the binary data into the correct type.

`df.selectExpr("CAST(key AS STRING)", "CAST(value AS STRING)")`

JSON is another common format for data that is written to Kafka. In this case, we can use the built-in `from_json` function along with the expected schema to convert a binary value into a Spark SQL struct.

        # value schema: { "a": 1, "b": "string" }
        schema = StructType().add("a", IntegerType()).add("b", StringType())
        df.select( \
          col("key").cast("string"),
          from_json(col("value").cast("string"), schema))

In some cases, you may already have code that implements the [Kafka Deserializer interface](https://kafka.apache.org/0100/javadoc/org/apache/kafka/common/serialization/Deserializer.html). You can take advantage of this code by wrapping it as a user defined function (UDF) using the Scala code shown below.

        object MyDeserializerWrapper {
          val deser = new MyDeserializer
        }
        spark.udf.register("deserialize", (topic: String, bytes: Array[Byte]) =>
          MyDeserializerWrapper.deser.deserialize(topic, bytes)
        )

        df.selectExpr("""deserialize("topic1", value) AS message""")

Note that the DataFrame code above is analogous to specifying `value.deserializer` when using the standard Kafka consumer.

Writing data from any Spark supported data source into Kafka is as simple as calling `writeStream` on any `DataFrame` that contains a column named **“value”**, and optionally a column named **“key”**. If a key column is not specified, then a *null valued key* column will be automatically added. A null valued key column may, in some cases, [lead to uneven data partitioning in Kafka](https://cwiki.apache.org/confluence/display/KAFKA/FAQ#FAQ-Whyisdatanotevenlydistributedamongpartitionswhenapartitioningkeyisnotspecified?), and should be used with care.

The destination topic for the records of the `DataFrame` can either be specified statically as an option to the `DataStreamWriter` or on a per-record basis as a column named **“topic”** in the DataFrame.

        # Write key-value data from a DataFrame to a Kafka topic specified in an option
        query = df \
          .selectExpr("CAST(userId AS STRING) AS key", "to_json(struct(*)) AS value") \
          .writeStream \
          .format("kafka") \
          .option("kafka.bootstrap.servers", "host1:port1,host2:port2") \
          .option("topic", "topic1") \
          .option("checkpointLocation", "/path/to/HDFS/dir") \
          .start()

The above query takes a `DataFrame` containing user information and writes it to Kafka. The `userId` is serialized as a string and used as the key. We take all the columns of the `DataFrame` and serialize them as a JSON string, putting the results in the value of the record.

The two required options for writing to Kafka are the `kafka.bootstrap.servers` and the `checkpointLocation`. As in the above example, an additional topic option can be used to set a single topic to write to, and this option *will override* the **“topic”** column if it exists in the DataFrame.

![media-T87665](../../media/media-T87665-1094258961.png)

At a high-level, the desired workflow looks like the graph above. Given a stream of updates from Nest cameras, we want to use Spark to perform several different tasks:

* Create an efficient, queryable historical archive of all events using a columnar format like Parquet.
* Perform low-latency event-time aggregation and push the results back to Kafka for other consumers.
*  Perform batch reporting on the data stored in a compacted topic in Kafka.

We’ll specifically examine data from Nest’s cameras, which look like the following JSON:

        "devices": {
          "cameras": {
            "device_id": "awJo6rH...",
            "last_event": {
              "has_sound": true,
              "has_motion": true,
              "has_person": true,
              "start_time": "2016-12-29T00:00:00.000Z",
              "end_time": "2016-12-29T18:42:00.000Z"
            }
          }
        }

Expected Schema for JSON data

        schema = StructType() \
          .add("metadata", StructType() \
            .add("access_token", StringType()) \
            .add("client_version", IntegerType())) \
          .add("devices", StructType() \
            .add("thermostats", MapType(StringType(), StructType().add(...))) \
            .add("smoke_co_alarms", MapType(StringType(), StructType().add(...))) \
            .add("cameras", MapType(StringType(), StructType().add(...))) \
            .add("companyName", StructType().add(...))) \
          .add("structures", MapType(StringType(), StructType().add(...)))

        nestTimestampFormat = "yyyy-MM-dd'T'HH:mm:ss.sss'Z'"

Parse the Raw JSON

        jsonOptions = { "timestampFormat": nestTimestampFormat }
        parsed = spark \
          .readStream \
          .format("kafka") \
          .option("kafka.bootstrap.servers", "localhost:9092") \
          .option("subscribe", "nest-logs") \
          .load() \
          .select(from_json(col("value").cast("string"), schema, jsonOptions).alias("parsed_value"))

Project Relevant Columns

        camera = parsed \
          .select(explode("parsed_value.devices.cameras")) \
          .select("value.*")

        sightings = camera \
          .select("device_id", "last_event.has_person", "last_event.start_time") \
          .where(col("has_person") == True)

To create the camera DataFrame, we first unnest the “cameras” json field to make it top level. Since “cameras” is a MapType, each resulting row contains a map of key-value pairs. So, we use the explode function to create a new row for each key-value pair, flattening the data. Lastly, we use star () to unnest the “value” column.

        sightingLoc \
          .groupBy("zip_code", window("start_time", "1 hour")) \
          .count() \
          .select( \
            to_json(struct("zip_code", "window")).alias("key"),
            col("count").cast("string").alias("value")) \
          .writeStream \
          .format("kafka") \
          .option("kafka.bootstrap.servers", "localhost:9092") \
          .option("topic", "nest-camera-stats") \
          .option("checkpointLocation", "/path/to/HDFS/dir") \
          .outputMode("complete") \
          .start()


The above query will process any sighting as it occurs and write out the updated count of the sighting to Kafka, keyed on the zip code and hour window of the sighting. Over time, many updates to the same key will result in many records with that key, and Kafka topic compaction will delete older updates as new values arrive for the key. *This way, compaction tries to ensure that eventually, only the latest value is kept for any given key.*

        camera.writeStream \
          .format("parquet") \
          .option("startingOffsets", "earliest") \
          .option("path", "s3://nest-logs") \
          .option("checkpointLocation", "/path/to/HDFS/dir") \
          .start()

Note that we can simply reuse the same camera DataFrame to start multiple streaming queries. For instance, we can query the DataFrame to get a list of cameras that are offline, and send a notification to the network operations center for further investigation.

Our next example is going to run a batch query over the Kafka “nest-camera-stats” compacted topic and generate a report showing zip codes with a significant number of sightings.

*Writing batch queries is similar to streaming queries with the exception that we use the `read` method instead of the `readStream` method and `write` instead of `writeStream`.*

Batch Read and Format the Data

        report = spark \
          .read \
          .format("kafka") \
          .option("kafka.bootstrap.servers", "localhost:9092") \
          .option("subscribe", "nest-camera-stats") \
          .load() \
          .select( \
            json_tuple(col("key").cast("string"), "zip_code", "window").alias("zip_code", "window"),
            col("value").cast("string").cast("integer").alias("count")) \
          .where("count > 1000") \
          .select("zip_code", "window") \
          .distinct()

This report DataFrame can be used for reporting or to create a real-time dashboard showing events with extreme sightings.

### Обработка нескольких топиков с разными схемами

Основная проблема API - как создать поток для нескольких топиков, логика работы которого будет зависеть от топика. При этом, схемы данных в топиках отличаются.

Нашёл вот такую статью [Two topics, two schemas, one subscription in Apache Spark Structured Streaming](https://www.waitingforcode.com/apache-spark-structured-streaming/two-topics-two-schemas-one-subscription-apache-spark-structured-streaming/read), в которой рассматривается три варианта обработки.

* [Spark Structured Streaming Multiple Kafka Topics With Unique Message Schemas](https://stackoverflow.com/questions/49781119/spark-structured-streaming-multiple-kafka-topics-with-unique-message-schemas)
* [What do foreachBatches contain in a streaming query from multiple Kafka topics?](https://stackoverflow.com/questions/56989068/what-do-foreachbatches-contain-in-a-streaming-query-from-multiple-kafka-topics)

### Интеграция с Confluent Schema Registry

* [Integrating Spark Structured Streaming with the Confluent Schema Registry](https://stackoverflow.com/questions/48882723/integrating-spark-structured-streaming-with-the-confluent-schema-registry)
* [ABRiS - Avro Bridge for Spark](https://github.com/AbsaOSS/ABRiS)
* [Read and write streaming Avro data](https://docs.databricks.com/spark/latest/structured-streaming/avro-dataframe.html) - это платная платформа

### Запись в разные файлы из одного источника

* [Apache Spark 2.4.0 features - foreachBatch](https://www.waitingforcode.com/apache-spark-structured-streaming/apache-spark-2.4.0-features-foreachbatch/read#use_case_multiple_sinks_from_single_source_side_output)
    * [spark-scala-playground](https://github.com/bartosz25/spark-scala-playground)
* [Structured Streaming examples](https://docs.databricks.com/spark/latest/structured-streaming/examples.html)
* [Write to arbitrary data sinks](https://docs.databricks.com/spark/latest/structured-streaming/foreach.html)

### Ссылки

* [Structured Streaming + Kafka Integration Guide](https://spark.apache.org/docs/latest/structured-streaming-kafka-integration.html)
* [Structured Streaming Programming Guide](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)
* [Spark Streaming with Kafka-Connect Debezium Connector](https://suchit-g.medium.com/spark-streaming-with-kafka-connect-debezium-connector-ab9163808667)
    * <https://github.com/suchitgupta01/spark-streaming-with-debezium>
* [Kafka offset committer for Spark structured streaming](https://github.com/HeartSaVioR/spark-sql-kafka-offset-committer)
* IBM Best practices using Spark SQL streaming
    * [Best practices using Spark SQL streaming, Part 1](https://developer.ibm.com/technologies/analytics/blogs/best-practices-around-spark-structured-streaming-series-1)
* [How to start Spark Structured Streaming by a specific Kafka timestamp](https://medium.com/@ZeevFeldbeine/how-to-start-spark-structured-streaming-by-a-specific-kafka-timestamp-e4b0a3e9c009)
* [Spark Streaming with Kafka Example](https://sparkbyexamples.com/spark/spark-streaming-with-kafka/)
* [Integrating Spark Structured Streaming with the Confluent Schema Registry](https://stackoverflow.com/questions/48882723/integrating-spark-structured-streaming-with-the-confluent-schema-registry)
* [What do foreachBatches contain in a streaming query from multiple Kafka topics?](https://stackoverflow.com/questions/56989068/what-do-foreachbatches-contain-in-a-streaming-query-from-multiple-kafka-topics)

