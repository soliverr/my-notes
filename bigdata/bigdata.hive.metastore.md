---
id: 4a9226p88d9e6r9k7rhg0iq
title: Metastore
desc: ''
updated: 1662552781986
created: 1620717330567
---

# Hive2 Metastore

https://cwiki.apache.org/confluence/display/Hive/AdminManual+Metastore+Administration

# Hive3 Metastore

https://cwiki.apache.org/confluence/display/Hive/AdminManual+Metastore+3.0+Administration

## Дистрибутивы

Если скачивать Hive Metastore Standalone через официальную страницу [Hive Download](https://hive.apache.org/downloads.html), то доступен только первый дистрибутив `hive-standalone-metastore-3.0.0`, видимо изменилась политика публикации дистрибутива, и с выходом новых версий Hive, публикуются артефакты в Maven:

* https://search.maven.org/artifact/org.apache.hive/hive-metastore
* https://mvnrepository.com/artifact/org.apache.hive/hive-metastore

Посмотреть файловый индекс и скачать файлы дистрибутива можно через зеркало:

* https://repo1.maven.org/maven2/org/apache/hive/hive-standalone-metastore/

![](/assets/images/2022-06-10-19-16-33.png)


# Установка Hive3

* нужен JDK8
* дистрибутив [Apache Hadoop](https://hadoop.apache.org/releases.html)
* дистрибутив [PostgreSQL JDBC-драйвер](https://jdbc.postgresql.org/)
* дистрибутив [Oracle MySQL connectors](https://dev.mysql.com/doc/index-connectors.html)
* для интеграции с AWS S3 требуется настроить пути к библиотекам в дистрибутиве Hadoop
* так же обнаружил специальное обновление Log4J

Реализацию установки лучше делать с помощью какой-то системы DevOps.

Пока выбрал Ansible, так как можно выполнять настройку на локальном хосте, с помощью _ansible-bender_ создавать образы для использования с Docker/Podman/Kubernetes.

## JMX


Разрешить активацию мониторинга через JMX:

https://docs.datadoghq.com/integrations/hive/?tabs=host:

>    Edit the Hive configuration file in HIVE_HOME/conf/hive-site.xml to enable the Hive Metastore and HiveServer2 metrics by adding these properties:
>
>```xml
    <property>
      <name>hive.metastore.metrics.enabled</name>
      <value>true</value>
    </property>
    <property>
      <name>hive.server2.metrics.enabled</name>
      <value>true</value>
    </property>
```
>    Enable a JMX remote connection for the HiveServer2 and/or for the Hive Metastore. For example, set the HADOOP_CLIENT_OPTS environment variable:
>
>```sh
    export HADOOP_CLIENT_OPTS="$HADOOP_CLIENT_OPTS -Dcom.sun.management.jmxremote \
    -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.ssl=false \
    -Dcom.sun.management.jmxremote.port=8808"
```
>    Then restart the HiveServer2 or the Hive Metastore. Hive Metastore and HiveServer2 cannot share the same JMX connection.

Затем нужен агент для экспорта метрик в системы мониторинга, см. [BigData JMX](bigdata.jmx.md)

Вот, пример подключения агента для Prometheus https://stackoverflow.com/questions/63754168/hive-jmx-metrics-enable:

> ```sh
if [ "$SERVICE" = "hiveserver2" ]; then
  export HADOOP_CLIENT_OPTS="$HADOOP_CLIENT_OPTS -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.port=9005 -Dcom.sun.management.jmxremote.local.only=false -Dcom.sun.management.jmxremote.authenticate=false -javaagent:/opt/java_metrics/jmx_prometheus_javaagent-0.3.0.jar=9008:/opt/java_metrics/config.yml -Dcom.sun.management.jmxremote.ssl=false"
fi
if [ "$SERVICE" = "metastore" ]; then
    export HADOOP_OPTS="$HADOOP_OPTS -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.port=9025 -Dcom.sun.management.jmxremote.local.only=false -Dcom.sun.management.jmxremote.authenticate=false -javaagent:/opt/java_metrics/jmx_prometheus_javaagent-0.3.0.jar=9028:/opt/java_metrics/config.yml -Dcom.sun.management.jmxremote.ssl=false  $HEAP_OPTS"
fi
```
> Restart hive-server2 and metastore service
> 
> Hive-Server2 metrics in prometheus format will be available in `http://<hive-server2-ip>:9008`
> 
> Hive-Metastore metrics in prometheus format will be available in `http://<hive-metastore-ip>:9028`


Даже без экспорта в Prometheus с помощью Azul Mission Control можно наблюдать оперативные метрики Hive Metastore

![](/assets/images/2022-07-13-15-38-59.png)

## Настройка

> The metastore reads its configuration from the file _metastore-site.xml_.  It expects to find this file in `$METASTORE_HOME/conf` where `$METASTORE_HOME` is an environment variable.  For backwards compatibility it will also read any hive-site.xml or _hive-metastoresite.xml_ files found in `$HIVE_HOME/conf`.

Нужен драйвер для выбранной СУБД, для примера PostgreSQL.

В СУБД нужно создать базу и пользователя с правами в ней:

```sh
$ psql -U postgres --host localhost

postgres=# create database hive3_metastore;
CREATE DATABASE
postgres=# create user hive3_metastore with password 'hive3_metastore';
CREATE ROLE
postgres=# grant all privileges on database hive3_metastore to hive3_metastore;
GRANT
```

Проверка доступа:

```sh
$ PGPASSWORD=hive3_metastore psql -U hive3_metastore --host localhost hive3_metastore

hive3_metastore=> 
```

Настроить параметры доступа в `metastore-site.xml`.

Инициализировать базу данных:

```sh
$METASTORE_HOME/bin/schematool --help
```

```sh
$METASTORE_HOME/bin/schematool -initSchema -dbType postgres
...
Closing: org.postgresql.jdbc.PgConnection
sqlline version 1.3.0
Initialization script completed
schemaTool completed
```

Запустить сервис:

```sh
$ $METASTORE_HOME/bin/start-metastore
2022-06-17 11:25:05,002 main INFO Log4j appears to be running in a Servlet environment, but there's no log4j-web module available. If you want better web container support, please add the log4j-web JAR to your web archive or server lib directory.
```

Не включён в дистрибутив:

```
# add the `log4j-web` jar to support web servlet containers
curl -L https://repo1.maven.org/maven2/org/apache/logging/log4j/log4j-web/${LOG4J_WEB_VERSION}/log4j-web-${LOG4J_WEB_VERSION}.jar -o log4j-web-${LOG4J_WEB_VERSION}.jar && \
cp log4j-web-${LOG4J_WEB_VERSION}.jar ${HIVE_HOME}/lib/ && \
rm log4j-web-${LOG4J_WEB_VERSION}.jar
```

Включил установку артефакта в роль Metastore.

Сервис запустился:

```sh
$ sudo netstat -ltnp | grep 9083
tcp6       0      0 :::9083                 :::*                    LISTEN      55695/java  
```

Проверить работу сервера можно с помощью клиента на Python:

* https://pypi.org/project/hive-metastore-client/
    * https://github.com/quintoandar/hive-metastore-client
    * https://hive-metastore-client.readthedocs.io/en/latest/index.html
    * https://github.com/quintoandar/hive-metastore-client/tree/main/examples

```$pip3 install hive-metastore-client

$ python3
```

```python

from hive_metastore_client.builders import DatabaseBuilder
from hive_metastore_client import HiveMetastoreClient

HIVE_HOST = 'localhost'
HIVE_PORT = 9083

database = DatabaseBuilder(name='new_db').build()
with HiveMetastoreClient(HIVE_HOST, HIVE_PORT) as hive_metastore_client:
    hive_metastore_client.create_database(database) 

Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
  File "/home/oliver/.local/lib/python3.10/site-packages/thrift_files/libraries/thrift_hive_metastore_client/ThriftHiveMetastore.py", line 2072, in create_database
    self.recv_create_database()
  File "/home/oliver/.local/lib/python3.10/site-packages/thrift_files/libraries/thrift_hive_metastore_client/ThriftHiveMetastore.py", line 2098, in recv_create_database
    raise result.o3
thrift_files.libraries.thrift_hive_metastore_client.ttypes.MetaException: MetaException(message='Unable to create database path file:/user/hive/warehouse/new_db.db, failed to create database new_db')
```

Надо задать каталог для хранилища по умолчанию. В файле _metastore-site.xml_ добавить параметр:

```xml
    <property>
      <name>hive.metastore.warehouse.dir</name>
      <value>/tmp/test</value>
    </property>
```

Создать каталог _/tmp/test_, дать права пользователю.

После этого база создаётся:

```sh
$ ls -l /tmp/test
drwxr-xr-x 1 oliver oliver 0 июн 17 12:58 new_db.db
```

## AWS S3

* http://www.devgrok.com/2018/12/using-s3-hive-metastore-with-emr.html

`HADOOP_CLASSPATH=${HADOOP_HOME}/share/hadoop/tools/lib/aws-java-sdk-bundle-1.11.375.jar:${HADOOP_HOME}/share/hadoop/tools/lib/hadoop-aws-3.2.0.jar`


# Errors

## MaterializationsCacheCleanerTask class not found

Возникла в версии 3.1.3

```
2022-09-07T13:55:40,423 ERROR [main] metastore.RetryingHMSHandler: MetaException(message:org.apache.hadoop.hive.metastore.MaterializationsCacheCleanerTask class not found)
        at org.apache.hadoop.hive.metastore.utils.JavaUtils.getClass(JavaUtils.java:54)
        at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.init(HiveMetaStore.java:589)
...
```

Оказалось, что этот класс выпилили из hive-metastore-standalone, в версии 3.0.0 этот класс ещё был.

Этот класс задаётся в параметре по умолчанию см. [Running the Metastore Without Hive](https://cwiki.apache.org/confluence/display/Hive/AdminManual+Metastore+3.0+Administration#AdminManualMetastore3.0Administration-RunningtheMetastoreWithoutHive):

| Configuration Parameter | Set to for Standalone Mode |
| metastore.task.threads.always	| org.apache.hadoop.hive.metastore.events.EventCleanerTask,org.apache.hadoop.hive.metastore.MaterializationsCacheCleanerTask | 
| metastore.expression.proxy	| org.apache.hadoop.hive.metastore.DefaultPartitionExpressionProxy |

Нужно явно задать параметр для Metastore Standalone без этого класса.

```json
metastore_thrift_port: 9083
metastore_properties:
  metastore-site:
    metastore.thrift.uris: "{{ hive3_metastore_servers_list }}"
    metastore.schema.verification: "true"
    metastore.metrics.enabled: "true"
    metastore.warehouse.dir: "{{ metastore_warehouse_dir }}/managed"
    hive.metastore.warehouse.external.dir: "{{ metastore_warehouse_dir }}/external"
    javax.jdo.option.ConnectionDriverName: "org.postgresql.Driver"
    javax.jdo.option.ConnectionURL: "jdbc:postgresql://{{ metastore_db_host }}:{{ metastore_db_port }}/{{ metastore_db_name }}"
    javax.jdo.option.ConnectionUserName: "{{ metastore_db_user }}"
    javax.jdo.option.ConnectionPassword: "{{ metastore_db_pass }}"
    datanucleus.autoCreateSchema: "true"
    datanucleus.schema.autoCreateAll: "false"
    datanucleus.autoStartMechanismMode: "ignored"
    datanucleus.connectionPoolingType: "HikariCP"
    metastore.task.threads.always: "org.apache.hadoop.hive.metastore.events.EventCleanerTask"
    metastore.expression.proxy: "org.apache.hadoop.hive.metastore.DefaultPartitionExpressionProxy"
  extra-jars:
    - "{{ hadoop_aux_dir }}/postgresql-jdbc-{{ postgres_jdbc_jar_version }}.jar"
```