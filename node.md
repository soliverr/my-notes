---
id: kjs1rpzmvbelr70btkio3l9
title: Node.js
desc: ''
updated: 1659679574616
created: 1630677672355
---


# Установка

* https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-20-04
* https://github.com/nodesource/distributions
* https://linuxize.com/post/how-to-install-node-js-on-ubuntu-20-04/

```sh
curl -sL https://deb.nodesource.com/setup_16.x -o nodesource_setup.sh
```

```sh
## Run `sudo apt-get install -y nodejs` to install Node.js 16.x and npm
## You may also need development tools to build native addons:
     sudo apt-get install gcc g++ make
## To install the Yarn package manager, run:
     curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | gpg --dearmor | sudo tee /usr/share/keyrings/yarnkey.gpg >/dev/null
     echo "deb [signed-by=/usr/share/keyrings/yarnkey.gpg] https://dl.yarnpkg.com/debian stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
     sudo apt-get update && sudo apt-get install yarn
```

# Дополнительные средства для разработки JavaScript

* ESLint и Prettier: https://ru.hexlet.io/blog/posts/rukovodstvo-eslint-prettier

Примеры для Prettier:

* https://github.com/JSMonk/sweet-monads/blob/master/.prettierrc
* https://github.com/JSMonk/sweet-monads/blob/master/package.json#L31

**husky** это утилита которая позволяет в package.json прописывать хуки гита и он ставит на pre-commit lint-staged
**lint-staged** это тузла которая из коммита вытаскивает имена измененных файлов

и так же дальше там правило

```
"lint-staged": {
    "*.ts": [
      "prettier --write",
      "eslint --quiet"
    ]
  },
```

что означает на файлы с раширением ts выполни команды
грубо говоря eslint проверит только измененные файлы и prettier пройдется только по измененным файлам - перед коммитом
это красиво - но по мне это нафиг не надо в небольшом проекте

ну раз пошла такая пьянка есть еще тузла под названием https://volta.sh/ - в package.json прописывается версии yarn node которые используются для проекта и когда ты попадаешь в папку с проектом они автоматически выставляются- это нужно для того что бы у все х разработчиков была одинаковая версия окружения в котором они работают + все должно быть одинаково в dockerfile - таким образом избегают ошибок сборки в больших проектах