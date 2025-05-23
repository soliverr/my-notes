---
id: cmwni2knku4yvlqjp94qg0s
title: Apache Spark
desc: ''
updated: 1679590927344
created: 1618474466673
---

# Введение

[Apache Spark](https://spark.apache.org) 


# Интересные ресурсы

* [SPARK CONFIGURATION OPTIMIZATION](http://spark-configuration.luminousmen.com/)
* [DASK and Apache Spark](https://databricks.com/session/dask-and-apache-spark)
* [Spark Standalone Cluster Internals](https://sandeepmspark.blogspot.com/2018/01/spark-standalone-cluster-internals.html)

# Dask and Apache Spark

[DASK and Apache Spark](https://databricks.com/session/dask-and-apache-spark)

![Снимок экрана от 2021-02-02 14-05-24](../../media/---2021-02-02-14-05-24-1486684943.png)

# Ускорение Apache Spark 3.0 с использованием NVIDIA GPU

 Материалы вебинара вы найдете по ссылкам ниже:

* [Запись вебинара](http://bit.ly/2MEdKmD)
* [Слайды](http://bit.ly/3jyFGEw)

# Spark Job Service

* [spark-jobserver](https://github.com/spark-jobserver/spark-jobserver)
* Livy
    
# Spark Hive integration

Для использования Hive Metastore server в Spark оказалось, что нужно иметь свежий установленный Hive. В Spark 3.0.2 поддержка Hive встроена, но отстаёт по версии, сейчас используется Hive 2.7.

Специальный артефакт `hive-standalone-metastore (group_id: org.apache.hive, version: 3.0.0)` не подходит, так как Spark использует большее количество библиотек.

Библиотеки подобрал экспериментально и по исходникам https://github.com/apache/spark/blob/0494dc90af48ce7da0625485a4dc6917a244d580/sql/hive/pom.xml

Для версии  Hive 3.1.2:

```
spark.sql.hive.metastore.jars   /opt/hive/3.1.2/lib/hive-standalone-metastore-3.1.2.jar:/opt/hive/3.1.2/lib/hive-exec-3.1.2.jar:/opt/hive/3.1.2/lib/hive-common-3.1.2.jar:/opt/hive/3.1.2/lib/hive-metastore-3.1.2.jar:/opt/hive/3.1.2/lib/hive-serde-3.1.2.jar:/opt/hive/3.1.2/lib/hive-shims-3.1.2.jar:/opt/hive/3.1.2/lib/commons-logging-1.0.4.jar:/opt/hive/3.1.2/lib/commons-io-2.4.jar:/opt/hive/3.1.2/lib/javax.servlet-api-3.1.0.jar:/opt/hive/3.1.2/lib/calcite-core-1.16.0.jar:/opt/hive/3.1.2/lib/commons-codec-1.7.jar:/opt/hive/3.1.2/lib/libfb303-0.9.3.jar:/opt/hive/3.1.2/lib/jackson-core-2.9.5.jar:/opt/hive/3.1.2/lib/jackson-databind-2.9.5.jar:/opt/hive/3.1.2/lib/jackson-annotations-2.9.5.jar
```

В Spark 3.1.x сделано уже удобнее, можно просто задать каталог, который будет подключен в ClassPath.

# Spark HDFS Failover

Можно задачать следующие параметры для правильного подключения Spark к HA кластеру HDFS:

```shell
"spark.hadoop.dfs.nameservices=CLUSTER_NAME",
"spark.hadoop.dfs.ha.namenodes.CLUSTER_NAME=namenode1,namenode2",
"spark.hadoop.dfs.namenode.rpc-address.CLUSTER_NAME.namenode1=host1.domain:9000",
"spark.hadoop.dfs.namenode.rpc-address.CLUSTER_NAME.namenode2=host2.domain:9000",
"spark.hadoop.dfs.client.failover.proxy.provider.CLUSTER_NAME=org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider",
```

# Spark on Kubernetes

* https://spark.apache.org/docs/latest/running-on-kubernetes.html
* [Apache Spark with Kubernetes and Fast S3 Access | by Yifeng Jiang | Towards Data Science](https://towardsdatascience.com/apache-spark-with-kubernetes-and-fast-s3-access-27e64eb14e0f) #[[Roam-Highlights]]
* [Running Spark on Kubernetes: Approaches and Workflow | by Yifeng Jiang | Towards Data Science](https://towardsdatascience.com/running-spark-on-kubernetes-approaches-and-workflow-75f0485a4333) #[[Roam-Highlights]]
* [The Internals of Spark on Kubernetes](https://jaceklaskowski.github.io/spark-kubernetes-book/)

## Dependency Management

[Running Spark on Kubernetes - Spark 3.2.2 Documentation](https://spark.apache.org/docs/3.2.2/running-on-kubernetes.html): 

> If your application’s dependencies are all hosted in remote locations like HDFS or HTTP servers, they may be referred to by their appropriate remote URIs. Also, application dependencies can be pre-mounted into custom-built Docker images. Those dependencies can be added to the classpath by referencing them with `local://` URIs and/or setting the `SPARK_EXTRA_CLASSPATH` environment variable in your Dockerfiles. **The `local://` scheme is also required when referring to dependencies in custom-built Docker images in `spark-submit`**. We support dependencies from the submission client’s local file system using the `file://` scheme or without a scheme (using a full path), where the destination should be a Hadoop compatible filesystem.
> 
> A typical example of this using S3 is via passing the following options:
> ```...
--packages org.apache.hadoop:hadoop-aws:3.2.2
--conf spark.kubernetes.file.upload.path=s3a://<s3-bucket>/path
--conf spark.hadoop.fs.s3a.access.key=...
--conf spark.hadoop.fs.s3a.impl=org.apache.hadoop.fs.s3a.S3AFileSystem
--conf spark.hadoop.fs.s3a.fast.upload=true
--conf spark.hadoop.fs.s3a.secret.key=....
--conf spark.driver.extraJavaOptions=-Divy.cache.dir=/tmp -Divy.home=/tmp file:///full/path/to/app.jar
> ```
> 
> The app jar file will be uploaded to the S3 and then when the driver is launched it will be downloaded to the driver pod and will be added to its classpath. Spark will generate a subdir under the upload path with a random name to avoid conflicts with spark apps running in parallel. User could manage the subdirs created according to his needs.
> 
> The client scheme is supported for the application jar, and dependencies specified by properties `spark.jars`, `spark.files` and `spark.archives`.
> 
> **Important**: all client-side dependencies will be uploaded to the given path with a flat directory structure so file names must be unique otherwise files will be overwritten. Also make sure in the derived k8s image default ivy dir has the required access rights or modify the settings as above. The latter is also important if you use `--packages` in cluster mode.

## Spark Operator

![[kubernetes.spark.sparkoperator]]

## Spark History Server

* [Run Spark History Server on Kubernetes using Helm | by Carlos Escura | Medium](https://medium.com/@carlosescura/run-spark-history-server-on-kubernetes-using-helm-7b03bfed20f6) #[[Roam-Highlights]]
* [Spark UI History server on Kubernetes?](https://stackoverflow.com/questions/51798927/spark-ui-history-server-on-kubernetes)

## Configuration

Можно использовать sparkConfigMap и hadoopConfigMap:
* sparkConfigMap - маппинг для spark-default.conf, будет замонтирован файл `/etc/spark/conf/spark-default.conf`
* hadoopConfigMap - маппинг для core-site.xml, будет замонтирован файл `/etc/hadoop/conf/core-site.xml`

Так же будут уставнолены переменные среды:

```
$ set | grep CONF
HADOOP_CONF_DIR=/etc/hadoop/conf

SPARK_CONF_DIR=/etc/spark/conf
```

Но приложение будет запущено с другим файлом конфигурации:

```
spark@debezium-consumer-prod-partners-driver:~/work-dir$ ps axwww
  PID TTY      STAT   TIME COMMAND
    1 ?        Ss     0:00 /usr/bin/tini -s -- /opt/spark/bin/spark-submit --conf spark.driver.bindAddress=10.0.19.129 --deploy-mode client --properties-file /opt/spark/conf/spark.properties --class com.winfinity.Spark.DebeziumSparkConsumer local:///opt/spark/wnf/WNF-DebeziumConsumer-2.6.jar --name=partners --topics=mysql.prod.partners.transactions.*:mysql.prod.partners..*sessions.*:mysql.prod.partners.users,mysql.prod.partners._MigrationHistory --last-operation=overwrite --max-offset-per-topic=300000 --table-data-model=delta --log-data-model=delta --warehouse-dir=/warehouse/mysql/data/partners --checkpoint-dir=/warehouse/mysql/offsets/partners --lock-dir=/tmp/mysql
   14 ?        Sl    30:36 /usr/local/openjdk-11/bin/java -cp /etc/spark/conf/:/opt/spark/jars/*:/etc/hadoop/conf/ -Xmx4096m -Dlog4j.configuration=file:///opt/spark/wnf/log4j.properties -Dlog4j.info -XX:+UseG1GC org.apache.spark.deploy.SparkSubmit --deploy-mode client --conf spark.driver.bindAddress=10.0.19.129 --properties-file /opt/spark/conf/spark.properties --class com.winfinity.Spark.DebeziumSparkConsumer local:///opt/spark/wnf/WNF-DebeziumConsumer-2.6.jar --name=partners --topics=mysql.prod.partners.transactions.*:mysql.prod.partners..*sessions.*:mysql.prod.partners.users,mysql.prod.partners._MigrationHistory --last-operation=overwrite --max-offset-per-topic=300000 --table-data-model=delta --log-data-model=delta --warehouse-dir=/warehouse/mysql/data/partners --checkpoint-dir=/warehouse/mysql/offsets/partners --lock-dir=/tmp/mysql
```

И если запустить `spark-shell`, то значения из sparkConfigMap не используются

```
spark@debezium-consumer-prod-partners-driver:~/work-dir$ /opt/spark/bin/spark-shell --deploy-mode client --properties-file /opt/spark/conf/spark.properties --master local

scala> spark.sql("show databases").show()
+---------+
|namespace|
+---------+
|  default|
+---------+
```

А вот при использовании настроек из файла по умолчанию:

```
$ /opt/spark/bin/spark-shell --deploy-mode client   --master local
scala> spark.sql("show databases").show()
Hive Session ID = 08930fb1-cb93-48d4-a909-9c702112d4a6
+---------------+
|      namespace|
+---------------+
|        default|
|        preprod|
|        release|
|reports_preprod|
|reports_release|
+---------------+
```

То, есть, похоже, что `properties-file` переопределяет все настройки для Spark-приложения.

Оказалось, можно использовать `sparkConf` и `sparkConfigMap` одновременно.

## Perfomance

* https://towardsdatascience.com/how-to-guide-set-up-manage-monitor-spark-on-kubernetes-with-code-examples-c5364ad3aba2 :

**Advanced tip:**
 
> Setting `spark.executor.cores` greater (typically **2x** or **3x** greater) than `spark.kubernetes.executor.request.cores` is called _oversubscription_ and can yield a  significant performance boost for workloads _where CPU usage is low_.


# Spark with S3

* [Apache Spark with Kubernetes and Fast S3 Access | by Yifeng Jiang | Towards Data Science](https://towardsdatascience.com/apache-spark-with-kubernetes-and-fast-s3-access-27e64eb14e0f)

```
"spark.hadoop.fs.s3a.endpoint": "192.168.170.12"
"spark.hadoop.fs.s3a.access.key": "S3_ACCESS_KEY"
"spark.hadoop.fs.s3a.secret.key": "S3_SECRET_KEY"
"spark.hadoop.fs.s3a.connection.ssl.enabled": "false"
```

Для работы с AWS S3 есть несколько вариантов библиотек, [Spark Read Text File from AWS S3 bucket - Spark by {Examples}](https://sparkbyexamples.com/spark/spark-read-text-file-from-s3/):

| Generation	| Usage	| Description | 
| First – s3	| s3:\\	| s3 which is also called classic (s3: filesystem for reading from or storing objects in Amazon S3 This has been deprecated and recommends using either the second or third generation library. |
| Second – s3n	| s3n:\\ | 	s3n uses native s3 object and makes easy to use it with Hadoop and other files systems. This is also not the recommended option. |
| Third – s3a	| s3a:\\ | 	s3a – This is a replacement of s3n which supports larger files and improves in performance. |


Реализация s3a считается наиболее продвинутой. Для этого необходимо добавить дополнительные библиотеки в `$SPARK_HOME/jars`:

* aws-java-sdk
* hadoop-aws

Нужно брать из соответствующей версии Hadoop, я взял из Hadoop 3.3. Нужно проверить возможность обновления aws-java-sdk из Maven

Вот свежая сборка на 24.08.2022:

```
<dependency>
  <groupId>com.amazonaws</groupId>
  <artifactId>aws-java-sdk-bundle</artifactId>
  <version>1.12.288</version>
</dependency>aws-java-sdk-bundle
```

Тоже работает!

При заврешении сессии возникает ошибка `WARN ShutdownHookManager: ShutdownHook 'ClientFinalizer' failed, java.util.concurrent.ExecutionException: java.lang.NoSuchMethodError: 'void org.apache.hadoop.fs.statistics.IOStatisticsLogging.logIOStatisticsAtLevel(org.slf4j.Logger, java.lang.String, java.lang.Object)'`

Вот код проверки для `spark-shell`:

```scala
scala> val df = sc.textFile("s3a://<backet-name>/<dir>/<file>")
df: org.apache.spark.rdd.RDD[String] = s3a://<backet-name>/<dir>/<file> MapPartitionsRDD[1] at textFile at <console>:23

scala> df.count()
res0: Long = nn

scala> df.collect().foreach(f=>{println(f)})
...
```

При записи также возникала ошибка:

```scala
scala> val df = sc.textFile("s3a://winfinity-data-warehouse/warehouse/passwd")
df: org.apache.spark.rdd.RDD[String] = s3a://winfinity-data-warehouse/warehouse/passwd MapPartitionsRDD[1] at textFile at <console>:23

scala> df.saveAsTextFile("s3a://winfinity-data-warehouse/warehouse/passwd.copy")

2/08/24 13:33:00 WARN ShutdownHookManager: ShutdownHook 'ClientFinalizer' failed, java.util.concurrent.ExecutionException: java.lang.NoSuchMethodError: 'void org.apache.hadoop.fs.statistics.IOStatisticsLogging.logIOStatisticsAtLevel(org.slf4j.Logger, java.lang.String, java.lang.Object)'
java.util.concurrent.ExecutionException: java.lang.NoSuchMethodError: 'void org.apache.hadoop.fs.statistics.IOStatisticsLogging.logIOStatisticsAtLevel(org.slf4j.Logger, java.lang.String, java.lang.Object)'
	at java.base/java.util.concurrent.FutureTask.report(FutureTask.java:122)
	at java.base/java.util.concurrent.FutureTask.get(FutureTask.java:205)
```

После усьановки корректных версий библиотек Hadoop, заработала запись в S3 и пропала ошибка обращения к статистике.

Из-за S3 Commiter problem необходимы ещё библиотеки:

* https://mvnrepository.com/artifact/org.apache.spark/spark-hadoop-cloud

```
<dependency>
    <groupId>org.apache.spark</groupId>
    <artifactId>spark-hadoop-cloud_2.12</artifactId>
    <version>3.2.2</version>
</dependency>
```

Настройка для Spark:

```
spark.hadoop.mapreduce.outputcommitter.factory.scheme.s3a   org.apache.hadoop.fs.s3a.commit.S3ACommitterFactory
spark.hadoop.fs.s3a.committer.name                          directory
spark.sql.sources.commitProtocolClass                       org.apache.spark.internal.io.cloud.PathOutputCommitProtocol
spark.sql.parquet.output.committer.class                    org.apache.spark.internal.io.cloud.BindingParquetOutputCommitter
```

# Spark with Hive Metastore

```
#spark.sql.catalogImplementation hive
spark.sql.hive.metastore.sharedPrefixes   org.postgresql
spark.hive.metastore.uris   thrift://metastore-0.dev.wnf:9083
spark.sql.hive.metastore.version   3.1.3
#spark.sql.hive.metastore.jars: "/opt/hive/3.1.2/lib/hive-standalone-metastore-3.1.2.jar:/opt/hive/3.1.2/lib/hive-exec-3.1.2.jar:/opt/hive/3.1.2/lib/hive-common-3.1.2.jar:/opt/hive/3.1.2/lib/hive-metastore-3.1.2
.jar:/opt/hive/3.1.2/lib/hive-serde-3.1.2.jar:/opt/hive/3.1.2/lib/hive-shims-3.1.2.jar:/opt/hive/3.1.2/lib/commons-logging-1.0.4.jar:/opt/hive/3.1.2/lib/commons-io-2.4.jar:/opt/hive/3.1.2/lib/javax.servlet-api-3
.1.0.jar:/opt/hive/3.1.2/lib/calcite-core-1.16.0.jar:/opt/hive/3.1.2/lib/commons-codec-1.7.jar:/opt/hive/3.1.2/lib/libfb303-0.9.3.jar:/opt/hive/3.1.2/lib/jackson-core-2.9.5.jar:/opt/hive/3.1.2/lib/jackson-databi
nd-2.9.5.jar:/opt/hive/3.1.2/lib/jackson-annotations-2.9.5.jar"
spark.sql.hive.metastore.jars   /opt/hive/3.1.3/lib/hive-standalone-metastore-3.1.3.jar:/opt/hive/3.1.3/lib/hive-exec-3.1.3.jar:/opt/hive/3.1.3/lib/hive-common-3.1.3.jar:/opt/hive/3.1.3/lib/hive-metastore-3.1.3.
jar:/opt/hive/3.1.3/lib/hive-serde-3.1.3.jar:/opt/hive/3.1.3/lib/hive-shims-3.1.3.jar:/opt/hive/3.1.3/lib/commons-logging-1.0.4.jar:/opt/hive/3.1.3/lib/commons-io-2.6.jar:/opt/hive/3.1.3/lib/javax.servlet-api-3.
1.0.jar:/opt/hive/3.1.3/lib/calcite-core-1.16.0.jar:/opt/hive/3.1.3/lib/commons-codec-1.15.jar:/opt/hive/3.1.3/lib/libfb303-0.9.3.jar:/opt/hive/3.1.3/lib/jackson-core-2.12.0.jar:/opt/hive/3.1.3/lib/jackson-datab
ind-2.12.0.jar:/opt/hive/3.1.3/lib/jackson-annotations-2.12.0.jar
#spark.sql.hive.metastore.jars maven
```

# Spark Shell

* https://sparkbyexamples.com/spark/spark-shell-usage-with-examples/
* https://sparkbyexamples.com/spark/spark-get-the-current-sparkcontext-settings/

```scala
sc.getConf.toDebugString

val arrayConfig = sc.getConf.getAll
for (conf <- arrayConfig)
  println(conf._1 +", "+ conf._2)

```

# Ошибки

## Несовпадение версии Scala

* https://stackoverflow.com/questions/45755881/unable-to-connect-to-remote-spark-master-ror-transportrequesthandler-error-wh

Проявляется вот так при подключении к Spark Master:

```
2022-08-17 15:43:45,592 ERROR server.TransportRequestHandler: Error while invoking RpcHandler#receive() for one-way message.
java.io.InvalidClassException: org.apache.spark.deploy.DeployMessages$ExecutorUpdated; local class incompatible: stream classdesc serialVersionUID = -1971851081955655249, local class serialVersionUID = 1654279024112373855
	at java.base/java.io.ObjectStreamClass.initNonProxy(ObjectStreamClass.java:560)
```

## java.lang.NoSuchMethodError: 'void org.apache.hadoop.fs.statistics.IOStatisticsLogging.logIOStatisticsAtLevel'

Эта ошибка также возникает при записи в S3.

```
22/08/24 13:09:26 WARN ShutdownHookManager: ShutdownHook 'ClientFinalizer' failed, java.util.concurrent.ExecutionException: java.lang.NoSuchMethodError: 'void org.apache.hadoop.fs.statistics.IOStatisticsLogging.logIOStatisticsAtLevel(org.slf4j.Logger, java.lang.String, java.lang.Object)'
java.util.concurrent.ExecutionException: java.lang.NoSuchMethodError: 'void org.apache.hadoop.fs.statistics.IOStatisticsLogging.logIOStatisticsAtLevel(org.slf4j.Logger, java.lang.String, java.lang.Object)'
```
* https://stackoverflow.com/questions/71059267/when-writing-parquet-files-to-s3-nosuchmethoderror-void-org-apache-hadoop-util
* https://github.com/apache/hudi/issues/5765

Проблема в версиях библиотек Hadoop.

Так как я сейчас использую Hadoop 3.3.3 и взял из этой версии библиотеку s3a. В поставке Spark 3.2.2 идут библиотеки от Hadoop 3.3.1.

Поэтому в `$SPARK_HOME/jars` я установил новые библиотеки из дистрибутива Hadoop:

* hadoop-client-api-3.3.3.jar
* hadoop-client-runtime-3.3.3.jar
* hadoop-common-3.3.3.jar