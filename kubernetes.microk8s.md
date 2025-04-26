---
id: vzvf6ngsuiedah3rzse5iiq
title: Microk8s
desc: ''
updated: 1657633572010
created: 1657616194665
---

# MicroK8S

* https://microk8s.io/

# Install 

https://microk8s.io/#install-microk8s


## Install MicroK8s on Linux

**Install:**

`sudo snap install microk8s --classic`

Don’t have the snap command? Get set up for snaps

**Check the status while Kubernetes starts**

```sh
$ sudo usermod -a -G microk8s oliver
$ $ sudo chown -f -R oliver ~/.kube
$ newgrp microk8s
```

```sh
microk8s status --wait-ready

microk8s is running
high-availability: no
  datastore master nodes: 127.0.0.1:19001
  datastore standby nodes: none
addons:
  enabled:
    ha-cluster           # (core) Configure high availability on the current node
  disabled:
    community            # (core) The community addons repository
    dashboard            # (core) The Kubernetes dashboard
    dns                  # (core) CoreDNS
    gpu                  # (core) Automatic enablement of Nvidia CUDA
    helm                 # (core) Helm 2 - the package manager for Kubernetes
    helm3                # (core) Helm 3 - Kubernetes package manager
    host-access          # (core) Allow Pods connecting to Host services smoothly
    hostpath-storage     # (core) Storage class; allocates storage from host directory
    ingress              # (core) Ingress controller for external access
    mayastor             # (core) OpenEBS MayaStor
    metallb              # (core) Loadbalancer for your Kubernetes cluster
    metrics-server       # (core) K8s Metrics Server for API access to service metrics
    prometheus           # (core) Prometheus operator for monitoring and logging
    rbac                 # (core) Role-Based Access Control for authorisation
    registry             # (core) Private image registry exposed on localhost:32000
    storage              # (core) Alias to hostpath-storage add-on, deprecated

```

**Turn on the services you want**

`microk8s enable dashboard dns registry istio`

Try `microk8s enable --help` for a list of available services and optional features. `microk8s disable <name>` turns off a service.

**Start using Kubernetes**

`microk8s kubectl get all --all-namespaces`

If you mainly use MicroK8s you can make our kubectl the default one on your command-line with `alias mkctl="microk8s kubectl"`. Since it is a standard upstream kubectl, you can also drive other Kubernetes clusters with it by pointing to the respective kubeconfig file via the `--kubeconfig` argument.

**Access the Kubernetes dashboard**

`microk8s dashboard-proxy`

После запуска Dashboard:

![](/assets/images/2022-07-12-14-11-08.png)

Ошибка:

`Internal error (500): Not enough data to create auth info structure.`


После ввода токена для пользователя `admin` попал в dashboard:

![](/assets/images/2022-07-12-18-45-43.png)

**Start and stop Kubernetes to save battery**

Kubernetes is a collection of system services that talk to each other all the time. If you don’t need them running in the background then you will save battery by stopping them. `microk8s start` and `microk8s stop` will do the work for you.

**Конфигурирование в kubeconfig**

```
microk8s config
```

Добавил параметры вручную, пока не понял, как это делается через команду.