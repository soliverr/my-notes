---
id: 6hvk0z83g6u755ww9csan7k
title: Postgresql
desc: ''
updated: 1697627294349
created: 1697626491490
---

# AWS Aurora

## Enhanced monitoring

Amazon RDS provides metrics in real time for the operating system (OS) that your DB
instance runs on. You can view all the system metrics and process information for your
RDS DB instances on the console. You can manage which metrics you want to monitor
for each instance and customize the dashboard according to your requirements.

* https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_Monitoring.OS.overview.html

## Performance Insights

Performance Insights expands on existing Amazon Aurora monitoring features to
illustrate and help you analyze your cluster performance. With the Performance Insights
dashboard, you can visualize the database load on your Amazon Aurora cluster load and
filter the load by waits, SQL statements, hosts, or users.

* https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_PerfInsights.html

## Additional information

1. [Create an Amazon CloudWatch dashboard to monitor Amazon RDS for PostgreSQL and Amazon Aurora PostgreSQL](https://aws.amazon.com/blogs/database/create-an-amazon-cloudwatch-dashboard-to-monitor-amazon-rds-for-postgresql-and-amazon-aurora-postgresql/)
2. Use [log_fdw](https://aws.amazon.com/blogs/database/working-with-rds-and-aurora-postgresql-logs-part-2/) extension to query the PostgreSQL log via SQL for PANIC, other errors
or information
3. [Understanding autovacuum in Amazon RDS for PostgreSQL environments](https://aws.amazon.com/blogs/database/understanding-autovacuum-in-amazon-rds-for-postgresql-environments/
)

# Performance 

## PostgreSQL Extensions for Performance Monitoring

* [pg_stat_statements](https://www.postgresql.org/docs/13/pgstatstatements.html) for tracking execution statistics of SQL statements
* [auto_explain](https://www.postgresql.org/docs/13/auto-explain.html) for logging execution plans of slow queries automatically
* [pg_proctab](https://github.com/markwkm/pg_proctab) exposes OS/proc information through SQL
* [plprofiler](https://github.com/bigsql/plprofiler) to find bottleneck in PL/pgSQL function and stored procedures

### Other useful tools and scripts for Monitoring

* [pg-collector](https://github.com/awslabs/pg-collector) collects database information and presents it in a consolidated HTML file
* [PGPerfStatsSnapper](https://github.com/aws-samples/aurora-and-database-migration-labs/tree/master/Code/PGPerfStatsSnapper) for periodic collection (snapping) of PostgreSQL performance related statistics and metrics
* [rds-support-tools](https://github.com/awslabs/rds-support-tools/tree/main/postgres) contains collection of useful database monitoring scripts
* [Amazon Aurora Postgres Advanced Monitoring ](https://github.com/awslabs/amazon-aurora-postgres-monitoring) creates CloudWatch dashboard with useful database monitoring metrics
* [Pgbadger](https://github.com/darold/pgbadger) PostgreSQL log analyzer with fully detailed reports and graphs

### PostgreSQL Extensions for Database and SQL Tuning

* [pg_repack](https://github.com/reorg/pg_repack) for rebuilding a table online
* [pg_partman](https://github.com/pgpartman/pg_partman) to partition tables with less effort
* [pg_hint_plan](https://github.com/ossc-db/pg_hint_plan) to bias queries away from big operations (hints related to Scans, Joins & Environment)

## Query Tuning

* [pg_stat_activity] : One row per server process showing information related to the current activity of that process

# Partitioning

## pg_partman

* https://github.com/pgpartman/pg_partman
* https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/PostgreSQL_Partitions.html#PostgreSQL_Partitions.enable ￼

Один важный момент. Extensions должны быть в списке trusted: https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html#PostgreSQL.Concepts.General.Extensions.Trusted

# Support for CDC

Да, по сути нужно будет включить `rds.logical_replication = 1`` в parameter group: 

* https://repost.aws/knowledge-center/rds-postgresql-use-logical-replication
* https://materialize.com/docs/ingest-data/cdc-postgres-kafka-debezium/#aws-rds-t0