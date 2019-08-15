### kafka故障解决机制
- 首先需要俩个概念
> **HW ：** 即使在ISR中，如果有大量的数据进入kafka，follow的数据不能及时和leader的同步，HW表示ISR队列中消息差距最大的那个，即消息队列最短的那个
> **LEO ：** 每个partition的最后一个offset的位置，包括leader和follow。
> 注：HW之前的数据consumer才能够消费，后面的数据不能消费

恢复策略：
- 如果follow故障了，follow需要将上一次记录的HW之后的记录全部删除，然后从leader主动拉取后面的数据
- leader故障：首先，当kafka启动时需要依赖于zookeeper，第一个进入zookeeper的follow就是contruller，leader挂掉之后，conturller会从ISR中将相似度最高的follow替换为leader，其他follow将上次HW之后的数据全部切除，与新的leader进行同步

