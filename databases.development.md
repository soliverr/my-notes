---
id: o65czwezcneazpgvnrn9fln
title: Development
desc: ''
updated: 1675182589429
created: 1664434260932
---

# Liquibase

[Liquibase](https://www.liquibase.org/) - Track, version, and deploy database changes.

Releases: https://github.com/liquibase/liquibase/releases

# Installation

* https://docs.liquibase.com/install/liquibase-linux.html
* https://hub.docker.com/r/liquibase/liquibase

Текущая версия релиза: https://github.com/liquibase/liquibase/releases

Загрузить и распаковать в каталог.

# Usage
## Using Liquibase with MongoDB

* https://docs.liquibase.com/install/tutorials/mongodb.html?Highlight=mongo
* https://github.com/liquibase/liquibase-mongodb

## Labels and Contexts

* https://www.liquibase.com/blog/contexts-vs-labels

**Contexts** allow the changeset author to specify the expression in the changeset. Developers can create the changeset as 
`<changeSet id=”1” author=”example” context=”pro AND shopping_cart”>`
and the deployment manager simply “tags” the environment at runtime with `liquibase update --contexts=pro,shopping_cart,bigco`.

**Labels**, on the other hand, limit the changeset author to simply “tagging” the changesets like 
`<changeSet id=”1” author=”example” labels=”pro, shipping_cart”>` and gives the filtering power to the deployment manager running `liquibase update --labels="pro and (shopping_cart or account_mgmt)"`.