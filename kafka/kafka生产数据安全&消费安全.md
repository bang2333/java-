### kafka数据生产与消费安全（kafka事物）
- #### 如何保证数据的生产安全性问题
生产者在生产数据并向kafka发送时，需要先将生产的消息包装为一个ProducerRecord对象。
****
**1. 怎么保证数据的平均分配**
发送的消息在有指定的partition的情况下，将会发送到指定的partition中存储，如果没有指定，根据指定的key计算hash值，与topic进行取余之后分配partition，如果没有指定key的话，第一次会产生一个随机数（这个随机数只会产生一次，之后会累加1），用这个随机数根据分区数量取余，进入对应的分区进行存储。这样的话，每次累加再进行存储，可以对所有的partition进行轮询（round-robin算法）
**2. 怎么保证每一条数据的生产安全**
发送的消息发送到指定的topic（话题）之后，会对应一个partition进行接收这个数据，partition对数据进行正确的存储后将会向生产者返回一个ack（acknowledgement）对消息进行确认，确认后生产者就可以发送下一条信息，否则会重新发送。
1. 生产者方面
> 为什么生产者可以对信息进行确认，未确认是等待，失败重新发送？
> e.g 生产者程序1
```java
package com.bang.producter;
import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.Producer;
import org.apache.kafka.clients.producer.ProducerRecord;
import java.util.Properties;
public class ProducterNoBack {
    public static void main(String[] args) {
        Properties props = new Properties();
 //对这个集群任意一个地址进行访问都可以获取kafka集群列表，与生产者沟通的为partition对应的leader，不会与follow进行数据传输
        props.put("bootstrap.servers", "bang02:9092");//kafka集群，broker-list
        props.put("acks", "all");
        props.put("retries", 1);//可以定制重试次数
        props.put("batch.size", 16384);//批次大小
        props.put("linger.ms", 1);//等待时间
        //kafka发送消息时，不是将消息直接发送到server当中的，而是现将消息缓存到这个RecordAccumulator中，
        //当缓存满了，或者发送的等待时间到了， 将消息发送到服务器中
        props.put("buffer.memory", 33554432);//RecordAccumulator缓冲区大小
        //要传输的数据类型，对应的kafka要进行序列化，所以要指定
        props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        Producer<String, String> producer = new KafkaProducer<String, String>(props);
        for (int i = 0; i < 100000; i++) {
        //Future<RecordMetadata> send(ProducerRecord<K, V> var1, Callback var2);
        //函数结构如上，需要传递一个producerrecord类型的数据给partition
        //同时传递一个Callback类型的对象，当partition接受到之后会调用回调函数，将写入的数据返回，或者将产生的异常返回
            producer.send(new ProducerRecord<String, String>("second",("i = " + i)),
                    (matadata, exception) -> {if (exception == null) System.out.println(matadata);
                                                if (exception != null) System.out.println(exception);});
        }
        producer.close();
    }
}
```
- 相关类
```java
public interface Callback {    
    void onCompletion(RecordMetadata var1, Exception var2);
}
//ProducerRecord构造方法种类：
public ProducerRecord(String topic, Integer partition, Long timestamp, K key, V value) 
public ProducerRecord(String topic, Integer partition, K key, V value, Iterable<Header> headers) 
public ProducerRecord(String topic, Integer partition, K key, V value) 
public ProducerRecord(String topic, K key, V value) 
public ProducerRecord(String topic, V value) 
```



2. kafka服务端接受策略
生产者受到server端的ack之后就可以确认自己确实写入成功， 而服务端确认写入成功的策略有两种。
> 1. 策略一
>     半数以上与leader同步成功，就发送ack
> 2. 策略二
>     全部同步之后才发送ack
> 策略二要求所有的成功备份才能够去发送ack，所以延迟比较高，并且存在如果某个节点接受数据特别慢，影响整体进度，所以kafka采用ISR进度表来保证数据接受慢的节点不会影响整体进度，也就是实际不一定所有都接受到
> 
> ---- 
> 实际上ISR就是在一定时间都可以跟上leader消息同步的一个列表，其中排位靠前的节点与leader的同步率最高
ack应答策略
>  策略一
> producer不等待 broke的返回ack，只要leader接受到数据之后就可以立即应答ack，follow之后慢慢同步，响应速度最快，leader挂了丢失数据概率最高
> 策略二、三就是上面的一二。
