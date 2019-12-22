### RDD创建方式

RDD共有三种创建方式：

> - 从集合中创建
> - 从外部存储创建（读取存档，或者读取文件创建RDD）
> - 从其他RDD转换得到新的RDD（计算过程中的RDD转换）

一般的创建套路：(从外部创建)

```scala
//含义，创建一个配置对象，给当前的配置对象起名为wordcount，选择启动模式为本地，核心数自动获取[*]
val sparkconf = new SparkConf().setAppName("wordcount").setMaster("local[*]")
//创建一个spark环境连接对象，其实也就是spark上下文环境对象的类，称之为Driver类
val sc = new SparkContext(sparkconf)
//通过Driver类对当前环境中的文件进行读取，可以返回一个初始状态的RDD对象，默认的这个读取方式为按行读取，将每一行读取到一个string当中
val linkRDD: RDD[String] = sc.textFile("data/input")
```

从内存中创建parallelize

```scala
val sc = new SparkContext(new SparkConf().setAppName("test").setMaster("local[*]"))
val list = List("hello","spark")
val listRDD : RDD(String) = sc.parallelize(list)
//以及还可以使用makeRDD方法进行创建
val makerdd : RDD(String) = sc.makeRDD(list)
```

从外部创建文件是需要写明绝对路径，hdfs等文件系统，需要加文件协议头部，本地磁盘读写也可以加入file:///