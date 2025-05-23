---
id: a0a81r08hdsokao9lhge361
title: Celery for Airflow
desc: Scale-out Airflow with Redis and Celery
updated: 1630476862345
created: 1630394462846
---

## Redis и Celery

[Setting Up Apache Airflow Celery Executor Cluster](https://wang-kuanchih.medium.com/setting-up-apache-airflow-celery-executor-cluster-a76dd49b4968):

![](/assets/images/2021-08-31-12-31-52.png)

[Airflow – Scale-out with Redis and Celery](https://www.cloudwalker.io/2019/09/30/airflow-scale-out-with-redis-and-celery/):

To create an infrastructure like this we need to do the following steps

1. Install & Configure Redis server on a separate host – 1 server
1. Install & Configure Airflow with Redis and Celery Executor support – 4 servers
1. Start airflow scheduler & webserver
1. Start airflow workers
1. Start flower UI
1. Run a DAG
1. Watch it all happen!

_Оказалось, что надо обеспечить доступность DAG на всех узлах Airflow Celery Worker._ Необходимо настроить [[DAG Serialization|airflow.dag#dag-serialization]].
