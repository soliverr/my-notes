---
id: 9tjii51rnfaczo43stnumkh
title: Spark
desc: ''
updated: 1620118392391
created: 1620117960597
---

## Ошибки

### Логгирование при работе в YARN

Почему-то не работает YARN-агрегация логов Spark-задач.

![](/assets/images/2021-05-04-13-50-52.png)
    
Доступ к логам через WebUI v1 и CLI есть.

Может быть нужно понять как указывать каталог логирования через `${spark.yarn.app.container.log.dir}` (см. https://medium.com/@iacomini.riccardo/spark-logging-configuration-in-yarn-faf5ba5fdb01)
