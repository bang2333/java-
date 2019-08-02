### java语法糖之：assert

assert：断言，大部分情况下没有使用这个语句的代码，在java中要想使这个语句起效果，需要开启ea开关

下面是演示：

```java
public static void main(String[] args) {
    //断言直接赋值true，程序继续执行
    assert true;
    System.out.println("hello assert");
    System.out.println("======================");
    //断言2赋值为false，程序中断抛出AssertionError错误
    assert false : "抛出错误，不是异常";
    System.out.println("assert is good");
}
```

上述代码在IDE环境中运行不会有任何效果，因为IDE基本没有默认开启这个开关的

执行程序时需要开启-ea开关，命令行执行时应该是：

java -ea AssertTest，这样的命令可以达到预期效果。

```java
@Test
public void test01() {
    //断言直接赋值true，程序继续执行
    assert true;
    System.out.println("hello assert");
    System.out.println("======================");
    //断言2赋值为false，程序中断抛出AssertionError错误
    assert false : "抛出错误，不是异常";
    System.out.println("assert is good");
}
```

当然也可以这样去执行，Junit中是开启这个的，而且大部分情况下在测试程序中才有见到assert

