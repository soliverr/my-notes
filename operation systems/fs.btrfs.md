---
id: pkqs73op4q15tqpcngdrncl
title: Btrfs
desc: ''
updated: 1654863780892
created: 1654696860723
---

## Домашние каталоги

Домашние каталоги как тома в BTRFS.

* [How to Create and Mount Btrfs Subvolumes](https://linuxhint.com/create-mount-btrfs-subvolumes/)

**Посмотреть текущие тома**:

```sh
sudo btrfs subvolume list /home
```

**Создать том:**

```sh
sudo btrfs subvolume create /home/<username>
```

**Параметры тома:**

```sh
sudo btrfs subvolume show /home/<username>
```

**Замонтировать том:**

Пока делаю через `/etc/fstab`:

```
LABEL=ubuntu-home                   /home           btrfs   defaults,noatime,autodefrag,compress=zstd,commit=120,subvol=/         0       2
LABEL=ubuntu-home                   /home/oliver    btrfs   defaults,noatime,autodefrag,compress=zstd,commit=120,subvol=/oliver   0       2
LABEL=ubuntu-home                   /home/ola       btrfs   defaults,noatime,autodefrag,compress=zstd,commit=120,subvol=/ola      0       2
LABEL=ubuntu-home                   /home/tima      btrfs   defaults,noatime,autodefrag,compress=zstd,commit=120,subvol=/tima     0       2
LABEL=ubuntu-home                   /home/vasya     btrfs   defaults,noatime,autodefrag,compress=zstd,commit=120,subvol=/vasya    0       2
LABEL=ubuntu-home                   /home/alex      btrfs   defaults,noatime,autodefrag,compress=zstd,commit=120,subvol=/alex     0       2
```

`$ sudo mount /dev/sdb1 -o subvol=projects /tmp/projects`

**Добавить пользователя**:

```sh
sudo adduser --home /home/<username> --no-create-home
```
