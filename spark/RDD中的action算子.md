### RDD中的action算子

- 1. reduce(func)

        通过func中的方法对RDD中的元素进行聚合，先在分区聚合，然后再次聚合

    2. collect()

        会将所有的数据全部采集到driver端，所以一般避免使用

    3. count()：求和

    4. take()：把所有数据拉去到driver端之后进行截取

    5. first：返回第一个元素，同take(1)

    6. takeOrdered()：排序之后拉取数据到driver端

    7. aggregate(zerovalue)(seqOp,combOp)

        将每个分区内的数据按照seqop进行聚合，分区之间的数据按照combop进行聚合，zerovalue会在分区内以及分区间都有效。

    8. fold：当aggregate的seqop以及combop的操作相同时可以使用fold进行聚合，fold同样需要zerovalue

    9. saveAsTextFile()

        将数据保存到文本格式到指定的文件系统目录

    10. saveAsSequenceFile()

        将数据保存到序列化的文件到hdfs以及其他支持Hadoop文件系统的系统中，采用的Hadoop的序列化接口

    11. saveAsObjactFile：

        将数据保存为对象，采用的是spark的序列化

    

    

    