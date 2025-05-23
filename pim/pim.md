---
id: 401d7004-5697-43d9-a97a-709017f8af41
title: pim
desc: ""
created: 1743690953
updated: 1747905903
author: Sergei Kryazvevskikh <soliverr@gmail.com>
tags:
  - knowledge-base
  - note-organization
  - personal-information
  - pim
  - note-taking
---
# [Personal Information Management](https://en.wikipedia.org/wiki/Personal_information_management)

Список решений для создания собственных баз знаний и организации заметок:

* Notion
* QOwnNotes
* Joplin
* [[pim.dendron|Dendron]]
* Loqseq
* [NotebookLM](https://notebooklm.google/)
* OpenNotes AI
* [Obsidian](pim.obsidian.md)


## Методы организации заметок

* [Organising Your Notes in Obsidian — A Complete Guide](https://medium.com/obsidian-observer/organising-your-notes-in-obsidian-a-complete-guide-bf939d7e47e8)
* [The Best Way I Found To Organize My 5,173 Obsidian Notes](https://mattgiaro.com/organize-notes-obsidian/)
* [How to organize your notes in Obsidian // The LATCH method](https://summarize.ing/video-15951-How-to-organize-your-notes-in-Obsidian-The-LATCH-method)
* 

Мне известны следующие методы организации заметок:

* интуитивный метод организации заметок с помощью папок (**Folders**) и тэгов (**Tags**)
* метод **PARA** (**Projects–Areas–Resources–Archives**)
* метод **Zettelkasten**
* метод **LATCH** system (**Location, Alphabet, Time, Category, Hierarchy**)
* метод **Dendron**

### 📁 Folders and 🏷️ Tags

Интуитивный метод создания иерархии каталогов и назначения заметкам тэгов. Смысл иерархии каталогов определяется конкретным человеком для себя, тэгам также назначается индивидуальный смысл, понятный автору заметок.

---
Which methods are exist to organize notes in Obsidian?

<hr class="__chatgpt_plugin">

### role::assistant<span style="font-size: small;"> (gpt-4.1)</span>

Great overview! Here’s a summary of the main methods for organizing notes in 

<hr class="__chatgpt_plugin">

### role::assistant<span style="font-size: small;"> (gpt-4.1)</span>

Конечно! Вот краткое описание ключевых методов организации заметок — **PARA**, **PARAZETTEL**, **LATCH**, **Zettelkasten** — с указанием принципов и особенностей. Такой обзор хорошо структурирует способы организации информации в Obsidian.

---

## 📁 Основные методы организации заметок в Obsidian

---

### 1. **PARA (Projects–Areas–Resources–Archives)**

- **Автор**: Тиаго Форте ([Building a Second Brain](https://fortelabs.com/blog/para/))
- **Структура**:
    - **Projects** — проекты и задачи с чёткими дедлайнами.
    - **Areas** — сферы ответственности/долговременные области (здоровье, финансы и т.д.).
    - **Resources** — справочные материалы, полезная информация.
    - **Archives** — архив: завершённые проекты, неактуальное.
- **Применение**: Используются папки или теги для каждой категории. Метод подходит как для иерархической структуры, так и для плоской с тегами.
- **Преимущества**: Простота и гибкость, подходит как для работы, так и для личных задач.

---

### 2. **PARAZETTEL**

- **Описание**: Гибрид PARA и Zettelkasten для сочетания порядка и глубины связей.


<hr class="__chatgpt_plugin">

### role::user

Obsidian, with a description of each:

---

# 📁 Main Methods of Organizing Notes in Obsidian

## 1. **PARA (Projects–Areas–Resources–Archives)**
- **Origin**: Tiago Forte's “Building a Second Brain”
- **Structure**:
    - **Projects**: Short-term goals or tasks with deadlines.
    - **Areas**: Long-term responsibilities or spheres (health, finances, etc.)
    - **Resources**: Reference information, topics, or material for future projects.
    - **Archives**: Inactive or finished items from other categories.
- **Implementation**: Folders or tags for each P-A-R-A, notes are sorted accordingly. Works well with both folders and flat architecture + tags.
    - Example: `/Projects/Project_A/`, `/Areas/Health/`, `/Resources/Obsidian tricks/`, `/Archive/Old Project/`
- **Learn more**: [What is PARA?](https://fortelabs.com/blog/para/)

---

## 2. **Zettelkasten**
- **Origin**: Niklas Luhmann's note-taking method (“slip box” in German)
- **Structure**:
    - Notes are atomic (one idea per note).
    - Each note has a unique ID (often timestamp or sequential).
    - Notes are densely linked with [[

<hr class="__chatgpt_plugin">

### role::user



### PARA

### PARAZETTEL

### LATCH

### Zetellkasten


Для чего в Obsidian используются папки, названия которых начинается с цифр?

<hr class="__chatgpt_plugin">

### role::assistant<span style="font-size: small;"> (gpt-4.1)</span>

В Obsidian (и вообще в digital note-taking системах) часто используется метод структурирования папок с помощью числовых префиксов (например, 01_INBOX, 02_PROJECTS, 03_RESOURCES, 04_ARCHIVE и т.п.). Это делается по следующим причинам:

### Зачем использовать папки с цифрами в начале названия Obsidian?

1. **Явное определение порядка отображения**  
   В файловых менеджерах и в Obsidian по умолчанию папки сортируются по алфавиту. Добавляя числа в начало имени (“01_”, “02_”), вы определяете жёсткий порядок показа разделов, который не зависит от их буквенного названия.
   - Например, вы хотите, чтобы “INBOX” всегда был первым, а “ARCHIVE” последним.

1. **Структурирование и навигация**  
   Цифровые префиксы делают структуру “плоской” и очень понятной на первом уровне, помогают не заблудиться даже в большой базе заметок.
   - Часто используются в системах PARA, GTD, Zettelkasten и их гибридных вариантах (вроде PARAZETTEL).

1. **Автоматизация и шаблоны**  
   Если вы используете плагины для создания задач, шаблонов, автосортировки, проще автомат

<hr class="__chatgpt_plugin">

### role::user

Есть ли плагин для Obsidian, который управляет порядком папок ?

<hr class="__chatgpt_plugin">

### role::assistant<span style="font-size: small;"> (gpt-4.1)</span>

В настоящее время в Obsidian **нет штатного или массово используемого плагина, который позволяет напрямую управлять порядком папок** (например, «перетаскивать» папки вручную для изменения их порядка в списке). Папки в панели файлов сортируются по алфавиту или стандартному файловому менеджеру ОС.

**Самый простой и устойчивый способ управления порядком папок** — это цифровые префиксы, как вы уже указали (например, 01_Inbox, 02_Projects и т.д.).

---

## Что может помочь?

### 1. [Folder Note Core plugin](https://help.obsidian.md/Plugins/Folder+notes) (встроенный)
- Позволяет создавать заметку-представление для папки, но **не управляет порядком папок**.

### 2. [Advanced URI](https://github.com/Vinzent03/obsidian-advanced-uri)
- Управляет действиями по ссылкам, но не имеет функционала сортировки папок.

### 3. [File Tree Alternative](https://github.com/ozntel/file-tree-alternative)
- Улучшает отображение папок, позволяет фильтровать их, сворачивать дерево, но **ручной порядок неден**. См. [официальную документацию](https://github.com/ozntel/file-tree-alternative#features).

### 4

<hr class="__chatgpt_plugin">

### role::user

