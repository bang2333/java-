### RDD算子之transform算子介绍

> transform算子包括value类型算子，key-value类型算子，后者只能对键值对类型的数据进行计算

- 1. map(func)

        与Scala中的map相似，spark中的map可以对RDD中的元素挨个进行转换，传入的匿名函数类型应该是：S=>T，即传入一个S类型的值，可以转换为任意A类型的值

  2. flatmap(func)

   传回值需要是一个可迭代集合，通过flatmap函数可以将这个集合自动退化

    3. mapPartitions(func)

        与map作用相同，原理有所差异，这样内部操作中，map一次将集体操作一个partition内的所有元素，即通过源码了解到，一次性读取一个分区的所有内容，然后在内存中挨个对数据进行map操作，由于需要大量读取数据到内存，对于数据量太大或者性能不足的情况下，不建议使用

    4. mapPartitionWithIndex(func)

        调用这个函数时，匿名函数方法的参数中会给一个当前元素所在partition分区的一个参数，以及整个分区的元素形成的可迭代元素，我们可以通过迭代器将分区号加入每个元素中返回到分区
  
        匿名函数格式	(Int,Iterator[String]) => Iterator[U] 
  
        ```scala
      listRDD.mapPartitionsWithIndex((n,iter)=>{
            iter.map(s=>n+s)
      }).collect().foreach(println)
        ```

    5. glom()

        将每个分区的数据聚合为数组，例如如果有8个分区，那么就会有8个数组，这样可以对聚合好的分区内部的内容进行一定的筛选，应该配合分区器使用，但是分区器需要用在kv类型的RDD当中，后面演示

    6. groupBy()

        将所有的数据根据传入函数的返回值进行分组，结果是一个序列号和一个分组，但是这个过程当中，RDD的分区也发生了改变，所以groupby之后的分组内部的数据都是不同的，分组的个数与分区的数目保持一致，当分组为一个时，分区将会变为一个，慎用。

    7. filter()

        跟所有的filter一样，spark当中的filter可以将经过func计算为true的数据进行保留形成新的RDD

    8. sample(withrepliacement,fraction,seed)

        随机抽取函数，第一个参数withrepliacement可以设置为true和false，true表示会将取出的数据放回，seed为生成数据的种子，fraction表示抽出的比例，抽出数据的数量与这个比例不一定相等，种子是可以默认的
  
    9. distinct()
  
        去重函数，可以将RDD当中的重复元素去重，操作过程需要经过shuffle机制，所以经过distinct之后数据的顺序可能于原来的存储顺序不一致，因为去重一般需要排序
  
  10. coalesce()
  
        缩减分区为指定的数目，第二个参数为是否进行shuffle，默认为false不进行shuffle，直接将数据按照分区进行重新划分，有可能造成数据倾斜，开启shuffle可以使数据均匀分配
  
  11. repartition()
  
        实际上底层调用的就是coalesce的开启shuffle方法，如果数据不均匀一定要进行shuffle
  
  12. sortBy()
  
         第一个参数传递根据什么进行排序，第二个数据传递是否升序，如果是false那么就是按照降序进行排序
  
  13. pipe()
  
         pipe里可以传一个sh文件进去，对每条数据进行操作，每个分区将会分发一个sh文件，sh文件是按分区进行操作的
        
         sh文件举例：
        
         ```shell
         echo "hello"
         while read line;do
             echo ">>>"$line
         done
         ```
  
  
  - key value 数值转换