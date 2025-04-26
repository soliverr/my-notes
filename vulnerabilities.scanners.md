---
id: utzbq7lftgzqnruzdfi1z26
title: Scanners
desc: ''
updated: 1649418365118
created: 1649418365118
---

# trivy

[trivy](https://github.com/aquasecurity/trivy)

## Installation

Можно установить пакет для дистрибутива.

Текущая версия релиза:

```shell
curl --silent "https://api.github.com/repos/aquasecurity/trivy/releases/latest" | grep '"tag_name":' | sed -E 's/.*"v([^"]+)".*/\1/'
```

Варианты загрузки:

* https://github.com/aquasecurity/trivy/releases/download/v${trivy_version}/trivy_${trivy_version}_Linux-64bit.tar.gz
* https://github.com/aquasecurity/trivy/releases/download/v${trivy_version}/trivy_${trivy_version}_Linux-64bit.deb
* https://github.com/aquasecurity/trivy/releases/download/v{trivy_version}/trivy_{trivy_version}_checksums.txt
* https://github.com/aquasecurity/trivy/archive/refs/tags/v{trivy_version}.tar.gz
* https://github.com/aquasecurity/trivy/archive/refs/tags/v{trivy_version}.zip
