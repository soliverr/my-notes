---
id: lzglq14d1qd2vbih3wmr486
title: Dag
desc: ''
updated: 1646213393157
created: 1630070984651
---

## Best practice

* [Apache Airflow: how to add task to DAG without changing dag file?](https://xnuinside.medium.com/apache-airflow-add-validation-tasks-to-production-dag-for-end-2-end-tests-184e6d0af991)

## Cross-DAG Dependencies

[Cross-DAG Dependencies](https://airflow.apache.org/docs/apache-airflow/stable/howto/operator/external_task_sensor.html#cross-dag-dependencies)

* ExternalTaskSensor
* ExternalTaskMarker

### Примеры использования

* [Dependencies between DAGs in Apache Airflow](https://towardsdatascience.com/dependencies-between-dags-in-apache-airflow-2f5935cde3f0)
* [Cross-DAG dependencies in Apache Airflow](https://medium.com/quintoandar-tech-blog/effective-cross-dags-dependency-in-apache-airflow-1885dc7ece9f)

## DAG Serialization

[Airflow DAG Serialization](https://blog.duyet.net/2020/05/airflow-dag-serialization.html):

> Without DAG Serialization & persistence in DB, the Webserver and the Scheduler both needs access to the DAG files. Both the scheduler and webserver parses the DAG files.

[DAG Serialization](https://airflow.apache.org/docs/apache-airflow/stable/dag-serialization.html)

![](/assets/images/2021-09-01-11-21-58.png)

> As shown in the image above, when using this feature, the `DagFileProcessorProcess` in the Scheduler parses the DAG files, serializes them in JSON format and saves them in the Metadata DB as `SerializedDagModel` model.

> The Webserver now instead of having to parse the DAG files again, reads the serialized DAGs in JSON, de-serializes them and creates the DagBag and uses it to show in the UI. And the Scheduler does not need the actual DAGs for making scheduling decisions, instead of using the DAG files, we use the serialized DAGs that contain all the information needed to schedule the DAGs from Airflow 2.0.0 (this was done as part of [[Scheduler HA|airflow.ha]]).

> You can enable the source code to be stored in the database to make the Webserver completely independent of the DAG files. This is not necessary if your files are embedded in the Docker image or you can otherwise provide them to the Webserver. The data is stored in the `DagCode` model.

## DAG Bags and DAG Factories

* [DAG Factories — A better way to Airflow](https://towardsdatascience.com/dag-factories-a-better-way-to-airflow-9aa3cf003169)
* [Airflow DAGBags](https://xnuinside.medium.com/how-to-load-use-several-dag-folders-airflow-dagbags-b93e4ef4663c)
* [AIP-5 Remote DAG Fetcher](https://cwiki.apache.org/confluence/display/AIRFLOW/AIP-5+Remote+DAG+Fetcher)
* https://github.com/ajbosco/dag-factory
* https://airflow.apache.org/docs/apache-airflow/stable/_api/airflow/models/dagbag/index.html#module-airflow.models.dagbag

https://airflow.apache.org/docs/apache-airflow/stable/concepts/dags.html#loading-dags:

> When searching for DAGs inside the `DAG_FOLDER`, Airflow only considers Python files that contain the strings airflow and dag (case-insensitively) as an optimization.
> 
> To consider all Python files instead, disable the `DAG_DISCOVERY_SAFE_MODE` configuration flag.
> 
> You can also provide an _.airflowignore_ file inside your `DAG_FOLDER`, or any of its subfolders, which describes files for the loader to ignore. It covers the directory it’s in plus all subfolders underneath it, and should be one regular expression per line, with `#` indicating comments.

https://towardsdatascience.com/how-to-build-a-dag-factory-on-airflow-9a19ab84084c:

> There’s a little trick in the file in order for Airflow to recognize it’s a proper DAG that we’re returning.
> 
> When Airflow starts, the so-called DagBag process will parse all the files looking for DAGs. The way the current implementation works is something like this:
> 
> * The DagBag spawns different processes that look through the files of the dag folder.
> * The function called `process_file` [here](https://airflow.apache.org/docs/apache-airflow/stable/_modules/airflow/models/dagbag.html) runs for each file to figure out if there’s a DAG there.
> * The code runs might_contain_dag which returns a True depending if the file contains both “dag” and “airflow” in their code. Implementation [here](https://github.com/apache/airflow/blob/c61f3d45b4a1799e92ead1532d36f232ebc4686e/airflow/utils/file.py#L198).


## SubDAGs

[Using SubDAGs in Airflow](https://www.astronomer.io/guides/subdags/):

> **Note**: Astronomer highly recommends avoiding SubDAGs if the intended use of the SubDAG is to simply group tasks within a DAG’s Graph View. Airflow 2.0 introduces [Task Groups](https://airflow.apache.org/docs/apache-airflow/stable/concepts/dags.html#taskgroups-vs-subdags) which is a UI grouping concept that satisfies this purpose without the performance and functional issues of SubDAGs. While the `SubDagOperator` will continue to be supported, Task Groups are intended to replace it long-term.

SubDAGs, while serving a similar purpose as TaskGroups, introduces both performance and functional issues due to its implementation.

* The SubDagOperator starts a BackfillJob, which ignores existing parallelism configurations potentially oversubscribing the worker environment.
* SubDAGs have their own DAG attributes. _When the SubDAG DAG attributes are inconsistent with its parent DAG, unexpected behavior can occur._
* Unable to see the “full” DAG in one view as SubDAGs exists as a full fledged DAG.
* SubDAGs introduces all sorts of edge cases and caveats. This can disrupt user experience and expectation.

## [TaskGroups](https://airflow.apache.org/docs/apache-airflow/stable/concepts/dags.html#taskgroups)

* https://github.com/apache/airflow/blob/main/airflow/example_dags/example_task_group.py
