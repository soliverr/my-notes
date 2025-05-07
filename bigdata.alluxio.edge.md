---
id: 5mgtz1t689etf7xrdnx569i
title: Alluxio Edge
desc: Proprietary caching solution for Trino/Presto from Alluxio
updated: 1706641794691
created: 1703082739898
tags:
  - trino
  - alluxio/edge
---
# [Alluxio Edge](https://documentation.alluxio.io/ee-edge)

**Alluxio Edge** is a lightweight fast data access caching library for Trino and PrestoDB.

Request a trial version of Alluxio Edge. Contact your Alluxio account representative at `sales@alluxio.com` to request a trial version of Alluxio Edge. Follow their instructions to download the installation tar file into the directory you prepared.

## Current version

Currently I  use a `edge-1.1-6.0.0` version. There exists a `edge-1.1-6.0.3` version, but it has some bugs and Alluxio support recommends me to downgrade to `edge-1.1-6.0.0` version.

## Installation

### Official way

[Official instructions](https://documentation.alluxio.io/ee-edge/installation)

### My way

This instructions I had from Greg Palmer, an Alluxio's engineer who helped me to spin up Trino's cluster with Alluxio Edge. The instructions is not up-do-date, but I keep it here for history and memorize that old good time 😏

Here is the Alluxio Edge for Trino download and instructions for building a Docker image with Trino and Alluxio Edge. Please reach out if you have any questions or concerns.

#### Download the source

Please download the TAR file from Greg's public bucket, like this:

     wget https://greg-palmer-alluxio-public-bucket.s3.amazonaws.com/installers/alluxio-edge-304-SNAPSHOT-jars.tar

Then follow Step 3 through Step 5 in the following Git repo to configure and build your Docker image:

https://github.com/gregpalmr/alluxio-trino-edge-cache
 
#### Step 1: Update the Dockerfile file

In Dockerfile, make sure the following files are created or copied, using the these examples:

```
ARG ALLUXIO_VERSION=304-SNAPSHOT
ARG JMX_PROMETHEUS_AGENT_VERSION=0.20.0
```
Remove old versions of Alluxio jar files from the container

```
RUN find /usr/lib/trino -name alluxio*shaded* -exec rm {} \;
```

Copy the Alluxio Edge client jar file to the Trino catalog dirs

```
COPY jars/alluxio-emon-${ALLUXIO_VERSION}-client.jar /usr/lib/trino/plugin/hive
COPY jars/alluxio-emon-${ALLUXIO_VERSION}-client.jar /usr/lib/trino/plugin/hudi
COPY jars/alluxio-emon-${ALLUXIO_VERSION}-client.jar /usr/lib/trino/plugin/delta-lake  
COPY jars/alluxio-emon-${ALLUXIO_VERSION}-client.jar /usr/lib/trino/plugin/iceberg
```
Make a home directory for Alluxio Edge

```
RUN mkdir -p /home/trino/alluxio/lib && mkdir -p /home/trino/alluxio/conf
```
Copy the Alluxio Edge under store jar file to the Trino lib dir

```
COPY jars/alluxio-underfs-emon-s3a-${ALLUXIO_VERSION}.jar /home/trino/alluxio/lib
```
Create a metrics property file that enables the Alluxio JmxSink

```
COPY <<EOF /home/trino/alluxio/conf/metrics.properties
sink.jmx.class=alluxio.metrics.sink.JmxSink
EOF
```
Copy the JVX Prometheus agent jar file to the Alluxio lib dir

```
COPY jars/jmx_prometheus_javaagent-${JMX_PROMETHEUS_AGENT_VERSION}.jar /home/trino/alluxio/lib
```
Create a JMX export configuration file
```
COPY <<EOF /etc/trino/jmx_export_config.yaml
---
startDelaySeconds: 0
ssl: false
global:
  scrape_interval:     15s
  evaluation_interval: 15s
rules:
- pattern: ".*"
EOF
```

#### Step 2: Update the Trino helm chart's values_preprod.yaml file

In the Trino helm chart's *values_preprod.yaml* file, I changed the following sections:

`additionalConfigFiles`:
```
  ...

    core-site.xml: |
      <?xml version="1.0"?>
      <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
      
      <!--
          FILE: core-site.xml
          
          DESC: This is the Alluxio Edge core-site.xml file and should be
                placed in: /etc/trino/
      -->
      <configuration>
        <property>
          <name>fs.s3.impl</name>
          <value>alluxio.emon.hadoop.FileSystemEE</value>
        </property>
        <property>
          <name>fs.s3a.impl</name>
          <value>alluxio.emon.hadoop.FileSystemEE</value>
        </property>
      </configuration>
```

`additionalConfigFiles`:
```
  ...

    alluxio-site.properties: |
      #
      # Alluxio under file system setup (S3)
      #
      s3a.accessKeyId=<PUT_YOUR_AWS_ACCESS_KEY_ID_HERE>
      s3a.secretKey=<PUT_YOUR_AWS_SECRET_KEY_HERE>
      alluxio.underfs.s3.region=eu-central-1
      #alluxio.underfs.s3.inherit.acl=false
      #alluxio.underfs.s3.disable.dns.buckets=true
      #
      # Enable Alluxio Edge cache on Trino nodes (example with 2 SSD volumes)
      #
      alluxio.user.client.cache.enabled=true
      alluxio.user.client.cache.size=<PUT_YOUR_SSD1_VOL_SIZE_HERE>GB,<PUT_YOUR_SSD2_VOL_SIZE_HERE>GB
      alluxio.user.client.cache.dirs=<PUT_YOUR_SSD1_VOL_MOUNT_POINT_HERE>/alluxio_cache,/<PUT_YOUR_SSD2_VOL_MOUNT_POINT_HERE>/alluxio_cache
      #
      # Enable edge metrics collection
      alluxio.user.metrics.collection.enabled=true
      #
      # Disable DORA
      alluxio.dora.enabled=false
      #
```

`additionalConfigFiles`:
  ```
...

    jmx_export_config.yaml: |
      ---
      startDelaySeconds: 0
      ssl: false
      global:
        scrape_interval:     15s
        evaluation_interval: 15s
      rules:
      - pattern: ".*"
```

`additionalJVMConfig`:
```
    - "-XX:+UnlockDiagnosticVMOptions"
    - "-XX:+UseAESCTRIntrinsics"
    # Add following two lines to Enable Alluxio Edge integration
    - "-Dalluxio.home=/home/trino/alluxio"
    - "-Dalluxio.conf.dir=/etc/trino/"
    # Add following two lines to enable Alluxio Edge JMX/Prometheus export support
    -Dalluxio.metrics.conf.file=/home/trino/alluxio/conf/metrics.properties
    -javaagent:/home/trino/alluxio/lib/jmx_prometheus_javaagent-0.20.0.jar=9696:/etc/trino/jmx_export_config.yaml
```
  
  **Delta LakeHouse**
  
  ```
lakehouse: |
    connector.name=delta_lake
    hive.metastore.uri=thrift://hive-metastore:9083
    #hive.s3.endpoint=s3.eu-central-1.amazonaws.com
    #hive.s3.aws-access-key=<key>
    #hive.s3.aws-secret-key=<secret>
    #hive.s3.path-style-access=true
    hive.config.resources=/etc/trino/core-site.xml
    hive.s3-file-system-type=HADOOP_DEFAULT
    delta.hive-catalog-name=lakehouse
    delta.enable-non-concurrent-writes=true
    delta.vacuum.min-retention=1h
    delta.register-table-procedure.enabled=true
    # Parameter required for creating external tables with data
    delta.legacy-create-table-with-existing-location.enabled=true
    # Only for AWS Glue catalog
    # delta.hide-non-delta-lake-tables=true
```

#### Step 3: Additional configs

> Sergey,
> 
> If you see the same errors tomorrow, we suggest that you configure Alluxio Edge to filter what Delta Lake metadata files get cached.
> 
> Modify your Trino Helm chart to modify the Alluxio Edge alluxio-site.properties file and have it create a new file called cache_filter.properties.
> 
> Here are the overview instructions:

Configure Alluxio Edge to filter the delta lake metadata files that it caches

* Step 1: Enable the Alluxio cache filter by adding the two properties below into the file: 
```
    /etc/trino/alluxio-site.properties
    alluxio.user.client.cache.filter.enabled=true
    alluxio.user.client.cache.filter.class=alluxio.client.file.cache.filter.PatternBasedCacheFilter
```
* Step 2: Configure the Alluxio Edge blacklist policy in the file:
 ```
   /etc/trino/cache_filter.properties
    {
      "filterType": "BLOCK_LIST",
      "regxPatternStrList": [".*_delta_log.*"]
    }
```

## Problem with Trino versions above 455

### fs.native-s3.enabled=true

It was hard to figure out the root issue of the error when native s3 API was activated.

The error is:

`​java.lang.IllegalArgumentException: Unsupported class file major version 66`

It is mean there is an error with some loaded class in JVM that exceed allowed major version for given Java version.

The root of the issue is wrong parameters to access to the S3 service.

Correct parameters should be:

```sh
fs.native-s3.enabled=true
s3.endpoint=https://s3.eu-central-1.amazonaws.com
s3.region=eu-central-1
s3.path-style-access=true
s3.aws-access-key=<access key>
s3.aws-secret-key=<secret key>
```

📝 **Important:**
* prefix `https://` is absolutely required for given endpoint
* parameter  `s3.endpoint` should include AWS region
* use dedicated parameter  `s3.region` to point AWS region twice

✅ After that Trino is up and running with S3 DFS normally.

### fs.native-s3.enabled=false

Starting from version 457 Trino can't update Delta-tables using legacy S3 API.

This mode is turned on by the next parameters:

```sh
fs.native-s3.enabled=false
fs.hadoop.enabled=true
```

If these parameters turned on, Trino can **create and delete delta tables**, but **can't update/insert/delete rows into delta tables**. 

The error is:

```
SQL Error [84279299]: Query failed (#20250327_133009_00022_8thne): Failed to write Delta Lake transaction log entry  
Query failed (#20250327_133009_00022_8thne): Failed to write Delta Lake transaction log entry  
Query failed (#20250327_133009_00022_8thne): Failed to write Delta Lake transaction log entry  
io.trino.spi.TrinoException: Failed to write Delta Lake transaction log entry  
Failed to write Delta Lake transaction log entry  
java.lang.UnsupportedOperationException: createExclusive not supported for alluxio.hadoop.FileSystem@10f1d6d3  
createExclusive not supported for alluxio.hadoop.FileSystem@10f1d6d3
```

It is not the case of `HdfsOutputfile.java` of [version 457]([https://github.com/trinodb/trino/blob/457/lib/trino-hdfs/src/main/java/io/trino/filesystem/hdfs/HdfsOutputFile.java#L80-L86](https://github.com/trinodb/trino/blob/457/lib/trino-hdfs/src/main/java/io/trino/filesystem/hdfs/HdfsOutputFile.java#L80-L86)), because version 455 has [the same piece of code]([https://github.com/trinodb/trino/blob/455/lib/trino-hdfs/src/main/java/io/trino/filesystem/hdfs/HdfsOutputFile.java#L80-L86](https://github.com/trinodb/trino/blob/455/lib/trino-hdfs/src/main/java/io/trino/filesystem/hdfs/HdfsOutputFile.java#L80-L86))

I think issue is deeper inside DeltaLake connector.

For version 455 we have the following [list of files](https://github.com/trinodb/trino/tree/455/plugin/trino-delta-lake/src/main/java/io/trino/plugin/deltalake/transactionlog/writer](https://github.com/trinodb/trino/tree/455/plugin/trino-delta-lake/src/main/java/io/trino/plugin/deltalake/transactionlog/writer). While for version 457 [classes are changed](https://github.com/trinodb/trino/tree/457/plugin/trino-delta-lake/src/main/java/io/trino/plugin/deltalake/transactionlog/writer](https://github.com/trinodb/trino/tree/457/plugin/trino-delta-lake/src/main/java/io/trino/plugin/deltalake/transactionlog/writer). There is new class `S3ConditionalWriteLogSynchronizer.java` instead of `S3NativeTransactionLogSynchronizer.java`.
And 457 source has [this line](https://github.com/trinodb/trino/blob/457/plugin/trino-delta-lake/src/main/java/io/trino/plugin/deltalake/transactionlog/writer/S3ConditionalWriteLogSynchronizer.java#L44](https://github.com/trinodb/trino/blob/457/plugin/trino-delta-lake/src/main/java/io/trino/plugin/deltalake/transactionlog/writer/S3ConditionalWriteLogSynchronizer.java#L44). So, it looks like we **should use new S3 API** activated by `fs.native-s3.enabled=true` and `fs.hadoop.enabled=false`

🚨Seems we stuck to the 455 version of Trino while using the current Alluxio Edge solution for caching.

I've got a confirmation from the Alluxio team at 2025-05-07:

> I checked with my Engineering team — Alluxio Edge is compatible **ONLY** with the Hadoop FileSystem API (`fs.hadoop.enabled=true`)
> 
> *In other words, Trino with Alluxio Edge can access S3 using only the Hadoop FileSystem API*
> 
> Alluxio Edge **is not compatible** with the S3 FileSystem API (`fs.native-s3.enabled=true`), and my Engineering and PM teams tell me there **are currently no plans to support native Trino S3 access**.

