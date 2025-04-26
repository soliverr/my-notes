---
id: ittd270kw16e9p4i7kpmuim
title: Snapshots
desc: ''
updated: 1631085494599
created: 1630321563599
---

## Виды снапшотов

_Поддреживаются не всеми коннекторами Debezium._

Тип снапшота указан в поле `source.snapshot`:

* "true" - Начальный снапшот (initial)
* "false" - Данные обновления 
* "last" - Последняя строка в снапшоте
* "incremental" - инкрементальный снапшот

### Initial snapshot

https://debezium.io/documentation/reference/connectors/mysql.html#mysql-snapshots

### Ad-hoc snapshot

https://debezium.io/documentation/reference/connectors/mysql.html#_ad_hoc_snapshot

### Incremental snapshot 

https://debezium.io/documentation/reference/connectors/mysql.html#_incremental_snapshot


### Преобразование операции при снапшоте

Преобразование операции `r` в `c` во время выполнения снапшота.

https://debezium.io/documentation/reference/connectors/mysql.html#mysql-snapshot-events

```
transforms=snapshotasinsert,...
transforms.snapshotasinsert.type=io.debezium.connector.mysql.transforms.ReadToInsertEvent
```


### Signaling Table

* [Signaling Table](https://debezium.io/blog/2021/03/15/debezium-1-5-beta2-released/)
* [Sending signals to a Debezium connector](https://debezium.io/documentation/reference/1.6/configuration/signalling.html)

```sql
CREATE TABLE debezium_signal (id VARCHAR(32) PRIMARY KEY, type VARCHAR(32) NOT NULL, data VARCHAR(2048) NULL);
```

`signal.data.collection` - указание на данную таблицу в БД

 Эта таблица должна присутствовать в `table.include.list`.

 С версии 1.6 работает ввод в лог при вставке в эту таблицу:

 ```sql
 INSERT INTO cdi.debezium_signal (id,`type`,`data`)
	VALUES ('7','log','{"message": "Signal message at offset {}"}');
```

```sh

```

С версии 1.7 работает инкрементальный снапшот:

```sql
INSERT INTO cdi.debezium_signal (id,`type`,`data`)
	VALUES ('8','execute-snapshot','{"data-collections": ["cdi.account"]}');
```

Но, если попросить данные из таблицы, которая не существует в `table.include.list`, то коннектор падает:

```sh
2021-09-07 15:26:05,123 INFO   MySQL|c-t-cdi-sql-db-1|binlog  Requested 'INCREMENTAL' snapshot of data collections '[cdi.account]'   [io.debezium.pipeline.signal.ExecuteSnapshot]
2021-09-07 15:26:05,166 ERROR  MySQL|c-t-cdi-sql-db-1|binlog  Error during binlog processing. Last offset stored = null, binlog reader near position = binlog.000458/585837826   [io.debezium.connector.mysql.MySqlStreamingChangeEventSource]
2021-09-07 15:26:05,166 ERROR  MySQL|c-t-cdi-sql-db-1|binlog  Producer failure   [io.debezium.pipeline.ErrorHandler]
io.debezium.DebeziumException: Error processing binlog event
...
2021-09-07 15:26:05,263 INFO   ||  Stopping down connector   [io.debezium.connector.common.BaseSourceTask]
```

И будет падать, пока эта строка есть в истории изменений БД, но не произведен первоначальный снапшот этой таблицы.
