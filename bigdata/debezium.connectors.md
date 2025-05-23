---
id: clpmrqc0j0sv8upiwfqsvhv
title: Debezium Connectors
desc: ''
updated: 1693817035644
created: 1659681299918
---

# MongoDB

* [Debezium connector for MongoDB :: Debezium Documentation](https://debezium.io/documentation/reference/connectors/mongodb.html) #[[Roam-Highlights]]
* [Streaming Data from MongoDB into Kafka with Kafka Connect and Debezium](https://rmoff.net/2018/03/27/streaming-data-from-mongodb-into-kafka-with-kafka-connect-and-debezium/) #[[Roam-ighlights]]
* [MongoDB New Document State Extraction :: Debezium Documentation](https://debezium.io/documentation/reference/1.9/transformations/mongodb-event-flattening.html) #[[Roam-Highlights]]
* [MongoDB Outbox Event Router :: Debezium Documentation](https://debezium.io/documentation/reference/1.9/transformations/mongodb-outbox-event-router.html) #[[Roam-Highlights]]
* [MongoDB Change Data Capture via Debezium Kafka Connector with a .NET 5 Client - Vasil Kosturski](https://vkontech.com/mongodb-change-data-capture-via-debezium-kafka-connector-with-a-net-5-client/#Debezium_Connector_vs_Native_Mongo_Connector) #[[Roam-Highlights]]

* [Capture MongoDB Change Events with Debezium and Kafka Connect](https://thecloudnativedataengineer.com/capture-mongodb-change-event-with-debezium-and-kafka-connect/) #[[Roam-Highlights]]



> Note that the MongoDB document is not as fields in the Kafka message, but instead everything is in the payload as a `string` field as escaped JSON.
> 
> Debezium does provide a [Single Message Transform (SMT) to flatten the MongoDB record](http://debezium.io/docs/configuration/mongodb-event-flattening/) out like this, but in using it I hit a bug ([DBZ-649](https://issues.jboss.org/browse/DBZ-649)) that seems to be down to the MongoDB collection documents having different fields between documents. The reported error was `org.apache.kafka.connect.errors.DataException: <field> is not a valid field name`.
>
> However, using KSQL’s `EXTRACTJSONFIELD` you can still work with the data as-is:
> ```sql
ksql> CREATE STREAM DEVICE (after VARCHAR) WITH (KAFKA_TOPIC='ubnt.ace.device-07',VALUE_FORMAT='JSON');
ksql> select EXTRACTJSONFIELD(after,'$.name'),EXTRACTJSONFIELD(after,'$.ip') from device;
Unifi AP - Study | 192.168.10.68
Unifi AP - Attic | 192.168.10.67
ubnt.moffatt.me | 77.102.5.159
Unifi AP - Pantry | 192.168.10.71
>```

## Пользователь в Mongo для Debezium

Скрипт создания пользователя (взял из образа MongoDb из каталога примеров https://github.com/debezium/debezium-examples/tree/main/mongodb-outbox):

```mongo
db.runCommand({
        createRole: "listDatabases",
        privileges: [
            { resource: { cluster : true }, actions: ["listDatabases"]}
        ],
        roles: []
    });

    db.runCommand({
        createRole: "readChangeStream",
        privileges: [
            { resource: { db: "", collection: ""}, actions: [ "find", "changeStream" ] }
        ],
        roles: []
    });

    db.createUser({
        user: 'debezium',
        pwd: 'dbz',
        roles: [
            { role: "readAnyDatabase", db: "admin" },
            { role: "listDatabases", db: "admin" },
            { role: "readChangeStream", db: "admin" },
            {role: 'readAnyDatabase', db: 'admin'},
            // Роли для конкретных баз данных
            #{ role: "readWrite", db: "inventory" },
            { role: "read", db: "local" },
            { role: "read", db: "config" },
            { role: "read", db: "admin" }
        ]
    });
```

## Подключение к Mongo

Вот тут https://issues.redhat.com/browse/DBZ-5566 нашёл информацию для Debezium 1.9.5:

In such case the value for mongodb.hosts should have the following format

```
shard01=rs01/primary01:port;shard02=rs02/primary02:port
```

However with debezium 2.0 you should be able to simplify the connection by using the new mognodb.connection.string property and set that to connection string of your sharded claster. E.g. with MongoDB Atlas

```
mognodb+srv://username:password@your.host
```
# PostgreSQL

* [Setting up PostgreSQL for Debezium – Elephant Tamer](https://elephanttamer.net/?p=50)
* [Streaming Data with PostgreSQL + Kafka + Debezium: Part 1 - DEV Community](https://dev.to/arctype/streaming-data-with-postgresql-kafka-debezium-part-1-4e39)
* https://debezium.io/documentation/reference/2.1/connectors/postgresql.html


Поддерживается только режим [logical decoding](https://www.postgresql.org/docs/current/static/logicaldecoding-explanation.html), начиная с версии PostgreSQL 9.4 и выше.

The PostgreSQL connector contains two main parts that work together to read and process database changes: 

* A logical decoding output plug-in. You might need to install the output plug-in that you choose to use. You must configure a replication slot that uses your chosen output plug-in before running the PostgreSQL server. The plug-in can be one of the following: 
** [`decoderbufs`](https://github.com/debezium/postgres-decoderbufs) is based on Protobuf and maintained by the Debezium community.
** `pgoutput` is the standard logical decoding output plug-in in PostgreSQL 10+. It is maintained by the PostgreSQL community, and used by PostgreSQL itself for [logical replication](https://www.postgresql.org/docs/current/logical-replication-architecture.html). This plug-in is always present so no additional libraries need to be installed. The Debezium connector interprets the raw replication event stream directly into change events. 
* Java code (the actual Kafka Connect connector) that reads the changes produced by the chosen logical decoding output plug-in. It uses PostgreSQL’s __streaming replication protocol__, by means of the PostgreSQL __JDBC driver__

## Настройка PostgreSQL

### AWS

* https://debezium.io/documentation/reference/2.1/connectors/postgresql.html#postgresql-on-amazon-rds
* https://docs.amazonaws.cn/en_us/AmazonRDS/latest/AuroraUserGuide/AuroraPostgreSQL.Replication.Logical.html


### wal_level parameter

Параметр [wal_level](https://postgresqlco.nf/doc/en/param/wal_level/) должен быть установлен в `logical`. 

```
> show wal_level;

> alter system set wal_level to 'logical';
```

После изменения параметра необходим перезапуск экземпляра СУБД.

## Плагины для формирования CDC

* [Using PostgreSQL pgoutput plugin for change data capture with Debezium on Azure - DEV Community](https://dev.to/azure/using-postgresql-pgoutput-plugin-for-change-data-capture-with-debezium-on-azure-26jp)
    * https://github.com/abhirockzz/debezium-postgres-pgoutput

Варианты плагинов:

* pgoutput
* decoderbufs
* wal2json

## Publications and slots

Для формирования потока CDC в PostgreSQL необходимо создать публикации и слоты логической репликации.

* https://www.postgresql.org/docs/15/sql-createpublication.html

> A publication is essentially a group of tables whose data changes are intended to be replicated through logical replication. 

* [PostgreSQL: Documentation: 15: 49.2. Logical Decoding Concepts](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html)

> Logical decoding is the process of extracting all persistent changes to a database's tables into a coherent, easy to understand format which can be interpreted without detailed knowledge of the database's internal state.
> 
> In PostgreSQL, logical decoding is implemented by decoding the contents of the write-ahead log, which describe changes on a storage level, into an application-specific form such as a stream of tuples or SQL statements.
> 
> Replication Slots
> 
> In the context of logical replication, a slot represents a stream of changes that can be replayed to a client in the order they were made on the origin server. Each slot streams a sequence of changes from a single database.

Вот здесь приведена инструкция по ручному созданию публикаций https://elephanttamer.net/?p=50: 

```sql
CREATE PUBLICATION debezium FOR TABLE <your table list here>;
SELECT pg_create_logical_replication_slot('debezium', 'pgoutput');

select * from pg_publication;

select * from pg_replication_slots;
select pg_drop_replication_slot('debezium');
```

On the Debezium side, set:

```json
"publication.name":"debezium",
"slot.name":"debezium"
```

Создание публикаций управляется параметром `publication.autocreate.mode`. 

https://dev.to/azure/using-postgresql-pgoutput-plugin-for-change-data-capture-with-debezium-on-azure-26jp:

> With the `pgoutput` plugin, it's important that you use the appropriate value for `publication.autocreate.mode`. 
> * If you're using `all_tables` (_which is the default_), you need to ensure that the publication is created up-front for the specific table(s) you want to configure for change data capture. If the publication is not found, the connector will try to create one using `CREATE PUBLICATION <publication_name> FOR ALL TABLES;` which will fail due to lack of permissions.
>
> * `disabled`: you need to ensure that the publication is created up-front. The connector will not attempt to create the publication if it isn't found to exist upon startup - it will throw an exception and stop.
> * `filtered`: you can (optionally) choose to create the publication up-front. If the publication is not found, the connector will create a new publication for all those tables matching the current filter configuration.
>   When `publication.autocreate.mode` is set to `filtered`, this works well with Azure PostgreSQL - it does not require super user permissions because the connector creates the publication for a specific table(s) based on the filter/*list values 

## Пример создания пользователя для работы Debezium

```sql
drop publication dbz_publication;
select * from pg_publication;

select * from pg_replication_slots;
SELECT pg_create_logical_replication_slot('debezium', 'pgoutput');

select pg_drop_replication_slot('debezium');


CREATE ROLE debezium REPLICATION LOGIN;
ALTER USER debezium WITH PASSWORD 'd7rABuRueZk';

grant select on all tables in schema public to debezium; 



CREATE ROLE debezium_replication_group;

GRANT debezium_replication_group TO wnf_bj;
GRANT debezium_replication_group TO debezium;

ALTER TABLE public.bets OWNER TO debezium_replication_group;
ALTER TABLE public.cards OWNER TO debezium_replication_group;
ALTER TABLE public.decisions OWNER TO debezium_replication_group;
ALTER TABLE public.games OWNER TO debezium_replication_group;
ALTER TABLE public.game_players OWNER TO debezium_replication_group;
ALTER TABLE public.game_states OWNER TO debezium_replication_group;
ALTER TABLE public.player_hand_states OWNER TO debezium_replication_group;
ALTER TABLE public.player_hands OWNER TO debezium_replication_group;
ALTER TABLE public.results OWNER TO debezium_replication_group;
ALTER TABLE public.table_players OWNER TO debezium_replication_group;
ALTER TABLE public.users OWNER TO debezium_replication_group;

create publication dbz_pub_blackjack;
alter publication dbz_pub_blackjack owner to debezium;
SELECT pg_create_logical_replication_slot('debezium_blackjack', 'pgoutput');

alter publication dbz_pub_blackjack add table public.bets;
alter publication dbz_pub_blackjack add table public.cards;
alter publication dbz_pub_blackjack add table public.decisions;
alter publication dbz_pub_blackjack add table publbtc.games;
alter publication dbz_pub_blackjack add table public.game_players;
alter publication dbz_pub_blackjack add table public.game_states;
alter publication dbz_pub_blackjack add table public.player_hand_states;
alter publication dbz_pub_blackjack add table public.player_hands;
alter publication dbz_pub_blackjack add table public.results;
alter publication dbz_pub_blackjack add table public.table_players;
alter publication dbz_pub_blackjack add table public.users;

ALTER TABLE public.bets OWNER TO wnf_bj;
ALTER TABLE public.cards OWNER to wnf_bj;
ALTER TABLE public.bets OWNER TO wnf_bj;
ALTER TABLE public.cards OWNER TO wnf_bj;
ALTER TABLE public.decisions OWNER TO wnf_bj;
ALTER TABLE public.games OWNER TO wnf_bj;
ALTER TABLE public.game_players OWNER TO wnf_bj;
ALTER TABLE public.game_states OWNER TO wnf_bj;
ALTER TABLE public.player_hand_states OWNER TO wnf_bj;
ALTER TABLE public.player_hands OWNER TO wnf_bj;
ALTER TABLE public.results OWNER TO wnf_bj;
ALTER TABLE public.table_players OWNER TO wnf_bj;
ALTER TABLE public.users OWNER TO wnf_bj;
```

# MySQL

Для получения данных в хранилище данных DataEngineering в СУБД MySQL необходимо активировать режим BinLog: https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.MySQL.BinaryFormat.html 

`binlog_format = ROW`