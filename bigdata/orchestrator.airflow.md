---
id: cgnc3y95mnxmr2feejs1o8h
title: Airflow
desc: ''
updated: 1629730613008
created: 1622191155171
---

## Установка

### Docker

## Провайдеры

Список [провайдеров Airflow](https://airflow.apache.org/docs/#providers-packages-docs-apache-airflow-providers-index-html)

### Microsoft SQL Server (MSSQL)

<https://airflow.apache.org/docs/apache-airflow-providers-microsoft-mssql/stable/index.html>

MsSqlOperator

* http://www.datumbucle.com/post/mssqloperator/

### Apache Spark

<https://airflow.apache.org/docs/apache-airflow-providers-apache-spark/stable/index.html>

<https://spark.apache.org/docs/latest/submitting-applications.html>

<https://stackoverflow.com/questions/39827804/how-to-run-spark-code-in-airflow>

[Using Airflow to Schedule Spark Jobs](https://medium.com/swlh/using-airflow-to-schedule-spark-jobs-811becf3a960)

## Примеры DAG

### Динамическая генерация DAG

Хорошая статья вот тут <https://www.cloudwalker.io/2020/12/21/airflow-dynamic-tasks/>

### Инкрементальный ETL для MS SQL Server

[Let's build an incremental ETL pipeline](https://akocukcu.github.io/incremental-etl-pipeline-airflow.html)

### ETL на Airflow

[Apache Airflow: делаем ETL проще](https://3-info.ru/post/18743)

## Масштабирование Airflow

### Celery

Для Celery используют либо RabbitMQ, либо  Redis.

[Airflow – Scale-out with Redis and Celery](https://www.cloudwalker.io/2019/09/30/airflow-scale-out-with-redis-and-celery/):

> Redis seems to be a better solution when compared to rabbitmq. On the whole, it is a lot easier to deploy and maintain when compared with the various steps taken to deploy a RabbitMQ broker. In a nutshell, I like it more than RabbitMQ!

### DASK

## Мои ошибки

### `airflow.exceptions.AirflowException: Task is missing the start_date parameter`

```sh
Broken DAG: [/var/lib/airflow/current/dags/vacuum_cdi_spark.py] Traceback (most recent call last):
  File "/opt/airflow/2.1.0/lib/python3.6/site-packages/airflow/models/baseoperator.py", line 774, in dag
    dag.add_task(self)
  File "/opt/airflow/2.1.0/lib/python3.6/site-packages/airflow/models/dag.py", line 1603, in add_task
    raise AirflowException("Task is missing the start_date parameter")
airflow.exceptions.AirflowException: Task is missing the start_date parameter
```
i
Причина: при динамической генерации задача не привязана к DAG. <https://stackoverflow.com/questions/61749480/dag-py-raises-airflow-exceptions-airflowexception-task-is-missing-the-start-d>

## Разработка

### Отправка pull-request

Отправил [первый pull-request](https://github.com/apache/airflow/blob/main/CONTRIBUTING.rst#coding-style-and-best-practices) в проект Airflow.

Получил вот такие советы от робота:

> Congratulations on your first Pull Request and welcome to the Apache Airflow community! If you have any issues or are unsure about any anything please check our Contribution Guide (https://github.com/apache/airflow/blob/main/CONTRIBUTING.rst)
>   
> Here are some useful points:
>
> * Pay attention to the quality of your code (flake8, mypy and type annotations). Our [pre-commits](https://github.com/apache/airflow/blob/main/STATIC_CODE_CHECKS.rst#prerequisites-for-pre-commit-hooks) will help you with that.
> * In case of a new feature add useful documentation (in docstrings or in docs/ directory). Adding a new operator? Check this short [guide](https://github.com/apache/airflow/blob/main/docs/apache-airflow/howto/custom-operator.rst) Consider adding an example DAG that shows how users should use it.
> * Consider using [Breeze](https://github.com/apache/airflow/blob/main/BREEZE.rst) environment for testing locally, it’s a heavy docker but it ships with a working Airflow and a lot of integrations.
> * Be patient and persistent. It might take some time to get a review or get the final approval from Committers.
> * Please follow [ASF Code of Conduct](https://www.apache.org/foundation/policies/conduct) for all communication including (but not limited to) comments on Pull Requests, Mailing list and Slack.
> * Be sure to read the [Airflow Coding style](https://github.com/apache/airflow/blob/main/CONTRIBUTING.rst#coding-style-and-best-practices).
>
> Apache Airflow is a community-driven project and together we are making it better rocket.
In case of doubts contact the developers at:
> 
> Mailing List: dev@airflow.apache.org
> 
> Slack: https://s.apache.org/airflow-slack

