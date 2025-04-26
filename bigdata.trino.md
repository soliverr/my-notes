---
id: 4sdld6x9vj83q87a35rg8xl
title: Trino
desc: ''
updated: 1686648987030
created: 1654754647540
---

[Trino](https://trino.io/) a query engine that runs at ludicrous speed. Fast distributed SQL query engine for big data analytics that helps you explore your data universe.

## Ресурсы

* Репозитории одного из разработчиков [Brian "bits" Olsen ](https://github.com/bitsondatadev?tab=repositories)
* [Trino: The Definitive GuideThe Definitive Guide](https://www.wisdominterface.com/wp-content/uploads/2021/07/Trino-Oreilly-Guide.pdf)

## Установка

[Deploying Trino — Trino 388 Documentation](https://trino.io/docs/current/installation/deployment.html) #[[Roam-Highlights]]
### Java runtime environment

https://trino.io/docs/current/installation/deployment.html#java-runtime-environment

* Trino requires a 64-bit version of Java 11, with a minimum required version of 11.0.15. Earlier patch versions such as 11.0.2 do not work, nor will earlier major versions such as Java 8. Newer major versions such as Java 12 or 13, including Java 17, are not supported – they may work, but are not tested.
* We recommend using [Azul Zulu](https://www.azul.com/downloads/zulu-community/)
* as the JDK for Trino, as Trino is tested against that distribution. Zulu is also the JDK used by the [Trino Docker image](https://hub.docker.com/r/trinodb/trino).
### Server

* https://janakiev.com/blog/presto-cluster/#single-node

### Docker

Официальный образ от Trino

### Helm

* https://trinodb.github.io/charts/
    * https://github.com/trinodb/charts
* https://posulliv.github.io/posts/trino-helm/

## PrestoSQL стал Trino

27 декабря 2020 PrestoSQL переименовано в Trino https://trino.io/blog/2020/12/27/announcing-trino.html

[PrestoSQL becomes Trino](https://blog.starburstdata.com/prestosql-becomes-trino):

> On Sunday, December 27, 2020, Presto creators Martin Traverso, Dain Sundstrom, and David Philips met with other maintainers of the PrestoSQL project and made the decision to rebrand with a new name: Trino.  If you’re wondering why (and I’m sure you are), I encourage you to read their blog post here: PrestoSQL is now Trino.
>
> In the paragraphs below, I’ll explain my views on all of this, but let me give you the TL;DR up front:
>
> * PrestoSQL is now called Trino
> * PrestoDB is still called PrestoDB
> * Starburst, as a member of the governing board of the Presto Foundation, will be working to harmonize the codebases and establish a Presto conformance program
> * If you use Starburst for Presto, absolutely nothing changes for you.

> Now, if you’re new to Presto, you may not even know that there have been 2 different Presto branches (PrestoSQL and PrestoDB) ever since the creators left Facebook in late 2018.  PrestoDB was the original repo that continued to be developed by Facebook after their departure and PrestoSQL was the new repo they created when they left.  When PrestoSQL was established, all of the leading contributors outside of Facebook followed the Presto creators to this new repo and development continued at an [incredibly fast pace](https://trino.io/assets/blog/trino-announcement/commits.png).  Starburst was one of the first to make the switch, and I explained the rationale in this [January 2019 blog post](https://blog.starburstdata.com/technical-blog/the-presto-software-foundation).

> In late 2019, Facebook transferred ownership of PrestoDB to the Linux Foundation and a new foundation, the Presto Foundation, was created. This was an important milestone for 2 reasons:
>
> 1. The Linux Foundation now owns the trademark, which is why PrestoSQL had to change its name.
> 1. The Linux Foundation understands it is super common to have multiple distributions of the same project and will establish a conformance program to allow exactly that. For example, in the case of Linux, there are over [600 different Linux distributions](https://upload.wikimedia.org/wikipedia/commons/8/8c/Linux_Distribution_Timeline_Dec._2020.svg) in existence today!


## PrestoSQL/Trino и Hive

О процессе интеграции Presto и Hive:
* https://stackoverflow.com/questions/59014358/presto-integration-with-hive-is-not-working
* https://github.com/trinodb/trino/issues/1218

Trino можно использовать как заменитель Hive, и даже читать/записывать данные в HDFS и S3:

    * [A gentle introduction to the Hive connector](https://trino.io/blog/2020/10/20/intro-to-hive-connector.html)
    * [Querying S3 Object Stores with Presto or Trino](https://janakiev.com/blog/presto-trino-s3/)
    * [Presto-Powered S3 Data Warehouse on Kubernetes](https://joshua-robinson.medium.com/presto-powered-s3-data-warehouse-on-kubernetes-aea89d2f40e8)
        * [Trino on K8S](https://github.com/joshuarobinson/trino-on-k8s)

   

## Starburst

[Starburst for Presto](https://www.starburstdata.com/starburst-presto-sql/)

## Коннекторы

[Connectors](https://trino.io/docs/current/connector.html)

### Hive

* [Hive connector](https://trino.io/docs/current/connector/hive.html)
* [Hive Amazon S3](https://trino.io/docs/current/connector/hive-s3.html)
* [A gentle introduction to the Hive connector](https://trino.io/blog/2020/10/20/intro-to-hive-connector.html)
* [How to Install Presto or Trino on a Cluster and Query Distributed Data on Apache Hive and HDFS - Parametric Thoughts](https://janakiev.com/blog/presto-cluster/#hive-connector)
* [Querying S3 Object Stores with Presto or Trino - Parametric Thoughts](https://janakiev.com/blog/presto-trino-s3/)

Trino uses its own S3 filesystem for the URI prefixes `s3://`, `s3n://` and `s3a://`.

Нужны библиотеки для доступа к AWS из Hadoop, также как и для Hive Metastore.

```
connector.name=hive
hive.metastore.uri=thrift://10.20.77.247:9083
#hive.metastore.warehouse.dir=s3://winfinity-data-warehouse/warehouse
#hive.metastore.glue.default-warehouse-dir=s3://winfinity-data-warehouse/warehouse
hive.s3.path-style-access=true
#hive.s3.endpoint=winfinity-data-warehouse.s3.eu-central-1.amazonaws.com
hive.s3.aws-access-key=<access>
hive.s3.aws-secret-key=<secret>
```
Когда установил вот этот ключ: `hive.s3.endpoint=winfinity-data-warehouse.s3.eu-central-1.amazonaws.com` получил ошибку в Trino:

```
 The specified key does not exist. (Service: Amazon S3; Status Code: 404; Error Code: NoSuchKey; Request ID: PYFPK5P3QWP92ZF9; S3 Extended Request ID: i9qWwRAhCreZxfWwopRz6lNcd32wTWqFvoJO0KpqaEDolERJGqtjQxS52tjaQtTHkAR2IhSoj1c=; Proxy: null)
```

Получилось создать партиционированную таблицу и вставить в неё данные:

```sql
CREATE SCHEMA lakehouse.web WITH (location = 's3a://winfinity-data-warehouse/warehouse');

CREATE TABLE lakehouse.web.page_views (
  view_time timestamp,
  user_id bigint,
  page_url varchar,
  ds date,
  country varchar
)
WITH (
  format = 'ORC',
  partitioned_by = ARRAY['ds', 'country'],
  bucketed_by = ARRAY['user_id'],
  bucket_count = 50
);

insert into lakehouse.web.page_views values (CURRENT_DATE, 1, 'http://test', CURRENT_DATE, 'NZ');
```

### MongoDB

* [MongoDB](https://trino.io/docs/current/connector/mongodb.html)

Нужно описание схемы коллекций

* https://www.mongodb.com/docs/bi-connector/current/schema/persist-schema/

### Redis

Для работы с Redis необходимо генерировать схемы данных таблиц в формате JSON

* https://stackoverflow.com/questions/40166544/redis-presto-connector-corrupt-key-when-redis-key-prefix-schema-table-true-for-j

### MySQL

https://overcoder.net/q/541/%D0%BB%D1%83%D1%87%D1%88%D0%B8%D0%B9-%D1%82%D0%B8%D0%BF-%D0%B4%D0%B0%D0%BD%D0%BD%D1%8B%D1%85-%D0%B4%D0%BB%D1%8F-%D1%85%D1%80%D0%B0%D0%BD%D0%B5%D0%BD%D0%B8%D1%8F-%D0%B7%D0%BD%D0%B0%D1%87%D0%B5%D0%BD%D0%B8%D0%B9-%D0%B2%D0%B0%D0%BB%D1%8E%D1%82%D1%8B-%D0%B2-%D0%B1%D0%B0%D0%B7%D0%B5-%D0%B4%D0%B0%D0%BD%D0%BD%D1%8B%D1%85-mysql:

Единственное, что вам нужно обратить внимание, это переход от одной базы данных к другой, вы можете обнаружить, что DECIMAL (19,4) и DECIMAL (19,4) означают разные вещи

(http://dev.mysql.com/doc/refman/5.1/en/precision-math-decimal-changes.html)

    DBASE: 10,5 (10 integer, 5 decimal)
    MYSQL: 15,5 (15 digits, 10 integer (15-5), 5 decimal)

**Поэтому Trino не может обработать такой тип данных в MySQL `DECIMAL(65,30)`**

### Elasticsearch

[Elasticsearch connector](https://trino.io/docs/current/connector/elasticsearch.html#primitive-types)

Для правильного парсинга массивов и вложенных JSON-типов необходимо задавать маппинг для полей индекса:

Для массивов вида 

```json
{
    "array_string_field": ["trino","the","lean","machine-ohs"],
    "long_field": 314159265359,
    "id_field": "564e6982-88ee-4498-aa98-df9e3f6b6109",
    "timestamp_field": "1987-09-17T06:22:48.000Z",
    "object_field": {
        "array_int_field": [86,75,309],
        "int_field": 2
    }
}
```

```sh
curl --request PUT \
    --url localhost:9200/doc/_mapping \
    --header 'content-type: application/json' \
    --data '
{
    "_meta": {
        "trino":{
            "array_string_field":{
                "isArray":true
            },
            "object_field":{
                "array_int_field":{
                    "isArray":true
                }
            },
        }
    }
}'
```

Для сложных вложенных типов:

```sh
 curl --request PUT --url 'host:9200/Index_name/_mapping' --header 'content-type: application/json' --data '{ "_meta": {"presto":{"message":{"asRawJson":true}, "kubernetes":{"asRawJson":true}}}}'
```

> **It is not allowed to use asRawJson and isArray flags simultaneously for the same column.**

После маппинга `asRasJson` поле приходит в виде `varchar` и его можно разбирать через функцию `json_value`. Но как парсить вложенные структуры, пока не понятно:

```sql
select json_value(kubernetes, 'lax $.container_id' RETURNING varchar) AS container_id
	   ,json_value(kubernetes, 'lax $.pod_ips' RETURNING varchar) AS pod_ips -- не работает, возвращает NULL
	   ,json_value(kubernetes, 'lax $.pod_lables' RETURNING varchar) AS pod_labels  -- не работает, возвращает NULL
	   ,kubernetes
	   ,file, source_type, stream,"timestamp"
 from elasticsearch."default"."gamemanagement-2022-07-20" 
where _id = 'e608GoIBAEDyQ1XuqvBn';
```

### OpenSearch

Пока нет коннектора для OpenSearch: [Add dedicated OpenSearch connector · Issue #11377 · trinodb/trino](https://github.com/trinodb/trino/issues/11377)

### Kafka

Не поддерживает фильтрацию топиков по шаблону.

#### Error: Multiple entries with same key

Столкнулся с ошибкой [Kafka connector with confluent schema registry - java.lang.IllegalArgumentException: Multiple entries with same key · Issue #9989 · trinodb/trino](https://github.com/trinodb/trino/issues/9989)

Эта ошибка связана с тем, что список полей из key и value может пересекаться, особенно, если используется Debezium Scripting и SMT. 

Пока решения нет.

#### Error: Cannot list splits for table

Не парсит данные из топиков ksqlDB.


## Security

Trino supports multiple authentication types to ensure all users of the system are authenticated. Different authenticators allow user management in one or more systems. Using TLS and a configured shared secret are required for all authentications types.

### TLS and HTTPS

To configure Trino with TLS support, consider two alternative paths:

* Use the load balancer or proxy at your site or cloud environment to terminate TLS/HTTPS. This approach is the simplest and strongly preferred solution.
* Secure the Trino server directly. This requires you to obtain a valid certificate, and add it to the Trino coordinator’s configuration.

Configure the coordinator:

```
http-server.https.enabled=true
http-server.https.port=8443
http-server.https.keystore.path=etc/clustercoord.pem
# Пароль для ключа
http-server.https.keystore.key=<keystore-password>
# Пароль для доступа к ключу в keystore
# http-server.https.keymanager.password=<key-password>
```

#### PEM files

PEM (Privacy Enhanced Mail) is a standard for public key and certificate information, and an encoding standard used to transmit keys and certificates.

Trino supports PEM-encoded certificates. If you want to use other supported formats, see:

* JKS keystores
* PKCS 12 stores. (Look up alternate commands for these in openssl references.)

A single PEM-encoded file can contain either certificate or key pair information, or both in the same file. Certified keys can contain a chain of certificates from successive certificate authorities.



Генерация самоподписанного сертификата: 
* https://www.ipswitch.com/blog/how-to-use-openssl-to-generate-certificates
* https://stackoverflow.com/questions/10175812/how-to-generate-a-self-signed-ssl-certificate-using-openssl#10176685

```
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -sha256 -days 365
```
Надо использовать вот эту команду для генерации сертификата без пароля для ключа, так как возникает вот такая ошибка в Trino:

`key manager password is not allowed with a PEM keystore`


```
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:4096 -keyout private.key -out certificate.crt
```
Add `-subj '/CN=localhost'` to suppress questions about the contents of the certificate (replace `localhost` with your desired domain).

```
cat private.key certificate.cert > clustercoord.pem
```

Test an RSA private key’s validity with the following command:

openssl rsa -in clustercoord.pem -check -noout

Analyze the certificate section of your PEM file with the following openssl command:

openssl x509 -in clustercoord.pem -text -noout



### [Secure internal communication — Trino 389 Documentation](https://trino.io/docs/current/security/internal-communication.html)

#### Configure shared secret

Set the shared secret to the same value in _config.properties_ on all nodes of the cluster:

```
internal-communication.shared-secret=<secret>
```

`openssl rand 512 | base64`

### JDBC Driver

Для самоподписанного сертификата файл ключа-сертификата должен быть локально доступен драйверу:  `SSLKeyStorePath`

https://trino.io/docs/current/installation/jdbc.html#connection-parameters


## Trino on Kubernetes

[24: Trinetes I: Trino on Kubernetes Aug 19, 2021](https://trino.io/episodes/24.html)

## Building DataLake

* https://normanlimxk.com/2022/02/19/build-a-data-lake-with-trino-kubernetes-helm-and-glue/

## Cache

* https://github.com/trinodb/trino/issues/13815

Похоже есть кеш в Hive коннекторе, а в общем рекомендуют использовать Alluxio.

Кеш развивается, в том числе на базе решения от Alluxio, надо разобраться.

### Alluxio Edge

[[bigdata.alluxio#Alluxio Edge]]
