---
id: 7u87ijw0af8j0hyynxoj6vj
title: Spark Operator for Kubernetes
desc: ''
updated: 1663231318256
created: 1663229051788
---

# Spark Operator

Оказывается, что есть несколько реализаций Spark Operator:

* https://github.com/radanalyticsio/spark-operator
* https://github.com/mesosphere/kudo-spark-operator - реализация на фреймворке KUDO
* https://github.com/canonical/spark-operator - реализация от Canonical
* https://github.com/GoogleCloudPlatform/spark-on-k8s-operator - **реализация для GCP, похоже эту реализацию больше всего и используют**

* [Как и зачем разворачивать приложение на Apache Spark в Kubernetes](https://habr.com/ru/company/vk/blog/549052/)
* [Running a spark job using spark on k8s operator | by Arthur Cesarino | Medium](https://medium.com/@contatocesarino/running-a-spark-job-using-spark-on-k8s-operator-12e5c8336fa8)
* [My Journey With Spark On Kubernetes... In Python](https://www.pascalgillet.net/posts/spark-k8s-0/)
* https://github.com/GoogleCloudPlatform/spark-on-k8s-operator

[Матрица версий](https://github.com/GoogleCloudPlatform/spark-on-k8s-operator#version-matrix). На 09.2022 доступен Spark 3.1.1, для более новых версий необходимо собирать свой образ.

При использовании Spark Operator рекомендуется установить специальный batch scheduler Volcano:

* https://github.com/volcano-sh/volcano
* https://github.com/TommyLike/spark-operator-volcano-demo
* https://github.com/GoogleCloudPlatform/spark-on-k8s-operator/blob/master/docs/volcano-integration.md

# Google Spark Operator

<https://github.com/GoogleCloudPlatform/spark-on-k8s-operator>

## Документация

* [Kubernetes Operator for Apache Spark Design](https://github.com/GoogleCloudPlatform/spark-on-k8s-operator/blob/master/docs/design.md)
* [User Guide](https://github.com/GoogleCloudPlatform/spark-on-k8s-operator/blob/master/docs/user-guide.md)
* [Реестр базовых образов](https://console.cloud.google.com/gcr/images/spark-operator)


As with all other Kubernetes API objects, a `SparkApplication` needs the `apiVersion`, `kind`, and `metadata` fields. For general information about working with manifests, see [object management using kubectl](https://kubernetes.io/docs/concepts/overview/object-management-kubectl/overview/).

## SparkCtl

* [sparkctl](https://github.com/GoogleCloudPlatform/spark-on-k8s-operator/blob/master/sparkctl/README.md)


## UserGuide

* https://github.com/GoogleCloudPlatform/spark-on-k8s-operator/blob/master/docs/user-guide.md

## Specifying Application Dependencies

* https://github.com/GoogleCloudPlatform/spark-on-k8s-operator/blob/master/docs/user-guide.md#specifying-application-dependencies

```
spec:
  deps:
    jars:
      - local:///opt/spark-jars/gcs-connector.jar
    files:
      - gs://spark-data/data-file-1.txt
      - gs://spark-data/data-file-2.txt
```

```
spec:
  deps:
    repositories:
      - https://repository.example.com/prod
    packages:
      - com.example:some-package:1.0.0
    excludePackages:
      - com.example:other-package
```

https://github.com/GoogleCloudPlatform/spark-on-k8s-operator/blob/master/docs/user-guide.md#specifying-spark-configuration

There are two ways to add Spark configuration: setting individual Spark configuration properties using the optional field .spec.sparkConf or mounting a special Kubernetes ConfigMap storing Spark configuration files (e.g. spark-defaults.conf, spark-env.sh, log4j.properties) using the optional field .spec.sparkConfigMap. If .spec.sparkConfigMap is used, additionally to mounting the ConfigMap into the driver and executors, the operator additionally sets the environment variable SPARK_CONF_DIR to point to the mount path of the ConfigMap.

spec:
  sparkConf:
    spark.ui.port: "4045"
    spark.eventLog.enabled: "true"
    spark.eventLog.dir": "hdfs://hdfs-namenode-1:8020/spark/spark-events"

https://github.com/GoogleCloudPlatform/spark-on-k8s-operator/blob/master/docs/user-guide.md#specifying-hadoop-configuration

There are two ways to add Hadoop configuration: setting individual Hadoop configuration properties using the optional field .spec.hadoopConf or mounting a special Kubernetes ConfigMap storing Hadoop configuration files (e.g. core-site.xml) using the optional field .spec.hadoopConfigMap. The operator automatically adds the prefix spark.hadoop. to the names of individual Hadoop configuration properties in .spec.hadoopConf. If .spec.hadoopConfigMap is used, additionally to mounting the ConfigMap into the driver and executors, the operator additionally sets the environment variable HADOOP_CONF_DIR to point to the mount path of the ConfigMap.

The following is an example showing the use of individual Hadoop configuration properties:

spec:
  hadoopConf:
    "fs.gs.project.id": spark
    "fs.gs.system.bucket": spark
    "google.cloud.auth.service.account.enable": true
    "google.cloud.auth.service.account.json.keyfile": /mnt/secrets/key.json

* [spark-on-k8s-operator/user-guide.md at master · GoogleCloudPlatform/spark-on-k8s-operator · GitHub](https://github.com/GoogleCloudPlatform/spark-on-k8s-operator/blob/master/docs/user-guide.md#specifying-hadoop-configuration)
    * Mounting a ConfigMap storing Spark Configuration Files
    * A `SparkApplication` can specify a Kubernetes ConfigMap storing Spark configuration files such as `spark-env.sh` or `spark-defaults.conf` using the optional field `.spec.sparkConfigMap` whose value is the name of the ConfigMap. The ConfigMap is assumed to be in the same namespace as that of the `SparkApplication`. The operator mounts the ConfigMap onto path `/etc/spark/conf` in both the driver and executors. Additionally, it also sets the environment variable `SPARK_CONF_DIR` to point to `/etc/spark/conf` in the driver and executors.
    * Note that the mutating admission webhook is needed to use this feature. Please refer to the [Quick Start Guide](https://github.com/GoogleCloudPlatform/spark-on-k8s-operator/blob/master/docs/quick-start-guide.md) on how to enable the mutating admission webhook.
    * Mounting a ConfigMap storing Hadoop Configuration Files
    * A `SparkApplication` can specify a Kubernetes ConfigMap storing Hadoop configuration files such as `core-site.xml` using the optional field `.spec.hadoopConfigMap` whose value is the name of the ConfigMap. The ConfigMap is assumed to be in the same namespace as that of the `SparkApplication`. The operator mounts the ConfigMap onto path `/etc/hadoop/conf` in both the driver and executors. Additionally, it also sets the environment variable `HADOOP_CONF_DIR` to point to `/etc/hadoop/conf` in the driver and executors.
    * Note that the mutating admission webhook is needed to use this feature. Please refer to the [Quick Start Guide](https://github.com/GoogleCloudPlatform/spark-on-k8s-operator/blob/master/docs/quick-start-guide.md) on how to enable the mutating admission webhook.
    
    https://github.com/GoogleCloudPlatform/spark-on-k8s-operator/blob/master/docs/user-guide.md#using-volume-for-scratch-space

    Using Volume For Scratch Space

By default, Spark uses temporary scratch space to spill data to disk during shuffles and other operations. The scratch directory defaults to /tmp of the container. If that storage isn't enough or you want to use a specific path, you can use one or more volumes. The volume names should start with spark-local-dir-.

spec:
  volumes:
    - name: "spark-local-dir-1"
      hostPath:
        path: "/tmp/spark-local-dir"
  executor:
    volumeMounts:
      - name: "spark-local-dir-1"
        mountPath: "/tmp/spark-local-dir"
    ...

Then you will get SPARK_LOCAL_DIRS set to /tmp/spark-local-dir in the pod like below.

Environment:
  SPARK_USER:                 root
  SPARK_DRIVER_BIND_ADDRESS:  (v1:status.podIP)
  SPARK_LOCAL_DIRS:           /tmp/spark-local-dir
  SPARK_CONF_DIR:             /opt/spark/conf
 
  https://github.com/GoogleCloudPlatform/spark-on-k8s-operator/blob/master/docs/user-guide.md#dynamic-allocation
 
    * Dynamic Allocation
        * The operator supports a limited form of [Spark Dynamic Resource Allocation](http://spark.apache.org/docs/latest/job-scheduling.html#dynamic-resource-allocation) through the shuffle tracking enhancement introduced in Spark 3.0.0 __without needing an external shuffle service__ (not available in the Kubernetes mode). See this [issue](https://issues.apache.org/jira/browse/SPARK-27963) for details on the enhancement. To enable this limited form of dynamic allocation, follow the example below:
        * spec
        * :
        * dynamicAllocation
        * :
        * enabled
        * :
        * true
        * initialExecutors
        * :
        * 2
        * minExecutors
        * :
        * 2
        * maxExecutors
        * :
        * 10
        * Note that if dynamic allocation is enabled, the number of executors to request initially is set to the bigger of `.spec.dynamicAllocation.initialExecutors` and `.spec.executor.instances` if both are set.