### scala类的定义及使用（面向对象）

- 1. Scala中没有public，Scala中的默认其实底层是使用private进行封装的，然后使用同名方法对属性进行访问，所以，Scala建议bean规范如下，所以Scala中的bean标准bean：

        ```scala
        class Person{
            var name:String = "";
            @BeanProperty//类似于java中的set/get 定义的bean，可以与java程序兼容
            var age:Int = 0;
        }
        ```

        标准方法定义：（可以使用至简原则进行简化）

        ```scala
        def method(args:Int):Int = {
        	return args+1
        }
        ```

        定义方法为参数：

        ```scala
        def main(args:Array[String]):Unit = {
            //method1((n)=>println(n))
            //method1(println(_))
            method1(println)
        }
        def method1(op:(Int)=>Unit):Unit = {
            op(666)
        }
        ```

    2. 对象构造器

        基本语法：

        ```scala
        class Person(){//class后面的是默认构造器，如果没有参数可以省略
            def this(args:???){
                this()
            }
            def this(args:???){
                this()
                //or this(args[])
            }
        }
        ```

        class后面的（）如果无参可以省略，this关键字可以使类具有多个构造器，this关键字的构造器必须调用主构造器，或者间接调用主构造器

        e . g :

        ```scala
        class Person(String name,var age:Int,val sex:String){
            
        }
        ```

        参数可以由var以及val修饰，没有任何修饰的属性只是一个局部变量作为参数传入，并不能后期进行操作，var修饰的参数可以当作一个可变的成员变量，val修饰的参数则可以当作一个只读的成员变量

        e . g:

        ```scala
        def main(args:Array[String]):Unit = {
            var person = new Person("zhangsan",18,"man")
            //println(person.name)//局部变量，不能访问
            person.age = 20
            println(person.age)
        	//person.sex = woman //不可变变量
            println(person.sex)
        }
        ```

    3. 继承

        Scala是单继承的；

        Scala子类将继承父类的属性和方法；

        语法及其他参考java