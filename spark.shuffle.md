---
id: 29d5h0qn241soqqncjw78me
title: Apache Spark Shuffle Service
desc: ''
updated: 1688982767976
created: 1618474722169
---

# Overview

* https://medium.com/dev-genius/apache-celeborn-shuffle-service-for-spark-32f599c858e5

External shuffle service:

* Spark - Spark shuffle service for YARN
* LinkedIn — Magnet (Donated to Spark)
* Uber — Zeus
* Facebook — Riffle
* Alibaba — RSS (Now Celeborn)

![](/assets/images/2023-07-10-11-52-28.png)

# Configuring the External Shuffle Service

Внешний сервис shuffle необходим при работе Spark в YARN и при динамическом использовании ресурсов кластера (Dynamic Resource Allocation).

(The way to set up the external shuffle service varies across cluster managers)[https://spark.apache.org/docs/latest/job-scheduling.html#configuration-and-setup]:

* In standalone mode, simply start your workers with `spark.shuffle.service.enabled` set to true.
* In Mesos coarse-grained mode, run `$SPARK_HOME/sbin/start-mesos-shuffle-service.sh` on all worker nodes with `spark.shuffle.service.enabled` set to `true`. For instance, you may do so through Marathon.
* In YARN mode, follow the instructions [here](https://spark.apache.org/docs/latest/running-on-yarn.html#configuring-the-external-shuffle-service).

## YARN

[Configuring the External Shuffle Service](https://spark.apache.org/docs/latest/running-on-yarn.html#configuring-the-external-shuffle-service):

To start the Spark Shuffle Service on each NodeManager in your YARN cluster, follow these instructions:

1. Build Spark with the [YARN profile](https://spark.apache.org/docs/latest/building-spark.html). Skip this step if you are using a pre-packaged distribution.
1. Locate the `spark-<version>-yarn-shuffle.jar`. This should be under `$SPARK_HOME/common/network-yarn/target/scala-<version>` if you are building Spark yourself, and under yarn if you are using a distribution.
1. Add this jar to the classpath of all *NodeManagers* in your cluster.
1. In the `yarn-site.xml` on each node, add `spark_shuffle` to `yarn.nodemanager.aux-services`, then set `yarn.nodemanager.aux-services.spark_shuffle.class` to `org.apache.spark.network.yarn.YarnShuffleService`.
1. Increase NodeManager's heap size by setting `YARN_HEAPSIZE`  (1000 by default) in `etc/hadoop/yarn-env.sh` to avoid garbage collection issues during shuffle.
1. Restart all *NodeManagers* in your cluster.

The following extra configuration options are available when the shuffle service is running on YARN:

| Property Name	| Default	| Meaning |
| ------------- | --------- | ------- |
| `spark.yarn.shuffle.stopOnFailure` | `false` | Whether to stop the NodeManager when there's a failure in the Spark Shuffle Service's initialization. This prevents application failures caused by running containers on NodeManagers where the Spark Shuffle Service is not running. |

Похоже для Hadoop 3.2.2 *heap size* необходимо задавать через переменные:
* `YARN_RESOURCEMANAGER_HEAPSIZE`
* `YARN_NODEMANAGER_HEAPSIZE`
