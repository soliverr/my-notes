---
id: qme9xy5tquxjdgw47dwbhrn
title: Spark Structured Streaming
desc: ''
updated: 1678703993772
created: 1622179078109
---

* https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html
* https://spark.apache.org/docs/latest/configuration.html#spark-streaming
* https://databricks.com/blog/2020/07/29/a-look-at-the-new-structured-streaming-ui-in-apache-spark-3-0.html

# Задание пропускной способности при обработке сообщений из потока 

https://stackoverflow.com/questions/65109892/spark-3-structured-streaming-use-maxoffsetspertrigger-in-kafka-source-with-trigg

Оказалось, что параметр `maxOffsetsPerTrigger` работает только если задать режим `.trigger(Trigger.ProcessingTime())`.

По умолчанию включается режим `Trigger.Once()`, который выгребает все доступные данные в потоке, что приводит к сбою по нехватке либо памяти, либо дискового пространства при операциях фильтрации и агрегации на больших объёмах данных (десятки и сотни миллионов строк входных данных).

# Back-pressure

* https://stackoverflow.com/questions/39981650/limit-kafka-batches-size-when-using-spark-streaming
* https://vanwilgenburg.wordpress.com/2015/10/06/spark-streaming-backpressure/
* https://issues.apache.org/jira/browse/SPARK-7398

> Note that spark.streaming.receiver.maxRate is still the maximum maximum, with backpressure the rate will never be higher than this maximum. It’s a good idea to set a maximum becasue the backpressure algorithm isn’t instant (which would be impossible). We ran into trouble with a job with Kafka input that could handle about 1000 events/sec when Kafka decided to give us 50.000 records/sec in the first few seconds. Set the maximum to about 150-200% of your maximum theoretical throughput and you should be safe.

`spark.streaming.backpressure.pid.minRate` ?

# Monitoring Metrics

* [Custom Kafka metrics using Apache Spark PrometheusServlet | by Vitor Teixeira | Feb, 2023 | Towards Data Science](https://medium.com/towards-data-science/custom-kafka-streaming-metrics-using-apache-spark-prometheus-sink-9c04cf2ddaf1)
    * https://github.com/Orpheuz/spark-prometheus-kafka-metrics