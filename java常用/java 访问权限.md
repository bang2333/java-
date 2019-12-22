### java 访问权限

访问权限：方法的提供者和方法的调用者之间的关系

```java
class Test{
    public static void main(String[] args){
        A a = new A();
        a.clone();//报错行
    }
}
class A{
    
}
```

正确解释：Test在它的main方法中调用了a当中的clone方法，调用的发起者是Test

<img src="/Users/bang/Documents/coder/imgs/java_quanxian.png?" alt="/Users/bang/Documents/coder/imgs/" style="zoom:40%;" />

. 的含义：

```java
User user = new User();
user.name = "zhangsan";
```

错误理解：调用name属性并赋值张三

正确：给user对象的name属性赋值为zhangsan

. 表示从属关系