### 函数式编程方法省略原则（Scala）

函数式编程遵循原则：

1. 如果有return则不推断返回结果，没有return对结果进行推断，因为对结果进行了推断，所以可以省略方法头中中的结果返回值
2. 如果函数体只有一行那么大括号可以省略
3. 如果函数头重声明了Unit类型为返回类型，函数体中即使有return语句也是无效的
4. 如果提前期望该函数返回值为Unit的话，可以直接省略“=”，这样即使有return也不去作用
5. 如果函数无参数，即使声明了参数列表，调用时“()”可加可不加
6. 如果函数无参数，且没有声明参数列表，调用时‘（）’不可以加
7. 如果只关心过程不关心结果，那么def以及函数名称都可以省略

```scala
1.标准函数格式
def opera(a:Int,b:Int):Int = {
    return a + b
}
2.return可省略
def opera(a:Int,b:Int):Int = {
    a+b
}
3.返回值类型可以推导
def opera(a:Int,b:Int) = {
    a+b
}
4.如果没有=则表示的意义跟指定返回类型为Unit一样
def opera(a:Int,b:Int){
    a+b
}
5.只有一行逻辑代码 {}省略
def opera(a:Int,b:Int) = a+b
6.如果没有参数
def opera() = 10
7.还可以
def opera = 10
```

