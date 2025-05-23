---
id: a8o0ppalydoehqdwmx7tv0v
title: Ansible
desc: ""
updated: 1655800741122
created: 1617891785340
tags:
  - configuration-management/ansible
  - devops/ansible
---
# [Ansible](https://docs.ansible.com/)

Ansible offers open-source automation that is simple, flexible, and powerful.
## [Installing Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)

* [Installing Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)

### Ubuntu

#### PPA 

На официальной странице документации по установке Ansible рекомендуют устанавливать свежую сборку из [этого PPA](https://launchpad.net/~ansible/+archive/ubuntu/ansible).

```console
$ sudo apt update
$ sudo apt install software-properties-common
$ sudo apt-add-repository --yes --update ppa:ansible/ansible
$ sudo apt install ansible
```

Оказалось, что нет сборки для свежей `Ubuntu Focal`:

```console
Ошб:19 http://ppa.launchpad.net/ansible/ansible/ubuntu focal Release
  404  Not Found [IP: 91.189.95.85 80]
...
E: Репозиторий «http://ppa.launchpad.net/ansible/ansible/ubuntu focal Release» не содержит файла Release.
...
```

Удалил:

```console
$ sudo apt-add-repository --remove ppa:ansible/ansible
```

Устанавливаю из официального репозитория:

```console
$ sudo apt -y install ansible ansible-doc ansible-lint
```

Получил базовые настройки на уровне системы:
* */etc/ansible/hosts* - системный файл инвентаризации
* */etc/ansible/ansible.cfg* - системный файл конфигурации

Получилось добавить PPA вручную:

```console
echo "deb http://ppa.launchpad.net/ansible/ansible/ubuntu focal main" | sudo tee /etc/apt/sources.list.d/ansible-ubuntu-ansible-focal.list
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 93C4A3FD7BB9C367
sudo apt update
```

20.02.21 12:31: Но есть только версия 2.9. Версия 2.10 присутствует как *ansible-base*

```console
$ apt-cache policy ansible
ansible:
  Установлен: 2.9.6+dfsg-1
  Кандидат:   2.9.6+dfsg-1
  Таблица версий:
 *** 2.9.6+dfsg-1 500
        500 http://ru.archive.ubuntu.com/ubuntu focal/universe amd64 Packages
        500 http://ru.archive.ubuntu.com/ubuntu focal/universe i386 Packages
        100 /var/lib/dpkg/status
$ apt-cache policy ansible-base
ansible-base:
  Установлен: (отсутствует)
  Кандидат:   2.10.6-1ppa~focal
  Таблица версий:
     2.10.6-1ppa~focal 500
        500 http://ppa.launchpad.net/ansible/ansible/ubuntu focal/main amd64 Packages
        500 http://ppa.launchpad.net/ansible/ansible/ubuntu focal/main i386 Packages
```

Точно, изменили имя пакета:

```console
$ sudo apt remove ansible
$ sudo apt install ansible-base
$ ansible --version
ansible 2.10.6
```

#### APT

Для Ubuntu 22.04 _Ansible_ появился в официальном репозитории пакетов, можно установить из него:

```sh
$ sudo apt install ansible
$ ansible --version
ansible 2.10.8
```

Всё таки в официальном дистрибутиве версия старая, например, не работают коллекции:

```
[WARNING]: Collection community.general does not support Ansible version 2.10.8
```

### pip

__Предпочтительный способ__.

_Можно настроить виртуальное окружение для работы с Ansible, см. [[python.virtualenvironments.pyenv]]_:

```sh
$ pyenv virtualenv ansible
created virtual environment CPython3.10.4.final.0-64 in 217ms
  creator CPython3Posix(dest=/home/oliver/.pyenv/versions/ansible, clear=False, no_vcs_ignore=False, global=False)
  seeder FromAppData(download=False, pip=bundle, setuptools=bundle, wheel=bundle, via=copy, app_data_dir=/home/oliver/.local/share/virtualenv)
    added seed packages: pip==22.0.2, setuptools==59.6.0, wheel==0.37.1
  activators BashActivator,CShellActivator,FishActivator,NushellActivator,PowerShellActivator,PythonActivator
Looking in links: /tmp/tmp2j_ikcol
Requirement already satisfied: setuptools in /home/oliver/.pyenv/versions/ansible/lib/python3.10/site-packages (59.6.0)
Requirement already satisfied: pip in /home/oliver/.pyenv/versions/ansible/lib/python3.10/site-packages (22.0.2)
oliver@soliver:~$ pyenv virtualenvs
  ansible (created from /usr)
```

Активировать окружение:

``sh
$ pyenv activate ansible
```

Либо установить Ansible на уровне системы: 

`sudo pip3 install ansible`

Либо установить Ansible на уровне пользователя:

```shell
$ pip --version
pip 20.0.2 from /usr/lib/python3/dist-packages/pip (python 3.8)
$ pip install ansible

$ ansible --version
ansible [core 2.12.1]
  config file = <>
  configured module search path = [<>, <>]
  ansible python module location = <>
  ansible collection location = <>
  executable location = <>
  python version = 3.8.10 (default, Sep 28 2021, 16:10:42) [GCC 9.3.0]
  jinja version = 2.10.1
  libyaml = True
```

_Работа в виртуальном окружении, основанном на `system` позволяет не конфликтовать с пакетами python, распространяемым через APT_.

Дополнительные пакеты для установки:

```
$ pip install jmespath
```

## Настройка

[Getting Started](https://docs.ansible.com/ansible/latest/user_guide/intro_getting_started.html)

### Организация иерархии каталогов

В руководстве  [Best Practice от предыдущеё версии](https://docs.ansible.com/ansible/2.4/playbooks_best_practices.html) Ansible предлагается два варианта организации [иерархии рабочих каталогов](https://docs.ansible.com/ansible/2.4/playbooks_best_practices.html#directory-layout)

Новое руководство называется [Tips and Tricks](https://docs.ansible.com/ansible/latest/user_guide/playbooks_best_practices.html) и почему-то не содержит рекомендаций по организации содержимого рабочего каталога Ansible.

### Использование коллекций и ролей для проектов

[Ansible best practices: using project-local collections and roles](https://www.jeffgeerling.com/blog/2020/ansible-best-practices-using-project-local-collections-and-roles)

[Using collections](https://docs.ansible.com/ansible/latest/user_guide/collections_using.html)

Видимо, версия 2.10 будет предоставлять  возможность верификации утсановленных и внешних ролей и коллекций.

### Файл конфигурации ansible.cfg

[Ansible Configuration Settings](https://docs.ansible.com/ansible/latest/reference_appendices/config.html)

> Changes can be made and used in a configuration file which will be searched for in the following order:
> * `ANSIBLE_CONFIG` (environment variable if set)
> * `ansible.cfg` (in the current directory)
> * `~/.ansible.cfg` (in the home directory)
> * `/etc/ansible/ansible.cfg`

### Локальное использование

Можно использовать Ansible для управления ПО и конфигурацией своей рабочей станции.

Создал каталог inventory _<some_dir>/inventories/local_, в нём файл _hosts_:

```yaml
---
# Local access to workstation
local:
  hosts:
    localhost:
      ansible_connection: local
      ansible_python_interpreter: /usr/bin/python3
```

Создал файл конфигурации _<some_dir>/ansible-local.cfg_:

```
[defaults]
inventory = ./inventories/local
host_key_checking = False
remote_user = ansible
roles_path = ./roles
forks = 2

[privilege_escalation]
become = True
become_ask_pass = False
become_user = root
become_method = sudo
```

Создал разрешение на `sudo` для пользователя `ansible`:

```sh
$ sudoedit /etc/sudoers.d/ansible
ansible  ALL=(ALL) NOPASSWD:ALL
```

Необходимо разрешить доступ по ssh, активировать временный ключ доступа:

```sh
NoneType: None
fatal: [localhost]: FAILED! => {
    "changed": false,
    "msg": "Could not detect which package manager to use. Try gathering facts or setting the \"use\" option."
}

PLAY RECAP *******************************************************************************************************************************************************************************************************
localhost                  : ok=0    changed=0    unreachable=0    failed=1    skipped=2    rescued=0    ignored=0   

(ansible) oliver@ThinkPad-T15:~/src/ansible-for-local-use$ sudo ls
...
(ansible) oliver@ThinkPad-T15:~/src/ansible-for-local-use$ ansible-playbook -i inventories/local plays/trino.yml --tags trino_install 
```

Пример использования:

```sh
export ANSIBLE_CONFIG=<some_dir>/ansible-local.cfg
$ ansible -m ping all
localhost | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```


## Тестирование

### [Molecule](https://molecule.readthedocs.io/en/latest/)

#### [Installation](https://molecule.readthedocs.io/en/latest/installation.html)

**Системные пакеты**

```console
sudo apt-get install -y python3-pip libssl-dev
```

**Molecule**

```console
$ python3 -m pip install "molecule[ansible]"
# or
$ python3 -m pip install "molecule[ansible-base]"
```

**Модули Molecule**

```console
$ python3 -m pip install --user "molecule[docker,lint,podman]"
```

Поставил Ansible 3.0.0

Other drivers, such as `molecule-podman`, `molecule-vagrant`, `molecule-azure` or `molecule-hetzner` are also available.

Похоже ещё нужен `molecule-goss` ?

Наткнулся на ошибку:  https://github.com/ansible-community/molecule/issues/1851

(-) Похоже надо остаться на версии Molecule 2.x https://github.com/ansible-community/molecule/issues/1851#issuecomment-763711895

(-) Проблема в способе установки пакетов: на уровне системы и на уровне pip: https://github.com/ansible-community/molecule/issues/2173#issuecomment-589607268

$ sudo apt remove ansible-base
$ python3 -m pip install --user ansible
$ python3 -m pip install --user "molecule[lint,docker]"
$ python3 -m pip install --user "molecule-goss"

Так и не помогает:

```
TASK [Create Dockerfiles from image names] *************************************
fatal: [localhost]: FAILED! => {"msg": "An unhandled exception occurred while templating '{{ lookup('file', molecule_file) | molecule_from_yaml }}'. Error was a <class 'ansible.errors.AnsibleError'>, original message: template error while templating string: no filter named 'molecule_from_yaml'. String: {{ lookup('file', molecule_file) | molecule_from_yaml }}"}
```

Всё таки этот фильтр удалён, https://github.com/ansible-community/molecule/releases/tag/3.1.1 Нужно от него отказаться

Вот что надо сделать: https://github.com/ansible-community/molecule/issues/2872

> The main reason behind this move is to finalize decoupling of molecule tool from ansible internals and removing the need to install Ansible python packages in the same python environment as molecule.

## Использование Ansible

### Ansible и Python3

https://docs.ansible.com/ansible/latest/reference_appendices/python_3_support.html

### Переменные для Playbook

[Ansible - Pass extra variables to Playbook](https://ttl255.com/ansible-pass-extra-variables-to-playbook/)

### Установка переменной в цикле при использовании jinja2

[SaltStack: Setting a jinja2 variable from an inner block scope](https://fabianlee.org/2016/10/18/saltstack-setting-a-jinja2-variable-from-an-inner-block-scope/):

> ... it is unfortunately the case that a jinja2 globally scoped variable cannot be modified from an inner scope.

Можно использовать `dictionaries`, так как можно менять значение записи в словаре, а сам словарь остаётся "неизменным":

```python
# works because dictionary pointer cannot change, but entries can 

{% set users = ['alice','bob','eve'] %} 
{% set foundUser = { 'flag': False } %} 

initial-check-on-global-foundUser: 
  cmd.run: 
    name: echo initial foundUser = {{foundUser.flag}} 

{% for user in users %} 
{%- if user == "bob" %} 
{%-   if foundUser.update({'flag':True}) %}{%- endif %} 
{%- endif %} 
echo-for-{{user}}: 
  cmd.run: 
    name: echo my name is {{user}}, has bob been found? {{foundUser.flag}} 
{% endfor %} 

final-check-on-global-foundUser: 
  cmd.run: 
    name: echo final foundUser = {{foundUser.flag}}
```

### Факты в Ansible

* [Discovering variables: facts and magic variables](https://docs.ansible.com/ansible/latest/user_guide/playbooks_vars_facts.html)
* [Usage of ansible local facts](https://evrard.me/usage-of-ansible-local-facts/)

## Ansible для контейнеризации

### Ansible Bender

[ansible-bender](https://github.com/ansible-community/ansible-bender)

This is a tool which bends containers using Ansible playbooks and turns them into container images. It has a pluggable builder selection — it is up to you to pick the tool which will be used to construct your container image. Right now the only supported builder is [buildah](https://github.com/containers/buildah). More to come in the future. Ansible-bender (ab) relies on Ansible connection plugins for performing builds.

The concept is described in following blog posts:

* [Building containers with buildah and ansible](https://blog.tomecek.net/post/building-containers-with-buildah-and-ansible/)
* [Ansible and Podman Can Play Together Now](https://blog.tomecek.net/post/ansible-and-podman-can-play-together-now/)

#### [Installation](https://ansible-community.github.io/ansible-bender/build/html/installation.html)

```sh
$ pip3 install ansible-bender
```

Похоже надо установить [buildah](https://github.com/containers/buildah/blob/main/install.md) и [podman](https://podman.io/getting-started/installation)

```sh
sudo apt-get -y update
sudo apt-get -y install buildah
sudo apt-get -y install podman
sudo apt-get -y install containers-storage
```

#### [Usage example](https://ansible-community.github.io/ansible-bender/build/html/usage.html)

```yaml
---
- name: Demonstration of ansible-bender functionality
  hosts: all
  vars:
    ansible_bender:
      base_image: python:3-alpine

      working_container:
        volumes:
          - '{{ playbook_dir }}:/src'

      target_image:
        name: a-very-nice-image
        working_dir: /src
        environment:
          FILE_TO_PROCESS: README.md
  tasks:
  - name: Run a sample command
    command: 'ls -lha /src'
  - name: Stat a file
    stat:
      path: "{{ lookup('env','FILE_TO_PROCESS') }}"
```

Не работает:

```sh
$ ansible-bender build ./simple-playbook.yaml 
There was an error during execution: ansible-playbook execution failed: Command '['ansible-playbook', '-c', 'local', '-i', '/tmp/ab1xma3f2r/i', '-e', 'ansible_python_interpreter=/home/oliver/.pyenv/versions/3.8.12/envs/ansible/bin/python3.8', './.simple-playbook-20220613145826952873-toieayelto.yaml']' returned non-zero exit status 2.
(ansible) oliver@soliver:~/tmp$ ansible-bender -vvvv build ./simple-playbook.yaml 
14:58:40.663 utils.py          INFO   running command: "['ansible-playbook', '--version']"
14:58:41.003 utils.py          INFO   running command: "['ansible-playbook', '-c', 'local', '-i', '/tmp/abks89k0rb/i', '-e', 'ansible_python_interpreter=/home/oliver/.pyenv/versions/3.8.12/envs/ansible/bin/python3.8', './.simple-playbook-20220613145840663271-bdyoqqswma.yaml']"
There was an error during execution: ansible-playbook execution failed: Command '['ansible-playbook', '-c', 'local', '-i', '/tmp/abks89k0rb/i', '-e', 'ansible_python_interpreter=/home/oliver/.pyenv/versions/3.8.12/envs/ansible/bin/python3.8', './.simple-playbook-20220613145840663271-bdyoqqswma.yaml']' returned non-zero exit status 2.
```

Проблема оказалась в том, что переменная *ansible_user* не определена, при этом _bender_ не показывает нормальный вывод _ansible-play_.

После изменения: `'{{ ansible_user | default(test) }}'` процесс заработал, но теперь возникла ошибка в работе Ansible:

```sh
PLAY [Demonstration of ansible-bender functionality] ***************************

TASK [Gathering Facts] *********************************************************
fatal: [a-very-nice-image-20220613-164152901194-cont]: UNREACHABLE! => {"changed": false, "msg": "Failed to create temporary directory.In some cases, you may have been able to authenticate and did not have permissions on the target directory. Consider changing the remote tmp path in ansible.cfg to a path rooted in \"/tmp\", for more error information use -vvv. Failed command was: ( umask 77 && mkdir -p \"` echo /tmp `\"&& mkdir \"` echo /tmp/ansible-tmp-1655120518.0186617-162223-255714983312628 `\" && echo ansible-tmp-1655120518.0186617-162223-255714983312628=\"` echo /tmp/ansible-tmp-1655120518.0186617-162223-255714983312628 `\" ), exited with result 125", "unreachable": true}

PLAY RECAP *********************************************************************
a-very-nice-image-20220613-164152901194-cont : ok=0    changed=0    unreachable=1    failed=0    skipped=0    rescued=0    ignored=0   
```




### Ansible и Podman

[Ansible and Podman Can Play Together Now](https://blog.tomecek.net/post/ansible-and-podman-can-play-together-now/):

```sh
$ podman pull registry.fedoraproject.org/fedora:32
$ podman run -d --name container registry.fedoraproject.org/fedora:32 sleep infinity

$ cat playbook.yml
- hosts: all
  connection: podman
  tasks:
  - dnf:
      state: latest

$ cat inv
container ansible_connection=podman ansible_python_interpreter=/usr/bin/python3

$ ansible-playbook -i inv ./playbook.yml

PLAY [all] *****************************************************************************************

TASK [Gathering Facts] *****************************************************************************
ok: [container]

TASK [dnf] *****************************************************************************************
ok: [container]

PLAY RECAP *****************************************************************************************
container                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0
```

### Ansible и Packer

[Build Docker Images Using Ansible and Packer](https://getbetterdevops.io/build-docker-images-using-ansible-and-packer/)


## Разработка коллекций

* https://docs.ansible.com/ansible/latest/user_guide/collections_using.html
* https://docs.ansible.com/ansible/latest/dev_guide/developing_collections.html
* https://goetzrieger.github.io/ansible-collections/5-creating-collections/


collection_dir#> ansible-galaxy collection init my_namespace.my_collection

collection_dir#> ansible-galaxy collection build

ansible-galaxy collection install /path/to/collection -p ./collections

collection_dir#> ansible-galaxy collection install my_namespace-my_collection-1.0.0.tar.gz -p ./collections

collection_dir#> ansible-galaxy collection install git+https://github.com/org/repo.git,devel

ansible-galaxy collection install my_namespace.my_collection

ansible-galaxy collection install my_namespace.my_collection --upgrade

ansible-galaxy collection publish path/to/my_namespace-my_collection-1.0.0.tar.gz --token abcdefghijklmnopqrtuvwxyz

collections:
  # Install a collection from Ansible Galaxy.
  - name: geerlingguy.php_roles
    version: 0.9.3
    source: https://galaxy.ansible.com

collections:
  # directory containing the collection
  - source: ./my_namespace/my_collection/
    type: dir

  # directory containing a namespace, with collections as subdirectories
  - source: ./my_namespace/
    type: subdirs

    collections:
  - source: /tmp/my_namespace-my_collection-1.0.0.tar.gz
    type: file

collections:
  - name: git@github.com:my_org/private_collections.git#/path/to/collection,devel
  - name: https://github.com/ansible-collections/amazon.aws.git
    type: git
    version: 8102847014fd6e7a3233df9ea998ef4677b99248

## Полезные приёмы

### Разбиение длинных строк 

https://stackoverflow.com/questions/3790454/how-do-i-break-a-string-in-yaml-over-multiple-lines

![](/media/2021-10-15-19-16-15.png)
