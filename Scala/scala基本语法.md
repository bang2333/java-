### scala

scala的hello world
```scala
object HelloWorld {
    def main(args: Array[String]): Unit = {
        println("hello world")
	}
}

//常量定义
		var a = 1
		val b = 2
		var c = 1
		val d = 2
		var hello = "hello" + " " + "world"
		printf("hello%s","zhangsan")
		val sql =
		  s"""
		    |select
		    |     name,
		    |     age
		    |from
		    |     stu
		    |where name = "$d"
		    |""".stripMargin
```

Scala中的""""""可以将string原样输出

特殊类型
Unit：表示无值，用来替代java中的void，但在Scala中还是有值的，表示()
Null： 替代null，只有一个值null，引用类型的子类，与数值类型做区分
Nothing：是所有类型的子类，当需要返回一个空的值但是不确定返回什么时，用Nothing代替
Any：是所有类型的父类，当不知道要传过来一个什么值可以用any代替，范型之后做记录

运算符注意事项：
没有++ -- 
==表示的是比较值
.eq 表示比较地址


Scala语法特征：一切皆方法，一切皆对象
上述条件是保证Scala函数式编程的基础
具体表现：Scala中的运算符号都是方法：在Scala中，对象后面的调用方法可以将.省略，如果方法只有一个参数可以将()省略
如果1 + 2 用函数调用来表示应该是 1.+(2)

Scala中的if
```scala

首先if可以用来替代java中的三元运算
	语法结构：
		if (flag) "true" else "false"
```
Scala中的for
```scala
基本语法格式： 循环的区间[1,3]
	for(i <- 1 to 3){

	}

	第二种：	循环的区间[1,3)
	for(i <- 1 until 3){

	}

	第三种：	循环守卫	守卫原则：当后面的if成立时，执行{}中的语句，否则不执行
	for(i <- 1 to 5 if != 2){

	}
	第四种：	设置循环步长	by后面为步长，表示i每次加的数值
	for(i <- 1 to 10 by 2){

	}
	第五种：	嵌套循环
    for(i <- 1 to 9; j <- i to 9){
        print(i + "*" + j + "=" + (i*j) + " ")
        if(j == 9) println()
    }
	另一种写法
    for{
    	i <- 1 to 9
    	j <- i to 9
    } {
        print(i + "*" + j + "=" + (i*j) + " ")
        if(j == 9) println()
    }
	第六种：	引入变量
	for(i <- 1 to 9; j = 10 - i){
		println(j)
	}
	等价于：
	for(i <- 1 to 9){
		var j = 10 - i
		println(j)
	}
	第七种：循环返回值
	val res = for(i <- 1 to 10) yield i
	println(res)

	作用：每次循环，把i返回，交付到res中	，所以i也可以进行处理

	第八种：倒序打印
	for(i <- 1 to 10 reverse){
		println(i)
	}
```
while 以及 do_while 和java中的语法相近
但是while中常会引用





















