---
id: 5l51h2qhjt8wio6p2wap0ks
title: Python Virtual Environments
desc: All about Python's virtual environment
updated: 1716202115227
created: 1629875851526
tags:
  - python
  - virtualenv
---

# Виртуальные окружения в Python

* <https://realpython.com/python-virtual-environments-a-primer/>
* <https://medium.com/@dakota.lillie/an-introduction-to-virtual-environments-in-python-ce16cda92853>
* <https://pythonspeed.com/articles/activate-virtualenv-dockerfile/>

## venv

📝 *Нужно что-то написать, хотя и очень старый вариант*

## [[python.virtualenvironments.pyenv|pyenv]]

## pipenv

[Pipenv](https://pipenv.pypa.io/en/latest/index.html): Python Dev Workflow for Humans.
    * https://pipenv.pypa.io/en/latest/index.html
    * https://docs.pipenv.org/

Pipenv is a tool that aims to bring the best of all packaging worlds (bundler, composer, npm, cargo, yarn, etc.) to the Python world. Windows is a first-class citizen, in our world.

It automatically creates and manages a virtualenv for your projects, as well as adds/removes packages from your Pipfile as you install/uninstall packages. It also generates the ever-important Pipfile.lock, which is used to produce deterministic builds.

Pipenv is primarily meant to provide users and developers of applications with an easy method to setup a working environment. For the distinction between libraries and applications and the usage of setup.py vs Pipfile to define dependencies, see [☤ Pipfile vs setup.py](https://docs.pipenv.org/advanced/#pipfile-vs-setuppy).

The problems that Pipenv seeks to solve are multi-faceted:

* You no longer need to use pip and virtualenv separately. They work together.
* Managing a requirements.txt file can be problematic, so Pipenv uses Pipfile and Pipfile.lock to separate abstract dependency declarations from the last tested combination.
* Hashes are used everywhere, always. Security. Automatically expose security vulnerabilities.
* Strongly encourage the use of the latest versions of dependencies to minimize security risks arising from outdated components.
* Give you insight into your dependency graph (e.g. $ pipenv graph).
* Streamline development workflow by loading .env files.

**Есть поддержка в JetBrains Idea.**

### Installation

```sh
$ pip install --user pipenv
```


## poetry

[Poetry](https://python-poetry.org/) - Python packaging and dependency management made easy.

* Develop: `poetry add pendulum`: Poetry comes with all the tools you might need to manage your projects in a deterministic way. 
* Build: `poetry build`: Easily build and package your projects with a single command. 
* Publish: `poetry publish`: Make your work known by publishing it to PyPI. 
* Track: `poetry show --tree`: Having an insight of your project's dependencies is just one command away. 

### Installation

* https://python-poetry.org/docs/#installation

```shell
oliver@soliver:~$ curl -sSL https://install.python-poetry.org | python3 -

Retrieving Poetry metadata

# Welcome to Poetry!

This will download and install the latest version of Poetry,
a dependency and package manager for Python.

It will add the `poetry` command to Poetry's bin directory, located at:

/home/oliver/.local/bin

You can uninstall at any time by executing this script with the --uninstall option,
and these changes will be reverted.

Installing Poetry (1.2.2): Done

Poetry (1.2.2) is installed now. Great!

You can test that everything is set up by executing:

`poetry --version`

oliver@soliver:~$ poetry --version
```

### Usage

* https://python-poetry.org/docs/basic-usage/

### Python 

https://python-poetry.org/docs/managing-environments/

Разные версии Python нужно ставить себе самостоятельно.

#### Env

https://stackoverflow.com/questions/73366237/poetry-what-is-the-benefit-of-creating-the-project-virtual-environment-within-y: 


* `poetry config virtualenvs.in-project true`
* `poetry env list`
* `source $(poetry env info --path)/bin/activate`

### Use system packages

* https://github.com/python-poetry/poetry/issues/6035

### Errors

#### ModuleNotFoundError: No module named 'distutils.cmd'

* https://github.com/python-poetry/poetry/discussions/6651

Для работы в Ubuntu c Python 3 нужен вот этот пакет

```
sudo apt install python3-distutils
```

#### Use system packages

* https://github.com/python-poetry/poetry/issues/6035

## pipx

[pipx](https://github.com/pypa/pipx) -  Install and Run Python Applications in Isolated Environments
    * https://pypa.github.io/pipx/