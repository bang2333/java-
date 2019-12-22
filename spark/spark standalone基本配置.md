### spark standalone基本配置

conf/spark-env.sh添加：

```shell
SPARK_MASTER_HOST=hadoop201
SPARK_MASTER_PORT=7077 # 默认端口就是7077, 可以省略不配
```

master通讯端口：7077

slaves文件中将从属节点添加进来

```ini
bang02
bang03
bang04
```

启动集群脚本	sbin/start-all.sh

master的web端口8080，可以查看整个集群状况



