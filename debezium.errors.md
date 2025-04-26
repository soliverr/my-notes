---
id: i1d7i5hk8qsx8kgzbve1u1h
title: Debezium Errors and Solutions
desc: Ошибки в работе Debezium
updated: 1664455154326
created: 1642059109128
---

# org.apache.kafka.common.errors.RecordTooLargeException

```sh
org.apache.kafka.connect.errors.ConnectException: java.util.concurrent.ExecutionException: org.apache.kafka.common.errors.RecordTooLargeException: The message is 1050429 bytes when serialized which is larger than 1048576, which is the value of the max.request.size configuration.
	at io.debezium.connector.mysql.AbstractReader.wrap(AbstractReader.java:241)
	at io.debezium.connector.mysql.AbstractReader.failed(AbstractReader.java:218)
	at io.debezium.connector.mysql.BinlogReader.handleEvent(BinlogReader.java:607)
	at com.github.shyiko.mysql.binlog.BinaryLogClient.notifyEventListeners(BinaryLogClient.java:1104)
	at com.github.shyiko.mysql.binlog.BinaryLogClient.listenForEventPackets(BinaryLogClient.java:955)
	at com.github.shyiko.mysql.binlog.BinaryLogClient.connect(BinaryLogClient.java:595)
	at com.github.shyiko.mysql.binlog.BinaryLogClient$7.run(BinaryLogClient.java:839)
	at java.base/java.lang.Thread.run(Thread.java:834)
```

Для решения проблемы необходимо изменить конфигурацию:

* брокера
* продюсера Debezium
* топиков Kafka, так как для них может быть тоже установлено ограничение
* консюмера

## Увеличение размера сообщения в Kafka

[How can I send large messages with Kafka (over 15MB)?](https://stackoverflow.com/questions/21020347/how-can-i-send-large-messages-with-kafka-over-15mb)

* **Consumer side**: `fetch.message.max.bytes` - this will determine the largest size of a message that can be fetched by the consumer.
* **Broker side**: `replica.fetch.max.bytes` - this will allow for the replicas in the brokers to send messages within the cluster and make sure the messages are replicated correctly. If this is too small, then the message will never be replicated, and therefore, the consumer will never see the message because the message will never be committed (fully replicated).
* **Broker side**: `message.max.bytes` - this is the largest size of the message that can be received by the broker from a producer.
* **Broker side (per topic)**: `max.message.bytes` - this is the largest size of the message the broker will allow to be appended to the topic. This size is validated pre-compression. (Defaults to broker's message.max.bytes.)

Ещё пишут вот про эти параметры:
* **Consumer side**: `max.partition.fetch.bytes`
* **Producer side**: `max.request.size`
* **Broker side**: `socket.request.max.bytes`

## Увеличение размера сообщения в Debezium


[How to enlarge the maximum size of the message delivered to Kafka?](https://github.com/debezium/debezium.github.io/blob/develop/documentation/faq.asciidoc#how-to-enlarge-the-maximum-size-of-the-message-delivered-to-kafka)

_Вот эта [ветка](https://groups.google.com/g/debezium/c/lb8uWcmo-jM) обсуждения_.

> To solve the issue the configuration option `producer.max.request.size` must be set in Kafka Connect worker config file _connect-distributed.properties_. If the global change is not desirable then the connector can override the default setting using configuration option `producer.override.max.request.size` set to a larger value.
>
> In the latter case it is also necessary to configure `connector.client.config.override.policy=ALL` option in Kafka Connect worker config file _connect-distributed.properties_. For Debezium connect Docker image the environment variable `CONNECT_CONNECTOR_CLIENT_CONFIG_OVERRIDE_POLICY` can be used to configure the option.

 Есть информация, что параметры коннетора Debezium можно переопределить через переменные среды при запуске Docker-контейнера:

 * [Debezium with Single Message Transformation](https://medium.com/trendyol-tech/debezium-with-simple-message-transformation-smt-4f5a80c85358)

 > Environment variables that start with CONNECT_ will be used to update the Kafka Connect worker configuration file. To update kafka connect configurations, we can use CONNECT_PRODUCER_MAX_REQUEST_SIZE as an environment variable.

 * [Snapshot fails if message size exceeds max.request.size](https://issues.redhat.com/browse/DBZ-2664)

 > Setting both CONNECT_PRODUCER_MAX_REQUEST_SIZE and CONNECT_MESSAGE_MAX_BYTES to something slightly higher than our biggest message solved the issue. 

 * [Kafka/Confluent: ProducerConfig change default max.request.size using docker images](https://stackoverflow.com/questions/46647337/kafka-confluent-producerconfig-change-default-max-request-size-using-docker-ima)

 > the IRC chat helped me: i had to specify both `KAFKA_PRODUCER_MAX_REQUEST_SIZE` and `CONNECT_PRODUCER_MAX_REQUEST_SIZE` while starting the docker image.

* [Where should i change max.request.size in confluent](https://forum.confluent.io/t/where-should-i-change-max-request-size-in-confluent/1304/10)

> **Kafka Connect Config**:
> 
> ```
    CONNECT_OFFSET_FLUSH_INTERVAL_MS: "10000"
    CONNECT_OFFSET_FLUSH_TIMEOUT_MS: "60000"
    CONNECT_CONNECTOR_CLIENT_CONFIG_OVERRIDE_POLICY: All
>
    CONNECT_BUFFER_MEMORY: "335544320"
    CONNECT_MAX_REQUEST_SIZE: "104857600"
    CONNECT_RECEIVE_BUFFER_BYTES: "26214400"
    CONNECT_SEND_BUFFER_BYTES: "104857600"
>
    CONNECT_PRODUCER_BUFFER_MEMORY: "335544320"
    CONNECT_PRODUCER_MAX_REQUEST_SIZE: "104857600"
    CONNECT_PRODUCER_RECEIVE_BUFFER_BYTES: "26214400"
    CONNECT_PRODUCER_SEND_BUFFER_BYTES: "104857600"
>
    CONNECT_PRODUCER_OVERRIDE_BUFFER_MEMORY: "335544320"
    CONNECT_PRODUCER_OVERRIDE_MAX_REQUEST_SIZE: "104857600"
    CONNECT_PRODUCER_OVERRIDE_RECEIVE_BUFFER_BYTES: "26214400"
    CONNECT_PRODUCER_OVERRIDE_SEND_BUFFER_BYTES: "104857600"
```
>
> **Connector Settings (Debezium)**:
>
> ```
    "producer.override.buffer.memory"        = "335544320"
    "producer.override.compression.type"     = "lz4"
    "producer.override.max.request.size"     = "104857600"
    "producer.override.receive.buffer.bytes" = "26214400"
    "producer.override.send.buffer.bytes"    = "104857600"
```
> 
> Broker Settings:
> 
> ```
    socket.request.max.bytes=104857600
    message.max.bytes = 5242880
    replica.fetch.max.bytes = 5242880
    socket.receive.buffer.bytes=102400
    socket.send.buffer.bytes=102400
    group.max.session.timeout.ms=300000
```
>
> **Topic Settings**:
> 
> ```
    # internal topics (config, offset, status)
    'max.message.bytes' => 104857600,
>
    # db history topic
    'max.message.bytes' => 104857600,
>
    # output topic
    'max.message.bytes' => 104857600,
```

# Executing stage 'TRANSFORMATION' with class 'io.debezium.connector.mongodb.transforms.ExtractNewDocumentState'

```
2022-09-29 12:02:58,450 ERROR  ||  Error encountered in task release.playerstats-13-0. Executing stage 'TRANSFORMATION' with class 'io.debezium.connector.mongodb.transforms.ExtractNewDocumentState',
...
org.apache.kafka.connect.errors.ConnectException: Field screenName of schema mongodb.release.playerstats.gameData is not the same type for all documents in the array.
Check option 'struct' of parameter 'array.encoding'
        at io.debezium.connector.mongodb.transforms.MongoDataConverter.testType(MongoDataConverter.java:467)
        at io.debezium.connector.mongodb.transforms.MongoDataConverter.addFieldSchema(MongoDataConverter.java:381)
...
```

Проблема в том, что приходит массив записей, в котором поле `screenName` для некоторых записей `null`, а для некоторых - строковое значение.
