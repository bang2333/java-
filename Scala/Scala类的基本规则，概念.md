### Scala类的基本规则，概念

- package功能：

    Scala中的package除了java中的风格还可以有

    ```scala
    package com{
        package bang{
            package scala{
                
            }
        }
    }
    ```

    新规则：

    1. scala的package可以多次声明，package的声明和包可以没关系，即可叠加

    2. 可以嵌套形成父子包：

        ```scala
        package com.bang
        package test{
            //test的作用范围
        }
        object C1{
            
        }
        ```

    3. 子包无需import即可访问父包中的类

    4. 包对象概念：示例

        ```scala
        package com.bang
        package object test{
            val name = "bang"
            def getName = name
        }
        object ObEx03{
            def main():Unit = {
                test.getName
            }
        }
        ```

        类对象仅作用于本包对象，其他包无法引用

- 访问权限：

    默认（public）：什么都不加就是public，且Scala中没有这个关键字

    protected：仅父子类可以访问

    private[包名]：包私有，可以在[]指定的包内访问该属性或方法

    private：私有，同java

- import的功能：

    1. 可以导包，java只是在导类

        一次导入太多会导致oom

        递归调用会导致栈溢出（不是栈内存溢出）栈里面总共有16*1024个小格子，这个东西溢出了

    2. 同一个包中的多个类需要引入的时候：

        import java.util.{ArrayList,HashMap}

    3. 导入多个类可以直接使用_代替，例如 import java.util._

    4. import可以声明在任何一行，不一定是开头

    5. 可以将某个包中的某个类屏蔽，避免冲突，例如

        ```java
        import java.sql.{Date=>_,Array=>_,_}
        ```

    6. 可以给导入的类起别名

        ```java
        import java.util.{Map=>JavaMap}
        ```

    7. 默认使用的使用的包是相对包，但是有时候需要绝对包，示例：

        ```scala
        import java.util.HashMap
        \\----------------------
        \\如果想使用java本身的HashMap对象
        import _root_.java.util.HashMap
        
        object Test{
            def main(args:Array[Int]())Unit = {
                new HashMap
            }
        }
        package java{
            package util{
                class HashMap{
                    
                }
            }
        }
        ```

        

- 