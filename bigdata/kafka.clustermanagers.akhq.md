---
id: 3h9cvf6plhf9obq8en3l460
title: Akhq
desc: ''
updated: 1678794770078
created: 1678793608037
---

[akhq](https://akhq.io/) - Kafka GUI for Apache Kafka ® to manage topics, topics data, consumers group, schema registry, connect and more...

# Authentifications

## Basic Auth

* https://akhq.io/docs/configuration/authentifications/basic-auth.html

```sh
echo -n "password" | sha256sum
```

```json
micronaut:
  security:
    enabled: true
akhq.security:
  basic-auth:
    - username: admin
      password: "$2a$<hashed password>"
      passwordHash: BCRYPT
      groups:
      - admin
    - username: reader
      password: "<SHA-256 hashed password>"
      groups:
      - reader
```