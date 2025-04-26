---
id: uy6qccq037pltswo4wnedeb
title: Mongodb
desc: ''
updated: 1671806397597
created: 1657006953772
---

# Tools

Средства подключения к БД:

* mongosh: https://www.mongodb.com/products/shell
* Compass: https://www.mongodb.com/products/compass

## Download

https://www.mongodb.com/try/download/shell

## Mongo Shell

* https://www.mongodb.com/docs/mongodb-shell/

# Подключение

## MongoDB+SRV

https://www.mongodb.com/developer/products/mongodb/srv-connection-strings/


# Репликация

* [Replication](https://www.mongodb.com/docs/manual/replication/)
* [Convert a Standalone to a Replica Set — MongoDB Manual](https://www.mongodb.com/docs/manual/tutorial/convert-standalone-to-replica-set/) #[[Roam-Highlights]]
* [Replica OpLog](https://www.mongodb.com/docs/manual/core/replica-set-oplog/)
* [Replica Set Arbiter — MongoDB Manual](https://www.mongodb.com/docs/manual/core/replica-set-arbiter/) #[[Roam-Highlights]]
* [Replica Set Data Synchronization — MongoDB Manual](https://www.mongodb.com/docs/manual/core/replica-set-sync/#std-label-replica-set-sync) #[[Roam-Highlights]]
* [Change Streams — MongoDB Manual](https://www.mongodb.com/docs/manual/changeStreams/) #[[Roam-Highlights]]

## Replication state

* **Конфигурация**: `rs.conf()`
* **Статус**: `rs.status()`


## Change Streams
        
Starting in MongoDB 3.6, [change streams](https://www.mongodb.com/docs/manual/changeStreams/) are available for replica sets and sharded clusters. Change streams allow applications to access real-time data changes without the complexity and risk of tailing the oplog. Applications can use change streams to subscribe to all data changes on a collection or collections.

# Connectors


## Kafka

Список коннекторов можно посмотреть на [Confluent Hub](https://www.confluent.io/hub/):

* [MongoDB Connector (Source and Sink) | Confluent Hub](https://www.confluent.io/hub/mongodb/kafka-connect-mongodb) #[[Roam-Highlights]]
  * [Apache Kafka Connector | MongoDB](https://www.mongodb.com/kafka-connector) #[[Roam-Highlights]]
  * [MongoDB Kafka Connector — MongoDB Kafka Connector](https://www.mongodb.com/docs/kafka-connector/current/) #[[Roam-Highlights]]
  * https://github.com/mongodb/mongo-kafka
* [MongoDB Sink Connector | Confluent Hub](https://www.confluent.io/hub/hpgrahsl/kafka-connect-mongodb) #[[Roam-Highlights]]
  * https://github.com/hpgrahsl/kafka-connect-mongodb  
* [Debezium MongoDB CDC Source Connector | Confluent Hub](https://www.confluent.io/hub/debezium/debezium-connector-mongodb) #[[Roam-Highlights]]
  * [[debezium.connectors]]

### Пример использования

* https://www.mongodb.com/docs/kafka-connector/current/quick-start/

#### Download sandbox

```sh
git clone https://github.com/mongodb-university/kafka-edu.git
cd kafka-edu/docs-examples/examples/v1.7/quickstart
```

#### Start sandbox

```sh
docker-compose -p quickstart up -d
```

#### Go to shell

```
docker exec -it shell /bin/bash
```

Далее команды выполнять из shell

#### Mongo Shell

```sh
$ mongosh mongodb://mongo1:27017/?replicaSet=rs0
```

Посмотреть статус репликации:

```sh
rs0 [primary] test> rs.status();
{
  set: 'rs0',
  date: ISODate("2022-08-05T08:56:09.677Z"),
  myState: 1,
  term: Long("1"),
  syncSourceHost: '',
  syncSourceId: -1,
  heartbeatIntervalMillis: Long("2000"),
  majorityVoteCount: 1,
  writeMajorityCount: 1,
  votingMembersCount: 1,
  writableVotingMembersCount: 1,
  optimes: {
    lastCommittedOpTime: { ts: Timestamp(1, 1659689761), t: Long("1") },
    lastCommittedWallTime: ISODate("2022-08-05T08:56:01.341Z"),
    readConcernMajorityOpTime: { ts: Timestamp(1, 1659689761), t: Long("1") },
    appliedOpTime: { ts: Timestamp(1, 1659689761), t: Long("1") },
    durableOpTime: { ts: Timestamp(1, 1659689761), t: Long("1") },
    lastAppliedWallTime: ISODate("2022-08-05T08:56:01.341Z"),
    lastDurableWallTime: ISODate("2022-08-05T08:56:01.341Z")
  },
  lastStableRecoveryTimestamp: Timestamp(1, 1659689721),
  electionCandidateMetrics: {
    lastElectionReason: 'electionTimeout',
    lastElectionDate: ISODate("2022-08-05T08:49:21.279Z"),
    electionTerm: Long("1"),
    lastCommittedOpTimeAtElection: { ts: Timestamp(1, 1659689361), t: Long("-1") },
    lastSeenOpTimeAtElection: { ts: Timestamp(1, 1659689361), t: Long("-1") },
    numVotesNeeded: 1,
    priorityAtElection: 1,
    electionTimeoutMillis: Long("10000"),
    newTermStartDate: ISODate("2022-08-05T08:49:21.318Z"),
    wMajorityWriteAvailabilityDate: ISODate("2022-08-05T08:49:21.341Z")
  },
  members: [
    {
      _id: 0,
      name: 'mongo1:27017',
      health: 1,
      state: 1,
      stateStr: 'PRIMARY',
      uptime: 419,
      optime: { ts: Timestamp(1, 1659689761), t: Long("1") },
      optimeDate: ISODate("2022-08-05T08:56:01.000Z"),
      lastAppliedWallTime: ISODate("2022-08-05T08:56:01.341Z"),
      lastDurableWallTime: ISODate("2022-08-05T08:56:01.341Z"),
      syncSourceHost: '',
      syncSourceId: -1,
      infoMessage: '',
      electionTime: Timestamp(2, 1659689361),
      electionDate: ISODate("2022-08-05T08:49:21.000Z"),
      configVersion: 1,
      configTerm: 1,
      self: true,
      lastHeartbeatMessage: ''
    }
  ],
  ok: 1,
  '$clusterTime': {
    clusterTime: Timestamp(1, 1659689761),
    signature: {
      hash: Binary(Buffer.from("0000000000000000000000000000000000000000", "hex"), 0),
      keyId: Long("0")
    }
  },
  operationTime: Timestamp(1, 1659689761)
}
```

Базы данных и таблицы:

```sh
rs0 [primary] test> show databases;
admin       81.9 kB
config      73.7 kB
local        414 kB
quickstart  16.4 kB

rs0 [primary] test> use quickstart;
switched to db quickstart
rs0 [primary] quickstart> show tables;
sink
source

rs0 [primary] quickstart> db.sink.find();

rs0 [primary] quickstart> db.source.find();
```

#### Add Connectors

**Список коннекторов**

```sh
curl -X GET http://connect:8083/connectors
[]
```

**Список топиков**

```sh
kafkacat -b broker:9092 -L  | grep topic
Metadata for all topics (from broker -1: broker:9092/bootstrap):
 4 topics:
  topic "docker-connect-status" with 5 partitions:
  topic "docker-connect-offsets" with 25 partitions:
  topic "docker-connect-configs" with 1 partitions:
  topic "__consumer_offsets" with 50 partitions:
```

**Add a Source Connector**

```sh
curl -X POST \
     -H "Content-Type: application/json" \
     --data '
     {"name": "mongo-source",
      "config": {
         "connector.class":"com.mongodb.kafka.connect.MongoSourceConnector",
         "connection.uri":"mongodb://mongo1:27017/?replicaSet=rs0",
         "database":"quickstart",
         "collection":"sampleData",
         "pipeline":"[{\"$match\": {\"operationType\": \"insert\"}}, {$addFields : {\"fullDocument.travel\":\"MongoDB Kafka Connector\"}}]"
         }
     }
     ' \
     http://connect:8083/connectors -w "\n"
```

В образе есть файл _source-connector.json_

```sh
cat source-connector.json 
{
  "name": "mongo-source",
  "config": {
    "connector.class": "com.mongodb.kafka.connect.MongoSourceConnector",
    "connection.uri": "mongodb://mongo1",
    "database": "quickstart",
    "collection": "source"
  }
}
```

Сделал свой файл _source-connector-test.json_ из примера в доке:

```sh
cat > source-connector-test.json
{"name": "mongo-source",
    "config": {
        "connector.class":"com.mongodb.kafka.connect.MongoSourceConnector",
        "connection.uri":"mongodb://mongo1:27017/?replicaSet=rs0",
        "database":"quickstart",
        "collection":"source",
        "pipeline":"[{\"$match\": {\"operationType\": \"insert\"}}, {$addFields : {\"fullDocument.travel\":\"MongoDB Kafka Connector\"}}]"
    }
}
```

Создал коннектор:

```sh
curl -X POST -X POST -H "Content-Type: application/json" --data @source-connector-test.json http://connect:8083/connectors -w "\n"
curl -X GET http://connect:8083/connectors -w "\n"
["mongo-source"]
```

_Топика в Kafka не появилось_.

**Add a Sink Connector**

```sh
curl -X POST \
     -H "Content-Type: application/json" \
     --data '
     {"name": "mongo-sink",
      "config": {
         "connector.class":"com.mongodb.kafka.connect.MongoSinkConnector",
         "connection.uri":"mongodb://mongo1:27017/?replicaSet=rs0",
         "database":"quickstart",
         "collection":"topicData",
         "topics":"quickstart.sampleData",
         "change.data.capture.handler": "com.mongodb.kafka.connect.sink.cdc.mongodb.ChangeStreamHandler"
         }
     }
     ' \
     http://connect:8083/connectors -w "\n"
```

```sh
cat sink-connector.json 
{
  "name": "mongo-sink",
  "config": {
    "connector.class": "com.mongodb.kafka.connect.MongoSinkConnector",
    "connection.uri": "mongodb://mongo1",
    "database": "quickstart",
    "collection": "sink",
    "topics": "quickstart.source",
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",
    "value.converter.schemas.enable": "false",
    "key.converter": "org.apache.kafka.connect.json.JsonConverter",
    "key.converter.schemas.enable": "false"
  }
}
```

Сделал свой файл для конфигурации коннектора:

```sh
cat > sink-connector-test.json
{"name": "mongo-sink",
    "config": {
        "connector.class":"com.mongodb.kafka.connect.MongoSinkConnector",
        "connection.uri":"mongodb://mongo1:27017/?replicaSet=rs0",
        "database":"quickstart",
        "collection":"sink",
        "topics":"quickstart.source",
        "change.data.capture.handler": "com.mongodb.kafka.connect.sink.cdc.mongodb.ChangeStreamHandler"
    }
}
```

Создал коннектор:

```sh
curl -X POST -X POST -H "Content-Type: application/json" --data @sink-connector-test.json http://connect:8083/connectors -w "\n"
curl -X GET http://connect:8083/connectors -w "\n"
["mongo-sink","mongo-source"]
```

Если вот это `"change.data.capture.handler": "com.mongodb.kafka.connect.sink.cdc.mongodb.ChangeStreamHandler"` не задать, то в таблицу записываются CDC-данные как есть:

```
rs0 [primary] quickstart> db.source.find();
[
  { _id: ObjectId("62ed04c79978a5d8266f12e8"), hello: 'world' },
  { _id: ObjectId("62ed08039978a5d8266f12e9"), hello: 'Lord' }
]
rs0 [primary] quickstart> db.sink.find();
[
  {
    _id: ObjectId("62ed04c79978a5d8266f12e8"),
    hello: 'world',
    travel: 'MongoDB Kafka Connector'
  },
  {
    _id: ObjectId("62ed0806f12ac37aebf260d3"),
    schema: { optional: false, type: 'string' },
    payload: '{"_id": {"_data": "8262ED0803000000012B022C0100296E5A1004E4CE2EC073834669A3F78D99C6F8B96046645F6964006462ED08039978A5D8266F12E90004"}, "operationType": "insert", "clusterTime": {"$timestamp": {"t": 1659701251, "i": 1}}, "fullDocument": {"_id": {"$oid": "62ed08039978a5d8266f12e9"}, "hello": "Lord", "travel": "MongoDB Kafka Connector"}, "ns": {"db": "quickstart", "coll": "source"}, "documentKey": {"_id": {"$oid": "62ed08039978a5d8266f12e9"}}}'
  }
]
```

#### Data Replication

```sh
use quickstart
db.source.insertOne({"hello":"world"})
db.source.find();
[ { _id: ObjectId("62ed04c79978a5d8266f12e8"), hello: 'world' } ]
```

Появился топик в Kafka:

```sh
$ kafkacat -b broker:9092 -L  | grep topic
Metadata for all topics (from broker -1: broker:9092/bootstrap):
 5 topics:
  topic "docker-connect-status" with 5 partitions:
  topic "__consumer_offsets" with 50 partitions:
  topic "docker-connect-configs" with 1 partitions:
  topic "docker-connect-offsets" with 25 partitions:
  topic "quickstart.source" with 1 partitions:
```

Данные в топике в виде строк JSON:

```sh
$ kafkacat -b broker:29092 -C -t quickstart.source  -e  
{"schema":{"type":"string","optional":false},"payload":"{\"_id\": {\"_data\": \"8262ED04C7000000012B022C0100296E5A1004E4CE2EC073834669A3F78D99C6F8B96046645F6964006462ED04C79978A5D8266F12E80004\"}, \"operationType\": \"insert\", \"clusterTime\": {\"$timestamp\": {\"t\": 1659700423, \"i\": 1}}, \"fullDocument\": {\"_id\": {\"$oid\": \"62ed04c79978a5d8266f12e8\"}, \"hello\": \"world\", \"travel\": \"MongoDB Kafka Connector\"}, \"ns\": {\"db\": \"quickstart\", \"coll\": \"source\"}, \"documentKey\": {\"_id\": {\"$oid\": \"62ed04c79978a5d8266f12e8\"}}}"}
% Reached end of topic quickstart.source [0] at offset 1: exiting
```

```sh
db.sink.find()
[
  {
    _id: ObjectId("62ed04c79978a5d8266f12e8"),
    hello: 'world',
    travel: 'MongoDB Kafka Connector'
  }
]
```

### Операция удаления

**MongoDB Kafka Коннектор не поддерживает операции удаления**


```sh
rs0 [primary] quickstart> db.source.insertOne({"hello":"Delete"})
rs0 [primary] quickstart> db.source.find();
[
  { _id: ObjectId("62ed04c79978a5d8266f12e8"), hello: 'world' },
  { _id: ObjectId("62ed1cca9978a5d8266f12ea"), hello: 'Delete' }
]
rs0 [primary] quickstart> db.source.deleteOne({hello: "Delete"});
{ acknowledged: true, deletedCount: 1 }
rs0 [primary] quickstart> db.source.find();
[ { _id: ObjectId("62ed04c79978a5d8266f12e8"), hello: 'world' } ]

rs0 [primary] quickstart> db.sink.find();
[
  {
    _id: ObjectId("62ed04c79978a5d8266f12e8"),
    hello: 'world',
    travel: 'MongoDB Kafka Connector'
  },
  {
    _id: ObjectId("62ed1cca9978a5d8266f12ea"),
    hello: 'Delete',
    travel: 'MongoDB Kafka Connector'
  }
]
```


## Kafka CDC

* https://github.com/srigumm/Mongo-To-Kafka-CDC
* [Explore MongoDB Change Streams](https://www.mongodb.com/docs/kafka-connector/v1.7/tutorials/explore-change-streams/)

Коннектор Kafka поддерживает CDC, но его формат не совпадает с Debezium:

```json
{
  "_id": {
    "_data": "826264..."
  },
  "operationType": "insert",
  "clusterTime": {
    "$timestamp": {
      "t": 1650754657,
      "i": 1
    }
  },
  "fullDocument": {
    "_id": {
      "$oid": "62648461d9440c0c72a2202c"
    },
    "test": 1
  },
  "ns": {
    "db": "Tutorial1",
    "coll": "orders"
  },
  "documentKey": {
    "_id": {
      "$oid": "62648461d9440c0c72a2202c"
    }
  }
}
```

Вот [тут](https://www.mongodb.com/community/forums/t/mongodb-as-sink-connector-not-capturing-data-as-expected-kafka/112158) есть об этом заметка.

Мысли и сравнение Mongo Kafka Connector и Debezium:

* [Debezium Connector vs. Native Mongo Connector](https://vkontech.com/mongodb-change-data-capture-via-debezium-kafka-connector-with-a-net-5-client/#Debezium_Connector_vs_Native_Mongo_Connector)
* in a Reddit thread [here](https://www.reddit.com/r/bigdata/comments/pp86fw/comment/hd2m31n/?utm_source=share&utm_medium=web2x&context=3):

* https://github.com/hpgrahsl/kafka-connect-mongodb
* https://github.com/hpgrahsl/kafka-connect-mongodb/issues/82


**Оказывается формат Debezium поддерживается!!!**

[Change Data Capture Handlers — MongoDB Kafka Connector](https://www.mongodb.com/docs/kafka-connector/v1.2/sink-connector/fundamentals/change-data-capture/#how-to-use-your-cdc-handler) #[[Roam-Highlights]]:

> **Available CDC Handlers**
> 
> The MongoDB Kafka Connector provides CDC handlers for the following CDC event producers:
> * MongoDB
> * Debezium
> 
> _Click the following tabs to learn how to configure CDC handlers for the preceding event producers:_
> 
> MongoDB:
> 
```json
connector.class=com.mongodb.kafka.connect.sink.MongoSinkConnector
connection.uri=<your connection uri>
database=<your mongodb database>
collection=<your mongodb collection>
topics=<topic containing debezium cdc events>
change.data.capture.handler=com.mongodb.kafka.connect.sink.cdc.debezium.mongodb.MongoDbHandler
```

### Интеграция с Debezium

* Релизы официального коннектора для Kafka от Mongo https://github.com/mongodb/mongo-kafka/releases
* [Change Data Capture Handlers — MongoDB Kafka Connector](https://www.mongodb.com/docs/kafka-connector/current/sink-connector/fundamentals/change-data-capture/) #[[Roam-Highlights]]
    * [the MongoDB Kafka Connector source code](https://github.com//mongodb/mongo-kafka/tree/r1.7.0/src/main/java/com/mongodb/kafka/connect/sink/cdc/debezium)

При использовании в качестве источника Debezium - сбой при обработке событий обновления:

```
[2022-08-16 16:44:57,905] INFO [mongo-sink-latest|task-0] Opened connection [connectionId{localValue:6, serverValue:20255}] to mongodb-headless.db:27017 (org.mongodb.driver.connection:71)
[2022-08-16 16:48:49,793] ERROR [mongo-sink-latest|task-0] Unable to process record SinkRecord{kafkaOffset=4, timestampType=CreateTime} ConnectRecord{topic='mongodb.latest.playerstats.testDebeziumCollection', kafkaPartition=0, key=Struct{id={"$oid": "62fb837e0c639cb635358329"}}, keySchema=Schema{mongodb.latest.playerstats.testDebeziumCollection.Key:STRUCT}, value=Struct{after={"_id": {"$oid": "62fb837e0c639cb635358329"},"x": 4,"y": 4,"desc": "Test Update"},updateDescription=Struct{updatedFields={"desc": "Test Update"}},source=Struct{version=1.9.5.Final,connector=mongodb,name=mongodb.latest,ts_ms=1660650529000,snapshot=false,db=playerstats,rs=rs0,collection=testDebeziumCollection,ord=24},op=u,ts_ms=1660650529306}, valueSchema=Schema{mongodb.latest.playerstats.testDebeziumCollection.Envelope:STRUCT}, timestamp=1660650529745, headers=ConnectHeaders(headers=)} (com.mongodb.kafka.connect.sink.MongoProcessedSinkRecordData:109)
org.apache.kafka.connect.errors.DataException: Value expected to be of type STRING is of unexpected type NULL
	at com.mongodb.kafka.connect.sink.cdc.debezium.mongodb.MongoDbUpdate.perform(MongoDbUpdate.java:88)
	at com.mongodb.kafka.connect.sink.cdc.debezium.mongodb.MongoDbHandler.handle(MongoDbHandler.java:82)
	at com.mongodb.kafka.connect.sink.MongoProcessedSinkRecordData.lambda$buildWriteModelCDC$3(MongoProcessedSinkRecordData.java:99)
	at java.base/java.util.Optional.flatMap(Optional.java:294)
	at com.mongodb.kafka.connect.sink.MongoProcessedSinkRecordData.lambda$buildWriteModelCDC$4(MongoProcessedSinkRecordData.java:99)
	at com.mongodb.kafka.connect.sink.MongoProcessedSinkRecordData.tryProcess(MongoProcessedSinkRecordData.java:105)
	at com.mongodb.kafka.connect.sink.MongoProcessedSinkRecordData.buildWriteModelCDC(MongoProcessedSinkRecordData.java:98)
	at com.mongodb.kafka.connect.sink.MongoProcessedSinkRecordData.createWriteModel(MongoProcessedSinkRecordData.java:81)
	at com.mongodb.kafka.connect.sink.MongoProcessedSinkRecordData.<init>(MongoProcessedSinkRecordData.java:51)
	at com.mongodb.kafka.connect.sink.MongoSinkRecordProcessor.orderedGroupByTopicAndNamespace(MongoSinkRecordProcessor.java:45)
	at com.mongodb.kafka.connect.sink.StartedMongoSinkTask.put(StartedMongoSinkTask.java:75)
	at com.mongodb.kafka.connect.sink.MongoSinkTask.put(MongoSinkTask.java:90)
...
```

Следить вот за этими ISSUE:
* https://jira.mongodb.org/browse/KAFKA-299
* https://jira.mongodb.org/browse/KAFKA-300
* https://github.com/mongodb/mongo-kafka/pull/107

### Конвертеры

* [Converters — MongoDB Kafka Connector](https://www.mongodb.com/docs/kafka-connector/current/introduction/converters) #[[Roam-Highlights]]

## AVRO

Не работает обратное преобразование в Sink: 

```
[2022-08-16 16:19:15,113] ERROR [mongo-sink-latest|task-0] Unable to process record SinkRecord{kafkaOffset=2, timestampType=CreateTime} ConnectRecord{topic='mongodb.latest.playerstats.testDebeziumCollection.mongocdc', kafkaPartition=0, key=Struct{_id={"_id": {"$oid": "62fb465568480ea21a747883"}, "copyingData": true}}, keySchema=Schema{keySchema:STRUCT}, value=Struct{_id={"_id": {"$oid": "62fb465568480ea21a747883"}, "copyingData": true},operationType=insert,fullDocument={"_id": {"$oid": "62fb465568480ea21a747883"}, "x": 3, "y": 3, "z": 3, "desc": "Description"},ns=Struct{db=playerstats,coll=testDebeziumCollection},documentKey={"_id": {"$oid": "62fb465568480ea21a747883"}}}, valueSchema=Schema{ChangeStream:STRUCT}, timestamp=1660648570885, headers=ConnectHeaders(headers=)} (com.mongodb.kafka.connect.sink.MongoProcessedSinkRecordData:109)
org.apache.kafka.connect.errors.DataException: Unexpected documentKey field type, expecting a document but found `BsonString{value='{"_id": {"$oid": "62fb465568480ea21a747883"}}'}`: {"_id": "{\"_id\": {\"$oid\": \"62fb465568480ea21a747883\"}, \"copyingData\": true}", "operationType": "insert", "fullDocument": "{\"_id\": {\"$oid\": \"62fb465568480ea21a747883\"}, \"x\": 3, \"y\": 3, \"z\": 3, \"desc\": \"Description\"}", "ns": {"db": "playerstats", "coll": "testDebeziumCollection"}, "to": null, "documentKey": "{\"_id\": {\"$oid\": \"62fb465568480ea21a747883\"}}", "updateDescription": null, "clusterTime": null, "txnNumber": null, "lsid": null}
	at com.mongodb.kafka.connect.sink.cdc.mongodb.operations.OperationHelper.getDocumentKey(OperationHelper.java:53)
	at com.mongodb.kafka.connect.sink.cdc.mongodb.operations.Replace.perform(Replace.java:46)
	at com.mongodb.kafka.connect.sink.cdc.mongodb.ChangeStreamHandler.handle(ChangeStreamHandler.java:84)
	at com.mongodb.kafka.connect.sink.MongoProcessedSinkRecordData.lambda$buildWriteModelCDC$3(MongoProcessedSinkRecordData.java:99)
	at java.base/java.util.Optional.flatMap(Optional.java:294)
  ...
```

#### JSONSchema

Не работает обратное преобразование в Sink:

```
java.lang.AbstractMethodError: Receiver class io.confluent.kafka.schemaregistry.json.JsonSchemaProvider does not define or inherit an implementation of the resolved method 'abstract java.util.Optional parseSchema(java.lang.String, java.util.List, boolean)' of interface io.confluent.kafka.schemaregistry.SchemaProvider.
	at io.confluent.kafka.schemaregistry.SchemaProvider.parseSchema(SchemaProvider.java:63)
```

Видимо не хватает какого-то класса.

#### JSON 

Это преобразование без использования схемы пока работает наиболее понятно и без проблем

## Spark

* [MongoDB Connector for Spark — MongoDB Spark Connector](https://www.mongodb.com/docs/spark-connector/current/) #[[Roam-Highlights]]

# Использование 

## Выборка данных

* `db.getCollection('_migrations').find();`
* `db.mytestcolelction.find();`


## Информация о хосте

`db.hostInfo()`

## Список всех подключений клиентов

`db.currentOp(true).inprog.forEach(function(d){if(d.client)print(d.client, d.connectionId)})`

## Роли

* Встроенные роли: https://www.mongodb.com/docs/manual/reference/built-in-roles/
* Привилегии: https://www.mongodb.com/docs/manual/reference/privilege-actions

```
> use admin
admin> db.system.roles.find()
```

### Список пользовательских ролей

`db.runCommand({rolesInfo: 1})`

### Создание роли

```
> use admin
admin> db.createRole({role: 'trinoRole', privileges:[{actions: ["createCollection", "createIndex", "find", "insert", "remove", "update"], resource: {db:"", collection: "_trino_schema"}}], roles:[{role: 'readAnyDatabase', db: 'admin'}]})
```

### Убрать роль у пользователя

```
db.revokeRolesFromUser(
    "reportsUser",
    [
      { role: "readWrite", db: "accounts" }
    ]
)
```

### Добавить роль пользователю

```
db.grantRolesToUser(
    "reportsUser",
    [
      { role: "read", db: "accounts" }
    ]
)
```

### Пользователи

### Список всех пользователей

```
> use admin
admin> db.system.users.find()
```

### Информация о пользователе

`db.getUser("trino")`

### Создать пользователя

Создать пользователя для чтения всех баз https://dba.stackexchange.com/questions/283843/create-user-for-all-databases-in-mongodb

`admin> db.createUser({user: '<user_name>', pwd: '<password>', roles: [{role: 'readAnyDatabase', db: 'admin'}]})`


## Python Client

* https://github.com/trinodb/trino-python-client
