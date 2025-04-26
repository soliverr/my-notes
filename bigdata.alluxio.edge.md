---
id: 5mgtz1t689etf7xrdnx569i
title: Edge
desc: ''
updated: 1706641794691
created: 1703082739898
---

Here is the Alluxio Edge for Trino download and instructions for building a Docker image with Trino and Alluxio Edge. Please reach out if you have any questions or concerns.

Please download the TAR file from Greg's public bucket, like this:

     wget https://greg-palmer-alluxio-public-bucket.s3.amazonaws.com/installers/alluxio-edge-304-SNAPSHOT-jars.tar

Then follow Step 3 through Step 5 in the following Git repo to configure and build your Docker image:

https://github.com/gregpalmr/alluxio-trino-edge-cache
 

I would be happy to schedule a zoom meeting where we can perform these steps together. Please indicate what times work best for you.


Step 1. Update the Dockerfile file

In Dockerfile, make sure the following files are created or copied, using the these examples:

ARG ALLUXIO_VERSION=304-SNAPSHOT
ARG JMX_PROMETHEUS_AGENT_VERSION=0.20.0

# Remove old versions of Alluxio jar files from the container
RUN find /usr/lib/trino -name alluxio*shaded* -exec rm {} \;

# Copy the Alluxio Edge client jar file to the Trino catalog dirs
COPY jars/alluxio-emon-${ALLUXIO_VERSION}-client.jar /usr/lib/trino/plugin/hive
COPY jars/alluxio-emon-${ALLUXIO_VERSION}-client.jar /usr/lib/trino/plugin/hudi
COPY jars/alluxio-emon-${ALLUXIO_VERSION}-client.jar /usr/lib/trino/plugin/delta-lake  
COPY jars/alluxio-emon-${ALLUXIO_VERSION}-client.jar /usr/lib/trino/plugin/iceberg

# Make a home directory for Alluxio Edge
RUN mkdir -p /home/trino/alluxio/lib && mkdir -p /home/trino/alluxio/conf

# Copy the Alluxio Edge under store jar file to the Trino lib dir
COPY jars/alluxio-underfs-emon-s3a-${ALLUXIO_VERSION}.jar /home/trino/alluxio/lib

# Create a metrics property file that enables the Alluxio JmxSink
COPY <<EOF /home/trino/alluxio/conf/metrics.properties
sink.jmx.class=alluxio.metrics.sink.JmxSink
EOF

# Copy the JVX Prometheus agent jar file to the Alluxio lib dir
COPY jars/jmx_prometheus_javaagent-${JMX_PROMETHEUS_AGENT_VERSION}.jar /home/trino/alluxio/lib

# Create a JMX export configuration file
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

Step 2. Update the Trino helm chart's values_preprod.yaml file

In the Trino helm chart's values_preprod.yaml file, I changed the following sections:

  additionalConfigFiles:
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

  additionalConfigFiles:
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

  additionalConfigFiles:
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

  additionalJVMConfig:
    - "-XX:+UnlockDiagnosticVMOptions"
    - "-XX:+UseAESCTRIntrinsics"
    # Add following two lines to Enable Alluxio Edge integration
    - "-Dalluxio.home=/home/trino/alluxio"
    - "-Dalluxio.conf.dir=/etc/trino/"
    # Add following two lines to enable Alluxio Edge JMX/Prometheus export support
    -Dalluxio.metrics.conf.file=/home/trino/alluxio/conf/metrics.properties
    -javaagent:/home/trino/alluxio/lib/jmx_prometheus_javaagent-0.20.0.jar=9696:/etc/trino/jmx_export_config.yaml

  #
  # Delta LakeHouse
  #
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


Sergey,

If you see the same errors tomorrow, we suggest that you configure Alluxio Edge to filter what Delta Lake metadata files get cached.

Modify your Trino Helm chart to modify the Alluxio Edge alluxio-site.properties file and have it create a new file called cache_filter.properties.

Here are the overview instructions:

    Configure Alluxio Edge to filter the
    delta lake metadata files that it caches
    ---------------------------------------------------------------

    Step 1: Enable the Alluxio cache filter by adding the two properties below into the file: 

    /etc/trino/alluxio-site.properties

    alluxio.user.client.cache.filter.enabled=true
    alluxio.user.client.cache.filter.class=alluxio.client.file.cache.filter.PatternBasedCacheFilter

    Step 2: Configure the Alluxio Edge blacklist policy in the file:

    /etc/trino/cache_filter.properties

    {
      "filterType": "BLOCK_LIST",
      "regxPatternStrList": [".*_delta_log.*"]
    }