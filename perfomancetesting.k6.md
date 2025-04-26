---
id: 6jvsw11e4gmwohrny0rcay3
title: K6
desc: ''
updated: 1659598000330
created: 1659514728711
---

# Installation

* https://k6.io/docs/getting-started/installation/

Этот вариант получения ключа не работает:

```sh
sudo gpg --no-default-keyring --keyring /usr/share/keyrings/k6-archive-keyring.gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys C5AD17C747E3415A3642D57D77C6C491D6AC1D69
```

Другой вариант:

```sh
curl -sL "https://keyserver.ubuntu.com/pks/lookup?op=get&search=0xC5AD17C747E3415A3642D57D77C6C491D6AC1D69" | sudo gpg --dearmour -o /usr/share/keyrings/k6-archive-keyring.gpg
```

```sh
echo "deb [signed-by=/usr/share/keyrings/k6-archive-keyring.gpg] https://dl.k6.io/deb stable main" | sudo tee /etc/apt/sources.list.d/k6.list
sudo apt-get update
sudo apt-get install k6
```

# Пример простого теста

*script.js*:

```js
import http from 'k6/http';

import { sleep } from 'k6';


export default function () {

  http.get('https://test.k6.io');

  sleep(1);

}
```

## Запуск теста

* `k6 run script.js`
* `k6 run --vus 10 --duration 30s script.js`

[k6 supports three execution modes to run a k6 test: local, distributed, and cloud](https://k6.io/docs/getting-started/running-k6/#execution-modes).


# Сценарии

* [Как управлять сценариями разрешить/запретить в K6](https://community.k6.io/t/execution-of-new-scenarios-from-cli/796/2)
* [Advanced Examples](https://k6.io/docs/using-k6/scenarios/advanced-examples/)

# Вывод результатов

* [Results output](https://k6.io/docs/getting-started/results-output/)

## Cloud

* [Cloud](https://k6.io/docs/results-visualization/cloud/)

```sh
$ k6 login cloud --token <YOUR_K6_CLOUD_API_TOKEN>
$ k6 run --out cloud script.js
```

or 

```sh
$ K6_CLOUD_TOKEN=<YOUR_K6_CLOUD_API_TOKEN> k6 run --out cloud script.js
```
