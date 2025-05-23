---
id: 0oi1l8tx06ox7m7hf9944z7
title: Examples
desc: ''
updated: 1683207415576
created: 1682774672744
---

# Виртуальное окружение

см. [[orchestrator.dagster#installation]]

# Tutorial

* https://docs.dagster.io/tutorial
* https://github.com/dagster-io/dagster/tree/master/examples

## Creating Dagster project

```sh
(dagster)$ dagster project scaffold --name tutorial-project
(dagster)$ cd tutorial-project
(dagster)$ dagster dev
```

## Hello Dagster

* https://docs.dagster.io/getting-started/hello-dagster#hello-dagster

**Добавить pandas**

```
pip install pandas
```

**Создать скрипт** `hello-dagster.py`.

**Запустить скрипт** 

```
# run in a terminal in your favorite python environment
dagit -f hello-dagster.py
```

**Открыть в браузере ссылку** http://localhost:3000/

# Dagster examples

```sh
$ dagster project list-examples
```

## Dynamic partition Example

* https://github.com/dagster-io/dagster/tree/master/examples/assets_dynamic_partitions

```sh
$ dagster project from-example --name assets-dynamic-partitions --example assets_dynamic_partitions
Downloading example 'assets_dynamic_partitions'. This may take a while.

$ cd assets-dynamic-partitions
$ pip install -e ".[dev]"
$ dagster dev
```

Необходимо сконфигурировать доступ к GitHub через переменные среды:

* `GITHUB_USER_NAME`
* `GITHUB_ACCESS_TOKEN` - [instructions on how to make one](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)

## Graph-backed assets Example

* https://github.com/dagster-io/dagster/tree/master/examples/feature_graph_backed_assets

```sh
$ dagster project from-example --name graph-backed-assets --example feature_graph_backed_assets
$ cd graph-backed-assets
$ pip install -e ".[dev]"
$ dagster dev
```


## Hacker News Example

* https://github.com/dagster-io/dagster/tree/master/examples/project_fully_featured

