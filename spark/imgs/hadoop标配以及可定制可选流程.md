### hadoop标配以及可定制可选流程

- map：

    如果标准输入一个文本文件信息，并且使用fileinputstream进行读取文件，map将会传入一个文件读取行数的偏移量longwrite类型k以及一行数据text类型，可以根据用户自定义输出相应的k和v，如果只是一个etl过程的话，只需要map不需要reduce，需要在driver端设置reducetask的数量为0，并且不需要写driver类也无需注册

- reduce

    将所有的k相同的数据集中到reduce的v（一个可迭代类型的value值）中，用于统计

- shuffle

    shuffle是map和reduce之间的一个过程，将map生成的数据进行排序之后给到reduce端进行聚合处理

    **其中如果不需要value的操作用，可以使用NullWritable.get()代替**

- combiner

    在map端的一个预聚合处理，可以增快shuffle的操作速度，减少shuffle的时间，具体实现例如：

    ```java
    //继承reducer接口重写reduce方法
    @Override
    protected void reduce(Text key, Iterable<IntWritable> values, Context context) throws IOException, InterruptedException {
        // 1 汇总
        int sum = 0;
        for(IntWritable value :values){
            sum += value.get();
        }
        v.set(sum);
        // 2 写出
        context.write(key, v);
    }
    //在driver端注册该类
    job.setCombinerClass(MyCombiner.class);
    ```

    如果reduce阶段的操作和combine阶段的操作相同，那么可以直接使用reduce方法进行聚合driver注册时直接使用reduce的类注册。

    **不同：**

    ​	如果需要对分好组的内容进行二次排序，可以使用groupingcomparator进行分组。

- inputformat

    可自定义输入方式

    Hadoop自带的常用inputformat有：

    ​	fileinputformat：按行读取文件，k是偏移量，按照单个文件切片，且小文件不会合并

    ​	combinetextinputformat：可以将小文件合并的，将所有文件都打散之后合并到指定大小进行切片，可以减少task的提交数量，例如：

    ```java
    // 如果不设置InputFormat，它默认用的是TextInputFormat.class
    job.setInputFormatClass(CombineTextInputFormat.class);
    //设置之后传入job的引用以及要指定的大小
    //虚拟存储切片最大值设置4m
    CombineTextInputFormat.setMaxInputSplitSize(job, 4194304);
    ```

    ​	keyvalueinputformat：将当前行的开头作为key，\\t后面的内容作为value传入，特殊需求下可用

    ​	nlineinputformat：按行划分切片的inputformat，例如：

    ```java
    // 7设置每个切片InputSplit中划分三条记录
    NLineInputFormat.setNumLinesPerSplit(job, 3);
    //可以将maptask按照行数提交
    // 8使用NLineInputFormat处理记录数  
    job.setInputFormatClass(NLineInputFormat.class);  
    ```

    

- combineTextInputFormat

- 计数器：在Hadoop中可以创建自己的计数器，用于方便观察数据的处理是否符合预期。方法：context.getCounter("etl","elt1").increment(1);

- 分区器（partitioner）:	自定义分区器之后就可以让不同key的数据进入不同分区，生成与分区个数相同的输出文件，并且，partition数量和最终的reduce数量相同

