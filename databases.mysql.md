---
id: ob2x6xueegjr3te1zxp7hjt
title: Mysql
desc: ''
updated: 1670950466496
created: 1657269854024
---

* https://dataedo.com/kb/query/mysql

* [MySQL :: MySQL 8.0 Reference Manual :: 5.1.12.1 Connection Interfaces](https://dev.mysql.com/doc/refman/8.0/en/connection-interfaces.html)
* [Essential concepts for Aurora MySQL tuning - Amazon Aurora](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Managing.Tuning.concepts.html)
* [Amazon Aurora connection management - Amazon Aurora](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.Endpoints.html)
* [Three Things That Differentiate Amazon Aurora From MySQL - Orange Matter](https://orangematter.solarwinds.com/2017/01/27/three-things-that-differentiate-amazon-aurora-from-mysql/)
* [Amazon Aurora DB clusters - Amazon Aurora](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.html)

# Анализ производительности

* [Percona Toolkit Documentation](https://www.percona.com/doc/percona-toolkit/LATEST/index.html) #[[Roam-Highlights]]
* [MySQL Workbench](https://www.mysql.com/products/workbench/)
* [major/MySQLTuner-perl: MySQLTuner is a script written in Perl that will assist you with your MySQL configuration and make recommendations for increased performance and stability.](https://github.com/major/MySQLTuner-perl) #[[Roam-Highlights]]

## Метрики

* https://dev.mysql.com/doc/mysql-em-plugin/en/myoem-metrics.html

## General Log and Slow Queries

* AWS Aurora MySQL: https://aws.amazon.com/ru/premiumsupport/knowledge-center/rds-mysql-logs/
* [How to flush data from mysql.slow_log table in mysql?](https://stackoverflow.com/questions/29163588/how-to-flush-data-from-mysql-slow-log-table-in-mysql)
* [mysql.rds_rotate_slow_log - Amazon Relational Database Service](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/mysql_rds_rotate_slow_log.html)

# Секционирование таблиц

https://dev.mysql.com/doc/refman/8.0/en/partitioning-overview.html

All columns used in the table's partitioning expression must be part of every unique key that the table may have, including any primary key. 

## [RANGE Partitioning](https://dev.mysql.com/doc/refman/8.0/en/partitioning-range.html)

```sql
CREATE TABLE quarterly_report_status (
    report_id INT NOT NULL,
    report_status VARCHAR(20) NOT NULL,
    report_updated TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
)
PARTITION BY RANGE ( UNIX_TIMESTAMP(report_updated) ) (
    PARTITION p0 VALUES LESS THAN ( UNIX_TIMESTAMP('2008-01-01 00:00:00') ),
    PARTITION p1 VALUES LESS THAN ( UNIX_TIMESTAMP('2008-04-01 00:00:00') ),
    PARTITION p2 VALUES LESS THAN ( UNIX_TIMESTAMP('2008-07-01 00:00:00') ),
    PARTITION p3 VALUES LESS THAN ( UNIX_TIMESTAMP('2008-10-01 00:00:00') ),
    PARTITION p4 VALUES LESS THAN ( UNIX_TIMESTAMP('2009-01-01 00:00:00') ),
    PARTITION p5 VALUES LESS THAN ( UNIX_TIMESTAMP('2009-04-01 00:00:00') ),
    PARTITION p6 VALUES LESS THAN ( UNIX_TIMESTAMP('2009-07-01 00:00:00') ),
    PARTITION p7 VALUES LESS THAN ( UNIX_TIMESTAMP('2009-10-01 00:00:00') ),
    PARTITION p8 VALUES LESS THAN ( UNIX_TIMESTAMP('2010-01-01 00:00:00') ),
    PARTITION p9 VALUES LESS THAN (MAXVALUE)
);

```
Any other expressions involving TIMESTAMP values are not permitted. (See Bug #42849.) 

https://stackoverflow.com/questions/6093585/how-to-partition-a-table-by-datetime-column:


Partitions by HASH is a very bad idea with datetime columns, because it cannot use partition pruning. From the MySQL docs:

>    Pruning can be used only on integer columns of tables partitioned by HASH or KEY. For example, this query on table t4 cannot use pruning because dob is a DATE column:

```sql
SELECT * FROM t4 WHERE dob >= '2001-04-14' AND dob <= '2005-10-15';
```

    However, if the table stores year values in an INT column, then a query having WHERE year_col >= 2001 AND year_col <= 2005 can be pruned.

So you can store the value of TO_DAYS(DATE()) in an extra INTEGER column to use pruning.

Another option is to use RANGE partitioning:

```sql
CREATE TABLE raw_log_2011_4 (
  id bigint(20) NOT NULL AUTO_INCREMENT,
  logid char(16) NOT NULL,
  tid char(16) NOT NULL,
  reporterip char(46) DEFAULT NULL,
  ftime datetime DEFAULT NULL,
  KEY id (id)
) ENGINE=InnoDB AUTO_INCREMENT=286802795 DEFAULT CHARSET=utf8
  PARTITION BY RANGE( TO_DAYS(ftime) ) (
    PARTITION p20110401 VALUES LESS THAN (TO_DAYS('2011-04-02')),
    PARTITION p20110402 VALUES LESS THAN (TO_DAYS('2011-04-03')),
    PARTITION p20110403 VALUES LESS THAN (TO_DAYS('2011-04-04')),
    PARTITION p20110404 VALUES LESS THAN (TO_DAYS('2011-04-05')),
    ...
    PARTITION p20110426 VALUES LESS THAN (TO_DAYS('2011-04-27')),
    PARTITION p20110427 VALUES LESS THAN (TO_DAYS('2011-04-28')),
    PARTITION p20110428 VALUES LESS THAN (TO_DAYS('2011-04-29')),
    PARTITION p20110429 VALUES LESS THAN (TO_DAYS('2011-04-30')),
    PARTITION future VALUES LESS THAN MAXVALUE
  );
```

# Errors

## Bad handshake

`[MY-010914] [Server] Bad handshake`

* https://github.com/mysql-net/MySqlConnector/issues/726

## Got an error reading communication packets

`[MY-010914] [Server] Got an error reading communication packets`

* https://www.percona.com/blog/2016/05/16/mysql-got-an-error-reading-communication-packet-errors/
* https://aws.amazon.com/ru/premiumsupport/knowledge-center/rds-mysql-communication-packet-error/
