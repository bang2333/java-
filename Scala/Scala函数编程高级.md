### Scala函数编程高级

1. 函数可以当作值进行传递，实际上就是在传递函数的地址 

    ```scala
    def f1()={
        println("f1")
        10
    }
    var a1 = f1
    println(a1)
    var a2 = f1 _
    println(a2)
    
    打印结果：
    f1
    10
    com.bang.sc.Ex09$$$Lambda$3/42121758@13b6d03
    ```

    

2. 函数可以当作参数进行传递

    ```scala
    def method(op:(Int,Int) => Int){
        println(op(2,4))
    }
    method((a:Int,b:Int)=>{
        a+b
    })
    ```

    

3. 函数可以当作返回值进行返回

    ```scala
    def foo() ={
        def f1(a:Int)={
            a+1
        }
        f1 _
    }
    var f2 = foo()
    println(f2(10))
    println(foo()(20))
    ```

    

4. 匿名函数

    ```scala
    def operation(arr:Array[Int],op:Int=>Int)={
        for(elem <- arr) yield op(elem)
    }
    
    println(operation(Array(1, 2, 3, 4), (n: Int) => n + 1).mkString(","))
    ```

    简化版本

    ```scala
    println(operation(Array(1,2,3,4), _ + 1).mkString(","))
    ```

    

5. 闭包

    概念：如果一个函数访问到它外部（局部）变量的值，那么这个值和它（这个函数）所处的环境就称为闭包

    作用：这个局部变量在应该被释放掉的情况下，还是能够使用

    ```scala
    def oop()={
        var a = 1
        def f1(n:Int) ={
            println(a + n)
        }
        f1 _
    }
    oop()(2)
    ```

    

6. 函数柯里化

    概念：使用了闭包的思想，调用函数中的函数，并且函数中的函数可以使用前面函数的值

    ```scala
    def oop()={
        var a = 1
        def f1(n:Int) ={
            println(a + n)
        }
        f1 _
    }
    oop()(2)
    ```

    再例如，函数中的函数，可以使用外层函数的参数，此处采用闭包

    ```scala
    def oop(m:Int)={
        var a = 1
        def f1(n:Int) ={
            println(a + n + m)
        }
        f1 _
    }
    oop(10)(2)
    ```

    简单写，可以这样：

    ```scala
    def oop(m:Int)(n:Int){
        print(m + n)
    }
    oop(1)(2)
    ```

    

7. 递归

    概念：方法调用本身称为递归，重复调用方法会大量占用栈空间，不建议使用

    ```scala
    //5! = ?
    def su(n:Int):Int = {
        if (n == 1){
            1
        }else{
            n * su(n = n - 1)
        }
    
    }
    
    var s = su(5)
    println(s)
    ```

    

8. 控制抽象

    控制抽象：由于Scala中的函数可以抽象传递，同时，函数的最后一行将会被返回，为了控制这个传递过程，所以引入：值调用以及名调用

    ```scala
    def main(args: Array[String]): Unit = {
        def f2 = () => {
            print("f2")
            20
        }
        foo2(f2())
        foo3(f2())
        foo4(f2)
    }
    def foo2 (n:Int) = {
        print(n)
    }
    def foo3 (op: =>Int) = {
        println(op)
    }
    //区别，这个是要匿名函数，并不是实现名调用，而是特指需要传入一个方法进来的
    def foo4 (op: ()=>Int) = {
        print(op)
    }
    ```

    

9. 惰性函数

    概念：类似于实现懒加载效果，当该计算结果到调用的时候，才去执行计算任务，关键字lazy，实现：

    ```scala
    def sum(a:Int , b:Int) = {
        println("start sal")
        a + b
    }
    lazy val sum1 = sum(2,4)
    println("==================")
    print(sum1)
    ```

    lazy 只能与val使用，var不能使用lazy