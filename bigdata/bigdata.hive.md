---
id: vo5jt04njf1sz3uqw62rwry
title: Hive
desc: ''
updated: 1651729056183
created: 1619782094187
---

[Apache Hive](https://hive.apache.org/): data warehouse software facilitates reading, writing, and managing large datasets residing in distributed storage using SQL. Structure can be projected onto data already in storage. A command line tool and JDBC driver are provided to connect users to Hive.

## Hive on Ozone

[Benchmarking Ozone: Cloudera’s next generation Storage for CDP](https://blog.cloudera.com/benchmarking-ozone-clouderas-next-generation-storage-for-cdp/)

> In over 70% of the cases, queries run faster when the data resides in Ozone versus HDFS. The community effort put into stabilization and performance improvements seems to be paying off.  But there is still room to grow further.

## ORC vs Parquet

[ORC vs Parquet in CDP](https://docs.cloudera.com/runtime/7.2.6/using-hiveql/topics/hive-orc-parquet-compare.html)

The following table compares Hive and Impala support for ORC and Parquet in CDP Public Cloud and CDP Private Cloud Base. The Runtime Services column shows the supported services:

* Hive-on-Tez
* HiveLLAP, supported on CDP Public Cloud only
* Hive metastore (HMS)
* Impala
* Spark
* JDBC


| Capability | 	Data Warehouse | ORC | Parquet | Runtime Services |
|------------|-----------------|:---:|:-------:|------------------|
|Read non-transactional data|	Apache Hive| ✓ | 	✓ |(Hive-on-Tez \| HiveLLAP) & HMS|
|Read non-transactional data|	Apache Impala| 	✓ |	✓ |	Impala & HMS |
|**Full ACID transactions**|Apache Hive| 	✓ |		| (Hive-on-Tez \| HiveLLAP) & HMS|
|Read Insert-only transactions| 	Apache Impala |	✓ |	✓| 	Impala & HMS|
|Hive Warehouse Connector reads |	Apache Hive |	✓| 	✓ |	((Hive-on-Tez & JDBC) \| HiveLLAP) & Spark & HMS|
|**Hive Warehouse Connector writes** |	Apache Hive | 	✓ |		| ((Hive-on-Tez & JDBC) \| HiveLLAP) & Spark & HMS|
|Column index |	Apache Hive |	✓ |	✓ |	(Hive-on-Tez \| HiveLLAP) & HMS|
|**Column index** |	Apache Impala |		✓ | |	Impala & HMS |
|**CBO uses column metadata** |	Apache Hive |	✓ |		| (Hive-on-Tez \| HiveLLAP) & HMS|
|**Recommended format** |	Apache Hive |	✓| 		| (Hive-on-Tez \| HiveLLAP) & HMS |
|**Recommended format** |	Apache Impala |		✓ |	| Impala & HMS|
|Vectorized reader |	Apache Hive |	✓| 	✓| 	(Hive-on-Tez \| HiveLLAP) & HMS|
|Read complex types| 	Apache Impala | 	✓| 	✓| 	Impala & HMS |
|Read/write complex types |	Apache Hive |	✓ |	✓| 	(Hive-on-Tez \| HiveLLAP) & HMS|

![qownnotes-media-yP8891](../../media/qownnotes-media-yP8891-246784104.png)

### RecordWriter

<https://hive.apache.org/javadocs/r3.0.0/api/org/apache/hive/streaming/HiveStreamingConnection.html>

## PyHive

<https://github.com/dropbox/PyHive>

## Garbage Collector


* https://community.cloudera.com/t5/Community-Articles/How-to-change-Hiveserver2-GC-from-Parallel-GC-to-CMS/ta-p/248319
* https://aws.amazon.com/ru/premiumsupport/knowledge-center/emr-hive-outofmemoryerror-heap-space/
* https://stackoverflow.com/questions/35824572/g1gc-how-to-use-all-free-memory


## Hive Streaming

### Streaming Data Ingest V2

[Streaming Data Ingest V2](https://cwiki.apache.org/confluence/display/Hive/Streaming+Data+Ingest+V2)

Regardless of what values are set in hive-site.xml or custom HiveConf, the API will internally override some settings in it to ensure correct streaming behavior. The below is the list of settings that are overridden:

* hive.txn.manager = org.apache.hadoop.hive.ql.lockmgr.DbTxnManager
* hive.support.concurrency = true
* hive.metastore.execute.setugi = true
* hive.exec.dynamic.partition.mode = nonstrict
* hive.exec.orc.delta.streaming.optimizations.enabled = true
* hive.metastore.client.cache.enabled = false


### [Implementing a real-time Hive Streaming example](https://community.cloudera.com/t5/Community-Articles/Implementing-a-real-time-Hive-Streaming-example/ta-p/246972)

> `Created on ‎08-04-2016 10:37 PM`
>
> [HiveStreamingExamples on GitHub](https://github.com/mfjohnson/HiveStreamingExamples)

The Hive Streaming API enables the near real-time data ingestion into Hive. This two part posting reviews some of the design decisions necessary to produce a health Hive Streaming ingest process from which you can in a near real-time execute queries on the ingested data.

*Hive Streaming is able to work in a near real-time basis through the creation of a new ‘delta’ file on a bucketed Hive table with every table commit.*

#### Hive Streaming Compaction

The first thing to keep in mind is that there are two forms of Compaction; ‘minor’ and ‘major’.

A **‘minor’ compaction** will just consolidate the delta files. This approach does not have to worry about consolidating all of the delta files along with a large set of base bucket files and is thus the least disruptive to the system resources.

A **‘major’ compaction** consolidates all of the delta files just like the ‘minor’ compaction and in addition it consolidates the delta files with the base to produce a very clean physical layout for the hive table. However, major compactions can take minutes to hours and can consume a lot of disk, network, memory and CPU resources, so they should be invoked carefully.

The primary compaction configuration triggers to review when implementing or tuning your compaction processes are:
* `hive.compactor.initiator.on`
* `hive.compactor.cleaner.run.interval`
* `hive.compactor.delta.num.threshold` - Number of delta directories in a table or partition that will trigger a minor compaction.
* `hive.compactor.delta.pct.threshold` - Percentage (fractional) size of the delta files relative to the base that will trigger a major compaction. 1 = 100%, so the default 0.1 = 10%.
* `hive.compactor.abortedtxn.threshold` - Number of aborted transactions involving a given table or partition that will trigger a major compaction

```console
hive> alter table acidtest compact 'major';
Compaction enqueued.
OK
Time taken: 0.037 seconds
hive> show compactions;
OK
Database Table Partition Type State Worker Start Time
default acidtest NULL MAJOR working server2.hdp-26 1459100244000
Time taken: 0.019 seconds, Fetched: 2 row(s)
hive> show compactions;
OK
Database Table Partition Type State Worker Start Time
Time taken: 0.016 seconds, Fetched: 1 row(s)
hive>;
```


### [Integrating Apache Hive with Kafka, Spark, and BI](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.4/integrating-hive/content/hive-kafka-integration.html)

See also: [Hive Ingession](../Stream%20Processing%2FKafka.md#Integration-with-Hive

## Ошибки запуска Hive2

### Hive: Exception in thread "main" java.lang.NoSuchMethodError: com.google.common.base.Preconditions.checkArgument(ZLjava/lang/String;Ljava/lang/Object;)

Оказалось надо скопировать из дистрибутива Hadoop библиотеки guava, curator (при настройке Hive High Availability)

## Hive Query Language (HQL)

[LanguageManual DDL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL)


### Table partitions

[Hive – How to Show All Partitions of a Table](https://sparkbyexamples.com/apache-hive/hive-show-all-table-partitions/)

```sql
SHOW PARTITIONS [db_name.]table_name 
[PARTITION(partition_spec)] 
[WHERE where_condition] 
[ORDER BY col_list] [LIMIT rows];
```

```sql
DESCRIBE FORMATTED zipcodes PARTITION(state='PR');
SHOW TABLE EXTENDED LIKE zipcodes PARTITION(state='PR');
```

```sql
SHOW PARTITIONS LOG_TABLE LIMIT 10;
SHOW PARTITIONS LOG_TABLE PARTITION(LOG_DATE='2009-04-02') LIMIT 5;
SHOW PARTITIONS LOG_TABLE PARTITION(LOG_DATE='2008-06-03') WHERE hr >= 5 DESC LIMIT 5;
SHOW PARTITIONS LOG_TABLE PARTITION(LOG_DATE='2007-04-02') ORDER BY hr DESC LIMIT 5;
SHOW PARTITIONS LOG_TABLE WHERE hr >= 10 AND LOG_DATE='2010-03-03' ORDER BY hr DESC LIMIT 5;
```
