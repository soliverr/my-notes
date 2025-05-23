---
id: 9px91qejslht6q2a6osvrcn
title: Ambari
desc: ''
updated: 1629454945229
created: 1629454867090
---

## Изменение конфигурации кластера

https://cwiki.apache.org/confluence/display/AMBARI/Modify+configurations

[root@c7-hdp-prod-ambari scripts]# pwd
/var/lib/ambari-server/resources/scripts

1001  ./configs.py --help
 1002  ./configs.py --cluster=PROD --user=admin --password=tieRei1k --action=get --config-type=yarn-site 
 1003  ./configs.py --host=`hostname -f` --cluster=PROD --user=admin --password=tieRei1k --action=get --config-type=yarn-site 
 1004  ./configs.py --host=`hostname -f` --cluster=PROD --user=admin --password=tieRei1k --action=get --config-type=yarn-site | less
 1005  ./configs.py --cluster=PROD --user=admin --password=tieRei1k --action=get --config-type=yarn-site 
 1006  ./configs.py --help
 1007  ./configs.py --cluster=PROD --user=admin --password=tieRei1k --action=get --config-type=yarn-site --key=yarn.nodemanager.aux-services
 1008  ./configs.py --host=localhost --cluster=PROD --user=admin --password=tieRei1k --action=get --config-type=yarn-site --key=yarn.nodemanager.aux-services
 1009  ./configs.py --host=localhost --cluster=PROD --user=admin --password=tieRei1k --action=get --config-type=yarn-site | grep yarn.nodemanager.aux-services
 1010  ./configs.py --help
 1011  ./configs.py --host=localhost --cluster=PROD --user=admin --password=tieRei1k --action=delete --config-type=yarn-site --key=yarn.nodemanager.aux-services.spark_shuffle.classpath
 1012  ./configs.py --host=localhost --cluster=PROD --user=admin --password=tieRei1k --action=delete --config-type=yarn-site --key=yarn.nodemanager.aux-services.spark_shuffle.class

1016  ./configs.py --host=localhost --cluster=PROD --user=admin --password=tieRei1k --action=get --config-type=yarn-site | grep yarn.nodemanager.aux-services
 1017  ./configs.py --host=localhost --cluster=PROD --user=admin --password=tieRei1k --action=set --config-type=yarn-site --key=yarn.nodemanager.aux-services.spark_shuffle.class --value=org.apache.spark.network.yarn.YarnShuffleService
 1018  ./configs.py --host=localhost --cluster=PROD --user=admin --password=tieRei1k --action=set --config-type=yarn-site --key=yarn.nodemanager.aux-services.spark_shuffle.classpath --value='{{stack_root}}/{{spark_version}}/spark/aux/*'
