### java jdk1.8编译器

题目1:

```java
String a = "a" + "b";//有几个string对象
```

javac编译之后只有一个"ab"对象，所以只有一个

题目2:

```java
byte b = 10 + 10
```

提升类型之后后面的数值应该是int，为什么使用等号

javac编译之后 ```byte b = 20```

