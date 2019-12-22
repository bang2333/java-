### spark yarn配置

yarn模式氛围 **client模式** 和 **cluster模式** ，二者的区别主要是driver类在yarn的内部还是在yarn的外部，如果driver在yarn的外部的话，可以通过driver直接观察任务的运行情况，如果在cluster模式中，driver由yarn进行分配，所以无法直接通过driver对任务进行监控，只能通过日志系统对任务状况进行监控。

**client模式** ：driver程序运行在客户端，方便实时观察，适用于调试

**cluster模式** ：driver由resourcemanger管理启动的applicationmaster上，由于是集群内部进行数据交互，所以更适用于生产环境

**具体配置** ：如果是实验环境，并且机器配置可能不够好，那么可以在yarn-site.xml中添加以下配置

```xml
<!--是否启动一个线程检查每个任务正使用的物理内存量，如果任务超出分配值，则直接将其杀掉，默认是true -->
<property>
    <name>yarn.nodemanager.pmem-check-enabled</name>
    <value>false</value>
</property>
<!--是否启动一个线程检查每个任务正使用的虚拟内存量，如果任务超出分配值，则直接将其杀掉，默认是true -->
<property>
    <name>yarn.nodemanager.vmem-check-enabled</name>
    <value>false</value>
</property>
```

在spark中配置Hadoop的配置目录，就可以读取到yarn的相关信息

```shell
spark-env.sh
YARN_CONF_DIR=/opt/module/hadoop-2.7.2/etc/hadoop
```

启动日志服务系统，需要在spark-defult.conf中添加以下配置

```ini
spark.yarn.historyServer.address=hadoop201:18080
spark.history.ui.port=18080
```

当然首先要确定开启了yarn的历史服务器系统

```xml
yarn-site.xml
<property>
    <name>yarn.log.server.url</name>
    <value>http://hadoop201:19888/jobhistory/logs</value>
</property>
```



