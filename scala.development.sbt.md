---
id: dwvrg47xgxz0vm27q15brdv
title: sbt
desc: sbt - Scala Build Tool
updated: 1672930984895
created: 1628837693178
---

[sbt](https://www.scala-sbt.org/index.html) - The interactive build tool.

# Installation

* https://www.scala-sbt.org/download.html

## Debian/Ubuntu

```sh
curl -sL "https://keyserver.ubuntu.com/pks/lookup?op=get&search=0x2EE0EA64E40A89B84B2DF73499E82A75642AC823" | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/sbt.gpg
sudo add-apt-repository "deb [arch=amd64] https://repo.scala-sbt.org/scalasbt/debian all main"
sudo apt-get install sbt
```

# Plugins

## Assembly

<https://stackoverflow.com/questions/33305871/how-to-add-assembly-plugin-to-sbt-build#33317478>

# Usage examples
## % vs %%

* https://www.baeldung.com/scala/percent-symbols-build-sbt
* https://stackoverflow.com/questions/17461453/build-scala-and-symbols-meaning

Так как Scala разных версий не совместима между собой, библиотеки собирают под заданную версию Sacla, которая явно указывается в имени библиотеки. При этом, зависимость для библиотеки состоит из трёх частей:

`groupId, artifactId[_scalaVersion], artifactVersion`

Версия Scala является составной частью `artifactId`.

### Использование %% 

При подключении библиотек, sbt может автоматически подставлять версию Scala, для которой собрана библиотека:

```scala
scalaVersion := "2.12.11"

libraryDependencies += "com.typesafe.akka" %% "akka-actor" % "2.6.6"
```
 
### Использование %

В этом случае версию Scala нужно указывать явно

```scala
libraryDependencies += "com.typesafe.akka" % "akka-actor_2.12" % "2.6.5"
```
