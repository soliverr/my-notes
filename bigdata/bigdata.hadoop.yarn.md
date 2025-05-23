---
id: 9k9qzjnoratnavogfqt9k57
title: Yarn
desc: ""
updated: 1651729469209
created: 1649404153481
tags:
  - hadoop
  - hdfs
  - data-management
  - yarn
---

# Конфигурация YARN

[YARN Default parameters](https://hadoop.apache.org/docs/r3.2.3/hadoop-yarn/hadoop-yarn-common/yarn-default.xml)

## Дисковое пространство на узлах

* yarn.nodemanager.disk-health-checker.disk-utilization-threshold.enabled: "false"
* yarn.nodemanager.disk-health-checker.max-disk-utilization-per-disk-percentage: 98
* yarn.nodemanager.disk-health-checker.disk-utilization-watermark-low-per-disk-percentage: 95
* yarn.nodemanager.disk-health-checker.disk-free-space-threshold.enabled: "true"
* yarn.nodemanager.disk-health-checker.min-free-space-per-disk-mb: 4096
* yarn.nodemanager.disk-health-checker.min-free-space-per-disk-watermark-high-mb: 8192

## Дисковый кеш на узлах

https://aws.amazon.com/ru/premiumsupport/knowledge-center/user-cache-disk-space-emr/

* yarn.nodemanager.localizer.cache.cleanup.interval-ms: 400000
* yarn.nodemanager.localizer.cache.target-size-mb: 5120


## Application Catalog

Появился в версии Hadoop 3.3.2

https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/yarn-service/Examples.html#Application_Catalog_-_appcatalog

## Интеграции с YARN

### DASK в YARN

https://yarn.dask.org/en/latest/quickstart.html

### Docker в YARN

https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/DockerContainers.html
