File类是用来描述文件的，同样，也可以通过对文件的描述去创建一个文件或者目录，即可以认为是一个文件的属性，如果有了一个文件的属性，除了文件内容需要IO流去处理，其他的内容都可以通过File这个类进行相应处理

```java
//利用File创建文件或者目录
File file = new File("d:/1.txt");
if(!file.exists()){
	System.out.println("文件不存在，创建这个文件");
	file.createNewFile();
}
File file = new File("d:/IOTest");
if(!file.exists()){
	System.out.println("文件夹不存在，创建这个文件夹");
	file.mkdir();
}
//删除文件
File file = new File("d:/1.txt");
file.delete();
//删除文件目录，如果删除根目录，需要递归遍历挨个删除
public void deleteDir(File file){
	//（1）如果是文件夹，先把它的下一级删除
	if(file.isDirectory()){
		File[] listFiles = file.listFiles();
		//经过这个foreach循环，可以把file的所有的下一级删除
		for (File sub : listFiles) {
			deleteDir(sub);
		}
	}
	//文件夹就变成了空文件夹，就可以直接删
	//如果是文件也可以直接删除
	file.delete();
}
```
修改某些属性
```java
//修改文件目录名字
File file = new File("d:/IOTest");
File dest = new File("d:/IOGUIGU");
file.renameTo(dest);

//修改文件名字以及扩展名
File file1 = new File("d:/1.txt");
File file2 = new File("d:/2.sql");
file1.renameTo(file2);
```

获取file的属性：

```java
//file 的绝对路径

```

