---
id: z9e1k0wizo9smsm1ujq4984
title: Airflow High Availability
desc: Support for HA in Airflow 2.x
updated: 1630482821061
created: 1630476318371
---

## High Availability in Scheduler

* [The Airflow 2.0 Scheduler](https://www.astronomer.io/blog/airflow-2-scheduler)

* [Running More Than One Scheduler](https://airflow.apache.org/docs/apache-airflow/stable/concepts/scheduler.html#running-more-than-one-scheduler)

> The short version is that users of PostgreSQL 9.6+ or MySQL 8+ are all ready to go -- you can start running as many copies of the scheduler as you like -- there is no further set up or config options needed. If you are using a different database please read on.

## Deploy DAGs in cluster

[Production Deployment in Multi-Node Cluster](https://airflow.apache.org/docs/apache-airflow/stable/production-deployment.html#multi-node-cluster)

> Once you have configured the executor, it is necessary to make sure that every node in the cluster contains the same configuration and dags. Airflow sends simple instructions such as "execute task X of dag Y", but does not send any dag files or configuration. You can use a simple cronjob or any other mechanism to sync DAGs and configs across your nodes, e.g., checkout DAGs from git repo every 5 minutes on all nodes.


* DAG Serialization - для синхронизации WebServer

Сами DAG должны быть доступны на всех Worker нодах

https://stackoverflow.com/questions/48879920/airflow-worker-configuration

* https://galaxy.ansible.com/oefenweb/rsync_sync
* https://galaxy.ansible.com/infopen/rsync-server
