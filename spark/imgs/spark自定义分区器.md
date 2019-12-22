### spark自定义分区器

只存value类型的RDD中没有分区器，只有kv类型的RDD才有分区器

spark中的分区器默认有2种分区器，hashpartition和rangepartition，其中hashpartition的实现如下：

hashmap中的hash算法如下：

rangepartition的分区方案如下：

用户可以自定义分区器，其中分区器需要引入org.apache.spark.Partitioner特质，需要实现的方法以及作用分别是：