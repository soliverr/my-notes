---
id: civ2qegdisl6p2nlkh8b9ua
title: Rabbitmq
desc: ''
updated: 1677079982085
created: 1672745101136
---

# Источники

* Confluent
  * [RabbitMQ Source Connector](https://www.confluent.io/hub/confluentinc/kafka-connect-rabbitmq)
  * [RabbitMQ Sink Connector](https://www.confluent.io/hub/confluentinc/kafka-connect-rabbitmq-sink)
* Camel
  * https://camel.apache.org/camel-kafka-connector/3.18.x/index.html
  * https://camel.netlify.app/camel-kafka-connector/latest/connectors.html
  * https://github.com/apache/camel-kafka-connector-examples/tree/main/rabbitmq/rabbitmq-sink
  * https://github.com/apache/camel-kafka-connector-examples/tree/main/rabbitmq/rabbitmq-source
* https://github.com/jocelyndrean/kafka-connect-rabbitmq
* https://github.com/arkadiuszbicz/kafka-connect-rabbitmq
* https://github.com/jonahharris/kafka-connect-rabbitmq
* https://github.com/ibm-messaging/kafka-connect-rabbitmq-source

# Confluent RabbitMQ Source Connector

* [RabbitMQ Source Connector Configuration Properties | Confluent Documentation](https://docs.confluent.io/kafka-connectors/rabbitmq-source/current/config.html#rabbitmq-source-connector-configuration-properties)
* [Streaming messages from RabbitMQ into Kafka with Kafka Connect](https://rmoff.net/2020/01/08/streaming-messages-from-rabbitmq-into-kafka-with-kafka-connect/)
* [Kafka RabbitMQ connector - can only get byte arrays - Stack Overflow](https://stackoverflow.com/questions/59619667/kafka-rabbitmq-connector-can-only-get-byte-arrays)
* [Kafka RabbitMQ connector - can only get byte arrays](https://stackoverflow.com/questions/59619667/kafka-rabbitmq-connector-can-only-get-byte-arrays)

```json
{
        "connector.class" : "io.confluent.connect.rabbitmq.RabbitMQSourceConnector",
        "kafka.topic" : "rabbit-test-00",
        "rabbitmq.queue" : "test-queue-01",
        "rabbitmq.username": "guest",
        "rabbitmq.password": "guest",
        "rabbitmq.host": "rabbitmq",
        "rabbitmq.port": "5672",
        "rabbitmq.virtual.host": "/",
        "confluent.license":"",
        "confluent.topic.bootstrap.servers":"kafka:29092",
        "confluent.topic.replication.factor":1,
        "value.converter": "org.apache.kafka.connect.converters.ByteArrayConverter",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter"
}
```

> вообще правило работы с кроликом следующее. к очереди должен быть подключен только один логический консьюмер(может быть реплицирован), а если надо подключить ещё одного консьюмера, то надо создавать для него свою очередь. Все очереди цепляются к эксченджу, который раскидывает пришедшие в него сообщения на все подключенные очереди, если тип экченджа funout
> 
> https://www.rabbitmq.com/tutorials/tutorial-three-python.html

# Camel Kafka connector RabbitMQ Sink

* https://github.com/apache/camel-kafka-connector-examples/tree/main/rabbitmq/rabbitmq-sink

## Download

* https://repo1.maven.org/maven2/org/apache/camel/kafkaconnector/camel-rabbitmq-kafka-connector/

```
camel_kafka_rabbitmq_version=0.11.5
wget    
tar xvf camel-rabbitmq-kafka-connector-${camel_kafka_rabbitmq_version}-package.tar.gz -C <Kafka_Plugin_Dir>
```

## Configuration Properties

* https://camel.netlify.app/camel-kafka-connector/latest/connectors/camel-rabbitmq-kafka-source-connector.html
* https://camel.netlify.app/camel-kafka-connector/latest/connectors/camel-rabbitmq-kafka-sink-connector.html