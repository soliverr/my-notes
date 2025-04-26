---
id: aq2a6j0975zoxpoxy4ahg98
title: Delta Lake
desc: Delta Lake - Spark table level ACID
updated: 1679673354587
created: 1628076541150
---

[Delta Lake](https://delta.io/) - is an open-source storage layer that brings ACID transactions to Apache Spark™ and big data workloads.

Delta Lake provides ACID transactions, scalable metadata handling, and unifies streaming and batch data processing. Delta Lake runs on top of your existing data lake and is fully compatible with Apache Spark APIs.

Specifically, Delta Lake offers:

* ACID transactions on Spark: Serializable isolation levels ensure that readers never see inconsistent data.
* Scalable metadata handling: Leverages Spark’s distributed processing power to handle all the metadata for petabyte-scale tables with billions of files at ease.
* Streaming and batch unification: A table in Delta Lake is a batch table as well as a streaming source and sink. Streaming data ingest, batch historic backfill, interactive queries all just work out of the box.
* Schema enforcement: Automatically handles schema variations to prevent insertion of bad records during ingestion.
* Time travel: Data versioning enables rollbacks, full historical audit trails, and reproducible  learning experiments.
* Upserts and deletes: Supports merge, update and delete operations to enable complex use cases like change-data-capture, slowly-changing-dimension (SCD) operations, streaming upserts, and so on.

* [Databriks DeltaLake](https://docs.databricks.com/delta/)
* [Delta Lake on GitHub](https://github.com/delta-io/delta)
* [Delta Lake Documentation](https://docs.delta.io/latest/index.html)
* [Delta Lake and Delta Engine guide](https://docs.microsoft.com/en-us/azure/databricks/delta/)
* [What is Delta Lake](https://docs.microsoft.com/en-us/azure/synapse-analytics/spark/apache-spark-what-is-delta-lake)
* [Diving Into Delta Lake: DML Internals](https://databricks.com/blog/2020/09/29/diving-into-delta-lake-dml-internals-update-delete-merge.html)
* [Simplifying Change Data Capture with Databricks Delta](https://databricks.com/blog/2018/10/29/simplifying-change-data-capture-with-databricks-delta.html)
* [Delta Lake Series: Features](https://databricks.com/wp-content/uploads/2021/02/The-Delta-Lake-Series-features-012621.pdf)
* [DeltaLake Internals](https://github.com/japila-books/delta-lake-internals/)

## Connectors

https://github.com/delta-io/connectors

## Совместимость с версиями Spark

<https://docs.delta.io/latest/releases.html#compatibility-with-apache-spark>


Delta Lake version  | Apache Spark version
--------------------|---------------------
 1.0.x              | 3.1.x
 0.7.x and 0.8.x    | 3.0.x
 Below 0.7.0        | 2.4.2 - 2.4._latest_

## [Quick Start](https://docs.delta.io/latest/quick-start.html)

Текущая версия Delta Lake https://index.scala-lang.org/delta-io/delta

* [Local Databricks Development on Windows](https://pivotalbi.com/local-databricks-development-on-windows/)
* [Примеры операций над DeltaTable](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html)

> Похоже версия 1.0.0 ещё не стабильна в Spark 3.1

### Параметры Spark

Enable Delta tables:

```
    spark.sql.extensions: 'io.delta.sql.DeltaSparkSessionExtension'
    spark.sql.catalog.spark_catalog: 'org.apache.spark.sql.delta.catalog.DeltaCatalog'
    spark.databricks.delta.vacuum.parallelDelete.enabled: 'true'
    spark.databricks.delta.retentionDurationCheck.enabled: 'true'
```

There are lots of SparkSession configuration properties that are related to Delta. Just to name a few:
```
spark.databricks.delta.optimize.minFileSize
spark.databricks.delta.optimize.maxFileSize
spark.databricks.delta.optimize.maxThreads
spark.databricks.delta.autoCompact.maxFileSize
spark.databricks.delta.autoCompact.minNumFiles
spark.databricks.delta.commitInfo.userMetadata
spark.databricks.delta.merge.repartitionBeforeWrite.enabled
spark.databricks.delta.retentionDurationCheck.enabled
spark.databricks.delta.vacuum.parallelDelete.enabled
```

Другие параметры можно смотреть вот тут:

https://github.com/delta-io/delta/blob/a109bab61407e7c02fa551291f81cf12a58f1f6a/core/src/main/scala/org/apache/spark/sql/delta/sources/DeltaSQLConf.scala#L28

Префикс всех параметров: **`spark.databricks.delta.`**.

Параметры работы команды оптимизации `OPTIMIZE`:

* `optimize.minFileSize`: Files which are smaller than this threshold (in bytes) will be grouped together and rewritten as larger files by the OPTIMIZE command. `createWithDefault(1024 * 1024 * 1024)`
* `optimize.maxFileSize`: Target file size produced by the OPTIMIZE command. `createWithDefault(1024 * 1024 * 1024)`
* `optimize.maxThreads`: Maximum number of parallel jobs allowed in OPTIMIZE command. Increasing the maximum parallel jobs allows the OPTIMIZE command to run faster, but increases the job management on the Spark driver side. `createWithDefault(15)`
* `optimize.zorder.fastInterleaveBits.enabled`: When true, a faster version of the bit interleaving algorithm is used. `createWithDefault(false)`
* `optimize.zorder.checkStatsCollection.enabled`: When enabled, we will check if the column we're actually collecting stats on the columns we are z-ordering on. `createWithDefault(true)`

Оптимизация работы команды `MERGE`:

* `merge.optimizeInsertOnlyMerge.enabled`: If enabled, merge without any matched clause (i.e., insert-only merge) will be optimized by avoiding rewriting old files and just inserting new files. `createWithDefault(true)`
* `merge.repartitionBeforeWrite.enabled`: When enabled, merge will repartition the output by the table's partition columns before writing the files. `createWithDefault(true)`
* `merge.optimizeMatchedOnlyMerge.enabled`: If enabled, merge without 'when not matched' clause will be optimized to use a right outer join instead of a full outer join. `createWithDefault(true)`

### Создание таблицы

```console
$ spark-shell --packages io.delta:delta-core_2.12:0.8.0 --conf "spark.sql.extensions=io.delta.sql.DeltaSparkSessionExtension" --conf "spark.sql.catalog.spark_catalog=org.apache.spark.sql.delta.catalog.DeltaCatalog"

scala> val data = spark.range(0, 5)
data: org.apache.spark.sql.Dataset[Long] = [id: bigint]

scala> data.write.format("delta").save("/tmp/delta-table")
```

```console
$ ls -l /tmp/delta-table/
итого 16
drwxr-xr-x 1 oliver oliver  50 мар 17 17:02 _delta_log
-rw-r--r-- 1 oliver oliver 463 мар 17 17:02 part-00000-bcbe7f32-459c-4a4d-9286-674d016b1bca-c000.snappy.parquet
-rw-r--r-- 1 oliver oliver 463 мар 17 17:02 part-00001-baf05744-eaf0-4e90-b3d8-fe8a713f3f74-c000.snappy.parquet
-rw-r--r-- 1 oliver oliver 463 мар 17 17:02 part-00002-ed1558b5-1f17-4064-b2eb-00ec7dfc0c46-c000.snappy.parquet
-rw-r--r-- 1 oliver oliver 471 мар 17 17:02 part-00003-caadd78b-9c17-4538-a433-282625a1a1cc-c000.snappy.parquet

$ ls -l /tmp/delta-table/_delta_log/
итого 4
-rw-r--r-- 1 oliver oliver 1262 мар 17 17:02 00000000000000000000.json
```

`$ cat /tmp/delta-table/_delta_log/00000000000000000000.json`:

```json
{"commitInfo":{"timestamp":1615982568274,"operation":"WRITE","operationParameters":{"mode":"ErrorIfExists","partitionBy":"[]"},"isBlindAppend":true,"operationMetrics":{"numFiles":"4","numOutputBytes":"1860","numOutputRows":"5"}}}
{"protocol":{"minReaderVersion":1,"minWriterVersion":2}}
{"metaData":{"id":"d411c4a4-615b-4944-892a-d6c1b013966e","format":{"provider":"parquet","options":{}},"schemaString":"{\"type\":\"struct\",\"fields\":[{\"name\":\"id\",\"type\":\"long\",\"nullable\":true,\"metadata\":{}}]}","partitionColumns":[],"configuration":{},"createdTime":1615982566838}}
{"add":{"path":"part-00000-bcbe7f32-459c-4a4d-9286-674d016b1bca-c000.snappy.parquet","partitionValues":{},"size":463,"modificationTime":1615982568188,"dataChange":true}}
{"add":{"path":"part-00001-baf05744-eaf0-4e90-b3d8-fe8a713f3f74-c000.snappy.parquet","partitionValues":{},"size":463,"modificationTime":1615982568188,"dataChange":true}}
{"add":{"path":"part-00002-ed1558b5-1f17-4064-b2eb-00ec7dfc0c46-c000.snappy.parquet","partitionValues":{},"size":463,"modificationTime":1615982568188,"dataChange":true}}
{"add":{"path":"part-00003-caadd78b-9c17-4538-a433-282625a1a1cc-c000.snappy.parquet","partitionValues":{},"size":471,"modificationTime":1615982568188,"dataChange":true}}
```

```scala
df.write.format("delta").saveAsTable("events")      // create table in the metastore

df.write.format("delta").save("/delta/events")      // create table by path

df.writeTo("delta.`/delta/events`").using("delta").partitionedBy("date").createOrReplace() // create table by path

df.writeTo("events").using("delta").partitionedBy("date").createOrReplace()                // create table in the metastore

df.write.format("delta").partitionBy("date").saveAsTable("events")      // create table in the metastore

df.write.format("delta").partitionBy("date").save("/delta/events")      // create table by path
```

### Чтение таблицы

```console
scala> val df = spark.read.format("delta").load("/tmp/delta-table")
df: org.apache.spark.sql.DataFrame = [id: bigint]

scala> df.show()

scala> df.printSchema
root
 |-- id: long (nullable = true)
```

### Обновление данных

#### Добавление

```scala
scala> val data = spark.range(5, 10)
scala> data.write.format("delta").mode("append").save("/tmp/delta-table")
```

```console
$ ls -l /tmp/delta-table/
итого 32
drwxr-xr-x 1 oliver oliver 100 мар 17 17:11 _delta_log
-rw-r--r-- 1 oliver oliver 463 мар 17 17:11 part-00000-1299036c-11d3-4d88-b4aa-b9cabbfa6458-c000.snappy.parquet
-rw-r--r-- 1 oliver oliver 463 мар 17 17:02 part-00000-bcbe7f32-459c-4a4d-9286-674d016b1bca-c000.snappy.parquet
-rw-r--r-- 1 oliver oliver 463 мар 17 17:11 part-00001-52e78eca-3a0a-4149-9ace-849434838054-c000.snappy.parquet
-rw-r--r-- 1 oliver oliver 463 мар 17 17:02 part-00001-baf05744-eaf0-4e90-b3d8-fe8a713f3f74-c000.snappy.parquet
-rw-r--r-- 1 oliver oliver 463 мар 17 17:11 part-00002-6edf35ee-3157-4ebe-850b-d71cfb44dc67-c000.snappy.parquet
-rw-r--r-- 1 oliver oliver 463 мар 17 17:02 part-00002-ed1558b5-1f17-4064-b2eb-00ec7dfc0c46-c000.snappy.parquet
-rw-r--r-- 1 oliver oliver 471 мар 17 17:11 part-00003-7b6f5f75-c614-4d39-9f9c-509fd1b95e70-c000.snappy.parquet
-rw-r--r-- 1 oliver oliver 471 мар 17 17:02 part-00003-caadd78b-9c17-4538-a433-282625a1a1cc-c000.snappy.parquet

$ ls -l /tmp/delta-table/_delta_log/
итого 8
-rw-r--r-- 1 oliver oliver 1262 мар 17 17:02 00000000000000000000.json
-rw-r--r-- 1 oliver oliver  919 мар 17 17:11 00000000000000000001.json
```

#### Перезапись

```scala
spark> val data = spark.range(10, 15)
spark> data.write.format("delta").mode("overwrite").save("/tmp/delta-table")
```

```console
$ ls -lt /tmp/delta-table/
итого 48
drwxr-xr-x 1 oliver oliver 150 мар 17 17:17 _delta_log
-rw-r--r-- 1 oliver oliver 463 мар 17 17:17 part-00000-718357ea-eda7-4cc0-b659-a1ccd814a214-c000.snappy.parquet
-rw-r--r-- 1 oliver oliver 463 мар 17 17:17 part-00002-efff3159-f9ca-4c37-ae3d-7119646f3e93-c000.snappy.parquet
-rw-r--r-- 1 oliver oliver 471 мар 17 17:17 part-00003-23408586-c37c-4d61-95a7-f09504ae1d06-c000.snappy.parquet
-rw-r--r-- 1 oliver oliver 463 мар 17 17:17 part-00001-67125bd0-0bde-4e57-90d9-ba5fd978de28-c000.snappy.parquet
-rw-r--r-- 1 oliver oliver 463 мар 17 17:11 part-00002-6edf35ee-3157-4ebe-850b-d71cfb44dc67-c000.snappy.parquet
-rw-r--r-- 1 oliver oliver 463 мар 17 17:11 part-00000-1299036c-11d3-4d88-b4aa-b9cabbfa6458-c000.snappy.parquet
-rw-r--r-- 1 oliver oliver 471 мар 17 17:11 part-00003-7b6f5f75-c614-4d39-9f9c-509fd1b95e70-c000.snappy.parquet
-rw-r--r-- 1 oliver oliver 463 мар 17 17:11 part-00001-52e78eca-3a0a-4149-9ace-849434838054-c000.snappy.parquet
-rw-r--r-- 1 oliver oliver 463 мар 17 17:02 part-00000-bcbe7f32-459c-4a4d-9286-674d016b1bca-c000.snappy.parquet
-rw-r--r-- 1 oliver oliver 463 мар 17 17:02 part-00001-baf05744-eaf0-4e90-b3d8-fe8a713f3f74-c000.snappy.parquet
-rw-r--r-- 1 oliver oliver 463 мар 17 17:02 part-00002-ed1558b5-1f17-4064-b2eb-00ec7dfc0c46-c000.snappy.parquet
-rw-r--r-- 1 oliver oliver 471 мар 17 17:02 part-00003-caadd78b-9c17-4538-a433-282625a1a1cc-c000.snappy.parquet

$ ls -l /tmp/delta-table/_delta_log/
итого 12
-rw-r--r-- 1 oliver oliver 1262 мар 17 17:02 00000000000000000000.json
-rw-r--r-- 1 oliver oliver  919 мар 17 17:11 00000000000000000001.json
-rw-r--r-- 1 oliver oliver 2539 мар 17 17:17 00000000000000000002.json
```

#### Обновление по условию

```scala
scala> import io.delta.tables._
scala> import org.apache.spark.sql.functions._

scala> val deltaTable = DeltaTable.forPath("/tmp/delta-table")
deltaTable: io.delta.tables.DeltaTable = io.delta.tables.DeltaTable@745f5a40

// Update every even value by adding 100 to it
scala> deltaTable.update(condition = expr("id % 2 == 0"),set = Map("id" -> expr("id + 100")))
```

Пока не работает в Spark 3.1.1

```console
java.lang.NoSuchMethodError: 'void org.apache.spark.sql.catalyst.expressions.Alias.<init>(org.apache.spark.sql.catalyst.expressions.Expression, java.lang.String, org.apache.spark.sql.catalyst
.expressions.ExprId, scala.collection.Seq, scala.Option)'

// Это из-за ошибки в параметрах
org.apache.spark.sql.AnalysisException: This Delta operation requires the SparkSession to be configured with the
DeltaSparkSessionExtension and the DeltaCatalog. Please set the necessary
configurations when creating the SparkSession as shown below.

  SparkSession.builder()
    .option("spark.sql.extensions", "io.delta.sql.DeltaSparkSessionExtension")
    .option("spark.sql.catalog.spark_catalog", "org.apache.spark.sql.delta.catalog.DeltaCatalog")
    ...
    .build()
...
Caused by: java.lang.UnsupportedOperationException: UPDATE TABLE is not supported temporarily.
```

Оказалось, что пока поддерживается версия Spark 3.0.x https://github.com/delta-io/delta/issues/594 Как раз доделывают поддержку Spark 3.1.x


```scala
// Delete every even value
scala> deltaTable.delete(condition = expr("id % 2 == 0"))

scala> deltaTable.toDF.show
```

Это работает.

```scala
// Upsert (merge) new data
scala> val newData = spark.range(0, 20).as("newData").toDF

scala> deltaTable.as("oldData").merge(newData,"oldData.id = newData.id").whenMatched.update(Map("id" -> col("newData.id"))).whenNotMatched.insert(Map("id" -> col("newData.id"))).execute()

deltaTable.toDF.show()
```

То же не работает с той же ошибкой.


Для Spark 3.0.2 работает:

```scala
scala> deltaTable.update(condition = expr("id % 2 == 0"),set = Map("id" -> expr("id + 100")))
scala> deltaTable.toDF.orderBy('id).show

scala> deltaTable.delete(condition = expr("id % 2 == 0"))
scala> deltaTable.toDF.orderBy("id").show
```

## Optimization

* https://docs.delta.io/2.0.0/optimizations-oss.html#optimizations

### Z-Ordering

https://docs.delta.io/2.0.0/optimizations-oss.html#z-ordering-multi-dimensional-clustering


### Сжатие таблицы

* <https://docs.delta.io/latest/best-practices.html#compact-files>
* <https://mungingdata.com/delta-lake/compact-small-files/>

#### COMPACT FILES

Можно сжать файлы - уменьшить число файлов в файловой системе. Так как используется режим `dataChange = false` необходимо остановить запись в таблицу!

```scala
val path = "..."
val numFiles = 16

spark.read
 .format("delta")
 .load(path)
 .repartition(numFiles)
 .write
 .option("dataChange", "false")
 .format("delta")
 .mode("overwrite")
 .save(path)
```

После этого необходимо выполнить VACUUM.

Пример:

```sh

$ hdfs dfs -ls /warehouse/cdi/data/email | wc -l
4154

$ spark-shell --conf spark.databricks.delta.retentionDurationCheck.enabled=false

scala> val path = "/warehouse/cdi/data/email"
path: String = /warehouse/cdi/data/email

scala> val numFiles = 1
numFiles: Int = 1

scala> spark.read.format("delta").load(path).repartition(numFiles).write.option("dataChange", "false").format("delta").mode("overwrite").save(path)

scala> val deltaTable = DeltaTable.forPath(spark, path)

scala> deltaTable.vacuum(0.000001)

$ hdfs dfs -ls /warehouse/cdi/data/email | wc -l
3

$ hdfs dfs -ls /warehouse/cdi/data/email 
Found 2 items
drwxr-xr-x   - hive supergroup          0 2021-08-04 14:41 /warehouse/cdi/data/email/_delta_log
-rw-r--r--   3 hive supergroup 2824110969 2021-08-04 14:41 /warehouse/cdi/data/email/part-00000-446d7e15-0700-4a8c-b3c5-30a3650757b7-c000.snappy.parquet
```

#### VACUUM

https://docs.delta.io/latest/delta-utility.html#-delta-vacuum


* `vacuum` deletes only data files, not log files. Log files are deleted automatically and asynchronously after checkpoint operations. The default retention period of log files is 30 days, configurable through the delta.logRetentionDuration property which you set with the ALTER TABLE SET TBLPROPERTIES SQL method. See Table properties.
* The ability to time travel back to a version older than the retention period is lost after running vacuum.


When using VACUUM, to configure Spark to delete files in parallel (based on the number of shuffle partitions) set the session configuration "spark.databricks.delta.vacuum.parallelDelete.enabled" to "true" .

We do not recommend that you set a retention interval shorter than 7 days, because old snapshots and uncommitted files can still be in use by concurrent readers or writers to the table. If vacuum cleans up active files, concurrent readers can fail or, worse, tables can be corrupted when vacuum deletes files that have not yet been committed.

Delta Lake has a safety check to prevent you from running a dangerous vacuum command. If you are certain that there are no operations being performed on this table that take longer than the retention interval you plan to specify, you can turn off this safety check by setting the Apache Spark configuration property spark.databricks.delta.retentionDurationCheck.enabled to false. You must choose an interval that is longer than the longest running concurrent transaction and the longest period that any stream can lag behind the most recent update to the table.



```scala
spark> deltaTable.vacuum()

scala> deltaTable.vacuum(0.100)
java.lang.IllegalArgumentException: requirement failed: Are you sure you would like to vacuum files with such a low retention period? If you have
writers that are currently writing to this table, there is a risk that you may corrupt the
state of your Delta table.

If you are certain that there are no operations being performed on this table, such as
insert/upsert/delete/optimize, then you may turn off this check by setting:
spark.databricks.delta.retentionDurationCheck.enabled = false

If you are not sure, please use a value not less than "168 hours".
```

<https://mungingdata.com/delta-lake/vacuum-command/>

```console
$ spark-shell --packages io.delta:delta-core_2.12:0.8.0 --conf "spark.sql.extensions=io.delta.sql.DeltaSparkSessionExtension" --conf "spark.sql.catalog.spark_
catalog=org.apache.spark.sql.delta.catalog.DeltaCatalog" --conf "spark.databricks.delta.retentionDurationCheck.enabled=false"

scala> import io.delta.tables._

scala> val deltaTable2 = DeltaTable.forPath(spark, "/tmp/cdi-data/test_uniq_2")

scala> deltaTable2.vacuum(0.000001)
Deleted 23 files and directories in a total of 1 directories.
res3: org.apache.spark.sql.DataFrame = []
```


#### Сжатие секционированной таблицы

[Compacting partitioned Delta lakes](https://mungingdata.com/delta-lake/compact-small-files/):

```scala
    val table = "/some/path/data"
    val partition = "year = '2019'"
    val numFiles = 1
    spark.read
      .format("delta")
      .load(table)
      .where(partition)
      .repartition(numFiles)
      .write
      .format("delta")
      .mode("overwrite")
      .option("replaceWhere", partition)
      .save(table)
```

#### Проблемы со сжатием таблицы

##### Сжатие при активном потоке записи

Если есть активный поток записи и сжатие файлов происходит с параметром `dataChange=true`, то текущая операция в потоке записи ломается с ошибкой:

```
Files were added to the root of the table by a concurrent update. Please try the operation again.
Conflicting commit: {"timestamp":1630064076948,"operation":"WRITE","operationParameters":{"mode":Overwrite,"partitionBy":[]},"readVersion":36,"isBlindAppend":false,"operationMetrics":{"numFiles":"1","numOutputBytes":"650431627","numOutputRows":"3599994"}}
Refer to https://docs.delta.io/latest/concurrency-control.html for more details.
```

Более того, последующие операциии начинают сбоить, причину сбоя понять пока не получилось.

Установка параметра в `dataChange=false` в потоке сжатия решило проблему - при перезапуске операции в потоке записи, она отрабатывает нормально, последующие операции также отрабатывают наормально.

##### Число файлов после сжатия

После сжатия до 1 файла в логе Deltatable:

```
{"commitInfo":{"timestamp":1630064076948,"operation":"WRITE","operationParameters":{"mode":"Overwrite","partitionBy":"[]"},"readVersion":36,"isBlindAppend":false,"operationMetrics":{"numFiles":"1","numOutputByte
s":"650431627","numOutputRows":"3599994"}}}
{"add":{"path":"part-00000-ccecfe5c-0721-4a2e-b1dc-3f6e741608a6-c000.snappy.parquet","partitionValues":{},"size":650431627,"modificationTime":1630064076404,"dataChange":true}}
{"remove":{"path":"part-00018-dc79ac39-1626-4cb4-b548-6a2caf48b546-c000.snappy.parquet","deletionTimestamp":1630064076935,"dataChange":true,"extendedFileMetadata":true,"partitionValues":{},"size":117615}}
...
```

Однако последующая операция записи удаляет этот файл и создаёт 200 новых:

```
{"commitInfo":{"timestamp":1630064493629,"operation":"MERGE","operationParameters":{"predicate":"((1 = 1) AND (current.`hid_address` = new.`hid_address`))","matchedPredicates":"[{\"actionType\":\"update\"}]","no
tMatchedPredicates":"[{\"actionType\":\"insert\"}]"},"readVersion":37,"isBlindAppend":false,"operationMetrics":{"numTargetRowsCopied":"3599993","numTargetRowsDeleted":"0","numTargetFilesAdded":"200","executionTi
meMs":"0","numTargetRowsInserted":"99996","scanTimeMs":"24812","numTargetRowsUpdated":"1","numOutputRows":"3699990","numSourceRows":"99997","numTargetFilesRemoved":"1","rewriteTimeMs":"288168"}}}
{"remove":{"path":"part-00000-ccecfe5c-0721-4a2e-b1dc-3f6e741608a6-c000.snappy.parquet","deletionTimestamp":1630064493628,"dataChange":true,"extendedFileMetadata":true,"partitionValues":{},"size":650431627}}
{"add":{"path":"part-00000-0e188fd2-d051-4740-ad40-906bffeb21be-c000.snappy.parquet","partitionValues":{},"size":3408603,"modificationTime":1630064240277,"dataChange":true}}
{"add":{"path":"part-00001-6339004e-9c41-483a-8b36-26b03271b938-c000.snappy.parquet","partitionValues":{},"size":3350478,"modificationTime":1630064241542,"dataChange":true}}
{"add":{"path":"part-00002-eee8f5f4-9fd2-43b2-a45c-ddab8e8c97c8-c000.snappy.parquet","partitionValues":{},"size":3349674,"modificationTime":1630064242806,"dataChange":true}}
{"add":{"path":"part-00003-c2104839-e1af-463a-b5be-9749b00b1fa7-c000.snappy.parquet","partitionValues":{},"size":3352108,"modificationTime":1630064244099,"dataChange":true}}
```

Где-то есть параметр, устанавливающий число генерируемых файлов в 200.

Следующая операция:

```
{"commitInfo":{"timestamp":1630064837869,"operation":"MERGE","operationParameters":{"predicate":"((1 = 1) AND (current.`hid_address` = new.`hid_address`))","matchedPredicates":"[{\"actionType\":\"update\"}]","no
tMatchedPredicates":"[{\"actionType\":\"insert\"}]"},"readVersion":38,"isBlindAppend":false,"operationMetrics":{"numTargetRowsCopied":"3683335","numTargetRowsDeleted":"0","numTargetFilesAdded":"200","executionTi
meMs":"0","numTargetRowsInserted":"43502","scanTimeMs":"22108","numTargetRowsUpdated":"16655","numOutputRows":"3743492","numSourceRows":"60157","numTargetFilesRemoved":"200","rewriteTimeMs":"278797"}}}
{"remove":{"path":"part-00042-38372f1c-be1e-4cd4-a44c-cc050350ebcf-c000.snappy.parquet","deletionTimestamp":1630064837866,"dataChange":true,"extendedFileMetadata":true,"partitionValues":{},"size":3346526}}
{"remove":{"path":"part-00086-b709bdaa-d567-486d-904e-663d322577a0-c000.snappy.parquet","deletionTimestamp":1630064837867,"dataChange":true,"extendedFileMetadata":true,"partitionValues":{},"size":3403383}}
{"remove":{"path":"part-00177-70a44e62-447e-4297-8b17-97c38515c621-c000.snappy.parquet","deletionTimestamp":1630064837867,"dataChange":true,"extendedFileMetadata":true,"partitionValues":{},"size":3372446}}
...
{"remove":{"path":"part-00192-33787041-eb3b-413c-8adc-d6dacad75429-c000.snappy.parquet","deletionTimestamp":1630064837868,"dataChange":true,"extendedFileMetadata":true,"partitionValues":{},"size":3358233}}
{"add":{"path":"part-00000-bad9a019-a5ab-4f01-91c0-2bca5851cbe6-c000.snappy.parquet","partitionValues":{},"size":3454169,"modificationTime":1630064585371,"dataChange":true}}
{"add":{"path":"part-00001-6374c1cf-02a5-4cb8-a834-85054e4c4d1b-c000.snappy.parquet","partitionValues":{},"size":3395720,"modificationTime":1630064586731,"dataChange":true}}
{"add":{"path":"part-00002-80e719c1-92b4-41e9-9051-4f7549af87a4-c000.snappy.parquet","partitionValues":{},"size":3393036,"modificationTime":1630064587992,"dataChange":true}}
```

То есть каждая операция каждый раз пересоздаёт 200 файлов.

## Table batch reads and writes

* [Table batch reads and writes](https://docs.delta.io/latest/delta-batch.html)

### Обновление схемы таблицы

<https://docs.delta.io/latest/delta-batch.html#update-table-schema>

### Автоматическое обновление схемы таблицы

<https://docs.delta.io/latest/delta-batch.html#automatic-schema-update>

## Table streaming reads and writes

* [Table streaming reads and writes](https://docs.delta.io/latest/delta-streaming.html)

## Best Practices

* [Best Practices](https://docs.delta.io/latest/best-practices.html)

## Schema Evolution

* [Diving Into Delta Lake: Schema Enforcement & Evolution](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html)

## Idempotent

* https://github.com/delta-io/delta/pull/1555

> Currently, delta supports idempotent write using Dataframe writer options. These writer options are applicable to inserts only. This PR adds support for idempotency using SQL options(DELTA_IDEMPOTENT_DML_TXN_APP_ID and DELTA_IDEMPOTENT_DML_TXN_VERSION) to INSERTS/DELETE/UPDATE/MERGE etc. When both writer options and SQL conf are specified, we will use the writer option values.
> 
> Idempotent write works by checking the txnVersion and txnAppId from user-provided write options or from session configurations(as a SQL conf). If the same or higher txnVersion has been recorded, then it will skip the write.