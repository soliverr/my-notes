---
id: d961rhpnud0epoqg5hunkl1
title: Development
desc: ''
updated: 1668064659075
created: 1652689374838
---

# Use DFS in Spark

* https://stackoverflow.com/questions/54060265/how-to-list-files-in-s3-bucket-using-spark-session/67050173#67050173
* дока по API:
    * https://hadoop.apache.org/docs/current/api/org/apache/hadoop/fs/Path.html
    * https://hadoop.apache.org/docs/current/api/org/apache/hadoop/fs/FileStatus.html

```java
import java.net.URI
import org.apache.hadoop.fs.FileSystem
import org.apache.hadoop.fs.Path
import org.apache.hadoop.conf.Configuration

val path = "s3://somebucket/somefolder"
val fileSystem = FileSystem.get(URI.create(path), new Configuration())
val it = fileSystem.listFiles(new Path(path), true)
while (it.hasNext()) {
  ...
}
```

```python
sc = spark.sparkContext
myPath = f's3://my-bucket/my-prefix/'
javaPath = sc._jvm.java.net.URI.create(myPath)
hadoopPath = sc._jvm.org.apache.hadoop.fs.Path(myPath)
hadoopFileSystem = sc._jvm.org.apache.hadoop.fs.FileSystem.get(javaPath, sc._jvm.org.apache.hadoop.conf.Configuration())
iterator = hadoopFileSystem.listFiles(hadoopPath, True)

s3_keys = []
while iterator.hasNext():
    s3_keys.append(iterator.next().getPath().toUri().getRawPath())    
```

```python
myPath = 's3://my-bucket/my-prefix/*'
hadoopPath = sc._jvm.org.apache.hadoop.fs.Path(myPath)
hadoopFs = hadoopPath.getFileSystem(sc._jvm.org.apache.hadoop.conf.Configuration())
statuses = hadoopFs.globStatus(hadoopPath)

for status in statuses:
  status.getPath().toUri().getRawPath()
  # Alternatively, you can get file names only with:
  # status.getPath().getName()
```
# Compute size of Spark Dataframe
* [Compute size of Spark dataframe - SizeEstimator gives unexpected results - Stack Overflow](https://stackoverflow.com/questions/49492463/compute-size-of-spark-dataframe-sizeestimator-gives-unexpected-results)

**Dataframe should be cached.**

```scala
df.cache.foreach(_ => ())
val catalyst_plan = df.queryExecution.logical
val df_size_in_bytes = spark.sessionState.executePlan(
    catalyst_plan).optimizedPlan.stats.sizeInBytes

//
val inp_bytes = sparkSession.sessionState.executePlan(df.queryExecution.logical).optimizedPlan.stats.sizeInBytes
val inp_rows = sparkSession.sessionState.executePlan(df.queryExecution.logical).optimizedPlan.stats.rowCount
```

# Spark Structured Streaming examples
## Pass additional arguments to foreachBatch

* [Pass additional arguments to foreachBatch in pyspark](https://stackoverflow.com/questions/55973631/pass-additional-arguments-to-foreachbatch-in-pyspark)

```python
streamingDF.writeStream.foreachBatch(lambda df,epochId: writeToSQLWarehose(df, epochId,tableName )).start()
```

```scala
.withColumn("dl_tablePath", func.lit(silverPath))
.writeStream.format("delta")
.foreachBatch(insertIfNotExisting)

def insertIfNotExisting(batchDf, batchId):
  tablePath = batchDf.select("dl_tablePath").limit(1).collect()[0][0]
  realDf = batchDf.drop("dl_tablePath")
```


# Ошибки

## OutOfMemory

* java.lang.OutOfMemoryError: GC Overhead limit exceeded
* java.lang.OutOfMemoryError: Java heap space

[A step-by-step guide for debugging memory leaks in Spark Applications](https://medium.com/disney-streaming/a-step-by-step-guide-for-debugging-memory-leaks-in-spark-applications-e0dd05118958)

### GC Overhead limit exceeded

According to Spark [documentation](https://spark.apache.org/docs/latest/tuning.html), G1GC can solve problems in some cases where garbage collection is a bottleneck. We enabled G1GC using the following configuration:

```
spark.executor.extraJavaOptions: -XX:+UseG1GC
```

Thankfully, this tweak improved a number of things:

1. Periodic GC speed improved.
2. Full GC was still too slow for our liking, but the cycle of full GC became less frequent.
3. GC Overhead limit exceeded exceptions disappeared.

### Java heap space

#### Get heap dump

To get a heap dump on OOM, the following option can be enabled in the Spark Cluster configuration on the executor side:

```
spark.executor.extraJavaOptions: -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/dbfs/heapDumps
```

#### Take heap dump

Steps to take periodic heap dump:

1. ssh into worker 
2. Get Pid using top of the java process
3. Get Heapdump 
    - `jmap -dump:format=b,file=pbs_worker.hprof <pid>`
4. Provide correct permissions to Heapdump file.
    - `sudo chmod 444 pbs_worker.hprof`
5. Download file on your local 
    - `./rsync -chavzP --stats ubuntu@<worker_ip_address>:/home/ubuntu/pbs_worker.hprof .`

#### Analyze Heap Dumps

Heap dump analysis can be performed with tools like [YourKit](https://www.yourkit.com/) or [Eclipse MAT](https://www.eclipse.org/mat/).
In our case, heap dumps were large — in the range of 40gb or more. The size of the heap dumps made it difficult to analyze. There is a [workaround](https://stackoverflow.com/a/7254494/5036809) that can be used to index the large files and then analyze them.
