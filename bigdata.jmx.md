---
id: 9ufudq7mffj1a0r8b0zg2tg
title: Jmx
desc: ''
updated: 1657708936962
created: 1656054617321
---

* [Jolokia](https://jolokia.org/)
* [Prometheus' JMX Agent](https://github.com/prometheus/jmx_exporter)
* [Agent Bond](https://github.com/fabric8io/agent-bond)

# Jolokia

[Jolokia](https://jolokia.org/) is a JMX-HTTP bridge giving an alternative to JSR-160 connectors. It is an agent based approach with support for many platforms. In addition to basic JMX operations it enhances JMX remoting with unique features like bulk requests and fine grained security policies. 
  * https://jolokia.org/reference/html/agents.html#jvm-agent
  * https://github.com/rhuss/jolokia

# Prometheus' JMX Agent

[Prometheus' JMX Agent](https://github.com/prometheus/jmx_exporter)

Файлы конфигурации для агента можно смотреть тут: https://github.com/fahlke/jmx_exporter-cloudera-hadoop

# JMX Agent Bond

Посмотреть агента для мониторинга вот в этом проекте: https://github.com/fabric8io/agent-bond.git

Agent bond for export Jolokia and jmx_exporter data:

```
  - type: "git"
    url: "https://github.com/fabric8io/agent-bond.git"
    tag: v1.2.0
```

Встроена поддержка для:

* Jolokia
* Prometheus' JMX Agent
