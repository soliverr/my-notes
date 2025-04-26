---
id: 3q0tt9cz3foiajtt7b15uzh
title: Airflow Development Environment
desc: Разработка и тестирование в проекте Airflow
updated: 1647005303921
created: 1629730807088
---

# Подготовка среды для разработки и тестирования

Этапы по подготовке среды для разработки и тестирования приведены в документах:

* [Contributor's Quick Guide](https://github.com/apache/airflow/blob/main/CONTRIBUTORS_QUICK_START.rst)
* [Contributing](https://github.com/apache/airflow/blob/main/CONTRIBUTING.rst)
* [Breeze](https://github.com/apache/airflow/blob/main/BREEZE.rst)
* [Local Virtual Environment](https://github.com/apache/airflow/blob/main/LOCAL_VIRTUALENV.rst)

## Подготовка среды в Ubuntu

<https://github.com/apache/airflow/blob/main/CONTRIBUTORS_QUICK_START.rst#installing-prerequisites-on-ubuntu>

Рекомендуется установить следующие пакеты в системе:

```sh
$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
```

Так же отдельно выделяется потребность в `jq`:

```sh
$ sudo apt install jq
```

Необходимо активировать среду Docker:

* docker
* docker-compose

Рекомендуется использовать `pyenv` и плагин _virtualenv_. Для инициализации `pyenv` потребуются следующие пакеты:

```sh
sudo apt-get install -y build-essential libssl-dev zlib1g-dev libbz2-dev \
    libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev \
    xz-utils tk-dev libffi-dev liblzma-dev python-openssl git
```

Так же по результатам экспериментов для инициализации виртуального окружения потребуются следующие пакеты:

```sh
$ sudo apt install build-essential python3.6-dev python3.7-dev python3.8-dev python-dev openssl \
                  sqlite3 libsqlite3-dev default-libmysqlclient-dev postgresql rustc
```

_Любопытно, что где-то даже есть зависимость на Rust_.

## Тестирование в проекте Airflow

Использование [Local Virtual Environment](https://github.com/apache/airflow/blob/main/LOCAL_VIRTUALENV.rst) является необходимым условием для работы с проектом Airflow - это облегчает разработку и тестирование проекта под разные версии Python и модулей Python. Интеграция с IDE (например, IntelliJ PyCharm, VSCode, etc), позволяет пользоваться автоматизацией разработки, предлагаемой соответсвующей средой разработки.

> Use the local virtualenv development option in combination with the Breeze development environment. This option helps you benefit from the infrastructure provided by your IDE (for example, IntelliJ PyCharm/IntelliJ Idea) and work in the environment where all necessary dependencies and tests are available and set up within Docker images.
>
> But you can also use the local virtualenv as a standalone development option if you develop Airflow functionality that does not incur large external dependencies and CI test coverage.
>
> These are examples of the development options available with the local virtualenv in your IDE:
>
> * local debugging;
> * Airflow source view;
> * auto-completion;
> * documentation support;
> * unit tests.

Основным средством автоматизации тестирования и сборки проекта является [Breeze](https://github.com/apache/airflow/blob/main/BREEZE.rst). Breeze - это bash-скрипт, автоматизирующий основные операции разработки, тестирования, CI/CD в проекте Airflow.

## Установка системных пакетов

По реузльтатам экспериментов, лучше сразу установить в систему следующие пакеты, требующиеся на этапе инициализации виртуального окружения:

```sh
$ sudo apt install build-essential python3.6-dev python3.7-dev python3.8-dev python-dev openssl \
                  sqlite3 libsqlite3-dev default-libmysqlclient-dev postgresql rustc
```

## Создание виртуального окружения

Для Python существует несколько способов создания виртуального окружения.

_TODO_: должна быть ссылка на системы создания виртуальных окружений в Python.

### venv IntelliJ PyCharm

**Данный способ не сработал**

![Add Python Interprter](/assets/images/2021-08-23-14-19-16.png)

**Наследовать глобальные системные пакеты нельзя**, это приводит к последующим проблемам с инициализацией Breeze. Поэтому виртуальное окружение нужно создавать вот так:

![](/assets/images/2021-08-23-17-54-40.png)

Запустится процесс создания окружения:

![Creating Virtual Environment](/assets/images/2021-08-23-14-20-31.png)

### workon

_TODO_: Что это? Ссылка в документации.

### pyenv

#### Установка pyenv

[[Установка pyenv|python.virtualenvironments.pyenv]].

Должен установиться плагин `virtualenv`, который используется для создания виртуального окружения Python.

#### Установка требуемой версии Python

**Для pyenv Python устанавливается из исходников**:

```sh
$ pyenv install 3.6.14
```

#### Инициализация виртуального окружения

```sh
$ pyenv virtualenv 3.6.14 airflow-v2-1-stable
$ pyenv activate airflow-v2-1-stable
```

## Установка требуемых Python-пакетов

Для разработки и тестирования необходимо установить требуемые для Airflow версии модулей (пакетов) Python. В проекте Airflow, версии пакетов указываются в файле управления сборкой пакета `setup.py`. 

Инициализация окружения может быть реализована типичным для Python-проектов способом:

```sh
pip install --upgrade -e ".[devel,google,postgres]"
```

Либо с помощью _breeze_:

```sh
./breeze initialize-local-virtualenv
```

_Установка пакетов должна происходить в активированном виртуальном окружении Python_.

### Файлы ограничений

Для поддержки нескольких версий в Airflow реализована публикация файлов _ограничений_ - требуемых версий пакетов для каждой версии Airflow.

Файлы ограничений публикуются отдельно на сайте guthub, например:

* https://raw.githubusercontent.com/apache/airflow/constraints-main/constraints-3.6.txt
* https://raw.githubusercontent.com/apache/airflow/constraints-2-1/constraints-3.6.txt

Автоматизация реализована в _breeze_. Управление происходит через переменную `${DEFAULT_CONSTRAINTS_BRANCH}`, которая зависит от ветки проекта Airflow.

Значение этой переменной устанавливается в файле `airflow/scripts/ci/libraries/_initialization.sh`:

```sh
# Determine current branch
function initialization::initialize_branch_variables() {
    # Default branch used - this will be different in different branches
    export DEFAULT_BRANCH=${DEFAULT_BRANCH="v2-1-test"}
    export DEFAULT_CONSTRAINTS_BRANCH=${DEFAULT_CONSTRAINTS_BRANCH="constraints-2-1"}
    readonly DEFAULT_BRANCH
    readonly DEFAULT_CONSTRAINTS_BRANCH

    # Default branch name for triggered builds is the one configured in default branch
    # We need to read it here as it comes from _common_values.sh
    export BRANCH_NAME=${BRANCH_NAME:=${DEFAULT_BRANCH}}
}
```

Таким образом, _breeze_ - это **рекомендованный в проекте способ инициализации виртуального окружения для разработки**.

### Установка требуемых пакетов с помощью `pip`

**Попытка использования `venv` и `pip` не привела к работающему варианту инициализации виртуального окружения.**

**Надо использовать `breeze` и `pyenv` !!!**

Далее показаны шаги инициализации с помощью `pip`. Инициализация производилась методом venv в PyCharm.

```sh
oliver@kryazhevskikh:~/src/bigdata/airflow/airflow$ . venv3.6/bin/activate

(venv3.6) oliver@kryazhevskikh:~/src/bigdata/airflow/airflow$ pip install --upgrade -e ".[devel,google,postgres]"
```

#### Ошибки процесса

##### `mysql_config not found`

**Ошибка**:

```sh
Collecting mysqlclient<3,>=1.3.6
  Using cached mysqlclient-2.0.3.tar.gz (88 kB)
    ERROR: Command errored out with exit status 1:
     command: /home/oliver/src/bigdata/airflow/airflow/venv3.6/bin/python -c 'import io, os, sys, setuptools, tokenize; sys.argv[0] = '"'"'/tmp/pip-install-8kkff2xn/mysqlclient_d4f18028cb864d4b8fc46f8598e1f90e/setup.py'"'"'; __file__='"'"'/tmp/pip-install-8kkff2xn/mysqlclient_d4f18028cb864d4b8fc46f8598e1f90e/setup.py'"'"';f = getattr(tokenize, '"'"'open'"'"', open)(__file__) if os.path.exists(__file__) else io.StringIO('"'"'from setuptools import setup; setup()'"'"');code = f.read().replace('"'"'\r\n'"'"', '"'"'\n'"'"');f.close();exec(compile(code, __file__, '"'"'exec'"'"'))' egg_info --egg-base /tmp/pip-pip-egg-info-u6w6bqy0
         cwd: /tmp/pip-install-8kkff2xn/mysqlclient_d4f18028cb864d4b8fc46f8598e1f90e/
    Complete output (15 lines):
    /bin/sh: 1: mysql_config: not found
    /bin/sh: 1: mariadb_config: not found
    /bin/sh: 1: mysql_config: not found
    Traceback (most recent call last):
      File "<string>", line 1, in <module>
      File "/tmp/pip-install-8kkff2xn/mysqlclient_d4f18028cb864d4b8fc46f8598e1f90e/setup.py", line 15, in <module>
        metadata, options = get_config()
      File "/tmp/pip-install-8kkff2xn/mysqlclient_d4f18028cb864d4b8fc46f8598e1f90e/setup_posix.py", line 70, in get_config
        libs = mysql_config("libs")
      File "/tmp/pip-install-8kkff2xn/mysqlclient_d4f18028cb864d4b8fc46f8598e1f90e/setup_posix.py", line 31, in mysql_config
        raise OSError("{} not found".format(_mysql_config_path))
    OSError: mysql_config not found
```

**Решение**:

```sh
$ sudo apt install mysql-client default-libmysqlclient-dev
```

##### `Python.h: Нет такого файла или каталога`

**Ошибка**:

```sh
 ERROR: Command errored out with exit status 1:
   command: /home/oliver/src/bigdata/airflow/airflow/venv3.6/bin/python -u -c 'import io, os, sys, setuptools, tokenize; sys.argv[0] = '"'"'/tmp/pip-install-_cd7b8np/mysqlclient_ce1a310b984b46b996914b9816d2b81b/setup.py'"'"'; __file__='"'"'/tmp/pip-install-_cd7b8np/mysqlclient_ce1a310b984b46b996914b9816d2b81b/setup.py'"'"';f = getattr(tokenize, '"'"'open'"'"', open)(__file__) if os.path.exists(__file__) else io.StringIO('"'"'from setuptools import setup; setup()'"'"');code = f.read().replace('"'"'\r\n'"'"', '"'"'\n'"'"');f.close();exec(compile(code, __file__, '"'"'exec'"'"'))' bdist_wheel -d /tmp/pip-wheel-rlx8l9bt
       cwd: /tmp/pip-install-_cd7b8np/mysqlclient_ce1a310b984b46b996914b9816d2b81b/
  Complete output (44 lines):
  mysql_config --version
  ['8.0.26']
  mysql_config --libs
  ['-L/usr/lib/x86_64-linux-gnu', '-lmysqlclient', '-lpthread', '-ldl', '-lz', '-lssl', '-lcrypto', '-lresolv', '-lm', '-lrt']
  mysql_config --cflags
  ['-I/usr/include/mysql']
  ext_options:
    library_dirs: ['/usr/lib/x86_64-linux-gnu']
    libraries: ['mysqlclient', 'pthread', 'dl', 'resolv', 'm', 'rt']
    extra_compile_args: ['-std=c99']
    extra_link_args: []
    include_dirs: ['/usr/include/mysql']
    extra_objects: []
    define_macros: [('version_info', "(2,0,3,'final',0)"), ('__version__', '2.0.3')]
  running bdist_wheel
  running build
  running build_py
  creating build
  creating build/lib.linux-x86_64-3.6
  creating build/lib.linux-x86_64-3.6/MySQLdb
  copying MySQLdb/__init__.py -> build/lib.linux-x86_64-3.6/MySQLdb
  copying MySQLdb/_exceptions.py -> build/lib.linux-x86_64-3.6/MySQLdb
  copying MySQLdb/connections.py -> build/lib.linux-x86_64-3.6/MySQLdb
  copying MySQLdb/converters.py -> build/lib.linux-x86_64-3.6/MySQLdb
  copying MySQLdb/cursors.py -> build/lib.linux-x86_64-3.6/MySQLdb
  copying MySQLdb/release.py -> build/lib.linux-x86_64-3.6/MySQLdb
  copying MySQLdb/times.py -> build/lib.linux-x86_64-3.6/MySQLdb
  creating build/lib.linux-x86_64-3.6/MySQLdb/constants
  copying MySQLdb/constants/__init__.py -> build/lib.linux-x86_64-3.6/MySQLdb/constants
  copying MySQLdb/constants/CLIENT.py -> build/lib.linux-x86_64-3.6/MySQLdb/constants
  copying MySQLdb/constants/CR.py -> build/lib.linux-x86_64-3.6/MySQLdb/constants
  copying MySQLdb/constants/ER.py -> build/lib.linux-x86_64-3.6/MySQLdb/constants
  copying MySQLdb/constants/FIELD_TYPE.py -> build/lib.linux-x86_64-3.6/MySQLdb/constants
  copying MySQLdb/constants/FLAG.py -> build/lib.linux-x86_64-3.6/MySQLdb/constants
  running build_ext
  building 'MySQLdb._mysql' extension
  creating build/temp.linux-x86_64-3.6
  creating build/temp.linux-x86_64-3.6/MySQLdb
  x86_64-linux-gnu-gcc -pthread -DNDEBUG -g -fwrapv -O2 -Wall -g -fdebug-prefix-map=/build/python3.6-t1GNlY/python3.6-3.6.14=. -specs=/usr/share/dpkg/no-pie-compile.specs -fstack-protector-strong -Wformat -Werror=format-security -Wdate-time -D_FORTIFY_SOURCE=2 -fPIC -Dversion_info=(2,0,3,'final',0) -D__version__=2.0.3 -I/usr/include/mysql -I/home/oliver/src/bigdata/airflow/airflow/venv3.6/include -I/usr/include/python3.6m -c MySQLdb/_mysql.c -o build/temp.linux-x86_64-3.6/MySQLdb/_mysql.o -std=c99
  MySQLdb/_mysql.c:46:10: fatal error: Python.h: Нет такого файла или каталога
     46 | #include "Python.h"
        |          ^~~~~~~~~~
  compilation terminated.
  error: command 'x86_64-linux-gnu-gcc' failed with exit status 1
  ----------------------------------------
  ERROR: Failed building wheel for mysqlclient
```

[**Решение**](https://stackoverflow.com/questions/66256779/failed-to-install-mysqlclient-on-python3).

Необходимо поствить версию, соответствующую версии Python в виртуальном окружении:

```sh
$ sudo apt-get install python3.6-dev
```

##### `ImportError: cannot import name '_psutil_linux'`

**Ошибка**:

```sh
(venv3.6) oliver@kryazhevskikh:~/src/bigdata/airflow/airflow$ airflow db init

/usr/lib/python3/dist-packages/requests/__init__.py:91 RequestsDependencyWarning: urllib3 (1.26.6) or chardet (3.0.4) doesn't match a supported version!
Traceback (most recent call last):
  File "/home/oliver/src/bigdata/airflow/airflow/venv3.6/bin/airflow", line 11, in <module>
    load_entry_point('apache-airflow', 'console_scripts', 'airflow')()
  File "/home/oliver/src/bigdata/airflow/airflow/airflow/__main__.py", line 40, in main
    args.func(args)
  File "/home/oliver/src/bigdata/airflow/airflow/airflow/cli/cli_parser.py", line 47, in command
    func = import_string(import_path)
  File "/home/oliver/src/bigdata/airflow/airflow/airflow/utils/module_loading.py", line 32, in import_string
    module = import_module(module_path)
  File "/usr/lib/python3.6/importlib/__init__.py", line 126, in import_module
    return _bootstrap._gcd_import(name[level:], package, level)
  File "<frozen importlib._bootstrap>", line 994, in _gcd_import
  File "<frozen importlib._bootstrap>", line 971, in _find_and_load
  File "<frozen importlib._bootstrap>", line 955, in _find_and_load_unlocked
  File "<frozen importlib._bootstrap>", line 665, in _load_unlocked
  File "<frozen importlib._bootstrap_external>", line 678, in exec_module
  File "<frozen importlib._bootstrap>", line 219, in _call_with_frames_removed
  File "/home/oliver/src/bigdata/airflow/airflow/airflow/cli/commands/db_command.py", line 24, in <module>
    from airflow.utils import cli as cli_utils, db
  File "/home/oliver/src/bigdata/airflow/airflow/airflow/utils/db.py", line 26, in <module>
    from airflow.jobs.base_job import BaseJob  # noqa: F401 # pylint: disable=unused-import
  File "/home/oliver/src/bigdata/airflow/airflow/airflow/jobs/__init__.py", line 22, in <module>
    import airflow.jobs.scheduler_job  # noqa
  File "/home/oliver/src/bigdata/airflow/airflow/airflow/jobs/scheduler_job.py", line 61, in <module>
    from airflow.utils.dag_processing import AbstractDagFileProcessorProcess, DagFileProcessorAgent
  File "/home/oliver/src/bigdata/airflow/airflow/airflow/utils/dag_processing.py", line 53, in <module>
    from airflow.utils.process_utils import kill_child_processes_by_pids, reap_process_group
  File "/home/oliver/src/bigdata/airflow/airflow/airflow/utils/process_utils.py", line 34, in <module>
    import psutil
  File "/usr/lib/python3/dist-packages/psutil/__init__.py", line 95, in <module>
    from . import _pslinux as _psplatform
  File "/usr/lib/python3/dist-packages/psutil/_pslinux.py", line 26, in <module>
    from . import _psutil_linux as cext
ImportError: cannot import name '_psutil_linux'
```

[**Решение**](https://stackoverflow.com/questions/50671671/apache-airflow-importerror-cannot-import-name-psutil-linux):

```sh
(venv3.6) oliver@kryazhevskikh:~/src/bigdata/airflow/airflow$ pip install -U --ignore-installed psutil
```

### Установка требуемых пакетов с помощью `breeze`

#### Проблемы с _venv_

По непонятным пока причинам `breeze` в окружении _venv_ не может поставить требуемый набор пакетов.

Ошибки выглядят вот так, только могут быть указаны другие пакеты Python:

```sh
The conflict is caused by:
    apache-airflow[devel] 2.1.3 depends on sphinxcontrib-spelling==7.2.1; extra == "devel"
    The user requested (constraint) sphinxcontrib-spelling==5.2.1
```

#### Рабочий вариант с _pyenv_

```sh
oliver@kryazhevskikh:~/src/bigdata/airflow/airflow$ pyenv activate airflow-v2-1-stable
(airflow-v2-1-stable) oliver@kryazhevskikh:~/src/bigdata/airflow/airflow$ ./breeze initialize-local-virtualenv
```

##### Ошибка обнаружения Rust

**Ошибка**:

```
 error: can't find Rust compiler
```

**Решение**:

```sh
$ sudo apt install rustc
```

После этого инициализация окружения прошла успешно:

```sh
Initialization of virtualenv was successful! Go ahead and develop Airflow!

(airflow-v2-1-stable) oliver@kryazhevskikh:~/src/bigdata/airflow/airflow$ python
Python 3.6.14 (default, Aug 23 2021, 19:42:18) 
[GCC 9.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import airflow

(airflow-v2-1-stable) oliver@kryazhevskikh:~/src/bigdata/airflow/airflow$ airflow scheduler
  ____________       _____________
 ____    |__( )_________  __/__  /________      __
____  /| |_  /__  ___/_  /_ __  /_  __ \_ | /| / /
___  ___ |  / _  /   _  __/ _  / / /_/ /_ |/ |/ /
 _/_/  |_/_/  /_/    /_/    /_/  \____/____/|__/
 * Serving Flask app "airflow.utils.serve_logs" (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: off
[2021-08-23 20:02:08,646] {_internal.py:113} INFO -  * Running on http://0.0.0.0:8793/ (Press CTRL+C to quit)
[2021-08-23 20:02:08,657] {scheduler_job.py:661} INFO - Starting the scheduler
[2021-08-23 20:02:08,659] {scheduler_job.py:666} INFO - Processing each file at most -1 times
```


## Создание БД для Airflow

Для тестирования создаётся БД под управлением _sqlite_:

```sh
(airflow-v2-1-stable) oliver@kryazhevskikh:~/src/bigdata/airflow/airflow$ airflow db init
DB: sqlite:////home/oliver/airflow/airflow.db
[2021-08-24 12:42:18,691] {db.py:702} INFO - Creating tables
INFO  [alembic.runtime.migration] Context impl SQLiteImpl.
INFO  [alembic.runtime.migration] Will assume non-transactional DDL.
WARNI [airflow.providers_manager] Exception when importing 'airflow.providers.apache.hive.hooks.hive.HiveServer2Hook' from 'apache-airflow-providers-apache-hive' package: module 'pandas' has no attribute 'core'
WARNI [airflow.providers_manager] Exception when importing 'airflow.providers.apache.hive.hooks.hive.HiveMetastoreHook' from 'apache-airflow-providers-apache-hive' package: module 'pandas' has no attribute 'core'
WARNI [airflow.providers_manager] Exception when importing 'airflow.providers.apache.hive.hooks.hive.HiveCliHook' from 'apache-airflow-providers-apache-hive' package: module 'pandas' has no attribute 'core'
WARNI [airflow.providers_manager] Exception when importing 'airflow.providers.apache.hive.hooks.hive.HiveServer2Hook' from 'apache-airflow-providers-apache-hive' package: module 'pandas' has no attribute 'core'
WARNI [airflow.providers_manager] Exception when importing 'airflow.providers.apache.hive.hooks.hive.HiveMetastoreHook' from 'apache-airflow-providers-apache-hive' package: module 'pandas' has no attribute 'core'
WARNI [airflow.models.crypto] empty cryptography key - values will not be stored encrypted.
Initialization done
```

## Выполнение unit-тестов

Процесс запуска тестов описан на странице [TESTING](https://github.com/apache/airflow/blob/main/TESTING.rst) проекта Airflow.

Unit-тесты можно запускать в IDE или в командной строке, например:

```sh
(airflow-v2-1-stable) oliver@kryazhevskikh:~/src/bigdata/airflow/airflow$ pytest tests/utils/test_json.py

========================================================================== test session starts ==========================================================================
platform linux -- Python 3.6.14, pytest-6.2.4, py-1.10.0, pluggy-0.13.1 -- /home/oliver/.pyenv/versions/3.6.14/envs/airflow-v2-1-stable/bin/python3.6
cachedir: .pytest_cache
rootdir: /home/oliver/src/bigdata/airflow/airflow, configfile: pytest.ini
plugins: timeouts-1.2.1, cov-2.12.1, rerunfailures-9.1.1, instafail-0.4.2, anyio-3.3.0, flaky-3.7.0, requests-mock-1.9.3, forked-1.3.0, xdist-2.3.0, httpx-0.12.0
setup timeout: 0.0s, execution timeout: 0.0s, teardown timeout: 0.0s
collected 12 items  
...
============================================================================ 12 passed in 0.91s ============================================================================
```

# Сборка пакетов Airflow

## Сборка дополнительных пакетов 

```sh
(airflow-v2-1-stable) oliver@kryazhevskikh:~/src/bigdata/airflow/airflow$ ./breeze prepare-provider-packages --package-format wheel --version-suffix-for-pypi bia apache.spark
```

Согласно [схеме версий Python](https://www.python.org/dev/peps/pep-0440/#version-scheme) параметр `--version-suffix-for-pypi` работает следующим образом:

* `--version-suffix-for-pypi '-2'`: _apache_airflow_providers_apache_spark-2.0.0.**post2**-py3-none-any.whl_
* `--version-suffix-for-pypi '2'`: _apache_airflow_providers_apache_spark-**2.0.2**-py3-none-any.whl_


Собрался файл:

```sh
(airflow-v2-1-stable) oliver@kryazhevskikh:~/src/bigdata/airflow/airflow$ ls dist/
apache_airflow_providers_apache_spark-2.0.0bia-py3-none-any.whl
```

Загрузка в локальный репозиторий (Nexus):

```sh
$ twine upload --username <username> --password <password> --repository-url https://hdp-nx3.dellin.ru/repository/pipy-bia/ dist/*
```

# Разработка под Ariflow

* [Apache Airflow — Using PyCharm and Docker for remote debugging](https://medium.com/@andrewhharmon/apache-airflow-using-pycharm-and-docker-for-remote-debugging-b2d1edf83d9d)

## Управление пользовательскими модулями и плагинами

* [Modules Management](https://airflow.apache.org/docs/apache-airflow/stable/modules_management.html)
* [Plugins](https://airflow.apache.org/docs/apache-airflow/stable/plugins.html)

