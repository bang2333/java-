### kafka架构
- 名词解释
```shell
[bang@bang02 kafka]$ bin/kafka-topics.sh --zookeeper bang02:2181 --describe -topic first
Topic:first	PartitionCount:6	ReplicationFactor:3	Configs:
	Topic: first	Partition: 0	Leader: 4	Replicas: 4,3,2	Isr: 2,3,4
	Topic: first	Partition: 1	Leader: 2	Replicas: 2,3,4	Isr: 4,3,2
	Topic: first	Partition: 2	Leader: 3	Replicas: 3,4,2	Isr: 4,2,3
	Topic: first	Partition: 3	Leader: 4	Replicas: 4,3,2	Isr: 2,3,4
	Topic: first	Partition: 4	Leader: 2	Replicas: 2,4,3	Isr: 4,2,3
	Topic: first	Partition: 5	Leader: 3	Replicas: 3,2,4	Isr: 4,2,3
```
topic：话题，订阅和发布是通过制定kafka的topic进行的
partition：分块对话题进行存储，达到巨大吞吐量，也是kafka的缺点，多个partition会造成消费的数据没有写入时的顺序，只有单个partition才能对消息的内容实现有序
leader：当前partition对应的leader所在机器序号，序号由启动或者配置文件中设置
replicas（AR）：当前partition备份到哪些机器，列出序号
Isr：备份系统中按照与leader的相似度排名之后的序号，排名越前的相似度越高
- 文件系统解释
```shell
[bang@bang02 logs]$ cd first-0
[bang@bang02 first-0]$ ll
总用量 20
-rw-rw-r--. 1 bang bang 10485760 8月  14 00:11 00000000000000000000.index
-rw-rw-r--. 1 bang bang     1095 8月  13 10:52 00000000000000000000.log
-rw-rw-r--. 1 bang bang 10485756 8月  14 00:11 00000000000000000000.timeindex
-rw-rw-r--. 1 bang bang       10 8月  13 10:27 00000000000000000006.snapshot
-rw-rw-r--. 1 bang bang       10 8月  13 21:08 00000000000000000046.snapshot
-rw-rw-r--. 1 bang bang       12 8月  13 10:41 leader-epoch-checkpoint

```
单个partition建立一个文件夹，文件夹中包括多个log文件，对消息进行序列化存储，多个log文件成为segment（碎片），其中的index文件为元数据，log文件为实际存储的文件