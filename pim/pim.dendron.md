---
id: i2a33ne5u6xoftjk4j8x8i3
title: Dendron
desc: The hierarchical note taking tool that grows as you do!
updated: 1656672650777
created: 1629279152923
tags:
  - knowledge-management
  - note-taking
  - hierarchical-notes
  - wiki-system
  - markdown-integration
  - pim
  - dendron
---
# Dendron

[Dendron](https://wiki.dendron.so/) - программа для организации заметок в виде иерархии. Dendron - это локальная wiki-система, облегчающая ведение заметок в формате Markdown и организующая заметки в виде иерархий, что позволяет использовать накопленную информацию как небольшую локальную систему управления знаниями.

В основу организации знаний в Dendron положена плоская иерархическая модель организации файлов. Более подробно об этом подходе написано в статье [A Hierarchy First Approach to Note Taking](https://www.kevinslin.com/notes/3dd58f62-fee5-4f93-b9f1-b0f0f59a9b64.html).

[Community in Discord](https://discord.com/invite/xrKTUStHNZ)

[Awesome Dendron](https://github.com/dendronhq/awesome-dendron/)

> [!attention] **Dendron больше не жив, с 13 февраля 2024:**
>
> * https://github.com/dendronhq/dendron/pull/3979/commits/fe6263b80f2e80eb900766c12692489dfcd19443
> *  https://www.reddit.com/r/dendron/comments/10z19pz/so_on_tuesday_the_developer_of_dendron_announced/

## Установка Dendron

Официальная инструкция по установке доступна на странице [Getting Started](https://wiki.dendron.so/notes/678c77d9-ef2c-4537-97b5-64556d6337f1.html) проекта.

Для полноценной работы с базой знаний рекомендуется выделить отдельный экземпляр VSCode, в котором будет установлен минимально необходимый для Dendron набор плагинов.

### Установка VSCode/VSCodium

Использую VSCodium специально для Dendron, что бы не загружать инициализацией рабочий VSCode.
### Установка плагинов Dendron

* [Dendron Paste Image](https://marketplace.visualstudio.com/items?itemName=dendron.dendron-paste-image)
* [Dendron Markdown Shortcuts](https://marketplace.visualstudio.com/items?itemName=dendron.dendron-markdown-shortcuts)
* [Git Automator](https://marketplace.visualstudio.com/items?itemName=ivangabriele.vscode-git-add-and-commit): не справился с разными репозиториями для разных workspace
* [GitDoc](https://marketplace.visualstudio.com/items?itemName=vsls-contrib.gitdoc)
* [NeoVim](https://marketplace.visualstudio.com/items?itemName=asvetliakov.vscode-neovim)

### Установка дополнительных плагинов

## Инициализация рабочего проекта

* [Workspace](https://wiki.dendron.so/notes/c4cf5519-f7c2-4a23-b93b-1c9a02880f6b.html)

### Подключение базы знаний отдела НРАС

### Подключение дополнительных баз знаний

* [Seed bank](https://wiki.dendron.so/notes/6ff8cbb6-e4b8-449b-a967-277b76e4ecef.html)
    * https://github.com/dendronhq/dendron/blob/dev/packages/engine-server/src/seed/registry.ts

1. В VSCode установить extension Dendron
2. Использовать `ctrl+shift+p` для использования команд расширения
3. Ввести комнаду `Dendron: Vault Add`
4. Выбрать `remote`
5. Выделить `custom` и вставить url: `https://gitlab.dellin.ru/Hadoop_dev/quick-notes.wiki.git`
6. По желанию переименовать название создаваемого vault и папки, в которой он будет храниться

## Миграция на Dendron

* https://brunopaz.dev/blog/from-notion-to-dendron
* https://mstempl.netlify.app/post/dendron-git/

## Схемы в Dendron

* [Официальная документация](https://wiki.dendron.so/notes/c5e5adde-5459-409b-b34d-a0d75cbb1052.html)
* [Dendron Schemas](https://mstempl.netlify.app/post/dendron-schemas/)
* https://brunopaz.dev/blog/from-notion-to-dendron

Примеры схем:
* https://github.com/dendronhq/schema-library
* https://github.com/Bassmann/dendron-schemas

## Публикация в Dendron

[Publish](https://wiki.dendron.so/notes/4ushYTDoX0TYQ1FDtGQSg/#getting-started): Dendron lets you export any subset of your notes as static HTML via a custom nextjs template.

