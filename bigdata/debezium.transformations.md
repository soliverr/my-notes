---
id: 5q154si8j11tqpvqzhmsnuv
title: Transformations
desc: ''
updated: 1664192324449
created: 1662554804471
---

# Debezium Transformations

Need to enable Debezium Scripting `ENABLE_DEBEZIUM_SRCIPTING='true'` при запуске образа debezium/connect. _Файл Debezium Scripting уже включен в образ Debezium_.

## Event Flattening

https://debezium.io/documentation/reference/stable/transformations/event-flattening.html

Смысл этого преобразования - получить возможность применять стандартные трансформации Kafka Connect для потока CDC Debezium. 
 
* обрабатывать события Delete, где данные приходят в `before`
* сохранять Thumbstone, так как это помогает Kafka чистить топики (в режиме compact ?)

```
transforms=unwrap,...
transforms.unwrap.type=io.debezium.transforms.ExtractNewRecordState
transforms.unwrap.drop.tombstones=false
transforms.unwrap.delete.handling.mode=rewrite
transforms.unwrap.add.fields=table,lsn
```



drop.tombstones=false

    Keeps tombstone records for DELETE operations in the event stream.
delete.handling.mode=rewrite

    For DELETE operations, edits the Kafka record by flattening the value field that was in the change event. The value field directly contains the key/value pairs that were in the before field. The SMT adds __deleted and sets it to true, for example:

    "value": {
      "pk": 2,
      "cola": null,
      "__deleted": "true"
    }

add.fields=table,lsn

    Adds change event metadata for the table and lsn fields to the simplified Kafka record.



To add metadata to the simplified Kafka record’s header, specify the add.headers option. To add metadata to the simplified Kafka record’s value, specify the add.fields option. Each of these options takes a comma separated list of change event field names. Do not specify spaces. When there are duplicate field names, to add metadata for one of those fields, specify the struct as well as the field. For example:

transforms=unwrap,...
transforms.unwrap.type=io.debezium.transforms.ExtractNewRecordState
transforms.unwrap.add.fields=op,table,lsn,source.ts_ms
transforms.unwrap.add.headers=db
transforms.unwrap.delete.handling.mode=rewrite

With that configuration, a simplified Kafka record would contain something like the following:

{
 ...
	"__op" : "c",
	"__table": "MY_TABLE",
	"__lsn": "123456789",
	"__source_ts_ms" : "123456789",
 ...
}

Also, simplified Kafka records would have a __db header.

In the simplified Kafka record, the SMT prefixes the metadata field names with a double underscore. When you specify a struct, the SMT also inserts an underscore between the struct name and the field name.

To add metadata to a simplified Kafka record that is for a DELETE operation, you must also configure delete.handling.mode=rewrite.

## Content-based routing

* [Content-based routing :: Debezium Documentation](https://debezium.io/documentation/reference/stable/transformations/content-based-routing.html)

Для работы необходима установка языка: Groovy, JavaScript, JavaScript Graal.js и скриптовой связки JSR 223.

## JSR 223

Вот тут https://github.com/Trendyol/debezium-with-smt нашёл пример установки Groovy и JSR 223. Необходимо скачать следующие архивы, только с более свежей версией:

```
- $PWD/jars/debezium-scripting-1.4.0.Alpha2.jar:/kafka/connect/debezium-connector-postgres/debezium-scripting-1.4.0.Alpha2.jar
- $PWD/jars/groovy-3.0.6.jar:/kafka/connect/debezium-connector-postgres/groovy-3.0.6.jar
- $PWD/jars/groovy-json-3.0.6.jar:/kafka/connect/debezium-connector-postgres/groovy-json-3.0.6.jar
- $PWD/jars/groovy-jsr223-3.0.6.jar:/kafka/connect/debezium-connector-postgres/groovy-jsr223-3.0.6.jar
```

