---
id: b7576743-0a41-40ca-a434-de70dc9f8d96
title: Untitled
desc: ''
created: 1747842450
updated: 1747842450
author: Sergei Kryazvevskikh <soliverr@gmail.com>
tags:  Untitled
---

```python
import subprocess
import os

def submit_spark_job_to_kubernetes(
    app_path: str,
    app_name: str,
    k8s_master_url: str,
    spark_image: str,
    namespace: str,
    service_account: str,
    executor_instances: int = 1,
    executor_memory: str = "2g",
    driver_memory: str = "1g",
    app_arguments: list = None,
    additional_confs: dict = None
):
    """
    Отправляет PySpark приложение в кластер Kubernetes.

    Args:
        app_path (str): Путь к файлу вашего PySpark приложения
                        (например, "local:///opt/spark/work-dir/your_pyspark_app.py").
                        Примечание: "local://" означает, что файл находится внутри
                        Docker образа Spark.
        app_name (str): Имя вашего Spark приложения.
        k8s_master_url (str): URL API сервера Kubernetes
                              (например, "k8s://https://your-k8s-api-server:6443").
        spark_image (str): Полное имя Docker образа Spark с вашим приложением
                           (например, "your-registry/your-pyspark-app:v1.0").
        namespace (str): Пространство имен Kubernetes, где будет запущен Spark.
        service_account (str): Имя ServiceAccount для Spark driver pod.
        executor_instances (int): Количество Spark executor'ов.
        executor_memory (str): Память для каждого executor'а.
        driver_memory (str): Память для Spark driver'а.
        app_arguments (list): Список аргументов для вашего PySpark приложения.
        additional_confs (dict): Дополнительные конфигурации Spark в виде словаря.
    """

    spark_submit_command = [
        "spark-submit",
        "--master", k8s_master_url,
        "--deploy-mode", "cluster",  # Cluster mode - driver runs in K8s
        "--name", app_name,
        "--conf", f"spark.executor.instances={executor_instances}",
        "--conf", f"spark.executor.memory={executor_memory}",
        "--conf", f"spark.driver.memory={driver_memory}",
        "--conf", f"spark.kubernetes.container.image={spark_image}",
        "--conf", f"spark.kubernetes.namespace={namespace}",
        "--conf", f"spark.kubernetes.authenticate.driver.serviceAccountName={service_account}",
        "--conf", "spark.kubernetes.executor.deleteOnTermination=true", # Удалять поды executor после завершения
        # Если ваш файл pySpark является частью Docker образа, используйте local://
        app_path
    ]

    if app_arguments:
        spark_submit_command.extend(app_arguments)

    if additional_confs:
        for key, value in additional_confs.items():
            spark_submit_command.extend(["--conf", f"{key}={value}"])

    print(f"Executing Spark-submit command: {' '.join(spark_submit_command)}")

    try:
        # Запускаем spark-submit как подпроцесс
        # capture_output=True для захвата stdout и stderr
        # text=True для декодирования вывода как текста
        # check=True для вызова исключения при ошибке
        result = subprocess.run(
            spark_submit_command,
            capture_output=True,
            text=True,
            check=True
        )
        print("\n--- Spark Submit Output (STDOUT) ---")
        print(result.stdout)
        print("\n--- Spark Submit Output (STDERR) ---")
        print(result.stderr)
        print("\nSpark job submitted successfully!")
    except subprocess.CalledProcessError as e:
        print(f"\n--- Spark Submit Failed (ERROR) ---")
        print(f"Command: {e.cmd}")
        print(f"Return Code: {e.returncode}")
        print(f"STDOUT: {e.stdout}")
        print(f"STDERR: {e.stderr}")
        raise RuntimeError(f"Spark job submission failed: {e}")
    except FileNotFoundError:
        print("Error: 'spark-submit' command not found. Ensure Spark is installed and in your PATH.")
        raise


# --- Пример использования ---
if __name__ == "__main__":
    # Убедитесь, что spark-submit доступен в вашей переменной окружения PATH
    # или укажите полный путь:
    # os.environ["PATH"] += os.pathsep + "/path/to/spark/bin"

    # Ваши параметры Spark и Kubernetes
    K8S_API_SERVER_URL = "k8s://https://YOUR_KUBERNETES_API_SERVER_IP:YOUR_KUBERNETES_API_SERVER_PORT"
    SPARK_APP_IMAGE = "your-registry/your-pyspark-app:v1.0" # Ваш Docker образ
    SPARK_NAMESPACE = "spark-namespace" # Пространство имен K8s
    SPARK_SERVICE_ACCOUNT = "spark-sa" # ServiceAccount, созданный ранее

    # Ваш PySpark скрипт внутри Docker образа
    # Убедитесь, что путь внутри образа совпадает с путем, куда вы скопировали ваш файл
    PYSPARK_APP_PATH_IN_IMAGE = "local:///opt/spark/work-dir/your_pyspark_app.py"

    # Дополнительные аргументы для вашего PySpark приложения (если есть)
    app_args = ["--input", "/path/to/data", "--output", "/path/for/results"]

    # Дополнительные Spark конфигурации (если есть)
    # Например, для настроек log4j
    additional_spark_confs = {
        "spark.driver.extraJavaOptions": "-Dlog4j.configuration=file:/opt/spark/conf/log4j.properties",
        "spark.executor.extraJavaOptions": "-Dlog4j.configuration=file:/opt/spark/conf/log4j.properties",
        "spark.kubernetes.driver.limit.cores": "1", # Ограничение по ядрам для драйвера
        "spark.kubernetes.executor.limit.cores": "1" # Ограничение по ядрам для экзекьюторов
    }

    try:
        submit_spark_job_to_kubernetes(
            app_path=PYSPARK_APP_PATH_IN_IMAGE,
            app_name="MyPySparkK8sApp",
            k8s_master_url=K8S_API_SERVER_URL,
            spark_image=SPARK_APP_IMAGE,
            namespace=SPARK_NAMESPACE,
            service_account=SPARK_SERVICE_ACCOUNT,
            executor_instances=2,
            executor_memory="3g",
            driver_memory="2g",
            app_arguments=app_args,
            additional_confs=additional_spark_confs
        )
    except Exception as e:
        print(f"An error occurred during Spark job submission: {e}")
```