### spark架构

spark是一个具有多功能的集成框架，内置模块如图：

<img src="./imgs/spark架构图.png" style="zoom:50%;" />

而在spark中所有的元素都是通过RDD来构建的，包括结构化数据以及实时计算的功能模块中，也都是将spark的RDD进行进一步的封装得到更为适合场景计算的操作，而RDD进行计算的架构如图：

<img src="./imgs/spark 结构.png" style="zoom:50%;" />

在master端通过applicationmaster对驱动程序类进行封装，在work端对task用executor进行分装，以便可以替换spark的核心部件

RDD的本质：RDD是在spark框架中对所要操作的数据集的封装，可以使得使用者想操作一个集合一样取使用操作数据， RDD内建有分区，分区是根据集群可以同时承受的线程数决定的，且跟线程数保持一致，因此当操作RDD时，如果涉及到一个分区等待另一个分区的结果之后才能进行后续操作，那么该分区现时的计算结果就需要写入磁盘进行等待，也就是类似于Hadoop的shuffle过程，进行归并排序等一系列操作才能得到结果，因此如果能避免这种操作就尽量避免。

driver program：驱动程序是将RDD的逻辑操作下发到executer的程序，驱动程序运行的机器并不需要大量资源，因此在操作RDD时的action操作尽量避免将所有数据拉取到driver端进行统一操作，会引起内存溢出。action操作会将此前的所有RDD算子的操作进行提交job操作， 生成task下发到集群。

cluster managers(集群管理器)：集群管理器实时监控所有的集群资源信息，是sparkcontent可以连接到的管理器之一，国内通常通过yarn进行替代。

executer：真正执行计算的进程，在集群的每一个机器上都会有executer，每一个executer都会接受spark发送的应用程序打出的jar执行。

