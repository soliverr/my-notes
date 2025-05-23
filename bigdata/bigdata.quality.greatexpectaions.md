---
id: oodlrjjukho8jzbd0q8kg4k
title: Greatexpectaion
desc: ''
updated: 1676912181297
created: 1676567357097
---

[GreatExpectations](https://greatexpectations.io/) - A powerful platform to uphold data quality.
  * https://github.com/great-expectations/great_expectations
  * https://github.com/greatexpectationslabs

# Install

```
$ pyenv virtualenv greatexpectations
$ pyenv activate  greatexpectations
$ pip install great_expectations sqlalchemy pymysql trino

$ great_expectations --version
great_expectations, version 0.15.48
```

# Getting Started

Your specific use case will no doubt differ from that of our tutorial. However, the four steps you'll need to perform in order to get Great Expectations working for you will be the same. Setup, connect to data, create Expectations, and validate data. That's all there is to it! As long as you can perform these four steps you can have Great Expectations working to validate data for you.

* https://docs.greatexpectations.io/docs/tutorials/getting_started/tutorial_overview/
* https://docs.greatexpectations.io/docs/guides/expectations/advanced/how_to_compare_two_tables_with_the_user_configurable_profiler/


## Create a Data Context

```
$ mkdir <project>
$ cd <project>
$ great_expectations init
```

## Create a Datasource

```
$ great_expectations datasource new

Which database backend are you using?
    1. MySQL
    2. Postgres
    3. Redshift
    4. Snowflake
    5. BigQuery
    6. Trino
    7. other - Do you have a working SQLAlchemy connection string?

1
```

### The `datasource new` notebook

Мне нужно создать источники для СУБД источников и для LakeHouse через Trino:

```python
import great_expectations as gx
from great_expectations.cli.datasource import sanitize_yaml_and_save_datasource, check_if_datasource_name_exists
context = gx.get_context()

datasource_name = "preprod_partners_source"

host = "preprod-eu-central-1-aurora-mysql8-read-replica.cgrd19dumoxe.eu-central-1.rds.amazonaws.com"
port = "3306"
username = "trino"
password = "tOKkOqVhtlCVlWdkMknZKCUF"
database = "partners"
schema_name = "partners"

# A table that you would like to add initially as a Data Asset
table_name = "users"

example_yaml = f"""
name: {datasource_name}
class_name: Datasource
execution_engine:
  class_name: SqlAlchemyExecutionEngine
  credentials:
    host: {host}
    port: '{port}'
    username: {username}
    password: {password}
    database: {database}
    drivername: mysql+pymysql
data_connectors:
  default_runtime_data_connector_name:
    class_name: RuntimeDataConnector
    batch_identifiers:
      - default_identifier_name
  default_inferred_data_connector_name:
    class_name: InferredAssetSqlDataConnector
    include_schema_name: True
    introspection_directives:
      schema_name: {schema_name}
  default_configured_data_connector_name:
    class_name: ConfiguredAssetSqlDataConnector
    assets:
      {table_name}:
        class_name: Asset
        schema_name: {schema_name}
"""
print(example_yaml)

context.test_yaml_config(yaml_config=example_yaml)

sanitize_yaml_and_save_datasource(context, example_yaml, overwrite_existing=False)
context.list_datasources()
```

### Create an Expectation Suite

```
great_expectations suite new
```

# Tutorials

## Great Expectations tutorial

* https://github.com/datarootsio/tutorial-great-expectations

```
docker pull dataroots/tutorial-great-expectations && docker run -it --rm -p 8888:8888 dataroots/tutorial-great-expectations
```

```
docker build . -t tutorial-great-expectations && docker run -it --rm -p 8888:8888 tutorial-great-expectations``
```