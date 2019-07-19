### String总结

首先string存储字符的位置

```java
public final class String implements java.io.Serializable, Comparable<String>, CharSequence {
/** The value is used for character storage. */
	private final char value[];
}
```

修饰符为private final 不可变且私有的一个字符数组，所以string的每一次赋值都是改变string引用的指向地址

```java
 * 字符串常量池在哪里？Oracle官方虚拟机HotSpot
 * （1）JDK1.6以及之前：方法区
 * （2）JDK1.7：挪到堆中，即在堆中单独划分了一块来存字符串常量
 * （3）JDK1.8：从堆中挪出，挪到一个“元空间meta space”，即类似于方法区 
```

例如String的构造方法：

```java
public String(String original) {
    this.value = original.value;
    this.hash = original.hash;
}
```

如果定义一个String

```java
String str = new String("hello")
```

此时有2个String对象，堆内存中的String的value数组引用直接指向了常量池中的"hello"的地址，hash值也直接改变为"hello"的hash值，而引用值str指向的是堆内存中的new String(); 对象。

**注意** ：所有的""这种的都是在常量池中的

特殊方法intern();

```java
String str1 = "1";
String str2 = "2";
String str6 = "12";
String str13 = (str1 + str2).intern();
System.out.println("(10)"+ (str6 == str13));
```

intern()方法可以从常量池中拿出来和().intern();前面的结果一样的一个字符串来讲str13指向这个字符串，常量池中的字符串都是唯一的，所以str6和str13是相同的地址。

#### 常见方法

> 1. charAt(int index)：按照索引查询char值
> 2. boolean isEmpty()  当且仅当length()为0时返回true，使用时第一点一定要判空，否则空指针异常
> 3. replaceAll(String regex,String replacement): 使用给定的replacement替代符合regex的字符串中的字符，regex可以是正则表达式
> 4. replaceFirst(String regex,String replacement): 替代符合的头一个，其他的内容参考3
> 5. substring(int beginIndex): 返回beginIndex之后的所有的字符
> 6. substring(int beginIndex,int endIndex): 返回beginIndex和endIndex之间的子串
> 7. split：将字符串按照括号内的内容截取为一个数组保存，另一个带有limit的方法，limit用来控制可以切割为几个，也就是数组的长度，没有指定则从头切到尾。
