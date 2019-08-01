### 200wQQ号排序,生成QQ

首先要有200w个需要自动生成，可以使用int，但是生成时不方便，所以自定义一个数据类型，存放获取的QQ号

```java
public class StringBang {
	private char[] elements;
	private int total;
	char space;
    private final static char [] DigitOnes = {'0', '1', '2', '3', '4', '5', '6', '7', '8', '9'};

	public StringBang() {
		elements = new char[12];
		total = 0;
	}

	public void append(int ele) {
		if (ele >= 0 && ele < 10)  elements[total++] = DigitOnes[ele];
	}
	
	public void clear() {
		elements = new char[12];
		total = 0;
	}

	@Override
	public String toString() {
		int i = 0,j = 0;
		while(elements[i] != space) i++;
		char[] res = new char[i];
		while(j < i) res[j] = elements[j++];
		return new String(res);
	}
}
```

其实是仿写StringBuffer，DigitOnes用来拼接数字，多写了一个clear方法，让这个对象可以重复使用，而不是再new一个

生成类：

```java
public class Test {
	static char[] one = new char[10];
	public static void main(String[] args) throws IOException {
		File file = new File("d:/randomQQ2");
		Random random = new Random();
		StringBang sb = new StringBang();
		file.createNewFile();
		FileWriter fw = new FileWriter(file);
        //要生成200w个
		for (int i = 1; i <= 200*100*100; i++) {
            //生成的长度6-9位
            for (int j = 0; j < random.nextInt(4) + 6; j++) {
                //拼接到sb中
                sb.append(j != 0 ? random.nextInt(10):1+random.nextInt(9));
            }
            //输出
            fw.write(new String(sb.toString()) + (i % 10 == 0 ? "\n" : "   "));
            //不flush会缓存
            fw.flush();
            //sb.clear请出sb中的内容，下次使用不会累加
            sb.clear();
        }
		fw.close();
	}
}
```

