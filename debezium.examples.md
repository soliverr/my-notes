---
id: b530ywd4ik2ugr6ywz2ey1n
title: Debezium Examples
desc: ''
updated: 1659352000676
created: 1631004294350
---

https://github.com/debezium/debezium-examples


Репозиторий с примерами использования Debezium в различных конфигурациях.

# Tutorial

Курс обучения

# UI

Пример развёртывания Web-интерфейса для администрирования Debezium: 

* https://github.com/debezium/debezium-examples/tree/master/ui-demo
* [[debezium.ui]]

1. Необходимо задать версию Debezium через переменную среды `DEBEZIUM_VERSION`: 
    ```sh
    export DEBEZIUM_VERSION=1.7
    ```
1. Получить свежий репозитарий c примерами
    ```sh
    $ git clone git@github.com:debezium/debezium-examples.git
    $ cd debezium-examples\ui-demo
    ```
1. Запустить пример
    ```sh
    docker-compose up -d

    Creating db-mysql  ... done
    Creating db-pg     ... done
    Creating zookeeper ... done
    Creating db-mongo  ... done
    Creating ui-demo_mongo-initializer_1 ... done
    Creating kafka     ... done
    Creating connect   ... done
    Creating debezium-ui   ... done
    ```
1. Подключится к Web UI: http://localhost:8080

# Mongo

Скрипт создания пользователя Debezium в MongoDB. Взял из образа mongodb из репозитория примеров.

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
            { role: "readWrite", db: "inventory" },
            { role: "read", db: "local" },
            { role: "listDatabases", db: "admin" },
            { role: "readChangeStream", db: "admin" },
            { role: "read", db: "config" },
            { role: "read", db: "admin" }
        ]
    });
```