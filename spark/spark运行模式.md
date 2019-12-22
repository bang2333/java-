### spark运行模式

<img src="./imgs/spark架构图.png" style="zoom:50%;" />

最下面的就是spark的三种常用的模式

独立调度器模式：高度集合spark内部的部件，没有解决耦合问题，不能后期替换调度框架，一般不使用

yarn模式：使用yarn进行任务的调度

mesos模式：另一种框架，国内几乎不使用

