---
id: xf8ap44eh7yxjnep9i3vhsa
title: Kubernetes
desc: ''
updated: 1679582519226
created: 1622461598773
---
# Курсы по Kubernetes

* [Вечерняя школа Слёрма][https://www.youtube.com/watch?v=Jp866ltZBSk&list=PL8D2P0ruohOA4Y9LQoTttfSgsRwUGWpu6]
* [What is Headless Service? Setup a Service in Kubernetes - Knoldus Blogs](https://blog.knoldus.com/what-is-headless-service-setup-a-service-in-kubernetes/) #

# Дистрибутивы

* [k3s](https://k3s.io/)
* [minikube](kubernetes.minikube.md)
* [microk8s](kubernetes.microk8s.md)

# Kubernetes concepts and usage

* Pods
    * [Init Containers](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)
* Workload Resources
    * [Jobs](https://kubernetes.io/docs/concepts/workloads/controllers/job/)

## Configmaps

* [ConfigMaps](https://kubernetes.io/docs/concepts/configuration/configmap/)

Техника создания _Configmaps_ из локальных файлов и последующего использования в Pod:

```sh
$ kubectl create configmap metastore-cfg --dry-run --from-file=metastore-site.xml --from-file=core-site.xml -o yaml | kubectl apply -f -
```

```yaml
volumeMounts:
  - name: metastore-cfg-vol
    mountPath: /opt/hive-metastore/conf/metastore-site.xml
    subPath: metastore-site.xml
  - name: metastore-cfg-vol
    mountPath: /opt/hadoop/etc/hadoop/core-site.xml
    subPath: core-site.xml
  …
  volumes:
  - name: metastore-cfg-vol
    configMap:
      name: metastore-cfg
```

## Secrets

* [Secrets](https://kubernetes.io/docs/concepts/configuration/secret/)

Создание и использование secrets:

```sh
$ kubectl create secret generic my-s3-keys --from-literal=access-key=’XXXXXXX’ --from-literal=secret-key=’YYYYYYY’
```

```yaml
env:
  - name: AWS_ACCESS_KEY_ID
    valueFrom:
      secretKeyRef:
        name: my-s3-keys
        key: access-key
  - name: AWS_SECRET_ACCESS_KEY
    valueFrom:
      secretKeyRef:
        name: my-s3-keys
        key: secret-key
```

# Утилиты для работы с Kubernetes

## KubeCtl

* <https://ubuntu.com/kubernetes/install>
* <https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/>
* <https://www.nobackups.com/how-to-install-kubectl-on-ubuntu/>

Средства контроля кластера `kubectl` можно поставить несколькими способами.

### Installation

**Snap**

```sh
snap install kubectl --classic
```

Файл конфигурации по умолчанию <https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/>:

> By default, `kubectl` looks for a file named `config` in the `$HOME/.kube` directory. You can specify other kubeconfig files by setting the `KUBECONFIG` environment variable or by setting the `--kubeconfig` flag.

### Настройка

**bash auto-completion on Linux**

* https://kubernetes.io/docs/reference/kubectl/cheatsheet/
* https://kubernetes.io/docs/tasks/tools/included/optional-kubectl-configs-bash-linux/

**User level**:

```sh
source <(kubectl completion bash) # setup autocomplete in bash into the current shell, bash-completion package should be installed first.
echo "source <(kubectl completion bash)" >> ~/.bashrc # add autocomplete permanently to your bash shell.
```

**System level**:

```sh
$ sudo kubectl completion bash >/etc/bash_completion.d/kubectl
```

If you have an alias for kubectl, you can extend shell completion to work with that alias:

```sh
echo 'alias k=kubectl' >>~/.bashrc
echo 'complete -F __start_kubectl k' >>~/.bashrc
```

### Использование

* [Шпаргалка по kubectl](https://kubernetes.io/ru/docs/reference/kubectl/cheatsheet/)

* **Список пространтств**: `kubectl get namespaces`
* **Список сервисов**: `kubectl --namespace develop get services`
* **Список точек подключения**: `kubectl --namespace db get endpoints`
* **Список pod'ов**: `kubectl --namespace db get pod -o wide`
* **Поиск pod'ов**: `kubectl --namespace deveop get pods -o=jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.containers[*].env[?(@.name=="POSTGRE_HOST")]}{"\n"}{end}'`

### Krew - kubectl plugins manager

[Krew](https://github.com/kubernetes-sigs/krew/) is the package manager for kubectl plugins.

**Install**:

```sh
(
  set -x; cd "$(mktemp -d)" &&
  OS="$(uname | tr '[:upper:]' '[:lower:]')" &&
  ARCH="$(uname -m | sed -e 's/x86_64/amd64/' -e 's/\(arm\)\(64\)\?.*/\1\2/' -e 's/aarch64$/arm64/')" &&
  KREW="krew-${OS}_${ARCH}" &&
  curl -fsSLO "https://github.com/kubernetes-sigs/krew/releases/latest/download/${KREW}.tar.gz" &&
  tar zxvf "${KREW}.tar.gz" &&
  ./"${KREW}" install krew
)

# ~/.bashrc
export PATH="${KREW_ROOT:-$HOME/.krew}/bin:$PATH"

$ kubectl krew
krew is the kubectl plugin manager.
You can invoke krew through kubectl: "kubectl krew [command]..."

Usage:
  kubectl krew [command]

Available Commands:
  completion  generate the autocompletion script for the specified shell
  help        Help about any command
  index       Manage custom plugin indexes
  info        Show information about an available plugin
  install     Install kubectl plugins
  list        List installed kubectl plugins
  search      Discover kubectl plugins
  uninstall   Uninstall plugins
  update      Update the local copy of the plugin index
  upgrade     Upgrade installed plugins to newer versions
  version     Show krew version and diagnostics

Flags:
  -h, --help      help for krew
  -v, --v Level   number for the log level verbosity

Use "kubectl krew [command] --help" for more information about a command.
```


### kubectx & kubens

[kubectx + kubens](https://github.com/ahmetb/kubectx): Power tools for kubectl

**Install as plugin**:

```sh
kubectl krew install ctx
kubectl krew install ns

Updated the local copy of plugin index.
Installing plugin: ns
Installed plugin: ns
\
 | Use this plugin:
 | 	kubectl ns
 | Documentation:
 | 	https://github.com/ahmetb/kubectx
 | Caveats:
 | \
 |  | If fzf is installed on your machine, you can interactively choose
 |  | between the entries using the arrow keys, or by fuzzy searching
 |  | as you type.
 | /
/
```
### resource capacity

```sh
kubectl krew install resource-capacity
```

```sh
kubectl resource-capacity
```

### Alias for Bash and Zsh

```
#
# K8S
#
alias k='kubectl'
# При установленных плагинах CTX и NS
alias ktx='kubectl ctx'
alias kns='kubectl ns'
#
# Примеры переключения контекстов для работы с разными кластерами
#
#alias dev-cluster="aws eks --region eu-central-1 update-kubeconfig --name dev-cluster"
#alias prod-cluster="aws eks --region eu-central-1 update-kubeconfig --name prod-eu-central-1"
#alias minikube-cluster="k config use-context minikube"
```

## Lens

* https://k8slens.dev/
* https://docs.k8slens.dev/main/getting-started/#snap

Бинарная сборка для OpenLens https://github.com/MuhammedKalkan/OpenLens, не включает коммерческие функции и не требует входа в Lens.

## Helm

* [Helm](https://helm.sh/)
* [K8S package registry](https://artifacthub.io/)

Helm is a tool for managing Kubernetes packages called charts. Helm can do the following:

* Create new charts from scratch
* Package charts into chart archive (tgz) files
* Interact with chart repositories where charts are stored
* Install and uninstall charts into an existing Kubernetes cluster
* Manage the release cycle of charts that have been installed with Helm

For Helm, there are three important concepts:

1. The chart is a bundle of information necessary to create an instance of a Kubernetes application.
1. The config contains configuration information that can be merged into a packaged chart to create a releasable object.
1. A release is a running instance of a chart, combined with a specific config.


* https://medium.com/devops-mojo/helm-charts-overview-introduction-to-helm-101-ef6296ecff87
* https://devopslearning.medium.com/introduction-to-helm-and-creating-your-first-helm-chart-53c5df736bf2

### Install

https://helm.sh/docs/intro/install/

#### apt

```sh
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```

#### snap

```sh
sudo snap install helm --classic
```

### Использование Helm

#### Текущая версия

```sh
$ helm version
version.BuildInfo{Version:"v3.7.0", GitCommit:"eeac83883cb4014fe60267ec6373570374ce770b", GitTreeState:"clean", GoVersion:"go1.16.8"}
```

#### Текущее окружение

```sh
$ helm env
HELM_BIN="/snap/helm/353/helm"
HELM_CACHE_HOME="/home/oliver/.cache/helm"
HELM_CONFIG_HOME="/home/oliver/.config/helm"
HELM_DATA_HOME="/home/oliver/.local/share/helm"
HELM_DEBUG="false"
HELM_KUBEAPISERVER=""
HELM_KUBEASGROUPS=""
HELM_KUBEASUSER=""
HELM_KUBECAFILE=""
HELM_KUBECONTEXT=""
HELM_KUBETOKEN=""
HELM_MAX_HISTORY="10"
HELM_NAMESPACE="default"
HELM_PLUGINS="/home/oliver/.local/share/helm/plugins"
HELM_REGISTRY_CONFIG="/home/oliver/.config/helm/registry.json"
HELM_REPOSITORY_CACHE="/home/oliver/.cache/helm/repository"
HELM_REPOSITORY_CONFIG="/home/oliver/.config/helm/repositories.yaml"
```

#### Список репозиториев

`$ helm repo list`

#### Добавить репозитории

```sh
$ helm repo add stable https://charts.helm.sh/stable
$ helm repo add bitnami https://charts.bitnami.com/bitnami
$ helm repo add trino https://trinodb.github.io/charts/
$ helm repo add bigdata-gradiant https://gradiant.github.io/bigdata-charts/
```

#### Поиск пакета в репозитариях

`$ helm search repo <name>`

#### Поиск пакета в хабе

`$ helm search hub <name>`

#### Список установленных релизов в инфраструктуре K8S

`$ helm list`

### Helm Practice

#### [Docker + Kubernetes + Helm: A comprehensive step-by-step using Java](https://ignaciocicero.medium.com/
docker-kubernetes-helm-a-comprehensive-step-by-step-using-java-df83f6780d80)

https://github.com/nacho270/docker-k8s-helm-demo

> To use it (_Helm_), you just have to define:
> 
> * A Chart.yaml file stating the name of the chart
> * A values.yaml file with values for the variables you use inside your templates
> * A templates folder where you will store your kubernetes files.

#### [How To Create A Helm Chart](https://phoenixnap.com/kb/create-helm-chart)

* Как замапить файл в контейнер: https://emmenko.org/notes/mounting-a-file-with-helm-into-a-kubernetes-pod-using-a-config-map

### Frigate

[Figarate](https://frigate.readthedocs.io/en/latest/index.html)

## Kaniko

[kaniko](https://github.com/GoogleContainerTools/kaniko)  is a tool to build container images from a Dockerfile, inside a container or Kubernetes cluster.

kaniko doesn't depend on a Docker daemon and executes each command within a Dockerfile completely in userspace. This enables building container images in environments that can't easily or securely run a Docker daemon, such as a standard Kubernetes cluster.

kaniko is meant to be run as an image: gcr.io/kaniko-project/executor. We do not recommend running the kaniko executor binary in another image, as it might not work.

## sshuttle

[sshuttle](https://github.com/sshuttle/sshuttle) - where transparent proxy meets VPN meets ssh

* https://github.com/sshuttle/sshuttle/releases
### Installation

* **Ubuntu 16.04 or later**: `apt-get install sshuttle`
* **From PyPI**: `sudo pip install sshuttle`

### Usage

https://sshuttle.readthedocs.io/en/stable/usage.html#sudoers-file

* **Посмотреть изменения**: `sshuttle --sudoers-no-modify`
* **Создать запись для текущего пользователя**: `sshuttle --sudoers`

## kuttle

[kuttle](https://github.com/kayrus/kuttle) - kubectl wrapper for sshuttle without SSH

### Installation

```
mkdir $HOME/bin
curl -L https://github.com/kayrus/kuttle/raw/master/kuttle -o $HOME/bin/kuttle
chmod +x $HOME/bin/kuttle
```

sshuttle --dns -r '--namespace preprod kuttle' -e kuttle 0/0

Сам kuttle можно поставить в K8S вот так:

```
k run kuttle --image=alpine:latest --restart=Never -n preprod -- sh -c 'apk add python3 --update && exec tail -f /dev/null'
```


## telepresents

[telepresents](https://www.telepresence.io/) - Fast, local development for Kubernetes and OpenShift microservices

## Vector - parse logs

[Vector](https://vector.dev/) by DATADOG - A lightweight, ultra-fast tool for building observability pipelines

* https://github.com/vectordotdev/vector

## ExternalDNS

* [ExternalDNS](https://github.com/kubernetes-sigs/external-dns/blob/master/README.md)
* [How to expose Kubernetes DNS externally](https://stackoverflow.com/questions/55834721/how-to-expose-kubernetes-dns-externally)
* [Configuring your Linux host to resolve a local Kubernetes cluster’s service URLs | by Andy Goldstein | Heptio](https://blog.heptio.com/configuring-your-linux-host-to-resolve-a-local-kubernetes-clusters-service-urls-a8c7bdb212a7?gi=b43875876c47) #[[Roam-Highlights]]

## Kube Forward

* https://kubefwd.com/
  * [txn2/kubefwd: Bulk port forwarding Kubernetes services for local development.](https://github.com/txn2/kubefwd)

### Install

Список релизов https://github.com/txn2/kubefwd/releases

#### Ubuntu

Можно скачать deb-пакет 

```sh
kubefwd_version=1.22.4
curl -LOs https://github.com/txn2/kubefwd/releases/download/${kubefwd_version}/kubefwd_amd64.deb
sudo dpkg -i kubefwd_amd64.deb && rm  kubefwd_amd64.deb
```

## Kompose

[Kompose](https://kompose.io/) is a conversion tool for Docker Compose to container orchestrators such as Kubernetes (or OpenShift). 

## KubeNodeUsage

* https://github.com/AKSarav/Kube-Node-Usage