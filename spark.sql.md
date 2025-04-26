---
id: gw1ay5xaj5o93fteom4dp4s
title: SQL
desc: ''
updated: 1729592060691
created: 1729591990924
---


## Regexp for columns in select 

```sql
SET spark.sql.parser.quotedRegexColumnNames=true;

"select `^(?!create_dt$$).*$$` from <table_name>;
```
