---
id: yrqjrfqqxg4itlnvmbhqzh3
title: Minikube
desc: ''
updated: 1656419537489
created: 1656339875131
---

[minikube](https://minikube.sigs.k8s.io)

## Installation

* https://minikube.sigs.k8s.io/docs/start/
* https://www.nodinrogers.com/post/2022-04-25-minikube-on-ubuntu/

### Debian

```sh
$ curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb
$ sudo dpkg -i minikube_latest_amd64.deb
```

```sh
$ minikube start
😄  minikube v1.26.0 на Ubuntu 22.04
✨  Automatically selected the docker driver. Other choices: ssh, none
❗  docker is currently using the btrfs storage driver, consider switching to overlay2 for better performance
📌  Using Docker driver with root privileges
👍  Запускается control plane узел minikube в кластере minikube
🚜  Скачивается базовый образ ...
    > gcr.io/k8s-minikube/kicbase: 386.00 MiB / 386.00 MiB  100.00% 4.38 MiB p/
    > gcr.io/k8s-minikube/kicbase: 0 B [________________________] ?% ? p/s 1m1s
🔥  Creating docker container (CPUs=2, Memory=7900MB) ...
    > kubectl.sha256: 64 B / 64 B [--------------------------] 100.00% ? p/s 0s
    > kubelet.sha256: 64 B / 64 B [--------------------------] 100.00% ? p/s 0s
    > kubeadm.sha256: 64 B / 64 B [--------------------------] 100.00% ? p/s 0s
    > kubeadm: 42.31 MiB / 42.31 MiB [--------------] 100.00% 8.58 MiB p/s 5.1s
    > kubectl: 43.59 MiB / 43.59 MiB [---------------] 100.00% 2.87 MiB p/s 15s
    > kubelet: 110.96 MiB / 110.96 MiB [-------------] 100.00% 6.21 MiB p/s 18s

    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
🔎  Компоненты Kubernetes проверяются ...
    ▪ Используется образ gcr.io/k8s-minikube/storage-provisioner:v5
🌟  Включенные дополнения: storage-provisioner, default-storageclass
🏄  Готово! kubectl настроен для использования кластера "minikube" и "default" пространства имён по умолчанию
```

```sh
$ minikube status
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

```sh
$ minikube stop
✋  Узел "minikube" останавливается ...
🛑  Выключается "minikube" через SSH ...
🛑  Остановлено узлов: 1.
```

## Registry

* https://minikube.sigs.k8s.io/docs/handbook/registry/
* https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/
* https://stackoverflow.com/questions/42564058/how-to-use-local-docker-images-with-minikube

```
minikube addons configure registry-creds
```
вроде работает, еще вариант
```
kubectl create secret docker-registry regcred --docker-server=418337671376.dkr.ecr.eu-central-1.amazonaws.com --docker-username=AWS --docker-password=$(aws ecr get-login-password) --namespace=hive
```
добавить креды

## Использование


### PostgreSQL

```sh
$ helm install postgresdb bitnami/postgresql
NAME: postgresdb
LAST DEPLOYED: Tue Jun 28 15:49:11 2022
NAMESPACE: metastore
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
CHART NAME: postgresql
CHART VERSION: 11.6.10
APP VERSION: 14.4.0

** Please be patient while the chart is being deployed **

PostgreSQL can be accessed via port 5432 on the following DNS names from within your cluster:

    postgresdb-postgresql.metastore.svc.cluster.local - Read/Write connection

To get the password for "postgres" run:

    export POSTGRES_PASSWORD=$(kubectl get secret --namespace metastore postgresdb-postgresql -o jsonpath="{.data.postgres-password}" | base64 -d)

To connect to your database run the following command:

    kubectl run postgresdb-postgresql-client --rm --tty -i --restart='Never' --namespace metastore --image docker.io/bitnami/postgresql:14.4.0-debian-11-r3 --env="PGPASSWORD=$POSTGRES_PASSWORD" \
      --command -- psql --host postgresdb-postgresql -U postgres -d postgres -p 5432

    > NOTE: If you access the container using bash, make sure that you execute "/opt/bitnami/scripts/postgresql/entrypoint.sh /bin/bash" in order to avoid the error "psql: local user with ID 1001} does not exist"

To connect to your database from outside the cluster execute the following commands:

    kubectl port-forward --namespace metastore svc/postgresdb-postgresql 5432:5432 &
    PGPASSWORD="$POSTGRES_PASSWORD" psql --host 127.0.0.1 -U postgres -d postgres -p 5432
```
