---
id: xcyy2c37pw206i1tghs0s2q
title: Dagster
desc: 'Dagster is a data orchestrator for machine learning, analytics, and ETL'
updated: 1684497580237
created: 1625817823592
---

[Dagster](https://github.com/dagster-io/dagster) is a data orchestrator for machine learning, analytics, and ETL.

# About Dagster

* [Dagster vs. Airflow | Dagster Blog](https://dagster.io/blog/dagster-airflow)

# Installation

* https://docs.dagster.io/getting-started/install

> Note: Before you dive in, make sure you have Python 3.7+ installed.

Мне кажется, лучше сконфигурировать [виртуальное окружение Python](./python.virtualenvironments.md) для локальных экспериментов с Dagster. 

```sh
pyenv virtualenv dagster
pyenv activate dagster
```

```
pip install dagster dagit
```

# Deployment

* https://docs.dagster.io/deployment


* OSS (Open Source): Развёртывание собственного кластера Dagster
* [Dagster Cloud](https://docs.dagster.io/dagster-cloud): Dagster Cloud provides robust, managed infrastructure, elegant CI/CD capability, and multiple deployment options to suit your needs.

## OSS Architecture

* https://docs.dagster.io/deployment/overview

Dagster requires three long-running services, which are outlined in the table below:

| Service	| Description	| Replicas |
| Dagit	| Dagit serves the user interface and responds to GraphQL queries. It can have one or more replicas.	| Supported |
| Dagster daemon	| The Dagster daemon operates schedules, sensors, and run queuing. Currently, replicas are not supported.	| Not supported |
| Code location server	| Code location servers serve metadata about the collection of its Dagster definitions. You can have many code location servers; each server can have one or more replicas.	| Supported |

## Helm

* https://github.com/dagster-io/dagster/tree/master/helm/dagster

# Tutorial and Examples

* https://docs.dagster.io/tutorial
* https://github.com/dagster-io/dagster/tree/master/examples

## Hello Dagster

* https://docs.dagster.io/getting-started/hello-dagster#hello-dagster

**Добавить pandas**

```
pip install pandas
```

**Создать скрипт** `hello-dagster.py`.

**Запустить скрипт** 

```
# run in a terminal in your favorite python environment
dagit -f hello-dagster.py
```

**Открыть в браузере ссылку** http://localhost:3000/


# Concepts

* https://docs.dagster.io/concepts

## Code locations

* https://docs.dagster.io/concepts/code-locations

A code location is a collection of Dagster definitions loadable and accessible by Dagster's tools, such as the CLI, Dagit, and Dagster Cloud. A code location comprises:

* A reference to a Python module that has an instance of [Definitions](https://docs.dagster.io/_apidocs/definitions#dagster.Definitions) in a top-level variable
* A Python environment that can successfully load that module

Definitions within a code location have a common namespace and must have unique names. This allows them to be grouped and organized by code location in tools.

Code locations are loaded in a different process and communicate with Dagster system processes over an RPC mechanism. This architecture provides several advantages:

* When there is an update to user code, Dagit can pick up the change without a restart.
* You can use multiple code locations to organize jobs, but still work on all of your code locations using a single instance of Dagit.
* _The Dagit process can run in a separate Python environment from user code_ so job dependencies don't need to be installed into the Dagit environment.
* _Each code location can be sourced from a separate Python environment_, so teams can manage their dependencies (or even their Python versions) separately.

### Definitions

One `Definitions` object allowed per code location

```
defs = Definitions(
    assets=[dbt_customers_asset, dbt_orders_asset],
    schedules=[bi_weekly_schedule],
    sensors=[new_data_sensor],
    resources=dbt_resource
)
```

Definitions can be included in a Python file like `my_file.py` or a Python module. If using the latter, the Definitions object should be defined in the module's top-level `__init__.py` file.

**Local development**

```
dagster dev -f my_file.py -f my_second_file.py
```

**Cloud deployment**

```
# dagster_cloud.yaml

locations:
  - location_name: my-code-location
    code_source:
      python_file: my_file.py
```

**Open source deployment**

The workspace.yaml file is used to load code locations for open source (OSS) deployments. This file specifies how to load a collection of code locations and is typically used in advanced use cases. Refer to the [Open source deployment guides](https://docs.dagster.io/deployment/guides) for more info.


## Workspaces

* https://docs.dagster.io/concepts/code-locations/workspace-files#understanding-workspace-files

Workspace files contain a collection of user-defined [code locations](https://docs.dagster.io/concepts/code-locations) and information about where to find them. Code locations loaded via workspace files can contain either a [`Definitions`](https://docs.dagster.io/_apidocs/definitions#dagster.Definitions) object or multiple [repositories](https://docs.dagster.io/concepts/repositories-workspaces/repositories).

## Run Worker

The run worker is responsible for executing launched Dagster runs. _The run worker uses the same image as the user code deployment at the time the run was submitted._ Run worker jobs and pods are not automatically deleted so that users are able to inspect results. _It's up to the user to periodically delete old jobs and pods._

## Executor

Each Dagster job specifies an executor that determines how the run worker will execute each step of the job. Different executors offer different levels of isolation and concurrency. Common choices are:

* `in_process_executor` - All steps run serially in a single process in a single pod
* `multiprocess_executor` - Multiple processes run in a single pod
* `k8s_job_executor` - Each step runs in a separate Kubernetes job

Generally, increasing isolation incurs some additional overhead per step (e.g. starting up a new Kubernetes job vs starting a new process within a pod). The executor can be configured per-run in the `execution` block.

## Build a Docker image for user code

In this step, you'll build a Docker image containing your Dagster definitions and any dependencies needed to execute the business logic in your code. For reference, here is an example [Dockerfile](https://github.com/dagster-io/dagster/blob/master/python_modules/automation/automation/docker/images/k8s-example/Dockerfile) and the [corresponding user code directory](https://github.com/dagster-io/dagster/tree/master/examples/deploy_k8s/example_project).

For projects with many dependencies, we recommend publishing your Python project as a package and installing it in your Dockerfile.

### User code deployments

* https://www.vandebron.tech/blog/cicd-dagster-user-code
* https://medium.com/@custers.pieter/the-why-and-how-of-dagster-user-code-deployment-automation-f67c5d10ab7c

In Helm terms: there are 2 charts, namely the __system__: `dagster/dagster` ([values.yaml](https://github.com/dagster-io/dagster/blob/master/helm/dagster/values.yaml)), and the __user code__: `dagster/dagster-user-deployments` ([values.yaml](https://github.com/dagster-io/dagster/blob/master/helm/dagster/charts/dagster-user-deployments/values.yaml)). See [the docs](https://docs.dagster.io/deployment/guides/kubernetes/customizing-your-deployment) on how to enable this.

**This means system and user **__deployments__** are not actually completely separated!**

This implies that, if you want to add a __new__ user code repository, not only do you need to:
  - add the repo to the user code’s `values.yaml` (via a PR in the Git repo of your company's platform team, probably);
  - do a helm-upgrade of the corresponding `dagster/dagster-user-deployments` chart;

but because of the not-so-separation, you still need to:
  - add the user code server to the system’s `values.yaml` (via that same PR);
  - and do a helm-upgrade of the corresponding `dagster/dagster` chart.

If you run Dagit locally it relies on a `workspace.yaml` file; on Kubernetes it relies on a [ConfigMap](https://kubernetes.io/docs/concepts/configuration/configmap/) — a Kubernetes object used to store non-confidential data in key-value pairs, e.g. the content of a file — which they named `dagster-workspace-yaml`.

## Separately Deploying Dagster Infrastructure and User Code

* https://docs.dagster.io/deployment/guides/kubernetes/customizing-your-deployment#separately-deploying-dagster-infrastructure-and-user-code

It may be desirable to manage two Helm releases for your Dagster deployment: one release for the Dagster infrastructure, which consists of Dagit and the Daemon, and another release for your User Code, which contains the definitions of your pipelines written in Dagster. This way, changes to User Code can be decoupled from upgrades to core Dagster infrastructure.

To do this, we offer the [`dagster` chart](https://artifacthub.io/packages/helm/dagster/dagster) and the [`dagster-user-deployments` chart](https://artifacthub.io/packages/helm/dagster/dagster-user-deployments).

## Assets

* https://docs.dagster.io/concepts/assets

"Asset" is Dagster's word for an entity, external to ops, that is mutated or created by an op. An asset might be a table in a database that an op appends to, an ML model in a model store that an op overwrites, or even a slack channel that an op writes messages to.

Op outputs often correspond to assets. For example, an op might be responsible for recreating a table, and one of its outputs might be a dataframe containing the contents of that table.

Assets can also have partitions, which refer to slices of the overall asset. The simplest example would be a table that has a partition for each day. A given op execution may simply write a single day's worth of data to that table, rather than dropping the entire table and replacing it with new data.

### Multi-Assets

* https://docs.dagster.io/concepts/assets/multi-assets

### Graph-Backed Assets

* https://docs.dagster.io/concepts/assets/graph-backed-assets


### Asset Materialization 


* https://docs.dagster.io/concepts/assets/asset-materializations

The act of creating or updating the contents of an asset is called a "materialization", and Dagster tracks these materializations using AssetMaterialization events. 


There are two general patterns for dealing with assets when using Dagster:

* Put the logic to write/store assets inside the body of an op.
* Focus the op purely on business logic, and delegate the logic to write/store assets to an IO manager.

Regardless of which pattern you are using, AssetMaterialization events are used to communicate to Dagster that a materialization has occurred. You can create these events either by explicitly logging them at runtime, or (using an experimental interface), have Dagster automatically generate them by defining that a given op output corresponds to a given AssetKey.

**Software-defined assets vs. Asset Materializations**

  . When you look at software-defined assets in Dagit, you can see exactly what assets are going to be materialized before any code runs.

_Asset Materializations, on the other hand, are logged at run time_. When you run an op, you find out which assets were materialized while the op is running. This allows for some flexibility, like if you wanted to determine which assets should be materialized based on the output of a previous op.

#### Logging an AssetMaterialization from a Op

```python
from dagster import op


@op
def my_simple_op():
    df = read_df()
    remote_storage_path = persist_to_storage(df)
    return remote_storage_path

from dagster import AssetMaterialization, op


@op
def my_materialization_op(context):
    df = read_df()
    remote_storage_path = persist_to_storage(df)
    context.log_event(
        AssetMaterialization(
            asset_key="my_dataset", description="Persisted result to storage"
        )
    )
    return remote_storage_path
```

#### Logging an AssetMaterialization from an IO Manager

```python
import os

from dagster import AssetKey, AssetMaterialization, IOManager


class PandasCsvIOManager(IOManager):
    def load_input(self, context):
        file_path = os.path.join("my_base_dir", context.step_key, context.name)
        return read_csv(file_path)

    def handle_output(self, context, obj):
        file_path = os.path.join("my_base_dir", context.step_key, context.name)

        obj.to_csv(file_path)

        context.log_event(
            AssetMaterialization(
                asset_key=AssetKey(file_path),
                description="Persisted result to storage.",
            )
        )
```

#### Attaching Metadata to an AssetMaterialization

```python
from dagster import AssetMaterialization, MetadataValue, op


@op
def my_metadata_materialization_op(context):
    df = read_df()
    remote_storage_path = persist_to_storage(df)
    context.log_event(
        AssetMaterialization(
            asset_key="my_dataset",
            description="Persisted result to storage",
            metadata={
                "text_metadata": "Text-based metadata for this event",
                "path": MetadataValue.path(remote_storage_path),
                "dashboard_url": MetadataValue.url(
                    "http://mycoolsite.com/url_for_my_data"
                ),
                "size (bytes)": calculate_bytes(df),
            },
        )
    )
    return remote_storage_path

from dagster import AssetMaterialization, IOManager


class PandasCsvIOManagerWithAsset(IOManager):
    def load_input(self, context):
        file_path = os.path.join("my_base_dir", context.step_key, context.name)
        return read_csv(file_path)

    def handle_output(self, context, obj):
        file_path = os.path.join("my_base_dir", context.step_key, context.name)

        obj.to_csv(file_path)

        context.log_event(
            AssetMaterialization(
                asset_key=AssetKey(file_path),
                description="Persisted result to storage.",
                metadata={
                    "number of rows": obj.shape[0],
                    "some_column mean": obj["some_column"].mean(),
                },
            )
        )
```

#### Specifying a partition for an AssetMaterialization

```python
from dagster import AssetMaterialization, Config, op


class MyOpConfig(Config):
    date: str


@op
def my_partitioned_asset_op(context, config: MyOpConfig):
    partition_date = config.date
    df = read_df_for_date(partition_date)
    remote_storage_path = persist_to_storage(df)
    context.log_event(
        AssetMaterialization(asset_key="my_dataset", partition=partition_date)
    )
    return remote_storage_path
```


## Jobs

* https://docs.dagster.io/concepts/ops-jobs-graphs/jobs

Jobs are the main unit of execution and monitoring in Dagster. The core of a job is a graph of ops connected via data dependencies.

### Building jobs that materialize assets

**Jobs that target assets can materialize a fixed selection of assets each time they run and be placed on schedules and sensors**. Refer to the Jobs documentation for more info and examples.

## Partitioned assets and jobs

* https://docs.dagster.io/concepts/partitions-schedules-sensors/partitions
* https://docs.dagster.io/concepts/io-management/io-managers#handling-partitioned-assets

A software-defined asset can represent a collection of partitions that can be tracked and materialized independently. In many ways, each partition functions like its own mini-asset, but they all share a common materialization function and dependencies. Typically, each partition will correspond to a separate file, or a slice of a table in a database.

A graph of assets with the same partitions implicitly forms a partitioned data pipeline, and you can launch a run that selects multiple assets and materializes the same partition in each asset.

Similarly, a partitioned job is a job where each run corresponds to a partition. It's common to construct a partitioned job that materializes a single partition across a set of partitioned assets every time it runs.

Having defined a partitioned asset or job, you can:

* View runs by partition in Dagit.
* Define a schedule that fills in a partition each time it runs. For example, a job might run each day and process the data that arrived during the previous day.
* Launch backfills, which are sets of runs that each process a different partition. For example, after making a code change, you might want to run your job on all partitions instead of just one of them.


## Graphs

## Understanding how software-defined assets relate to ops and graphs

* https://docs.dagster.io/guides/dagster/how-assets-relate-to-ops-and-graphs

Under the hood, every software-defined asset contains an op (or graph of ops), which is the function that’s invoked to compute its contents. In most cases, the underlying op is invisible to the user.

A software-defined asset can be backed by an op graph, instead of an op. 

Graph-backed assets are useful when you want to execute multiple separate steps to compute an asset and some of those steps don’t produce assets of their own.

For example, to compute the contents of a table, you need to fetch data from an API and then perform a heavy data transformation on it. You don’t care about writing the fetched, pre-transformed data to any known location, but you want the fetching and transforming to happen in two separate steps that can run in different processes. If there’s a failure, you’d like to be able to re-execute the transformation step without re-executing the fetching step.

In some cases, you might want to build a job that doesn't produce any assets, but does read from at least one asset. Dagster facilitates this by allowing you to designate assets as inputs to ops within a graph or graph-based job:

```python
from dagster import asset, job, op


@asset
def emails_to_send():
    ...


@op
def send_emails(emails) -> None:
    ...


@job
def send_emails_job():
    send_emails(emails_to_send.to_source_asset())
```

In this case, the asset - _specifically, the table the job reads from_ - is only used as a data source for the job. It’s not materialized when the graph is run.

### Loading an asset as an input

* https://docs.dagster.io/concepts/ops-jobs-graphs/graphs#loading-an-asset-as-an-input

You can supply an asset as an input to one of the ops in a graph. Dagster can then use the I/O manager on the asset to load the input value for the op.

```python
from dagster import asset, job, op


@asset
def emails_to_send():
    ...


@op
def send_emails(emails) -> None:
    ...


@job
def send_emails_job():
    send_emails(emails_to_send.to_source_asset())
```

We must use the AssetsDefinition.to_source_asset, because SourceAssets are used to represent assets that other assets or jobs depend on, in settings where they won't be materialized themselves.

If the asset is partitioned, then:

* If the job is partitioned, the corresponding partition of the asset will be loaded.
* If the job is not partitioned, then all partitions of the asset will be loaded. The type that they will be loaded into depends on the I/O manager implementation.


## Intro to ops and jobs

* https://docs.dagster.io/guides/dagster/intro-to-ops-jobs

## Run Configuration

* https://docs.dagster.io/concepts/configuration/config-schema
* https://docs.dagster.io/concepts/configuration/advanced-config-types

Run configuration allows providing parameters to jobs at the time they're executed.

## Resources

* https://docs.dagster.io/concepts/resources

Resources are objects that are shared across the implementations of multiple software-defined assets, ops, schedules, and sensors. These resources can be plugged in after the definitions of your assets or ops, and can be easily swapped out.

* **Plug in different implementations in different environments** - If you have a heavy external dependency that you want to use in production, but avoid using in testing, you can accomplish this by providing different resources in each environment. Check out Separating Business Logic from Environments for more info about this capability.
* **Surface configuration in the UI** - Resources and their configuration are surfaced in the Dagster UI, making it easy to see where your resources are used and how they are configured.
* **Share configuration across multiple ops or assets** - Resources are configurable and shared, so you can supply configuration in one place instead of configuring the ops and assets individually.
* **Share implementations across multiple ops or assets** - When multiple ops access the same external services, resources provide a standard way to structure your code to share the implementations.

The configuration system has a few advantages over plain Python parameter passing; configured values are displayed in the Dagster UI and can be set dynamically using environment variables. Binding resource config values can also be delayed so that they can be specified at run launch time.

To provide resource values to your assets and ops, attach them to your Definitions call. These resources are automatically passed to the function at runtime.

### Configuring resources at launch time

* https://docs.dagster.io/concepts/resources#configuring-resources-at-launch-time

Because credentials requires launch time configuration through the launchpad, it must also be passed to the Definitions object, so that configuration can be provided at launch time. Nested resources only need to be passed to the Definitions object if they require launch time configuration.

### Defining resources which require state

* https://docs.dagster.io/concepts/resources#defining-resources-which-require-state
* https://docs.dagster.io/concepts/resources#testing-resources-with-state

Once a resource reaches a certain complexity, it may be desirable to manage the state of the resource over its lifetime. This is useful for resources which require special initilization or cleanup such as database connections or file handles. Since ConfigurableResource is a dataclass meant to encapsulate config, it is not a good fit for managing state. The recommended pattern in this case is to build a separate class which manages the state of the resource which is provided by the ConfigurableResource class.

### Parameterizing pipeline behavior

* https://docs.dagster.io/guides/dagster/using-environment-variables-and-secrets#parameterizing-pipeline-behavior


## IO Managers

* https://docs.dagster.io/concepts/io-management/io-managers

## Sensors

* https://docs.dagster.io/concepts/partitions-schedules-sensors/sensors

Sensors are definitions in Dagster that allow you to instigate runs based on some external state change. For example, you can:

* Launch a run whenever a file appears in an s3 bucket
* Launch a run whenever another job materializes a specific asset
* Launch a run whenever an external system is down

A sensor defines an evaluation function that returns either:

* One or more RunRequest objects. Each run request launches a run.
* An optional SkipReason, which specifies a message which describes why no runs were requested.

The Dagster daemon runs each sensor evaluation function on a tight loop. If using sensors, follow the instructions in the Dagster daemon documentation to run your sensors.

## Schedules

* https://docs.dagster.io/concepts/partitions-schedules-sensors/schedules

A schedule is a Dagster definition that is used to execute a job at a fixed interval. Each time at which a schedule is evaluated is called a tick. The schedule definition can generate run configuration for the job on each tick.

Each schedule:

* Targets a single job
* Optionally defines a function that returns either:
  * One or more RunRequest objects. Each run request launches a run.
  * An optional SkipReason, which specifies a message which describes why no runs were requested

Dagster includes a scheduler, which runs as part of the dagster-daemon process. Once you have defined a schedule, see the dagster-daemon page for instructions on how to run the daemon in order to execute your schedules.