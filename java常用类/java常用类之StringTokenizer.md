### java常用类之StringTokenizer

Hadoop官方样例中有一个处理文字分段的代码：

```java
public static class TokenizerMapper 
       extends Mapper<Object, Text, Text, IntWritable>{
    
    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();
      
    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
      StringTokenizer itr = new StringTokenizer(value.toString());
      while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
      }
    }
  }
```

其中使用了一个叫StringTokenizer的类：

```java
public StringTokenizer(String str) {
    this(str, " \t\n\r\f", false);
}
//使用这个构造器构造对象，可以将string中的空格换行tab什么的都去掉
```

调用hasMoreTokens可以判断是否有下一个遍历元素

调用nextToken可以输出下一个string元素