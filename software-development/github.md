---
id: 5h1m13f8ws0iftv99xm8c8o
title: GitHub
desc: ''
updated: 1678710056322
created: 1629706368985
---
GitHub
======

## [GitHub CLI](https://cli.github.com/)

### [Installing gh on Linux and FreeBSD](https://github.com/cli/cli/blob/trunk/docs/install_linux.md)

#### Debian, Ubuntu Linux (apt)

**Install:**

```
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-key C99B11DEB97541F0
sudo apt-add-repository https://cli.github.com/packages
sudo apt update
sudo apt install gh
```

```
type -p curl >/dev/null || sudo apt install curl -y
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg \
&& sudo chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg \
&& echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
&& sudo apt update \
&& sudo apt install gh -y
```

*Note:* If you are behind a firewall, the connection to keyserver.ubuntu.com might fail. In that case, try running `sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-key C99B11DEB97541F0`.

*Note:* If you get "gpg: failed to start the dirmngr '/usr/bin/dirmngr': No such file or directory" error, try installing the dirmngr package. Run `sudo apt-get install dirmngr` and repeat the steps above.

*Note:* most systems will have apt-add-repository already. If you get a command not found error, try running `sudo apt install software-properties-common` and trying these steps again.

Новый вариант скрипта установки gh:

```
type -p curl >/dev/null || sudo apt install curl -y
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg \
&& sudo chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg \
&& echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
&& sudo apt update \
&& sudo apt install gh -y
```

**Upgrade:**

```
sudo apt update
sudo apt install gh
```
