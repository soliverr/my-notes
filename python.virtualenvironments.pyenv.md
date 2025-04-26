---
id: aiwb0aauwrjgj3ao1zafwmj
title: Pyenv
desc: Simple Python version management
updated: 1722513458930
created: 1629728252652
tags:
  - python
  - virtualenv/pyenv
---
# [pyenv](https://github.com/pyenv/pyenv)

Simple Python Version Management

pyenv lets you easily switch between multiple versions of Python. It's simple, unobtrusive, and follows the UNIX tradition of single-purpose tools that do one thing well.

* [pyenv on GitHub](https://github.com/pyenv)
* [Managing Multiple Python Versions With pyenv](https://realpython.com/intro-to-pyenv/)

## Установка pyenv

<https://reshukumari120.medium.com/installing-pyenv-in-ubuntu-967fd05bf959>

### pyenv-installer

Инструкция по установке: <https://github.com/pyenv/pyenv-installer>

```sh
$ curl https://pyenv.run | bash

# (The below instructions are intended for common
# shell setups. See the README for more guidance
# if they don't apply and/or don't work for you.)

# Add pyenv executable to PATH and
# enable shims by adding the following
# to ~/.profile:

export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init --path)"

# If your ~/.profile sources ~/.bashrc,
# the lines need to be inserted before the part
# that does that. See the README for another option.

# If you have ~/.bash_profile, make sure that it
# also executes the above lines -- e.g. by
# copying them there or by sourcing ~/.profile

# Load pyenv into the shell by adding
# the following to ~/.bashrc:

eval "$(pyenv init -)"

# Make sure to restart your entire logon session
# for changes to profile files to take effect.

# Load pyenv-virtualenv automatically by adding
# the following to ~/.bashrc:

eval "$(pyenv virtualenv-init -)"
```

При попытке создать новое окружение из системного появилось вот такое сообщение:

```sh
pyenv virtualenv system-3.8.10 sphinx-doc

The virtual environment was not created successfully because ensure pip is not
available.  On Debian/Ubuntu systems, you need to install the python3-venv
package using the following command.

    apt install python3.8-venv

You may need to use sudo with that command.  After installing the python3-venv
package, recreate your virtual environment.
```

После установки пакета, новое виртуальное окружение создалось.

## Обновление pyenv

```shell
pyenv update
```

## Плагины pyenv

### [pyenv-virtualenv](https://github.com/pyenv/pyenv-virtualenv)

Устанавливается [установщиком pyenv-installer](#pyenv_installer).

### [pyenv-register](https://github.com/doloopwhile/pyenv-register)

Полезный плагин, позволяющий пользоваться системной версией Python.

#### Установка

```sh
$ git clone https://github.com/doloopwhile/pyenv-register.git $(pyenv root)/plugins/pyenv-register
```

#### Использование

```sh
$ python3 --version
Python 3.8.10

$ which python3
/usr/bin/python3

$ ls -l /usr/bin/python3*
...

$ pyenv register /usr/bin/python3.8

$ pyenv versions
  system
  system-3.8.10

$ pyenv shell system-3.8.10
$ python --version
Python 3.8.10
```

Версия `system` в Ubuntu - это Python 2.7:

```sh
$ pyenv shell system
$ python --version
Python 2.7.18
```

## Часто используемые команды

### Список доступных для установки версий Python

```sh
$ pyenv install -l
```

### Установить нужную версию Python

При использовании pyenv можно установить необходимую версию Python:

```shell
$ pyenv install --list
$ pyenv install 3.8.11
```

_Выбранная версия Python будет установлена в домашнем каталоге пользователя_.

### Создать виртуальную среду

#### Создать виртуальную среду для проекта:

```shell
$ pyenv virtualenv 3.8.11 cdc_1c_connector
$ pyenv versions
```

#### Активировать виртуальную среду

```shell
oliver@soliver-dell:~$ pyenv activate dagster 
pyenv-virtualenv: prompt changing will be removed from future release. configure `export PYENV_VIRTUALENV_DISABLE_PROMPT=1' to simulate the behavior.
```

## Ошибки при работе

### 🔥No module named '_tkinter'
#### Симптомы

```
$ pyenv install 3.11.2
Downloading Python-3.11.2.tar.xz...
-> https://www.python.org/ftp/python/3.11.2/Python-3.11.2.tar.xz
Installing Python-3.11.2...
Traceback (most recent call last):
  File "<string>", line 1, in <module>
  File "/home/oliver/.pyenv/versions/3.11.2/lib/python3.11/curses/__init__.py", line 13, in <module>
    from _curses import *
ModuleNotFoundError: No module named '_curses'
WARNING: The Python curses extension was not compiled. Missing the ncurses lib?
Traceback (most recent call last):
  File "<string>", line 1, in <module>
ModuleNotFoundError: No module named 'readline'
WARNING: The Python readline extension was not compiled. Missing the GNU readline lib?
Traceback (most recent call last):
  File "<string>", line 1, in <module>
  File "/home/oliver/.pyenv/versions/3.11.2/lib/python3.11/tkinter/__init__.py", line 38, in <module>
    import _tkinter # If this fails your Python may not be configured for Tk
    ^^^^^^^^^^^^^^^
ModuleNotFoundError: No module named '_tkinter'
WARNING: The Python tkinter extension was not compiled and GUI subsystem has been detected. Missing the Tk toolkit?
Installed Python-3.11.2 to /home/oliver/.pyenv/versions/3.11.2
```
#### Решение

* https://stackoverflow.com/questions/25905540/importerror-no-module-named-tkinter
* https://stackoverflow.com/questions/75205087/modulenotfounderror-no-module-named-tkinter-using-python-3-11-1
* https://stackoverflow.com/questions/17410116/error-no-module-named-curses

```
sudo apt-get install python3-tk
sudo apt install tk-dev
sudo apt libncurses-dev
sudo apt libreadline-dev
```

✅ **Сработало**:

```
$ pyenv install 3.11.2
pyenv: /home/oliver/.pyenv/versions/3.11.2 already exists
continue with installation? (y/N) y
Installing Python-3.11.2...
Installed Python-3.11.2 to /home/oliver/.pyenv/versions/3.11.2
```