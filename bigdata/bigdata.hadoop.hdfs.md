---
id: rh60m17i6r65hxrn4eiukrl
title: Hdfs
desc: ""
updated: 1661435756848
created: 1622183203541
tags:
  - hdfs
  - nfs-gateway
  - hadoop
  - data-management
---

# Snapshots

* http://www.hadooplessons.info/2017/09/exploring-snapshots-in-hdfs.html
* https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsSnapshots.html



**Listing all the snapshots under a snapshottable directory:**

`hdfs dfs -ls /foo/.snapshot`

**Listing the files in snapshot s0:**

`hdfs dfs -ls /foo/.snapshot/s0`

**Copying a file from snapshot s0:**

`hdfs dfs -cp -ptopax /foo/.snapshot/s0/bar /tmp`

**Allowing snapshots of a directory to be created:**

`hdfs dfsadmin -allowSnapshot <path>`

**Disallowing snapshots of a directory to be created:**

* `hdfs dfsadmin -disallowSnapshot <path>`
* `hdfs dfs -disableSnapshot <path>`

**Create a snapshot of a snapshottable directory:**

`hdfs dfs -createSnapshot <path> [<snapshotName>]`

**Delete a snapshot of from a snapshottable directory:**

`hdfs dfs -deleteSnapshot <path> <snapshotName>`

**Rename a snapshot:**

`hdfs dfs -renameSnapshot <path> <oldName> <newName>`

**Get all the snapshottable directories where the current user has permission to take snapshtos:**

`hdfs lsSnapshottableDir`

**Get the differences between two snapshots:**

`hdfs snapshotDiff <path> <fromSnapshot> <toSnapshot>`

# NFS Gateway

https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsNfsGateway.html


> The NFS gateway can be on the same host as DataNode, NameNode, or any HDFS client.
>
> Exactly one export point is supported. By default, the export point is the root directory “/”.

При интеграции Windows и Hadoop HDFS NFS Gateway основная проблема - маппинг пользователя Windows в пользователя Linux (Hadoop). Для этого необходима настройка на стороне AD: https://techcommunity.microsoft.com/t5/storage-at-microsoft/nfs-identity-mapping-in-windows-server-2012/ba-p/424602

Необходимо настроить идентификаторы пользователя и группы в Windows, и после этого можно настроить маппинг на стороне Hadoop.

Согласно https://social.technet.microsoft.com/Forums/en-US/dc158acf-8b75-4259-a60c-748f335ffa6e/nfs-probelm-on-windows-server-2012?forum=winserver8gen локальный маппинг пользователя похоже не работает или надо ещё дольше разбираться с его настройкой.

* https://stackoverflow.com/questions/31618768/windows-server-2012-r2-nfs-identity-mapping-linux-client
* https://www.ibm.com/docs/en/zos/2.4.0?topic=clients-configuring-windows-user-name-mapping

## Решение с локальным маппингом

Оказалось локальный маппинг клиента работает. Нужно сделать вот так.

1. Создать локального пользователя Windows, например, пользователь `hive`
1. Посмотреть `id` пользователя hive на строне сервера NFS Gateway
```sh
[hive@c-t-hdp-mapr ~]$ id
uid=1004(hive) gid=100(users) группы=100(users),1001(hadoop),1005(hive)
```
1. Прописать маппинг пользователя на сервере Windows
```sh
PS C:\Windows\system32\drivers\etc> cat .\passwd
m1-qlikviewnew\hive:x:1004:100:Hive User,,,:c:\Users\hive

PS C:\Windows\system32\drivers\etc> cat .\group
users:x:100:1004
```
1. Замонтировать NFS-диск от имени пользователя `hive`
```sh
net use Y: \\c-t-hdp-mapr.srv.bia-tech.ru\warehouse\gp_cdc\incoming [/persistent:YES]
```

# Обслуживание HDFS

## Очистка корзины

* https://low-orbit.net/hdfs-how-to-empty-trash
* https://stackoverflow.com/questions/49471846/what-is-hadoop-trash-checkpoint-for

To empty the trash in HDFS just run this:

hdfs dfs -expunge

Files are stored in the trash when you delete them:

hdfs://my-host1:50070/user/hadoop/.Trash/Current

After a regular interval the Current directory is renamed to a time stamped directory like this:

hdfs://my-host1:50070/user/hadoop/.Trash/DATE

The time stamped directory will eventually be deleted.
fs.trash.interval 	Number of minutes after which the checkpoint gets deleted
fs.trash.checkpoint.interval 	Number of minutes between trash checkpoints

NOTE - The trash may or may not be enabled so it is worth being aware of how your system is configured.

Checkpointing is just a way to not periodically clean the whole Trash folder, all in one swoop.

fs.trash.interval is what actually deletes the files.

fs.trash.checkpoint.interval is moving from Current to checkpoint folders.

    fs.trash.interval
    Default: 0
    Description: Number of minutes after which the checkpoint gets deleted. If zero, the trash feature is disabled. This option may be configured both on the server and the client. If trash is disabled server side then the client side configuration is checked. If trash is enabled on the server side then the value configured on the server is used and the client configuration value is ignored.

    fs.trash.checkpoint.interval
    Default: 0
    Description: Number of minutes between trash checkpoints. Should be smaller or equal to fs.trash.interval. If zero, the value is set to the value of fs.trash.interval. Every time the checkpointer runs it creates a new checkpoint out of current and removes checkpoints created more than fs.trash.interval minutes ago


## Сопровождение групп и пользователей

* https://data-flair.training/forums/topic/how-to-create-user-in-hadoop/
* https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html
* https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/GroupsMapping.html

> You can refresh the user and the group mappings to have the namenode knows about the new user.
> 
> `hdfs dfsadmin -refreshUserToGroupsMappings`


# Erasure Coding

* https://hadoop.apache.org/docs/r3.3.2/hadoop-project-dist/hadoop-hdfs/HDFSErasureCoding.html
* https://towardsdatascience.com/simplifying-hdfs-erasure-coding-9d9588975113
* https://blog.cloudera.com/introduction-to-hdfs-erasure-coding-in-apache-hadoop/
* https://blog.cloudera.com/hdfs-erasure-coding-in-production/

In storage systems, the most notable usage of EC is Redundant Array of Inexpensive Disks (RAID). RAID implements EC through striping, which divides logically sequential data (such as a file) _into smaller units_ (such as bit, byte, or block) and stores consecutive units on different disks. _In the rest of this guide this unit of striping distribution is termed **a striping cell** (or **cell**)_. For each stripe of original data cells, a certain number of _parity cells_ are calculated and stored – the process of which is called encoding. The error on any striping cell can be recovered through decoding calculation based on surviving data and parity cells.

## Erasure coding policies

To accommodate heterogeneous workloads, we allow files and directories in an HDFS cluster to have different replication and erasure coding policies. The erasure coding policy encapsulates how to encode/decode a file. Each policy is defined by the following pieces of information:

* The EC schema: This includes the numbers of data and parity blocks in an EC group (e.g., 6+3), as well as the codec algorithm (e.g., Reed-Solomon, XOR).
* The size of a striping cell. This determines the granularity of striped reads and writes, including buffer sizes and encoding work.

Policies are named _codec_-_num data blocks_-_num parity blocks_-_cell size_. Currently, five built-in policies are supported: 
* RS-3-2-1024k,
* RS-6-3-1024k,
* RS-10-4-1024k,
* RS-LEGACY-6-3-1024k,
* XOR-2-1-1024k.

![](/assets/images/2022-04-26-15-54-18.png)

Please refer _etc/hadoop/user_ec_policies.xml.template_ for the example policy file. The maximum cell size is defined in property `dfs.namenode.ec.policies.max.cellsize` with the default value 4MB. **Currently HDFS allows the user to add 64 policies in total**, and the added policy ID is in range of 64 to 127. Adding policy will fail if there are already 64 policies added.

_cellsize_ must be an positive integer multiple of 1024(1k).

**The default REPLICATION scheme is also supported**. _It can only be set on directory_, to force the directory to adopt 3x replication scheme, instead of inheriting its ancestor’s erasure coding policy. _This policy makes it possible to interleave 3x replication scheme directory with erasure coding directory_.

_REPLICATION is always enabled. Out of all the EC policies, RS(6,3) is enabled by default_.

Similar to HDFS storage policies, erasure coding policies are set on a directory. When a file is created, it inherits the EC policy of its nearest ancestor directory.

Directory-level EC policies only affect new files created within the directory. _Once a file has been created, its erasure coding policy can be queried but not changed_. If an erasure coded file is renamed to a directory with a different EC policy, the file retains its existing EC policy. _Converting a file to a different EC policy requires rewriting its data; do this by copying the file (e.g. via distcp) rather than renaming it_.

## Deployment

* Erasure coding places additional demands on the cluster in terms of CPU and network.
* Encoding and decoding work consumes additional CPU on both HDFS clients and DataNodes.
* **_Erasure coding requires a minimum of as many DataNodes in the cluster as the configured EC stripe width_. For EC policy RS (6,3), this means a minimum of 9 DataNodes.**
* Erasure coded files are also spread across racks for rack fault-tolerance. This means that when reading and writing striped files, most operations are off-rack. Network bisection bandwidth is thus very important.

## Configuration

* `dfs.namenode.ec.system.default.policy`: default RS-6-3-1024k, the default erasure coding policy name will be used on the path if no policy name is passed.
* `dfs.namenode.ec.policies.max.cellsize`: default 4194304, the maximum cell size of erasure coding policy. Default is 4MB. 
* `dfs.namenode.ec.userdefined.policy.allowed`: default true, if set to false, doesn't allow addition of user defined erasure coding policies. 
* `io.erasurecode.codec.rs.rawcoders` for the default RS codec, there is also a native implementation which leverages Intel ISA-L library to improve the performance of codec.
* `io.erasurecode.codec.rs-legacy.rawcoders` for the legacy RS codec, there is no RS-LEGACY native codec implementation so the default is pure Java implementation only.
* `io.erasurecode.codec.xor.rawcoders` for the XOR codec, a native implementation which leverages Intel ISA-L library to improve the performance of codec is also supported.
* `io.erasurecode.codec.self-defined-codec.rawcoders` for user's self-defined codec

Erasure coding background recovery work on the DataNodes can also be tuned via the following configuration parameters:

* `dfs.datanode.ec.reconstruction.stripedread.timeout.millis` - Timeout for striped reads. Default value is 5000 ms.
* `dfs.datanode.ec.reconstruction.stripedread.buffer.size` - Buffer size for reader service. Default value is 64KB.
* `dfs.datanode.ec.reconstruction.threads` - Number of threads used by the Datanode for background reconstruction work. Default value is 8 threads.
* `dfs.datanode.ec.reconstruction.xmits.weight` - Relative weight of xmits used by EC background recovery task comparing to replicated block recovery. Default value is 0.5. It sets to 0 to disable calculate weights for EC recovery tasks, that is, EC task always has 1 xmits. The xmits of an erasure coding recovery task is calculated as the maximum value between the number of read streams and the number of write streams. For example, if an EC recovery task need to read from 6 nodes and write to 2 nodes, it has xmits of max(6, 2) * 0.5 = 3. Recovery task for replicated file always counts as 1 xmit. NameNode utilizes `dfs.namenode.replication.max-streams` minus the total xmitsInProgress on the DataNode that combines of the xmits from replicated file and EC files, to schedule recovery tasks to this DataNode.


## Intel ISA-L 

* https://github.com/intel/isa-l

**Intel ISA-L** stands for Intel Intelligent Storage Acceleration Library.

DFS native implementation of default RS codec leverages Intel ISA-L library to improve the encoding and decoding calculation. To enable and use Intel ISA-L, there are three steps.

1. Build ISA-L library. Please refer to the official site https://github.com/01org/isa-l/ for detail information.
1. Build Hadoop with ISA-L support. Please refer to “Intel ISA-L build options” section in “Build instructions for Hadoop” in (BUILDING.txt) in the source code.
1. Use `-Dbundle.isal` to copy the contents of the isal.lib directory into the final tar file. Deploy Hadoop with the tar file. Make sure ISA-L is available on HDFS clients and DataNodes.

To verify that ISA-L is correctly detected by Hadoop, run the `hadoop checknative` command.

### Building Hadoop EC with ISA-L

При сборке Hadoop нужно передавать специальные ключи для использования ISA-L:

* `-Drequire.isal`

Бинарная сборка уже выполняется с этими ключами, так как происходит поиск библиотек:

```
[hdfs@c-d-hdp-mapr ~]$ hdfs dfs -put file0 /test_ec
2022-04-27 16:00:44,329 WARN erasurecode.ErasureCodeNative: Loading ISA-L failed: Failed to load libisal.so.2 (libisal.so.2: невозможно открыть разделяемый объектный файл: Нет такого файла или каталога)
2022-04-27 16:00:44,330 WARN erasurecode.ErasureCodeNative: ISA-L support is not available in your platform... using builtin-java codec where applicable
```

**Посмотреть найденные бинарные библиотеки**:

```sh
hadoop checknative

Native library checking:
hadoop:  true /opt/hadoop/3.3.2/lib/native/libhadoop.so.1.0.0
zlib:    true /lib64/libz.so.1
zstd  :  false 
bzip2:   true /lib64/libbz2.so.1
openssl: false Cannot load libcrypto.so (libcrypto.so: невозможно открыть разделяемый объектный файл: Нет такого файла или каталога)!
ISA-L:   false Loading ISA-L failed: Failed to load libisal.so.2 (libisal.so.2: невозможно открыть разделяемый объектный файл: Нет такого файла или каталога)
PMDK:    false The native code was built without PMDK support.
```

Нашёл описание процесса сборки: https://community.cloudera.com/t5/Community-Articles/Enable-Intel-s-Intelligent-Storage-Acceleration-Library-for/ta-p/249096


## Limitations

Certain HDFS operations, i.e., hflush, hsync, concat, setReplication, truncate and append, are not supported on erasure coded files due to substantial technical challenges.

* append() and truncate() on an erasure coded file will throw IOException.
* concat() will throw IOException if files are mixed with different erasure coding policies or with replicated files.
* setReplication() is no-op on erasure coded files.
* hflush() and hsync() on DFSStripedOutputStream are no-op. Thus calling hflush() or hsync() on an erasure coded file can not guarantee data being persistent.

A client can use StreamCapabilities API to query whether a OutputStream supports hflush() and hsync(). If the client desires data persistence via hflush() and hsync(), the current remedy is creating such files as regular 3x replication files in a non-erasure-coded directory, or using FSDataOutputStreamBuilder#replicate() API to create 3x replication files in an erasure-coded directory.

## File Size and Block Size

See: https://blog.cloudera.com/hdfs-erasure-coding-in-production/

 With EC, each of the underlying data blocks and parity blocks is located in a different 128MB regular block. Reading data from a block means reading data from all blocks in the block group.  **A block group** is `(128 MB * (Number of data blocks))` in size. For example, _RS(6, 3)_ will have a block group of `768 MB = (128 MB * 6)`.

For RS(6,3) the NameNode stores the same amount of block objects, nine block objects, for a 10MB file as for a 768 MB file. Comparing it with 3x replication, a 10MB file with 3x replication means three block objects in the NameNode; _a 10MB file with EC (RS(6, 3)) means nine block objects in the NameNode!_

For very small files of sizes _smaller than_ the value of `data blocks * cell size` (in case of RS(6, 3) it is 6 * 1MB), **the bytes/blocks ratio is also bad**. This is because the number of actual data blocks is less than the data blocks defined by the erasure coding policy, **though the number of parity blocks is always the same**. For example, in the case of RS(6,3) with a cell size of 1 MB, a 1MB file consists of one data block, rather than six, but it still has three parity blocks. A 1MB file would therefore require four block objects in total. If 3x replication were used, the same file would require only three block objects. 

![](/assets/images/2022-04-27-12-05-54.png)


## Maintain EC

* [Hadoop 3 : how to configure / enable erasure coding?](https://stackoverflow.com/questions/51475712/hadoop-3-how-to-configure-enable-erasure-coding)

In Hadoop3 Replication factor setting will affect only to other folders which is not configured by erasure code setPolicy. You can use both Erasure coding and replication factor settings in single cluster.

**List the supported erasure policies:**

`hdfs ec -listPolicies`

**Enable XOR-2-1-1024k Erasure policy:**

`hdfs ec -enablePolicy -policy XOR-2-1-1024k`

**Set Erasure policy to HDFS directory:**

`hdfs ec -setPolicy -path /tmp  -policy XOR-2-1-1024k`

**Get the policy set to the given directory:**

`hdfs ec -getPolicy -path /tmp`

**Remove the policy from the directory.i.e unset policy:**

`hdfs ec -unsetPolicy -path /tmp`

**Disable policy:**

`hdfs ec -disablePolicy -policy XOR-2-1-1024k`

## Testing

Контроль использования блоков

```sh
hdfs_block_size=`hdfs getconf -confKey dfs.blocksize`
hdfs_replication=`hdfs getconf -confKey dfs.replication`

hdfs dfs -mkdir /test_ec
hdfs ec -setPolicy -path /test_ec
hdfs dfs -mkdir /test_rep
```

**Файл меньше размера блока:**

```sh
dd if=/dev/zero of=file0 bs=$((hdfs_block_size/2)) count=1
hdfs dfs -put file0 /test_ec
hdfs dfs -put file0 /test_rep

hdfs dfs -ls -R /test*
-rw-r--r--   1 hdfs hadoop   16777216 2022-04-27 16:00 /test_ec/file0
-rw-r--r--   2 hdfs hadoop   16777216 2022-04-27 16:01 /test_rep/file0

hdfs fsck /test_ec/file0 -files -blocks -locations

/test_ec/file0 16777216 bytes, erasure-coded: policy=XOR-2-1-1024k, 1 block(s):  OK
0. BP-1892722178-10.212.3.18-1651054322539:blk_-9223372036854775792_1001 len=16777216 Live_repl=3  
blk_-9223372036854775792:DatanodeInfoWithStorage[10.212.3.101:9866,DS-6c64dd0e-4742-425c-8bac-524c0949b5d2,DISK], 
blk_-9223372036854775791:DatanodeInfoWithStorage[10.212.3.103:9866,DS-fc17cc9c-012f-4660-86f1-ff5dd988eb1e,DISK],
blk_-9223372036854775790:DatanodeInfoWithStorage[10.212.3.102:9866,DS-2b75afc2-a09e-4f5e-b257-2ed7ef383dea,DISK]


hdfs fsck /test_rep/file0 -files -blocks -locations

/test_rep/file0 16777216 bytes, replicated: replication=2, 1 block(s):  OK
0. BP-1892722178-10.212.3.18-1651054322539:blk_1073741825_1002 len=16777216 Live_repl=2  
DatanodeInfoWithStorage[10.212.3.103:9866,DS-fc17cc9c-012f-4660-86f1-ff5dd988eb1e,DISK],
DatanodeInfoWithStorage[10.212.3.104:9866,DS-12db5bee-76e6-4bd1-bc7b-066467b96c2d,DISK]
```

**Файл размером с блок:**

```sh
dd if=/dev/zero of=file1 bs=$hdfs_block_size count=1

hdfs dfs -put file1 /test_ec
hdfs dfs -put file1 /test_rep

hdfs dfs -ls -R /test*/file1
-rw-r--r--   1 hdfs hadoop   33554432 2022-04-27 16:14 /test_ec/file1
-rw-r--r--   2 hdfs hadoop   33554432 2022-04-27 16:14 /test_rep/file1

hdfs fsck /test_ec/file1 -files -blocks -locations

/test_ec/file1 33554432 bytes, erasure-coded: policy=XOR-2-1-1024k, 1 block(s):  OK
0. BP-1892722178-10.212.3.18-1651054322539:blk_-9223372036854775776_1003 len=33554432 Live_repl=3
blk_-9223372036854775776:DatanodeInfoWithStorage[10.212.3.102:9866,DS-2b75afc2-a09e-4f5e-b257-2ed7ef383dea,DISK],
blk_-9223372036854775775:DatanodeInfoWithStorage[10.212.3.104:9866,DS-12db5bee-76e6-4bd1-bc7b-066467b96c2d,DISK],
blk_-9223372036854775774:DatanodeInfoWithStorage[10.212.3.105:9866,DS-443e2ffe-3b14-4469-8dab-38179fb52fbe,DISK]

hdfs fsck /test_rep/file1 -files -blocks -locations

/test_rep/file1 33554432 bytes, replicated: replication=2, 1 block(s):  OK
0. BP-1892722178-10.212.3.18-1651054322539:blk_1073741826_1004 len=33554432 Live_repl=2
DatanodeInfoWithStorage[10.212.3.103:9866,DS-fc17cc9c-012f-4660-86f1-ff5dd988eb1e,DISK],
DatanodeInfoWithStorage[10.212.3.102:9866,DS-2b75afc2-a09e-4f5e-b257-2ed7ef383dea,DISK]
```

**Файл размером 6 блоков:**

Здесь число используемых блоков в режиме EC уже в 1,3 раза меньше, чем в режиме репликации.

```sh

dd if=/dev/zero of=file3 bs=$hdfs_block_size count=6

hdfs dfs -put file3 /test_ec
hdfs dfs -put file3 /test_rep

hdfs dfs -ls -R /test*/file3
-rw-r--r--   1 hdfs hadoop  201326592 2022-04-27 16:19 /test_ec/file3
-rw-r--r--   2 hdfs hadoop  201326592 2022-04-27 16:19 /test_rep/file3

hdfs fsck /test_ec/file3 -files -blocks -locations

/test_ec/file3 201326592 bytes, erasure-coded: policy=XOR-2-1-1024k, 3 block(s):  OK
0. BP-1892722178-10.212.3.18-1651054322539:blk_-9223372036854775760_1005 len=67108864 Live_repl=3
blk_-9223372036854775760:DatanodeInfoWithStorage[10.212.3.103:9866,DS-fc17cc9c-012f-4660-86f1-ff5dd988eb1e,DISK],
blk_-9223372036854775759:DatanodeInfoWithStorage[10.212.3.101:9866,DS-6c64dd0e-4742-425c-8bac-524c0949b5d2,DISK],
blk_-9223372036854775758:DatanodeInfoWithStorage[10.212.3.104:9866,DS-12db5bee-76e6-4bd1-bc7b-066467b96c2d,DISK]

1. BP-1892722178-10.212.3.18-1651054322539:blk_-9223372036854775744_1006 len=67108864 Live_repl=3
blk_-9223372036854775744:DatanodeInfoWithStorage[10.212.3.105:9866,DS-443e2ffe-3b14-4469-8dab-38179fb52fbe,DISK],
blk_-9223372036854775743:DatanodeInfoWithStorage[10.212.3.102:9866,DS-2b75afc2-a09e-4f5e-b257-2ed7ef383dea,DISK],
blk_-9223372036854775742:DatanodeInfoWithStorage[10.212.3.104:9866,DS-12db5bee-76e6-4bd1-bc7b-066467b96c2d,DISK]

2. BP-1892722178-10.212.3.18-1651054322539:blk_-9223372036854775728_1007 len=67108864 Live_repl=3
blk_-9223372036854775728:DatanodeInfoWithStorage[10.212.3.106:9866,DS-f9d32365-a667-475a-9ba2-95a4abede470,DISK],
blk_-9223372036854775727:DatanodeInfoWithStorage[10.212.3.103:9866,DS-fc17cc9c-012f-4660-86f1-ff5dd988eb1e,DISK],
blk_-9223372036854775726:DatanodeInfoWithStorage[10.212.3.104:9866,DS-12db5bee-76e6-4bd1-bc7b-066467b96c2d,DISK]

hdfs fsck /test_rep/file3 -files -blocks -locations

/test_rep/file3 201326592 bytes, replicated: replication=2, 6 block(s):  OK
0. BP-1892722178-10.212.3.18-1651054322539:blk_1073741827_1008 len=33554432 Live_repl=2
DatanodeInfoWithStorage[10.212.3.104:9866,DS-12db5bee-76e6-4bd1-bc7b-066467b96c2d,DISK],
DatanodeInfoWithStorage[10.212.3.106:9866,DS-f9d32365-a667-475a-9ba2-95a4abede470,DISK]

1. BP-1892722178-10.212.3.18-1651054322539:blk_1073741828_1009 len=33554432 Live_repl=2  
DatanodeInfoWithStorage[10.212.3.104:9866,DS-12db5bee-76e6-4bd1-bc7b-066467b96c2d,DISK],
DatanodeInfoWithStorage[10.212.3.105:9866,DS-443e2ffe-3b14-4469-8dab-38179fb52fbe,DISK]

2. BP-1892722178-10.212.3.18-1651054322539:blk_1073741829_1010 len=33554432 Live_repl=2
DatanodeInfoWithStorage[10.212.3.104:9866,DS-12db5bee-76e6-4bd1-bc7b-066467b96c2d,DISK],
DatanodeInfoWithStorage[10.212.3.106:9866,DS-f9d32365-a667-475a-9ba2-95a4abede470,DISK]

3. BP-1892722178-10.212.3.18-1651054322539:blk_1073741830_1011 len=33554432 Live_repl=2
DatanodeInfoWithStorage[10.212.3.106:9866,DS-f9d32365-a667-475a-9ba2-95a4abede470,DISK],
 DatanodeInfoWithStorage[10.212.3.101:9866,DS-6c64dd0e-4742-425c-8bac-524c0949b5d2,DISK]

4. BP-1892722178-10.212.3.18-1651054322539:blk_1073741831_1012 len=33554432 Live_repl=2
DatanodeInfoWithStorage[10.212.3.101:9866,DS-6c64dd0e-4742-425c-8bac-524c0949b5d2,DISK],
DatanodeInfoWithStorage[10.212.3.105:9866,DS-443e2ffe-3b14-4469-8dab-38179fb52fbe,DISK]

5. BP-1892722178-10.212.3.18-1651054322539:blk_1073741832_1013 len=33554432 Live_repl=2
DatanodeInfoWithStorage[10.212.3.105:9866,DS-443e2ffe-3b14-4469-8dab-38179fb52fbe,DISK],
DatanodeInfoWithStorage[10.212.3.104:9866,DS-12db5bee-76e6-4bd1-bc7b-066467b96c2d,DISK]
S

# AWS S3 Integration

* * [Apache Hadoop Amazon Web Services support – S3A Committers: Architecture and Implementation](https://hadoop.apache.org/docs/current/hadoop-aws/tools/hadoop-aws/committer_architecture.html)
* [Apache Hadoop Amazon Web Services support – Hadoop-AWS module: Integration with Amazon Web Services](https://hadoop.apache.org/docs/current/hadoop-aws/tools/hadoop-aws/index.html#Introducing_the_Hadoop_S3A_client.)
* [Apache Hadoop Amazon Web Services support – Committing work to S3 with the S3A Committers](https://hadoop.apache.org/docs/current/hadoop-aws/tools/hadoop-aws/committers.html)

