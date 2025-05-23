---
id: 83n9rkdo94w5wvp07jb0p29
title: Errors
desc: Ошибки в работе HDFS
updated: 1631261714145
created: 1631261263016
tags:
  - troubleshooting
  - hadoop
  - hdfs
---

Известные мне ошибки при эксплуатации HDFS и способы их устранения.

## java.net.SocketTimeoutException

* https://stackoverflow.com/questions/39940778/hadoopwhy-hdfs-throw-the-sockettimeoutexception-exception
* https://stackoverflow.com/questions/17438093/sockettimeoutexception-when-running-hadoop-distcp-update-between-clusters

### 10.09.2021 

* Добавил в _hdfs-site.xml_
    ```xml
    <property>
        <name>dfs.datanode.socket.write.timeout</name>
        <value>120000</value>
    </property>

    <property>
        <name>dfs.socket.timeout</name>
        <value>120000</value>
    </property>
    ```
* Увеличил память DataNode: HeapSize до 4G, MaxHeapSize до 8G
