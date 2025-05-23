---
id: vpir8bchjr9bage6fxn1g5x
title: ksqlDB
desc: ''
updated: 1677845887759
created: 1644475395558
---

**Проект Confluent ksqlDB**

* https://ksqldb.io/
* https://github.com/confluentinc/ksql

# Ссылки

* https://medium.com/trendyol-tech/how-we-track-seller-center-usage-statistics-with-ksqldb-7c8e696c3c9d#id_token=eyJhbGciOiJSUzI1NiIsImtpZCI6ImY0YmIyMjBjZDA5NGIwYWU5MGRkNzNlMTBjMTBlN2RiNTRiODkyODAiLCJ0eXAiOiJKV1QifQ.eyJpc3MiOiJodHRwczovL2FjY291bnRzLmdvb2dsZS5jb20iLCJuYmYiOjE2NDQ0NzQ2OTQsImF1ZCI6IjIxNjI5NjAzNTgzNC1rMWs2cWUwNjBzMnRwMmEyamFtNGxqZGNtczAwc3R0Zy5hcHBzLmdvb2dsZXVzZXJjb250ZW50LmNvbSIsInN1YiI6IjExNTY2MDg2MzEyMDMxMDI1MjI1MyIsImVtYWlsIjoic29saXZlcnJAZ21haWwuY29tIiwiZW1haWxfdmVyaWZpZWQiOnRydWUsImF6cCI6IjIxNjI5NjAzNTgzNC1rMWs2cWUwNjBzMnRwMmEyamFtNGxqZGNtczAwc3R0Zy5hcHBzLmdvb2dsZXVzZXJjb250ZW50LmNvbSIsIm5hbWUiOiLQodC10YDQs9C10Lkg0JrRgNGP0LbQtdCy0YHQutC40YUiLCJwaWN0dXJlIjoiaHR0cHM6Ly9saDMuZ29vZ2xldXNlcmNvbnRlbnQuY29tL2EtL0FPaDE0R2lvYXhfUXhLcEpFVUV2X09FNkM0VVZWbmtRYk50cnh3WUJCZWRCPXM5Ni1jIiwiZ2l2ZW5fbmFtZSI6ItCh0LXRgNCz0LXQuSIsImZhbWlseV9uYW1lIjoi0JrRgNGP0LbQtdCy0YHQutC40YUiLCJpYXQiOjE2NDQ0NzQ5OTQsImV4cCI6MTY0NDQ3ODU5NCwianRpIjoiMTM5MThhOWY3MGViODZiZGY0YjVhZGJmODQwODA3ZjkwOTEwNDQ3NSJ9.Irabp_w-4MuQe6PFOJLbQEppgaapaivpoqkOxk__OkkPLvGuW4QIBh6tLBgPDLDG4eBvVbIlduCgy0b0XyzOqaR1RYTL-DyLAWwvEjN-SxN5QT-yEpD1sGm9rQcluTO1ZVAoEhkviqVZBLF5cOGGavsgS_S1mWgoAkghEedp1Gnq8GdiCUlrsauxw8097N881G3NzpaBNmlkWprhbz2V2-hIMW9vXGB980JTGf0pJCMFbqA8Zwd6En3C0XkfFkrArkbCi6sj79hMDfZbIcz8dc6eAfpaHEz3l-hOW3mDxZizXNC-TZ1pCuDFDdq1Cjt7adNpwyKcmZXrXJsb9AE9gw
* https://www.confluent.io/blog/elasticsearch-ksqldb-integration-for-data-enrichment-and-analytics/
* https://www.confluent.io/blog/guide-to-stream-processing-and-ksqldb-fundamentals/

# Установка

## Standalone

https://ksqldb.io/quickstart-standalone-deb.html#quickstart-content

## Docker

```yaml
---
version: '2'

services:
  ksqldb-server:
    image: confluentinc/ksqldb-server:0.23.1
    hostname: ksqldb-server
    container_name: ksqldb-server
    ports:
      - "8088:8088"
    environment:
      KSQL_LISTENERS: http://0.0.0.0:8088
      KSQL_BOOTSTRAP_SERVERS: $BROKER_ENDPOINT
      KSQL_KSQL_LOGGING_PROCESSING_STREAM_AUTO_CREATE: "true"
      KSQL_KSQL_LOGGING_PROCESSING_TOPIC_AUTO_CREATE: "true"

  ksqldb-cli:
    image: confluentinc/ksqldb-cli:0.23.1
    container_name: ksqldb-cli
    depends_on:
      - ksqldb-server
    entrypoint: /bin/sh
    tty: true
```


## Debian

**Install basic software**

```sh
sudo apt update
sudo apt install -y software-properties-common curl gnupg
```

**Get current version**

* https://ksqldb.io/quickstart-standalone-deb.html#quickstart-content

```sh
ksql_version=0.28
```

**Import the public key**

```sh
curl -sq http://ksqldb-packages.s3.amazonaws.com/deb/$ksql_version/archive.key | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/ksqldb_$ksql_version.gpg
```

**Add the ksqlDB apt repository**

```sh
sudo add-apt-repository "deb http://ksqldb-packages.s3.amazonaws.com/deb/$ksql_version stable main"
```

**Install the package**

```sh
sudo apt install confluent-ksqldb
```

**Create log directory**

```sh
sudo ln -s  /var/log/confluent /usr/logs
sudo chmod 777 /var/log/confluent/
```

## RedHat

```sh
# Import the public key
sudo rpm --import http://ksqldb-packages.s3.amazonaws.com/rpm/0.23/archive.key

# Update the yum configuration with:
[KSQLDB]
name=KSQLDB
baseurl=http://ksqldb-packages.s3.amazonaws.com/rpm/0.23/
gpgcheck=1
gpgkey=http://ksqldb-packages.s3.amazonaws.com/rpm/0.23/archive.key
enabled=1

# Install the package
sudo yum install confluent-ksqldb
```

## Tarball

```sh
# Download the public key and follow prompts to import it.
curl -sq http://ksqldb-packages.s3.amazonaws.com/archive/0.23/archive.key | gpg --import

# Download the archive and its signature
curl http://ksqldb-packages.s3.amazonaws.com/archive/0.23/confluent-ksqldb-0.23.1.tar.gz --output confluent-ksqldb-0.23.1.tar.gz
curl http://ksqldb-packages.s3.amazonaws.com/archive/0.23/confluent-ksqldb-0.23.1.tar.gz.asc --output confluent-ksqldb-0.23.1.tar.gz.asc

# Validate the signature
gpg --verify confluent-ksqldb-0.23.1.tar.gz.asc confluent-ksqldb-0.23.1.tar.gz

# Extract the tarball to the directory of your choice
tar -xf confluent-ksqldb-0.23.1.tar.gz -C /opt/confluent
```

# Концепции

* https://docs.ksqldb.io/en/latest/concepts

## Events

ksqlDB aims to raise the abstraction from working with a lower-level stream processor. Usually, an event is called a "row", as if it were a row in a relational database. Each row is composed of a series of columns. Columns are either read from the event's key or value. _ksqlDB also supports 3 pseudo columns_, available on every row: `ROWTIME`, which represents the time of the event, as well as `ROWPARTITION` and `ROWOFFSET`, which represent the partition and offset of the source record, respectively. In addition, windowed sources have `WINDOWSTART` and `WINDOWEND` system columns.

## Stream processing

In ksqlDB, you manipulate events by deriving new collections from existing ones and describing the changes between them. When a collection is updated with a new event, ksqlDB updates the collections that are derived from it in real-time. This rich form of computing is known formally as stream processing, because it creates programs that operate continually over unbounded streams of events, ad infinitum. These processes stop only when you explicitly terminate them.

The general pattern for stream processing in ksqlDB is to create a new collection by using the SELECT statement on an existing collection. The result of the inner SELECT feeds into the outer declared collection. You don't need to declare a schema when deriving a new collection, because ksqlDB infers the column names and types from the inner SELECT statement. The value of the ROWTIME pseudo column defines the timestamp of the record written to Kafka, and the value of the ROWPARTITION and ROWOFFSET pseudo columns define the partition and offset of the source record, respectively. The value of system columns can not be set in the SELECT.

## Stream/Table Duality

Streams and tables are closely related. A stream is a sequence of events that you can derive a table from. For example, a sequence of credit scores for a loan applicant can change over time. The sequence of credit scores is a stream. But this stream can be interpreted as a table to describe the applicant's current credit score.

Conversely, the table that represents current credit scores is really two things: the current credit scores, and also the sequence of changes to the credit scores for each applicant. This is a profound realization, and much has been written on this stream/table duality. For more information, see Streams and Tables: Two Sides of the Same Coin.

Traditional databases have redo logs, but subscribing to changes can be cumbersome. Redo logs have much shorter retention than the Kafka changelog topic. A fully compacted Kafka changelog topic is the same as a database snapshot. Efficient queries evaluate just the changes.

In ksqlDB, a table can be materialized into a view or not. If a table is created directly on top of a Kafka topic, it's not materialized. **Non-materialized tables can't be queried, because they would be highly inefficient**. On the other hand, if a table is derived from another collection, ksqlDB materializes its results, and you can make queries against it.

## Queries

There are three kinds of queries in ksqlDB: persistent, push, and pull. Each gives you different ways to work with rows of events.

### Persistent

Persistent queries are server-side queries that run indefinitely processing rows of events. You issue persistent queries by deriving new streams and new tables from existing streams or tables

### Push

A push query is a form of query issued by a client that subscribes to a result as it changes in real-time.

A good example of a push query is subscribing to a particular user's geographic location. The query requests the map coordinates, and because it's a push query, any change to the location is "pushed" over a long-lived connection to the client as soon as it occurs. This is useful for building programmatically controlled microservices, real-time apps, or any sort of asynchronous control flow.

Push queries are expressed using a SQL-like language. They can be used to query either streams or tables for a particular key. Also, push queries aren't limited to key look-ups. They support a full set of SQL, including filters, selects, group bys, partition bys, and joins.
 
Push queries enable you to query a stream or materialized table with a subscription to the results. You can subscribe to the output of any query, including one that returns a stream. A push query emits refinements to a stream or materialized table, which enables reacting to new information in real-time. They’re a good fit for asynchronous application flows. 
 
_Execute a push query by sending an HTTP request to the ksqlDB REST API_, and the API sends back a chunked response of indefinite length.

The result of a push query isn't persisted to a backing Kafka topic. If you need to persist the result of a query to a Kafka topic, use a CREATE TABLE AS SELECT or CREATE STREAM AS SELECT statement.

### Pull

A pull query is a form of query issued by a client that retrieves a result as of "now", like a query against a traditional RDBMS. 

As a dual to the push query example, a pull query for a geographic location would ask for the current map coordinates of a particular user. Because it's a pull query, it returns immediately with a finite result and closes its connection. This is ideal for rendering a user interface once, at page load time. It's generally a good fit for any sort of synchronous control flow.

Pull queries enable you to fetch the current state of a materialized view. Because materialized views are incrementally updated as new events arrive, pull queries run with predictably low latency. They're a great match for request/response flows. For asynchronous application flows, see Push Query. Pull queries are expressed using a strict subset of ANSI SQL.

Execute a pull query by sending an HTTP request to the ksqlDB REST API, and the API responds with a single response.

## Joins

You can join streams and tables in these ways:

  *  Join multiple streams to create a new stream.
  *  Join multiple tables to create a new table.
  *  Join multiple streams and tables to create a new stream.

### Co-partitioned data

_For most joins, input data must be co-partitioned when joining_. This ensures that input records with the same key, from both sides of the join, are delivered to the same stream task during processing. For some cases, ksqlDB may repartition the data automatically, if required. But there are some cases for which it's your responsibility to ensure data co-partitioning when joining. The only join that does not require co-partitioning is a foreign-key table-table join. For more information, see Partition Data to Enable Joins.

When you use ksqlDB to join streaming data, you must ensure that your streams and tables are co-partitioned, _which means that input records on both sides of the join have the same configuration settings for partitions_. The only exception is foreign-key table-table joins, which do not have any co-partitioning requirement.

## Serialization/Deserialization

* https://docs.ksqldb.io/en/latest/reference/serialization

### Single fied wrapping

* https://docs.ksqldb.io/en/latest/reference/serialization/#single-field-unwrapping


*ksqlDB assumes that any single key is unwrapped*, which means that it's not contained in an outer record or object. *Conversely, ksqlDB assumes that any key with multiple columns* (for example, `CREATE STREAM x (K1 INT KEY, K2 INT KEY, C1 INT))` *is wrapped*, which means that it is a record with each column as a field within the key.
 
**To declare a single-column key that's wrapped**, specify a `STRUCT` type with a single column. For example, `K STRUCT<F1 INT> KEY`. See the next two sections on single values for more information about wrapped and unwrapped data.

If a statement doesn't set the value wrapping explicitly, ksqlDB uses the system default, which is defined by `ksql.persistence.wrap.single.values`. You can change the system default, if the format supports it. For more information, see [ksql.persistence.wrap.single.values](https://docs.ksqldb.io/reference/server-configuration#ksqlpersistencewrapsinglevalues).


* [SQL Changes and Key Columns in ksqlDB 0.10 Explained](https://www.confluent.io/blog/ksqldb-0-10-updates-key-columns/)

Declaring a column as a `KEY` column indicates that the data for the column is loaded from the Kafka record’s key, not its value. It does not infer any unique or non-null attributes of the data itself, as a primary key would.

> It’s worth saying this for a second time, as this is an important point about key columns: Declaring a column as a `KEY` or `PRIMARY KEY` column indicates that the data for the column _is loaded from the Kafka record’s key, not its value_.

_As you can see, having correctly partitioned data is important when building aggregates_. It’s also important when performing joins. **For joins to work, not only must your data be correctly partitioned but also both sides of the join must have a matching number of partitions**. This is necessary to ensure rows from both sides of a join, with the same key, go to the same processing node to facilitate the join. As with `GROUP BY`, ksqlDB may internally repartition the data to enable a join. This co-partitioning is key (sorry, bad pun), to getting your join to work. You can read more about it in the [Partitioning Requirements](https://docs.ksqldb.io/en/latest/developer-guide/joins/partition-data/#co-partitioning-requirements) page for joins on the [ksqlDB](https://ksqldb.io/) microsite.

To recap, there are two types of key columns:

*  **Tables have primary keys, which map to the data in the key of the Kafka record**. _A primary key is unique and must be non-null_. 
* **Streams have key columns, which also map to the data in the key of a Kafka record** _but don’t infer any unique or non-null attributes of the data itself_. 

Having correctly keyed data can save ksqlDB from performing additional steps, reducing latency and resource utilisation.

```sql
CREATE TABLE users (
    rowkey BIGINT PRIMARY KEY
    id BIGINT
    name VARCHAR
  ) WITH (
    KAFKA_TOPIC='users',
    VALUE_FORMAT='json',
    KEY='ID' -- ID column is an alias for ROWKEY column
  );
  ```

  Implicit `ROWKEY` columns and the `WITH(key='ID')` syntax have caused a lot of confusion in the community. The `WITH(key='ID')` syntax is something _we’ve been working to remove_, since it’s not the most appropriate semantic abstraction to describe how to model the data. 

```sql
-- new syntax for creating a table:
CREATE TABLE users (
    id BIGINT PRIMARY KEY,
    name VARCHAR
) WITH (
    KAFKA_TOPIC='users',
    VALUE_FORMAT='json'
);
```

As discussed above, streams don’t logically require a key column. To support this, _ksqlDB no longer adds an implicit key column if you don’t declare one_: No key column declared means your stream has no key column. Simple. Any data in the key will be ignored. Of course, this may mean you’ll pay the cost of repartitions should you later need to join or aggregate the stream. So it pays to think carefully about whether you should have a key column or not.

If you do want a key column for your stream, then you must make sure that the key part of the Kafka message is appropriately populated. Just as with the `WITH KEY `confusion before, _you cannot pick an arbitrary field from the value part of the message and denote this as the key_.

> Notice that the value has no `purchaseId` field this time, because the value of `purchaseId` is stored in the key.

What about tables? Can you have keyless tables? **No**, the key is needed to support the insert, update, and delete semantics of rows in the table.

Grouping by anything else adds a new column, with a system-generated column name. For example, the following has a key column called `KSQL_COL_0`.

ksqlDB 0.10 now requires you to include the full set of columns, including the key column, in the projection when creating a materialized view.

**If you actually need a copy of the key column in the value**, such as to keep the output the same as a previous version of ksqlDB, then you’ll need a way of letting ksqlDB know that’s your intent. The new `AS_VALUE` function allows you to express that intent:

```sql
CREATE TABLE users AS
   SELECT 
      LCASE(address->country) as rowkey, 
      AS_VALUE(LCASE(address->country)) as country
      COUNT() as count  
    FROM users
    GROUP BY LCASE(address->country);
```

The changelog topic’s Kafka record will then contain a copy of the key in the value: `"narnia" : {"COUNTRY": "narnia", "COUNT": 101}`.

#### Multi-column aggregations

```sql
-- This uses the arbitrary character § to separate fields. Use a different character
-- if this character appears in your data. (Yes, this is a hack).
CREATE TABLE products_by_sub_cat AS
    SELECT 
      CAST(categoryId AS STRING) 
          + '§' 
          + CAST(subCategoryId AS STRING) AS compositeKey
      SUM(quantity) as totalQty  
    FROM purchases
    GROUP BY 
      CAST(categoryId AS STRING)
      + '§'
      + CAST(subCategoryId AS STRING);

SELECT 
     SPLIT(compositeKey,'§')[0] AS categoryId, 
     SPLIT(compositeKey,'§')[1] AS subCategoryId, 
     totalQty
FROM products_by_sub_cat
EMIT CHANGES;
```
#### Repartitions

The key column of a statement including a `PARTITION BY` clause is resolved in much the same way as for `GROUP BY`.

```sql
CREATE STREAM purchases_by_user AS
    SELECT *
    FROM purchases
    PARTITION BY userId;
```

The above results in a `products_by_user` stream with the `userId` column as the key column. If the `purchases` stream has an existing key column, it will become a value column in `purchases_by_user`. To put this another way, the query is selecting all columns from the source with its `SELECT *`, so the result will contain all of the source’s columns. The only change is that the `userId` column is now the key column.

#### Joins

The final area affected are JOINs. The key column in the result of any join, other than a `FULL OUTER` join, will be the leftmost join column. If the leftmost join expression is not a simple column reference (i.e., anything other than a single column name), then the next leftmost join expression is evaluated. If no join expressions are simple column references, or if the join is a `FULL OUTER` join, then ksqlDB introduces a synthetic key column.

# Usage

## Creating streams

### SchemaRegistry AVRO 
* [Schema Inference - ksqlDB Documentation](https://docs.ksqldb.io/en/latest/operate-and-deploy/schema-registry-integration/)

```sh
ksql> create stream test with (kafka_topic='mysql-headless.latest.partners-test._MigrationHistory', key_format='avro', value_format='avro');

 Message        
----------------
 Stream created 
----------------

SELECT * FROM test WHERE ROWTIME >= '2019-11-20T00:00:00' EMIT CHANGES;

-- Timestamps can also be interpreted as epoch milliseconds:
SELECT * FROM test WHERE ROWTIME >= 1574208000000 EMIT CHANGES;
```

### Constructing STRUCT

* https://github.com/confluentinc/ksql/issues/2147
* https://github.com/confluentinc/ksql/issues/3876
* https://github.com/confluentinc/ksql/blob/master/ksqldb-functional-tests/src/test/resources/query-validation-tests/create-struct.json

```sql
reate or replace stream release_playerstats_game_output
with (format='AVRO', partitions=1, kafka_topic = 'release.playerstats.Game')
as select 
		STRUCT(`id` := gameId) as game_key,
		gameId as `id`,
		id as `playerstats_id`,
		game_user_action_id as `game_user_action_id`,
		game_data_id as `game_data_id`,
		tableId as `table_id`,
		dealerId as `dealer_id`,
		gameType as `game_type`,
		startedAt as `start_ts`,
		finishedAt as `finish_ts`,
		gameResult as `result`,
		status as `status`,
		0 as `issue`,
		__op as `__op`,
		__source_snapshot as `__source_snapshot`,
		__ts_ms as `__ts_ms`
	from _release_playerstats_gameData
	partition by STRUCT(`id` := gameId)
emit changes;
```

## Deployment script

* [A bash script to deploy ksqlDB queries automagically](https://rmoff.net/2021/04/01/a-bash-script-to-deploy-ksqldb-queries-automagically/)

# Errors

## KSQL max.request.size

* https://github.com/confluentinc/ksql/issues/5026

```
KSQL_OPTS: "-Dmax.request.size=30000000"

или параметр ENV: KSQL_KSQL_MAX_REQUEST_SIZE
```