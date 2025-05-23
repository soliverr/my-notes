---
id: re6155ewkhqbsv90gq91ovd
title: Deephaven
desc: ''
updated: 1651822366251
created: 1651758483790
---

* [Deephaven](https://deephaven.io/)
* [Deephaven Git](https://github.com/deephaven)
* [Deephaven Examples Git](https://github.com/deephaven-examples/)
    * [deephaven-debezium-demo](https://github.com/deephaven-examples/deephaven-debezium-demo)

# Deephaven overview

https://deephaven.io/core/docs/conceptual/deephaven-overview/

It is natural to place the new on a map framed by the familiar. Deephaven enters an arena defined by Kafka, Spark, Influx, Redshift, BigQuery, Snowflake, Postgres, and dozens of other players -- open-source, commercial, large and small.

A Venn diagram of industry capabilities would likely be multi-dimensional and would certainly be messy. Like others, Deephaven overlaps with the aforementioned technologies to varying degrees.

* Deephaven is best of breed with real-time data. But streaming analytics is Spark’s tagline.
* Deephaven’s architecture benefits from the separation of storage from compute. So does Snowflake’s.
* Deephaven is first class at manipulating streams and delivering derived, updating datasets to consumers. Kafka ksqlDB claims the same.
* Deephaven’s performance with time series is elite. Influx, Timescale, Prometheus, Druid somewhat battle for that turf.
* Deephaven is natural for data science and machine learning at scale. This places it in an arena with Jupyter and Pandas.

Though Deephaven users might argue it is the best alternative for a broad range of applications, Deephaven stands out as the only solution if you (i) have streaming data, (ii) need to do on-the-fly joins, and (iii) want to easily send derived, ticking data downstream; or (iv) if you’d like to interactively explore live sets in a browser; or (v) if you’d like to blissfully assume that anything supported in batch is also supported for streams.
