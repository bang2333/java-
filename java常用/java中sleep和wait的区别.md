### java中sleep和wait的区别

代码片段：

```java
class Object{
    public final void wait() throws InterruptedException {
        wait(0);
    }
}
class Thread{
    public static native void sleep(long millis) throws InterruptedException;
}
```

两个方法的来源不同

修饰符不同，sleep修饰的是static，所以他的内存块在方法区，如下代码所示，sleepex方法在主线程调用，所以会让主线程休眠，需要等待sleep之后才执行下面的代码

```java
public static void main(String[] args){
    sleepex();
    System.out.println(123);
}
static void sleepex(){
    Thread.sleep(10000);
}
```

wait方法之后写