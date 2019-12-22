### spark广播变量

**广播变量的意义：**

广播采用广播变量之后，driver发送给task的内容原本是以task为单位，现在则可以是以executer为单位进行发送。发送的，并且该值是只能读取的

```scala
//如何建立一个广播变量
def main(args: Array[String]): Unit = {
    val conf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("broad")
    val sc = new SparkContext(conf)
    val broadcast: Broadcast[Array[Int]] = sc.broadcast(Array(1,2,3,4))
    println(broadcast.value.foreach(println))
}
//之后这个broadcast就可以直接在各个节点之间互传，但是要注意broadcast内的value需要可以序列化的类型
```

### spark累加器

**累加器的作用：**

累加器可以在各个task中进行操作然后归并结果最后返回到driver端进行采集，因此累加器需要继承特质：AccumulatorV2，然后实现其中的方法，具体方法作用如下（为accumulate版的word count）：

```scala
package com.com.spark.op

import org.apache.spark.rdd.RDD
import org.apache.spark.util.AccumulatorV2
import org.apache.spark.{SparkConf, SparkContext}

import scala.collection.mutable

object AccumulatorEx {
    def main(args: Array[String]): Unit = {
        val conf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("AccEx")
        val sc = new SparkContext(conf)
        val list = List("hello","world","hello")
        val listRDD: RDD[String] = sc.makeRDD(list)
        val acc = new AccEx
        sc.register(acc)
        listRDD.map((_, 1)).foreach {
            t => {
                acc.add(t)
            }
        }
        acc.value.foreach(println)

    }
}
class AccEx extends AccumulatorV2[(String,Int),mutable.HashMap[String,Int]]{
    private val res = new mutable.HashMap[String,Int]()
    //确定是否为初始值
    override def isZero: Boolean = {
        res.size==0
    }
	//获取新的acc
    override def copy(): AccumulatorV2[(String, Int), mutable.HashMap[String, Int]] = {
        new AccEx
    }
	//重置acc
    override def reset(): Unit = {
        res.clear()
    }
	//累加acc的方法
    override def add(v: (String, Int)): Unit = {
        if (res.contains(v._1)){
            res.put(v._1,res.getOrElse(v._1,0)+v._2)
        }else{
            res.put(v._1,v._2)
        }
    }
	//归并2个acc的方法
    override def merge(other: AccumulatorV2[(String, Int), mutable.HashMap[String, Int]]): Unit = {
        val v: mutable.HashMap[String, Int] = other.value
        v.foreach{
            case (k,v)=>{
                if (res.contains(k)){
                    res.put(k,v+res.getOrElse(k,0))
                }else{
                    res.put(k,v)
                }
            }
        }

    }
	//得到acc内部存储的值
    override def value: mutable.HashMap[String, Int] = {
        res
    }
}

```

