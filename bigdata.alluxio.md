---
id: eonv4cpysit1zj996ggsp2y
title: Alluxio
desc: ''
updated: 1686220750816
created: 1675437479119
---
Alluxio
=======

[Alluxio](https://www.alluxio.io)

# Ресурсы

* [Alluxio Data Orchestration Summin 2020](https://www.alluxio.io/data-orchestration-summit-2020/#presentations)
* [Building High-Performance Data Lake Using Apache Hudi and Alluxio at T3Go](https://www.alluxio.io/blog/building-high-performance-data-lake-using-apache-hudi-and-alluxio-at-t3go/)

# Download

* https://www.alluxio.io/download/
    * https://downloads.alluxio.io/downloads/files/

# Alluxio CLI

* https://docs.alluxio.io/os/user/stable/en/operation/User-CLI.html

## mount

Показать замонтированные файловые системы или замонтировать ФС

```
$ alluxio fs mount
s3a://winfinity-data-warehouse/  on  /  (s3, capacity=-1B, used=-1B, not read-only, not shared, properties={s3a.accessKeyId=******, s3a.secretKey=******})
```

## ls

Посмотреть содержимое каталога и кешировать метаданные:

```
$ alluxio fs ls -f /
drwx------  alluxio        alluxio                      0       PERSISTED 01-30-2023 16:40:29:500  DIR /spark
drwx------  alluxio        alluxio                      0       PERSISTED 01-30-2023 16:40:29:563  DIR /tmp 
drwx------  alluxio        alluxio                      0       PERSISTED 01-30-2023 16:40:29:565  DIR /warehouse
```

# Configuration settings

* [Configuration Settings](https://docs.alluxio.io/ee/user/stable/en/operation/Configuration.html#path-defaults)
* [List of Configuration Properties](https://docs.alluxio.io/os/user/stable/en/reference/Properties-List.html)

## Authentication

* [Identity and Access Control of S3 Objects](https://docs.alluxio.io/os/user/stable/en/ufs/S3.html#file-owner-and-group)

Запретить ACL совсем:

```
alluxio.security.authentication.type: NOSASL
alluxio.security.authorization.permission.enabled: 'false'
alluxio.master.mount.table.root.shared: 'true'
```

Установить ACL без учёта настроек S3

Set alluxio.underfs.s3.inherit.acl=false and alluxio.underfs.s3.default.mode to a new default value other than 0700 that can enable other users to access.

В версии 2.8.1 появилось предупреждение: `WARN  InstancedConfiguration - alluxio.master.mount.table.root.shared is deprecated. Please avoid using this key in the future.`

Сделал вот такие настройки:

```
# Authentication
alluxio.security.authentication.type: NOSASL
alluxio.security.authorization.permission.enabled: 'false'
alluxio.security.authorization.permission.umask: '000'
# S3
alluxio.underfs.s3.inherit.acl: 'false'
alluxio.underfs.s3.default.mode: 777
```

## Short circuit

the fuse daemon (like any other alluxio client) detects if the worker is on the same node by inspecting the location `alluxio.worker.data.server.domain.socket.address=/opt/domain` if `alluxio.worker.data.server.domain.socket.as.uuid=true`.

if there's no worker, the directory will be empty. each worker creates a socket file with a uuid in that directory

**Конфигурация Spark**:

```
	# Alluxio
    spark.kubernetes.executor.volumes.hostPath.alluxio-domain.mount.path      /opt/domain
    spark.kubernetes.executor.volumes.hostPath.alluxio-domain.mount.readOnly  true
    spark.kubernetes.executor.volumes.hostPath.alluxio-domain.options.path    /tmp/alluxioworker
    spark.kubernetes.executor.volumes.hostPath.alluxio-domain.options.type    Directory

```

**Конфигурация Alluxio**:

```
  # Domain socket for worker
  alluxio.worker.data.server.domain.socket.as.uuid: 'true'
  alluxio.worker.data.server.domain.socket.address: '/opt/domain'
```

На хосте должен быть создан каталог `/tmp/alluxioworker`

В каждый pod worker'а для Alluxio и Spark необходимо подключить этот каталог.

**Helm Alluxio**:
```
# Short circuit related properties
shortCircuit:
  enabled: true
  # The policy for short circuit can be "local" or "uuid",
  # local means the cache directory is in the same mount namespace,
  # uuid means interact with domain socket
  policy: uuid
  # volumeType controls the type of shortCircuit volume.
  # It can be "persistentVolumeClaim" or "hostPath"
  volumeType: hostPath
  #size: 10Mi
  # Attributes to use if the domain socket volume is PVC
  #pvcName: alluxio-worker-domain-socket
  #accessModes:
  #  - ReadWriteOnce
  #storageClass: gp3
  # Attributes to use if the domain socket volume is hostPath
  hostPath: "/tmp/alluxioworker" # The hostPath directory to use
```

Helm SparkAplications:
```
  volumes:
    - name: wnf-debezium-consumer
      configMap:
        name: wnf-debezium-consumer
    - name: alluxio-domain
      hostPath:
        path: /tmp/alluxioworker
        type: Directory

  driver:
    cores: 1
    memory: "3072m"
    labels:
      version: 3.2.2
    serviceAccount: spark
    javaOptions: '-Dlog4j.configuration=file:///opt/spark/wnf/log4j.properties -Dlog4j.info -XX:+UseG1GC'
    # Mount common configuration from ConfigMap
    volumeMounts:
      - name: wnf-debezium-consumer
        mountPath: /opt/spark/wnf/config/wnf-debezium-consumer.json
        subPath: wnf-debezium-consumer.json
      - name: alluxio-domain
        mountPath: /opt/domain
```


### Verify Short-circuit Operations

To verify short-circuit reads and writes monitor the metrics displayed under:

1. the metrics tab of the web UI as `Domain Socket Alluxio Read` and `Domain Socket Alluxio Write`
1. or, the metrics json as `cluster.BytesReadDomain` and `cluster.BytesWrittenDomain`
1. or, the fsadmin metrics CLI as `Short-circuit Read (Domain Socket)` and `Alluxio Write (Domain Socket)`


# Alluxio Catalog Service

[Catalog](https://docs.alluxio.io/os/user/stable/en/core-services/Catalog.html)

> Alluxio 2.1.0 introduced a new service within Alluxio called the Alluxio Catalog Service. The Alluxio Catalog Service is a service for managing access to structured data, which serves a purpose similar to the [Apache Hive Metastore](https://cwiki.apache.org/confluence/display/Hive/Design#Design-Metastore).

> SQL engines like Presto, SparkSQL, and Hive, leverage these metastore-like services to determine which, and how much data to read when executing queries. They store information about different database catalogs, tables, storage formats, data location, and more. The Alluxio Catalog Service is designed to make it simple and straightforward to retrieve and serve structured table metadata to Presto query engines, e.g. [PrestoSQL](https://prestosql.io/), [PrestoDB](https://prestodb.io/), and [Starburst Presto](https://starburstdata.com/).

[Everything you want to know about how to decouple SQL engines from Hive Data Warehouse](https://www.alluxio.io/blog/everything-you-want-to-know-about-how-to-decouple-sql-engines-from-hive-data-warehouse/)

> Are you using SQL engines, such as Presto, to query existing Hive data warehouse and experiencing challenges including overloaded Hive Metastore with slow and unpredictable access, unoptimized data formats and layouts such as too many small files, or lack of influence over the existing Hive system and other Hive applications?

# Intergations

## S3

В параметрах chart'а Alluxio указал:

```
  # To reduce the charges
  alluxio.underfs.cleanup.enabled: 'true'
  # If the S3 connection is slow, a larger timeout is useful
  alluxio.underfs.s3.socket.timeout: 500sec
  alluxio.underfs.s3.request.timeout: 5min
  # If we expect a great number of concurrent metadata operations
  alluxio.underfs.s3.admin.threads.max: 80
  # If the total number of metadata + data operations is huge
  alluxio.underfs.s3.threads.max: 160
  # For a worker, the number of concurrent writes to S3
  # For a master, the number of threads to concurrently rename files within a directory
  alluxio.underfs.s3.upload.threads.max: 80
  # Thread-pool size to submit delete and rename operations to S3 on master
  alluxio.underfs.object.store.service.threads: 80
  #
  alluxio.user.metrics.collection.enabled: 'true'
  alluxio.security.stale.channel.purge.interval: 365d
  # AWS S3 access
  alluxio.master.mount.table.root.ufs: "s3a://<warehouse-bucket>/"
  alluxio.master.mount.table.root.option.s3a.accessKeyId: "<s3_access_key>"
  alluxio.master.mount.table.root.option.s3a.secretKey: "<s3_secret_key>"
```

Эти параметры передаются как параметры коммандной строки при запуске Alluxio.

## Trino

* https://docs.alluxio.io/ee/user/stable/en/compute/Trino.html
* https://www.alluxio.io/blog/get-started-with-trino-and-alluxio-in-5-minutes/
    * https://github.com/bitsondatadev/trino-getting-started/tree/main/iceberg/trino-alluxio-iceberg-minio

Путь для размещения клиента Alluxio в Docker-образе Trino:

* для всех коннекторов и для Transparent URI Handling -  `/usr/lib/trino/lib`
* для конкретного коннетора - `/usr/lib/trino/plugin/{connector}`
* конфигурация для Alluxio - `/etc/trino/alluxio-site.properties`

To configure additional Alluxio properties, you can append the conf path (i.e. `${ALLUXIO_HOME}/conf`) containing `alluxio-site.properties` to Trino’s JVM config at `etc/jvm.config` under Trino folder. The advantage of this approach is to have all the Alluxio properties set within the same file of `alluxio-site.properties`.

_/etc/trino/jvm.config_:

```
...
-Xbootclasspath/a:<path-to-alluxio-conf>
```

## Spark and DeltaLake

* https://dobachi.github.io/memo-blog/2020/12/31/Delta-Lake-with-Alluxio/

Вот это надо добавлять в HDFS _core_site.xml_:

```xml
<property>
  <name>fs.AbstractFileSystem.alluxio.impl</name>
  <value>alluxio.hadoop.AlluxioFileSystem</value>
  <description>The FileSystem for alluxio uris.</description>
</property>
```


# Unified Namespace

## Update Metadata
 
* [UFS Metadata Sync](https://docs.alluxio.io/ee/user/stable/en/core-services/Unified-Namespace.html#ufs-metadata-sync)

By default, Alluxio loads the list of files the first time a directory is visited. Alluxio will keep using the cached file list regardless of the changes in the under file system. To reveal new files from under file system, you can use the command
```
alluxio fs ls -R -Dalluxio.user.file.metadata.sync.interval=${SOME_INTERVAL} /path
``` 
or by setting the same configuration property in masters’ _alluxio-site.properties_. The value for the configuration property is used to determine the minimum interval between two syncs.

 A value of **0** indicates that the metadata will always be synced for every operation, whereas the default value of **-1** indicates the metadata is never synced again after the initial load.

 # Ошибки

 ## Метрики c версии 2.9.0

Клиент не работает, нужна библиотека метрик Dropwiz. 

* https://docs.alluxio.io/ee/user/stable/en/operation/Metrics-System.html с версии 2.9.0

```
SQL Error [65536]: Query failed (#20230206_181925_00013_scxwm): com.google.common.util.concurrent.ExecutionError: java.lang.NoClassDefFoundError: Could not initialize class alluxio.metrics.MetricsSystem

com.google.common.util.concurrent.ExecutionError: java.lang.NoClassDefFoundError: Could not initialize class alluxio.metrics.MetricsSystem
	at com.google.common.cache.LocalCache$Segment.get(LocalCache.java:2053)
	at com.google.common.cache.LocalCache.get(LocalCache.java:3966)
	at com.google.common.cache.LocalCache$LocalManualCache.get(LocalCache.java:4863)
	at io.trino.collect.cache.EvictableCache.get(EvictableCache.java:114)
	at io.trino.plugin.deltalake.transactionlog.TransactionLogAccess.loadSnapshot(TransactionLogAccess.java:153)
	at io.trino.plugin.deltalake.metastore.HiveMetastoreBackedDeltaLakeMetastore.getSnapshot(HiveMetastoreBackedDeltaLakeMetastore.java:222)
	at io.trino.plugin.deltalake.DeltaLakeMetadata.lambda$streamTableColumns$12(DeltaLakeMetadata.java:556)
	at java.base/java.util.stream.ReferencePipeline$7$1.accept(ReferencePipeline.java:273)
	at java.base/java.util.Collections$2.tryAdvance(Collections.java:4853)
	at java.base/java.util.stream.StreamSpliterators$WrappingSpliterator.lambda$initPartialTraversalState$0(StreamSpliterators.java:292)
	at java.base/java.util.stream.StreamSpliterators$AbstractWrappingSpliterator.fillBuffer(StreamSpliterators.java:206)
	at java.base/java.util.stream.StreamSpliterators$AbstractWrappingSpliterator.doAdvance(StreamSpliterators.java:161)
	at java.base/java.util.stream.StreamSpliterators$WrappingSpliterator.tryAdvance(StreamSpliterators.java:298)
	at java.base/java.util.Spliterators$1Adapter.hasNext(Spliterators.java:681)
	at io.trino.plugin.base.classloader.ClassLoaderSafeIterator.computeNext(ClassLoaderSafeIterator.java:39)
	at com.google.common.collect.AbstractIterator.tryToComputeNext(AbstractIterator.java:146)
	at com.google.common.collect.AbstractIterator.hasNext(AbstractIterator.java:141)
	at java.base/java.util.Iterator.forEachRemaining(Iterator.java:132)
	at io.trino.metadata.MetadataManager.listTableColumns(MetadataManager.java:563)
	at io.trino.metadata.MetadataListing.listTableColumns(MetadataListing.java:164)
	at io.trino.connector.system.jdbc.ColumnJdbcTable.cursor(ColumnJdbcTable.java:263)
	at io.trino.connector.system.SystemPageSourceProvider$1.cursor(SystemPageSourceProvider.java:129)
	at io.trino.split.MappedRecordSet.cursor(MappedRecordSet.java:53)
	at io.trino.spi.connector.RecordPageSource.<init>(RecordPageSource.java:37)
	at io.trino.connector.system.SystemPageSourceProvider.createPageSource(SystemPageSourceProvider.java:108)
	at io.trino.split.PageSourceManager.createPageSource(PageSourceManager.java:62)
	at io.trino.operator.ScanFilterAndProjectOperator$SplitToPages.process(ScanFilterAndProjectOperator.java:266)
	at io.trino.operator.ScanFilterAndProjectOperator$SplitToPages.process(ScanFilterAndProjectOperator.java:194)
	at io.trino.operator.WorkProcessorUtils$3.process(WorkProcessorUtils.java:360)
	at io.trino.operator.WorkProcessorUtils$ProcessWorkProcessor.process(WorkProcessorUtils.java:413)
	at io.trino.operator.WorkProcessorUtils$3.process(WorkProcessorUtils.java:347)
	at io.trino.operator.WorkProcessorUtils$ProcessWorkProcessor.process(WorkProcessorUtils.java:413)
	at io.trino.operator.WorkProcessorUtils$3.process(WorkProcessorUtils.java:347)
	at io.trino.operator.WorkProcessorUtils$ProcessWorkProcessor.process(WorkProcessorUtils.java:413)
	at io.trino.operator.WorkProcessorUtils.getNextState(WorkProcessorUtils.java:262)
	at io.trino.operator.WorkProcessorUtils.lambda$processStateMonitor$2(WorkProcessorUtils.java:241)
	at io.trino.operator.WorkProcessorUtils$ProcessWorkProcessor.process(WorkProcessorUtils.java:413)
	at io.trino.operator.WorkProcessorUtils.getNextState(WorkProcessorUtils.java:262)
	at io.trino.operator.WorkProcessorUtils.lambda$finishWhen$3(WorkProcessorUtils.java:256)
	at io.trino.operator.WorkProcessorUtils$ProcessWorkProcessor.process(WorkProcessorUtils.java:413)
	at io.trino.operator.WorkProcessorSourceOperatorAdapter.getOutput(WorkProcessorSourceOperatorAdapter.java:146)
	at io.trino.operator.Driver.processInternal(Driver.java:394)
	at io.trino.operator.Driver.lambda$process$8(Driver.java:297)
	at io.trino.operator.Driver.tryWithLock(Driver.java:689)
	at io.trino.operator.Driver.process(Driver.java:289)
	at io.trino.operator.Driver.processForDuration(Driver.java:260)
	at io.trino.execution.SqlTaskExecution$DriverSplitRunner.processFor(SqlTaskExecution.java:755)
	at io.trino.execution.executor.PrioritizedSplitRunner.process(PrioritizedSplitRunner.java:165)
	at io.trino.execution.executor.TaskExecutor$TaskRunner.run(TaskExecutor.java:523)
	at io.trino.$gen.Trino_406____20230206_180748_2.run(Unknown Source)
	at java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1136)
	at java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:635)
	at java.base/java.lang.Thread.run(Thread.java:833)
```

```
2023-02-06 17:51:04,221 WARN  ConfigurationChecker - Inconsistent configuration detected. Only a limited set of inconsistent configuration will be shown here. For details, please visit Alluxio web UI or run fsadmin doctor CLI.
Warnings: [InconsistentProperty{key=alluxio.worker.data.server.domain.socket.as.uuid, values=false (10.20.72.200:19998), true (10.20.71.6:29999, 10.20.78.52:29999, 10.20.69.36:29999)}]
```

# Alluxio Edge

Решение от Alluxio для кеширования файлов при чтении для Trino/Presto.

С версии Trino 458 обнаружилась проблема, которую Alluxio пока пофиксить не могут.

>Between 455 and 465, there is a big breaking change regarding how Trino supports legacy file system: [https://trino.io/docs/current/release/release-458.html](https://trino.io/docs/current/release/release-458.html) 
> 
  This is the cause of requiring `fs.hadoop.enabled=true` for versions 458+.  
  However it’s likely more than a simple code migration with a boolean to toggle the old codepath and is likely the reason why the same database operation is hitting a different codepath on the filesystem level within Trino’s code.

Вот [пердупреждение](https://trino.io/docs/current/release/release-458.html#delta-lake-connector) об изменениях в коннекторе DeltaLake:

>[⚠️ Breaking change:](https://trino.io/docs/current/release.html#breaking-changes "Breaking change") Deactivate [legacy file system support](https://trino.io/docs/current/object-storage.html#file-system-legacy) for all catalogs. You must activate the desired [file system support](https://trino.io/docs/current/object-storage.html#file-system-configuration) with `fs.native-azure.enabled`,`fs.native-gcs.enabled`, `fs.native-s3.enabled`, or `fs.hadoop.enabled` in each catalog. Use the migration guides for [Azure Storage](https://trino.io/docs/current/object-storage/file-system-azure.html#fs-legacy-azure-migration), [Google Cloud Storage](https://trino.io/docs/current/object-storage/file-system-gcs.html#fs-legacy-gcs-migration), and [S3](https://trino.io/docs/current/object-storage/file-system-s3.html#fs-legacy-s3-migration) to assist if you have not switched from legacy support. ([#23343](https://github.com/trinodb/trino/issues/23343))