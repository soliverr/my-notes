---
id: 11dvnvamqo6p9hy17jcx6l4
title: Docker
desc: ''
updated: 1663228232511
created: 1629706368983
---

## Docker Registry Mirror

* https://www.jianshu.com/p/405fe33b9032

```json
{
  "registry-mirrors" : [
    "http://ovfftd6p.mirror.aliyuncs.com",
    "http://registry.docker-cn.com",
    "http://docker.mirrors.ustc.edu.cn",
    "http://hub-mirror.c.163.com"
  ],
  "insecure-registries" : [
    "registry.docker-cn.com",
    "docker.mirrors.ustc.edu.cn"
  ],
  "debug" : true,
  "experimental" : true
}
```

* [-registry-proxy](https://github.com/tiangolo/docker-registry-proxy)

## Shared Volumes

* https://phoenixnap.com/kb/nfs-docker-volumes
* https://ender74.github.io/Sharing-Volumes-With-Docker-NFS/
* https://rexray.readthedocs.io/en/stable/
    * https://github.com/rexray/rexray
* https://dev.to/mohammedashour/setting-up-a-shared-volume-for-your-docker-swarm-using-glusterfs-3a16
* https://appfleet.com/blog/docker-swarm-and-shared-storage-volumes/
    * https://github.com/vieux/docker-volume-sshfs


## SSH и Docker

* [Build secrets and SSH forwarding in Docker 18.09](https://medium.com/@tonistiigi/build-secrets-and-ssh-forwarding-in-docker-18-09-ae8161d066)

## ARG, ENV and .env

[Docker ARG, ENV and .env - a Complete Guide](https://vsupalov.com/docker-arg-env-variable-guide/)

## ENTRYPOINT and CMD

[Demystifying ENTRYPOINT and CMD in Docker](https://aws.amazon.com/blogs/opensource/demystifying-entrypoint-cmd-docker/)
