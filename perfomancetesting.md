---
id: ghm7mefxehw2cga6b3pcbq4
title: Perfomancetesting
desc: ''
updated: 1659597941757
created: 1657805634322
---

* Apache JMeter
* https://gettaurus.org/
* https://k6.io/
    * https://k6.io/blazemeter-alternative/
* [Blazemeter](https://www.blazemeter.com/)

# K6

[[perfomancetesting.k6]]

# Taurus

## Install

```
pip install bzt
```

## Executors

### K6

https://gettaurus.org/docs/K6/

*quick_test.yml*:

```yaml
execution:
- executor: k6
  concurrency: 10  # number of K6 workers
  hold-for: 1m  # execution duration
  iterations: 20  # number of iterations
  scenario:
    script: k6-test.js  # has to be a valid K6 script

modules:
  k6:
    cmdline: --out json=result.json
```

*k6-test.js*:

```js
import http from 'k6/http';
import { sleep } from 'k6';
 
export default function () {
  http.get('https://blazedemo.com/');
  sleep(1);
}
```

Запуск теста:  `bzt quick_test.yml`
