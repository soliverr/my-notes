---
tags:
  - obsidian
  - note-taking
  - productivity
  - markdown
related: "[[pim]]"
title: Obsidian
id: e339e5fd-78af-4064-b5c7-e241b4c29fa6
desc: ""
created: 1745676368
updated: 1747408883
author: Sergei Kryazvevskikh <soliverr@gmail.com>
---
# Obsidian

[Obsidian](https://obsidian.md/)- Sharpen your thinking. The free and flexible app for your private thoughts.

## Редактирование заметок

### Примеры работы в Obsidian

* [Writing in Obsidian: A Comprehensive Guide](https://medium.com/@dianademco/writing-in-obsidian-a-comprehensive-guide-58a1306ed293)

### Базовый синтаксис

* [Basic formatting syntax](https://help.obsidian.md/syntax)
* [Advanced formatting syntax](https://help.obsidian.md/advanced-syntax)
* [Obsidian Flavored Markdown](https://help.obsidian.md/obsidian-flavored-markdown)
* [Internal links](https://help.obsidian.md/links)
* [Callouts](https://help.obsidian.md/callouts)

### [Шаблоны](https://help.obsidian.md/plugins/templates)

* [Ultimate Guide to Obsidian Templates (with Examples)](https://facedragons.com/productivity/obsidian-templates-with-examples/)

В стандартных плагинах нужно задать каталог для хранения шаблонов:

![[Pasted image 20250516120701.png|800]]

Стандартный механизм шаблонов имеет ограниченный функционал и маленькое число переменных. Рекомендуется использовать плагины для продвинутого использования шаблонов.
#### Шаблон заметки из Dendron

Dendron использует метаданные при создании и обновлении заметки. Основные поля шаблона, которые добавляются в [свойства](https://help.obsidian.md/properties) заметки:
```markdown
---
id: <internal Dendron ID>
title: <Note title>
desc: <Note description>
updated: <update timestamp in seconds>
created: <create timestamp in seconds>
---
```

Плагин [[pim.obsidian#[Dendron Tree](https //github.com/levirs565/obsidian-dendron-tree)|Dendron Tree]] может генерировать данные свойства, которые называются *Front Matter*, если это разрешить в конфигурации:

![[Pasted image 20250516130152.png|800]]

Но, при разрешении этой опции не работает механизм подстановки шаблонов.
#### [Templater plugin](https://github.com/SilentVoid13/Templater)

* [Документация по Templater](https://silentvoid13.github.io/Templater/)
* [15 Easy Templater Commands For Obsidian](https://www.redgregory.com/obsidian-content/2021/11/17/15-templater-commands-for-obsidian)
* [Templater snippets](https://zachyoung.dev/posts/templater-snippets)

Плагин предполагает настройку правил для применения шаблонов при создании документа. Правила срабатывают по регулярному выражению на имя заметки, побеждает [первое сработавшее правило](https://silentvoid13.github.io/Templater/settings.html#file-regex-templates)

##### Генерация FrontMatter a-la Dendron

Можно сгенерировать свойства документа так же как это делает Dendron.

Генерация уникального id доступна через пользовательскую функцию, вот [тут](https://forum.obsidian.md/t/could-templater-plugin-create-unique-id-for-every-note/21616) описан пример.

Создал пользовательскую функцию `uuid()` в настройках Templater:
![[Pasted image 20250516170001.png|800]]

> [!note] `uuidgen` - это утилита,  которая должна быть доступна в системе.

Создал два шаблона:

**`dendron-frontmatter-template`** - можно использовать для добавления свойств в текущие заметки

```markdown
---
id: <% tp.user.uuid() %>
title: <% tp.file.title.replaceAll('.', ' ') %>
desc: ''
created: <% tp.file.creation_date("X") %>
updated: <% tp.file.last_modified_date("X") %>
author: Sergei Kryazvevskikh <soliverr@gmail.com>
tags: <%tp.file.tags %> <% tp.file.title.replaceAll('.', ',') %>
---
```

**`dendron-frontmatter-template-create-page`** - можно назначить шаблоном по умолчанию при создании страницы:

```markdown
 <% tp.file.include("[[dendron-frontmatter-template]]") %>
 # <% tp.file.title.replaceAll('.', ' ') %>
```

Я использую плоские наименования файлов Dendron, поэтому части имени файла можно сразу добавлять в теги.

TODO: #todo
* [ ] получать имя пользователя и почту из конфигурации Git
* [ ] разобраться с пользовательскими макросами и формировать uuid через JavaScript
* [ ] поискать как работать с Git в JavaScript

##### Добавление полей в шаблон

Можно вручную добавлять свойства из шаблона через команду 'Template: Insert Template'. 

❗*Нужно пользоваться с осторожностью, похоже не все функции Templater срабатывают, особенно теги может перезаписать, а не добавить*

Как-то сложно получается:
* Templates: Insert template -> выбор файла шаблона
* Templater: Replace Templates in the active file

Ломает тэги, не работает пользовательская функция `uuid`.

#### [AI for Templater](https://github.com/TfTHacker/obsidian-ai-templater)

* [Introduction to AI for Templater](https://tfthacker.com/AIT)

> This plugin extends Templater to interact with large language models. It is primarily designed to work with OpenAI LLMs, like the ones used by ChatGPT, but is also compatible with any LLM that supports the OpenAI API.

Примеры использования новых макросов вот[здесь](https://tfthacker.com/AIT-Examples). Макрос можно вставить в любое время и место, и выполнить команду `Templater: Replace Templates in the active file (ALT+R)`

Пока из интересного:
* создание тегов, то же самое делает [[#[AI Tagger Universe](https //github.com/niehu2018/obsidian-ai-tagger-universe)|AI Tagger Universe]], но тут можно самому формировать Prompt, и включать туда существующие теги в документе
* создание резюме заметки

TODO: #todo
* [ ] Сформировать PROMPT для добавления тегов


### Вложения

 [Save images to a specific folder](https://forum.obsidian.md/t/save-images-to-a-specific-folder/27459):
 
> Settings → Files & Links → Default location for new attachments

![[Pasted image 20250409235102.png]]

## [Плагины](https://obsidian.md/plugins)

Далее веду список плагинов, которые кажутся интересными для работы с базой знаний.
### Dendron

У [[pim.dendron|Dendron]] есть отличная возможность хранить файлы в виде плоской структуры, без иерархии в виде папок.

Сравнение Dendron и Obsidian https://www.youtube.com/watch?v=c8HlFuf2dmo
#### [Obsidian-Structure](https://github.com/levirs565/obsidian-dendron-tree)

Нашёл плагин для такой же реализации в Obsidian 
  * https://forum.obsidian.md/t/dendron-style-notes-in-obsidian/47291/2
  
Плагин старый, надо попробовать его поставить. 
	  Поставил через BRAT
	  
Для установки из GitHub нужен плагин BRAT.

Пока не понял смысл этого плагина...

Может быть он ссылочную целостность поддерживает при переименовании файла?

#### [Dendron Tree](https://github.com/levirs565/obsidian-dendron-tree)

Визуализация плоских файлов Dendron в виде иерархии папок.

### Разработка плагинов
#### [BRAT](https://tfthacker.com/brat-plugins)

Нужен для установки плагинов из исходников, для разработки или для тестирования плагинов, которые не публикуются в реестре плагинов [[pim.obsidian#[Плагины](https //obsidian.md/plugins)]].

  * https://github.com/TfTHacker/obsidian42-brat
  
  > From the command palette, use the "Check for updates to beta plugins and UPDATE" to check if there are updates. Updates will be downloaded and installed, and the plugin will reload so that you can continue testing.
  > 
  > Also, in the settings tab for BRAT, you can configure that all beta plugins are checked for updates at the startup of Obsidian.

### Заметки как данные
#### [DataView](https://blacksmithgu.github.io/obsidian-dataview/)

> Dataview is a live index and query engine over your personal knowledge base. You can [**add metadata**](https://blacksmithgu.github.io/obsidian-dataview/annotation/add-metadata/) to your notes and **query** them with the [**Dataview Query Language**](https://blacksmithgu.github.io/obsidian-dataview/queries/structure/) to list, filter, sort or group your data. Dataview keeps your queries always up to date and makes data aggregation a breeze.

##### Примеры использования DataView

* https://github.com/s-blu/obsidian_dataview_example_vault

#### [DBFolder](https://github.com/RafaelGB/obsidian-db-folder)

This plugin allows you to create [Notion](https://www.notion.so/)'s like databases in your [Obsidian](https://obsidian.md/) Vault.

Using the search engine of the popular [Dataview plugin](https://github.com/blacksmithgu/obsidian-dataview), you can view the content of your notes and edit the fields directly from the table without the need to open the note.

You **need** to have Dataview installed to use this plugin.

### Активная работа с заметками

#### AI ассистенты

Вот из [этого места](https://help.obsidian.md/web-clipper/templates) документации Obsidian:

| Provider      | API key                                                | Models                                                                               |
| ------------- | ------------------------------------------------------ | ------------------------------------------------------------------------------------ |
| Anthropic     | [API key](https://console.anthropic.com/settings/keys) | [Models](https://docs.anthropic.com/en/docs/about-claude/models)                     |
| Azure OpenAI  | [API key](https://oai.azure.com/portal/)               | [Models](https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/models) |
| DeepSeek      | [API key](https://platform.deepseek.com/api_keys)      | [Models](https://api-docs.deepseek.com/quick_start/pricing)                          |
| Google Gemini | [API key](https://aistudio.google.com/apikey)          | [Models](https://ai.google.dev/gemini-api/docs/models/gemini)                        |
| Hugging Face  | [API key](https://huggingface.co/settings/tokens)      | [Models](https://huggingface.co/models?pipeline_tag=text-generation&sort=trending)   |
| Ollama        | n/a                                                    | [Models](https://ollama.com/search)                                                  |
| OpenAI        | [API key](https://platform.openai.com/api-keys)        | [Models](https://platform.openai.com/docs/models)                                    |
| OpenRouter    | [API key](https://openrouter.ai/settings/keys)         | [Models](https://openrouter.ai/models)                                               |
| Perplexity    | [API key](https://www.perplexity.ai/settings/api)      | [Models](https://docs.perplexity.ai/guides/model-cards)                              |
##### [LLM Plugin](https://github.com/eharris128/Obsidian-LLM-Plugin)
##### [AI integration Hub Plugin](https://github.com/hish-math/obsidian-ai-hub)

#### [Hints Flow](https://github.com/slpbx/obsidian-plugin)

#### [Fabric](https://github.com/danielmiessler/fabric)

###### [Unofficial Fabric](https://github.com/chasebank87/unofficial-fabric-plugin)

###### [Mesh AI](https://github.com/chasebank87/mesh-ai)

#### [Smart Connections](https://github.com/brianpetro/obsidian-smart-connections)

* [Smart Connections Documentation](https://docs.smartconnections.app)
* [Adding AI to your Obsidian Notes with SmartConnections and CoPilot](https://effortlessacademic.com/adding-ai-to-your-obsidian-notes-with-smartconnections-and-copilot/)

После установки:
![[Pasted image 20250409182119.png]]

Уже есть Smart Connection Visualizer #todo ссылку на этот плагин

#### Gemini

Доступно несколько плагинов для работы с Gemini. Нужно выбрать чем удобней пользоваться.

Вот [тут](https://console.cloud.google.com/apis/credentials) нужно создать креды и ключ доступа для использования в плагинах.
##### [YouTube Video Summarizer for Obsidian](https://github.com/mbramani/obsidian-yt-video-summarizer)
##### [Obsidian Gemini-Generator](https://github.com/BjarneRentz/obsidian-gemini-generator)

> Generate notes with the help of [Google Gemini](https://gemini.google.com/app?hl=de).
##### [Gemini Scribe for Obsidian](https://github.com/allenhutchison/obsidian-gemini)

> Gemini Scribe is an Obsidian plugin that integrates Google's Gemini AI models, providing powerful AI-driven assistance for note-taking, writing, and knowledge management directly within Obsidian. It leverages your notes as context for AI interactions, making it a highly personalized and integrated experience.

##### [Gemini AI Assistant](https://github.com/Artel250/Obsidian-Gemini-Assistant)
##### [Your Gemini AI Powered Assistant](https://github.com/eatgrass/obsidian-gemini-assistant)

#### Интеграция с Телеграмм

##### [Hints Flow](https://github.com/slpbx/obsidian-plugin)

A [Hints](https://productivity-ai.net/) quick-capturing plugin for [Obsidian](https://obsidian.md/plugins?id=hints-plugin).

You can save data directly to Obsidian with specified template from Telegram, WhatsApp, Slack, Email, SMS, Raycast and more.

Learn more about Hints integrations with Notion, Calendar, ClickUp, Trello, HubSpot, Pipedrive and Jira.

##### [Telegram Sync](https://github.com/soberhacker/obsidian-telegram-sync)

Transfer messages and files from [Telegram](https://telegram.org/) to your [Obsidian](https://obsidian.md/plugins?id=telegram-sync) vault. You can easily save text, voice transcripts, images, and other files from your Telegram chats to Obsidian for further processing and organization. ***This plugin is only available for desktops and would never be available on mobile platforms.***

1. Create a new bot on Telegram by talking to the [@botFather](https://t.me/botfather)
2. Copy the bot token provided by the @botFather
3. Open the Obsidian settings and navigate to the **Telegram Sync** settings tab
4. Paste your bot token to **Bot > Connect > Bot Token** field
5. Configure the remaining settings according to your preferences
6. Start sending messages and files to your Telegram bot
7. When the Obsidian app is running on your laptop or PC, it syncs all your messages
8. You can optionally add your bot to any chats that you want to sync (the bot needs admin rights)

* https://blog.devgenius.io/how-to-set-up-your-telegram-bot-using-botfather-fd1896d68c02

Работает, очень качественно создаёт заметки в отдельной папке. 
Надо разобраться с шаблонами для сообщений и путями для хранения вложенных файлов, которые я храню в подпапке *media*.
#### Интеграция с браузерами

Используется [Web Clipper](https://help.obsidian.md/web-clipper/capture)

Плагины для браузеров [тут](https://help.obsidian.md/web-clipper):
* [Obsidian Web Clipper](https://addons.mozilla.org/en-US/firefox/addon/web-clipper-obsidian/)

[Переменные Web Clipper](https://help.obsidian.md/web-clipper/variables)

[Использование шаблонов Web Clipper](https://help.obsidian.md/web-clipper/templates)
##### Firefox 

Права для использования режима подсветки и сохранения содержимого  страницы для Firefox

![[Pasted image 20250409121336.png]]

Горячие клавиши:
* Ctrl Shft O - Obsidian clipper
* Ctrl Sft H - Highlight clipper

#### Интеграция Jupyter ноутбуков

* <https://github.com/MaelImhof/obsidian-jupyter>
* <https://github.com/tillahoffmann/obsidian-jupyter>
##### [Jupyter for Obsidian](https://jupyter.mael.im/)

🗒️Надо бы попробовать этот плагин
### Тэги
#### [Tag Wrangler](https://github.com/pjeby/tag-wrangler)

This plugin adds a context menu for tags in the [Obsidian.md](https://obsidian.md/) tags view, with the following actions available:

[![Image of tag wrangler's context menu](https://raw.githubusercontent.com/pjeby/tag-wrangler/master/contextmenu.png)](https://raw.githubusercontent.com/pjeby/tag-wrangler/master/contextmenu.png)

- Open or create a [Tag Page](https://github.com/pjeby/tag-wrangler#tag-pages) (**NEW in 0.5.0**)
- [Rename the tag](https://github.com/pjeby/tag-wrangler#renaming-tags) (and all its subtags)
- Start a new search for the tag (similar to a plain click)
- Add the tag as a requirement (`tag:#whatever`) to the current search
- Add an exclusion for the tag (`-tag:#whatever`) to the current search
- Open a random note with that tag (if you have the [Smart Random Note](https://github.com/erichalldev/obsidian-smart-random-note/) plugin installed and enabled)
- Collapse all tags at the same level in the tags view
- Expand all tags at the same level in the tags view

#### [Tag Folder](https://github.com/vrtmrz/obsidian-tagfolder)

This is the plugin that shows your tags like folders.

#### [AI Tagger Universe](https://github.com/niehu2018/obsidian-ai-tagger-universe)

### Использование Git

#### [Git](https://github.com/Vinzent03/obsidian-git)

> A powerful community plugin for [Obsidian.md](https://github.com/Vinzent03/obsidian-git/blob/master/Obsidian.md) that brings Git integration right into your vault. Automatically commit, pull, push, and see your changes — all within Obsidian.

Необходимо настроить хранение паролей в libsecret, [Authentication](https://publish.obsidian.md/git-doc/Authentication):

```sh
sudo apt install libsecret-1-0 libsecret-1-dev make gcc

sudo make --directory=/usr/share/doc/git/contrib/credential/libsecret

# NOTE: This changes your global config, in case you don't want that you can omit the `--global` and execute it in your existing git repository.
git config --global credential.helper \
   /usr/share/doc/git/contrib/credential/libsecret/git-credential-libsecret

git config set credential.helper libsecret
```

Для работы с GitHub удобней использовать ключи SSH.

#### [GitHub Gitless Sync](https://github.com/silvanocerza/github-gitless-sync)
#### [GitHub Stars Plugin](https://github.com/flyingnobita/obsidian-github-stars)

### Шаблоны для ведения заметок

#### [[pim.obsidian#[Templater plugin](https //github.com/SilentVoid13/Templater)|Templater]]

## Публичные волты

### [Obsidian Hub](https://publish.obsidian.md/hub/00+-+Start+here)

Welcome! This is an experimental vault that is maintained by the Obsidian community.

For best results we recommend [downloading this vault](https://github.com/obsidian-community/obsidian-hub/releases/latest) locally and opening it in Obsidian or browsing it in our [publish site](https://publish.obsidian.md/hub).

This vault was inspired by the [awesome-obsidian](https://github.com/kmaasrud/awesome-obsidian) list.