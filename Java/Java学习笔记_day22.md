# Java学习笔记_day22

[TOC]

## 注册IO版

> 在局部变量初始化是null时，那么在后面调用的时候一定要判断是否为空
>
> 关闭流的操作放在finally语句中

```java
public class UserDaoImpl implements UserDao {
	// 为了保证文件一加载就创建
	private static File file = new File("user.txt");

	static {
		try {
			file.createNewFile();
		} catch (IOException e) {
			System.out.println("创建文件失败");
			// e.printStackTrace();
		}
	}

	@Override
	public boolean isLogin(String username, String password) {
		boolean flag = false;

		BufferedReader br = null;
		try {
			// br = new BufferedReader(new FileReader("user.txt"));
			br = new BufferedReader(new FileReader(file));
			String line = null;
			while ((line = br.readLine()) != null) {
				// 用户名=密码
				String[] datas = line.split("=");
				if (datas[0].equals(username) && datas[1].equals(password)) {
					flag = true;
					break;
				}
			}
		} catch (FileNotFoundException e) {
			System.out.println("用户登录找不到信息所在的文件");
			// e.printStackTrace();
		} catch (IOException e) {
			System.out.println("用户登录失败");
			// e.printStackTrace();
		} finally {
			if (br != null) {
				try {
					br.close();
				} catch (IOException e) {
					System.out.println("用户登录释放资源失败");
					// e.printStackTrace();
				}
			}
		}

		return flag;
	}

	@Override
	public void regist(User user) {
		/*
		 * 为了让注册的数据能够有一定的规则，我就自己定义了一个规则： 用户名=密码
		 */
		BufferedWriter bw = null;
		try {
			// bw = new BufferedWriter(new FileWriter("user.txt"));
			// bw = new BufferedWriter(new FileWriter(file));
			// 为了保证数据是追加写入，必须加true
			bw = new BufferedWriter(new FileWriter(file, true));
			bw.write(user.getUsername() + "=" + user.getPassword());
			bw.newLine();
			bw.flush();
		} catch (IOException e) {
			System.out.println("用户注册失败");
			// e.printStackTrace();
		} finally {
			if (bw != null) {
				try {
					bw.close();
				} catch (IOException e) {
					System.out.println("用户注册释放资源失败");
					// e.printStackTrace();
				}
			}
		}
	}
}

```

注意：文件名的命名

注意标签flag的返回，注意静态语句块的使用

## 操作基本数据类型的流

### DataInputStream

```
DataInputStream(InputStream in)
```

### DataOutputStream

```
DataOutputStream(OutputStream out)
```

## 内存操作流

### 字节数组

- ByteArrayInputStream

底层是一个byte数组，不用关闭流，即使关闭流，在调用函数也不会产生io异常

```java
   //close方法
   public void close() throws IOException {
    }
```

- ByteArrayOutputStream

底层是一个byte数组，不用关闭流，即使关闭流，在调用函数也不会产生io异常

示例代码：

```java
	public static void main(String[] args) throws IOException {
		// 写数据
		// ByteArrayOutputStream()
		ByteArrayOutputStream baos = new ByteArrayOutputStream();

		// 写数据
		for (int x = 0; x < 10; x++) {
			baos.write(("hello" + x).getBytes());
		}

		// 释放资源
		// 通过查看源码我们知道这里什么都没做，所以根本需要close()
		 baos.close();

		// public byte[] toByteArray()
		byte[] bys = baos.toByteArray();

		// 读数据
		// ByteArrayInputStream(byte[] buf)
		ByteArrayInputStream bais = new ByteArrayInputStream(bys);

		int by = 0;
		while ((by = bais.read()) != -1) {
			System.out.print((char) by);
		}

		// bais.close();
	}
```

操作流程：

1. 创建ByteArrayOutputStream输出流
2. 写数据，注意getBytes方法
3. 通过ByteArrayOutputStream的toByteArray方法把输出内容转换从字节数组（因为ByteArrayInputStream的构造方法需要一个字节数组）
4. 创建ByteArrayInputStream输入流
5. 读取数据，和字节流的读取数据一样，注意要把byte转换成char

> 当有中文字符时一个字节的byte无法表示，所以要用CharArrayReader，用法一样

### 字符数组

- CharArrayReader


- CharArrayWriter

### 字符串

- StringReader


- StringWriter

## 打印流

打印流只有输出流，只能写数据；可以操作任意类型的数据；开启自动刷新能够自动刷新；可以直接操作文本文件

> 可以操作文本文件的类
>
> FileInputStream--------------FileOutputStream
>
> FileReader----------------------FileWriter
>
> PrintStream--------------------PrintWriter
>
> buffer类不能直接操作文件

流的分类：

1. 基本流：可以直接读取文件
2. 高级流：在基本流提供其他功能

**查看API，流的构造方法如果同时又File类型和String类型的参数，一般来说就是可以直接操作文件的**

### 字节流打印流PrintStream

### 字符打印流PrintWriter

作为writer的子类使用时一定要关闭流，否则数据不会写进文件

可以打印任意类型的方法是print而不是write方法：

```java
		 pw.print(true);
		 pw.print(100);
		 pw.print("hello");
```

启动自动刷新：

```java
		PrintWriter pw = new PrintWriter(new FileWriter("pw2.txt"), true);
```

启动自动刷新时：调用 println、printf 或 format 方法将刷新输出缓冲区，即不用close就可以写入文件

> println()方法其实等价于于：bw.write()	bw.newLine()		bw.flush();

#### 示例代码

```java
/*
 * 需求：DataStreamDemo.java复制到Copy.java中
 * 数据源：
 * 		DataStreamDemo.java -- 读取数据 -- FileReader -- BufferedReader
 * 目的地：
 * 		Copy.java -- 写出数据 -- FileWriter -- BufferedWriter -- PrintWriter
 */
public class CopyFileDemo {
	public static void main(String[] args) throws IOException {
		// 以前的版本
		// 封装数据源
		BufferedReader br = new BufferedReader(new FileReader("DataStreamDemo.java"));
		// 封装目的地
		BufferedWriter bw = new BufferedWriter(new FileWriter("Copy.java"));

		String line = null;
		while ((line = br.readLine()) != null) {
			//这三句通常一起写
			bw.write(line);
			bw.newLine();
			bw.flush();
		}

		bw.close();
		br.close();

		// 打印流的改进版
		// 封装数据源
		BufferedReader br = new BufferedReader(new FileReader("DataStreamDemo.java"));
		// 封装目的地
		PrintWriter pw = new PrintWriter(new FileWriter("Copy.java"), true);

		String line = null;
		while ((line = br.readLine()) != null) {
			pw.println(line);
		}

		pw.close();
		br.close();
	}
}

```

注意：打印字符流是调用的println方法

PrintWriter的底层也封装了BufferedWriter，所以此类也是高效的

## 标准输入输出流

> public static final InputStream in “标准”输入流
>
> public static final PrintStream out “标准”输出流。

### 输出流

输出语句的本质：

```java
		System.out.println("helloworld");

		// 获取标准输出流对象
		PrintStream ps = System.out;
		ps.println("helloworld");
```

```java
		//转换流的思想		
		BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(
				System.out));

		bw.write("hello");
		bw.newLine();
		// bw.flush();
		bw.write("world");
		bw.newLine();
		// bw.flush();
		bw.write("java");
		bw.newLine();
		bw.flush();
		
		bw.close();
```

### 输入流

键盘录入数据的几种方式：

A:main方法的args接收参数。

​	java HelloWorld hello world java

B:Scanner(JDK5以后的)

​	Scanner sc = new Scanner(System.in);

​	String s = sc.nextLine();

​	int x = sc.nextInt()

C:通过字符缓冲流包装标准输入流实现

​	BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

#### 第三种方式的示例代码

```java
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

		System.out.println("请输入一个字符串：");
		String line = br.readLine();
		System.out.println("你输入的字符串是：" + line);

		System.out.println("请输入一个整数：");
		// int i = Integer.parseInt(br.readLine());
		line = br.readLine();
		int i = Integer.parseInt(line);
		System.out.println("你输入的整数是：" + i);
```

注意：integer类的paseInt方法，还有字节流和字符流转换的思想

## 随机访问流

RandomAccessFile类不属于流，是Object类的子类。但它融合了InputStream和OutputStream的功能。支持对随机访问文件的读取和写入

### 构造方法

```java
public RandomAccessFile(String name,String mode)：
第一个参数是文件路径，第二个参数是操作文件的模式。
模式有四种，我们最常用的一种叫"rw",这种方式表示我既可以写数据，也可以读取数据 
```

### 示例代码

```java
public class RandomAccessFileDemo {
	public static void main(String[] args) throws IOException {
		// write();
		read();
	}

	private static void read() throws IOException {
		// 创建随机访问流对象
		RandomAccessFile raf = new RandomAccessFile("raf.txt", "rw");

		int i = raf.readInt();
		System.out.println(i);
		// 该文件指针可以通过 getFilePointer方法读取，并通过 seek 方法设置。
		System.out.println("当前文件的指针位置是：" + raf.getFilePointer());

		char ch = raf.readChar();
		System.out.println(ch);
		System.out.println("当前文件的指针位置是：" + raf.getFilePointer());

		String s = raf.readUTF();
		System.out.println(s);
		System.out.println("当前文件的指针位置是：" + raf.getFilePointer());

		// 我不想重头开始了，我就要读取a，怎么办呢?
		raf.seek(4);
		ch = raf.readChar();
		System.out.println(ch);
	}

	private static void write() throws IOException {
		// 创建随机访问流对象
		RandomAccessFile raf = new RandomAccessFile("raf.txt", "rw");

		// 怎么玩呢?
		raf.writeInt(100);
		raf.writeChar('a');
		raf.writeUTF("中国");

		raf.close();
	}
}
```

## 合并流SequenceInputStream

SequenceInputStream类可以将多个输入流串流在一起，合并为一个输入流，因此，该流也被称为合并流

```java
		// SequenceInputStream(InputStream s1, InputStream s2)
		// 需求：把ByteArrayStreamDemo.java和DataStreamDemo.java的内容复制到Copy.java中
		InputStream s1 = new FileInputStream("ByteArrayStreamDemo.java");
		InputStream s2 = new FileInputStream("DataStreamDemo.java");
		SequenceInputStream sis = new SequenceInputStream(s1, s2);
		BufferedOutputStream bos = new BufferedOutputStream(
				new FileOutputStream("Copy.java"));

		// 如何写读写呢，其实很简单，你就按照以前怎么读写，现在还是怎么读写
		byte[] bys = new byte[1024];
		int len = 0;
		while ((len = sis.read(bys)) != -1) {
			bos.write(bys, 0, len);
		}

		bos.close();
		sis.close();
```

```java
		// 需求：把下面的三个文件的内容复制到Copy.java中
		// ByteArrayStreamDemo.java,CopyFileDemo.java,DataStreamDemo.java

		// SequenceInputStream(Enumeration e)
		// 通过简单的回顾我们知道了Enumeration是Vector中的一个方法的返回值类型。
		// Enumeration<E> elements()
				// 需求：把下面的三个文件的内容复制到Copy.java中
		// ByteArrayStreamDemo.java,CopyFileDemo.java,DataStreamDemo.java

		// SequenceInputStream(Enumeration e)
		// 通过简单的回顾我们知道了Enumeration是Vector中的一个方法的返回值类型。
		// Enumeration<E> elements()
		Vector<InputStream> v = new Vector<InputStream>();
		InputStream s1 = new FileInputStream("ByteArrayStreamDemo.java");
		InputStream s2 = new FileInputStream("CopyFileDemo.java");
		InputStream s3 = new FileInputStream("DataStreamDemo.java");
		v.add(s1);
		v.add(s2);
		v.add(s3);
		Enumeration<InputStream> en = v.elements();
		SequenceInputStream sis = new SequenceInputStream(en);
		BufferedOutputStream bos = new BufferedOutputStream(
				new FileOutputStream("Copy.java"));

		// 如何写读写呢，其实很简单，你就按照以前怎么读写，现在还是怎么读写
		byte[] bys = new byte[1024];
		int len = 0;
		while ((len = sis.read(bys)) != -1) {
			bos.write(bys, 0, len);
		}

		bos.close();
		sis.close();<InputStream> v = new Vector<InputStream>();
		InputStream s1 = new FileInputStream("ByteArrayStreamDemo.java");
		InputStream s2 = new FileInputStream("CopyFileDemo.java");
		InputStream s3 = new FileInputStream("DataStreamDemo.java");
		v.add(s1);
		v.add(s2);
		v.add(s3);
		Enumeration<InputStream> en = v.elements();
		SequenceInputStream sis = new SequenceInputStream(en);
		BufferedOutputStream bos = new BufferedOutputStream(
				new FileOutputStream("Copy.java"));

		// 如何写读写呢，其实很简单，你就按照以前怎么读写，现在还是怎么读写
		byte[] bys = new byte[1024];
		int len = 0;
		while ((len = sis.read(bys)) != -1) {
			bos.write(bys, 0, len);
		}

		bos.close();
		sis.close();
```

注意：Vector和Enumeration的使用

## 序列化流

Java序列化是指把Java对象转换为字节序列的过程，从而实现对象的持久化；而Java反序列化是指把字节序列恢复为Java对象的过程

```java
	public static void main(String[] args) throws IOException,
			ClassNotFoundException {
		// 由于我们要对对象进行序列化，所以我们先自定义一个类
		// 序列化数据其实就是把对象写到文本文件
		// write();

		read();
	}

	private static void read() throws IOException, ClassNotFoundException {
		// 创建反序列化对象
		ObjectInputStream ois = new ObjectInputStream(new FileInputStream(
				"oos.txt"));

		// 还原对象
		Object obj = ois.readObject();

		// 释放资源
		ois.close();

		// 输出对象
		System.out.println(obj);
	}

	private static void write() throws IOException {
		// 创建序列化流对象
		ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(
				"oos.txt"));

		// 创建对象
		Person p = new Person("林青霞", 27);

		// public final void writeObject(Object obj)
		oos.writeObject(p);

		// 释放资源
		oos.close();
	}
```

在使用序列化接口的时候，在javaBean的文件中我们必须实现序列化接口Serializable，而且要要创建一个静态的成员变量来防止当类改变造成无法读取对象的问题，ecplise可以自动生成

```java
private static final long serialVersionUID = -2071565876962058344L;
```

> NotSerializableException:未序列化异常
>
> 使用transient关键字声明不需要序列化的成员变量
>
>   序列化流：把对象按照流一样的方式存入文本文件或者在网络中传输。对象 -- 流数据(ObjectOutputStream)
>   反序列化流:把文本文件中的流对象数据或者网络中的流对象数据还原成对象。流数据 -- 对象(ObjectInputStream)

## Properties

继承自Hashtable所以底层是一个Map,是一个属性集合类，可以保存在流中，也可以从流中加载

> 该类在API中没有<>所以不能添加泛型

### 作为Map集合使用

```java
		Properties prop = new Properties();

		// 添加元素
		prop.put("it002", "hello");
		prop.put("it001", "world");
		prop.put("it003", "java");

		// System.out.println("prop:" + prop);

		// 遍历集合
		Set<Object> set = prop.keySet();
		for (Object key : set) {
			Object value = prop.get(key);
			System.out.println(key + "---" + value);
		}
```

注意集合的遍历得到key的集合再增强for循环

### 特殊功能

```java
  public Object setProperty(String key,String value)：添加元素
  public String getProperty(String key):获取元素
  public Set<String> stringPropertyNames():获取所有的键的集合
```

注意键的值是字符串一定要使用它的特殊功能，使用父类功能是Object类型，setProperty的本质：

```java
    public synchronized Object setProperty(String key, String value) {
        return put(key, value);
    }
```

就是调用父类的put方法，但是此时传入的参数是String类型，而不是父类的Object类型，这种封装思想可以方方便的使用特定类型的数据

### 和IO流的结合使用

>  public void load(Reader reader):把文件中的数据读取到集合中
>
>  public void store(Writer writer,String comments):把集合中的数据存储到文件

示例代码：

```java
	public static void main(String[] args) throws IOException {
		// myLoad();

		myStore();
	}

	private static void myStore() throws IOException {
		// 创建集合对象
		Properties prop = new Properties();

		prop.setProperty("林青霞", "27");
		prop.setProperty("武鑫", "30");
		prop.setProperty("刘晓曲", "18");
		
		//public void store(Writer writer,String comments):把集合中的数据存储到文件
		Writer w = new FileWriter("name.txt");
		prop.store(w, "helloworld");
		w.close();
	}

	private static void myLoad() throws IOException {
		Properties prop = new Properties();

		// public void load(Reader reader):把文件中的数据读取到集合中
		// 注意：这个文件的数据必须是键值对形式
		Reader r = new FileReader("prop.txt");
		prop.load(r);
		r.close();

		System.out.println("prop:" + prop);
	}
```

####练习题

```java
/*
 * 我有一个文本文件(user.txt)，我知道数据是键值对形式的，但是不知道内容是什么。
 * 请写一个程序判断是否有“lisi”这样的键存在，如果有就改变其实为”100”
 * 
 * 分析：
 * 		A:把文件中的数据加载到集合中
 * 		B:遍历集合，获取得到每一个键
 * 		C:判断键是否有为"lisi"的，如果有就修改其值为"100"
 * 		D:把集合中的数据重新存储到文件中
 */
public class PropertiesTest {
	public static void main(String[] args) throws IOException {
		// 把文件中的数据加载到集合中
		Properties prop = new Properties();
		Reader r = new FileReader("user.txt");
		prop.load(r);
		r.close();

		// 遍历集合，获取得到每一个键
		Set<String> set = prop.stringPropertyNames();
		for (String key : set) {
			// 判断键是否有为"lisi"的，如果有就修改其值为"100"
			if ("lisi".equals(key)) {
				prop.setProperty(key, "100");
				break;
			}
		}

		// 把集合中的数据重新存储到文件中
		Writer w = new FileWriter("user.txt");
		prop.store(w, null);
		w.close();
	}
}

```

注意每个流关闭的时间

#### 练习题二

```java
/*
 * 我有一个猜数字小游戏的程序，请写一个程序实现在测试类中只能用5次，超过5次提示：游戏试玩已结束，请付费。
 */
public class PropertiesTest2 {
	public static void main(String[] args) throws IOException {

		// 把数据加载到集合中
		Properties prop = new Properties();
		Reader r = new FileReader("count.txt");
		prop.load(r);
		r.close();

		// 我自己的程序，我当然知道里面的键是谁
		String value = prop.getProperty("count");
		int number = Integer.parseInt(value);

		if (number > 5) {
			System.out.println("游戏试玩已结束，请付费。");
			System.exit(0);
		} else {
			number++;
			prop.setProperty("count", String.valueOf(number));
			Writer w = new FileWriter("count.txt");
			prop.store(w, null);
			w.close();

			GuessNumber.start();
		}
	}
}
```

注意stirng.valueOf的写法和Integer.parseInt(value)的写法

## NIO

NIO其实就是新IO的意思，可以像访问内存一样访问文件

## JDK的IO改进

- Path:与平台无关的路径。


- Paths:包含了返回Path的静态方法。

> public static [Path](mk:@MSITStore:E:\JavaSE\API\JDK_API7.0.CHM::/java/nio/file/Path.html) get([URI](mk:@MSITStore:E:\JavaSE\API\JDK_API7.0.CHM::/java/net/URI.html) uri):根据给定的URI来确定文件路径。

- Files:操作文件的工具类。提供了大量的方法，简单了解如下方法

> public static long copy([Path](mk:@MSITStore:E:\JavaSE\API\JDK_API7.0.CHM::/java/nio/file/Path.html) source,[OutputStream](mk:@MSITStore:E:\JavaSE\API\JDK_API7.0.CHM::/java/io/OutputStream.html) out):复制文件

> public static [Path](mk:@MSITStore:E:\JavaSE\API\JDK_API7.0.CHM::/java/nio/file/Path.html) write([Path](mk:@MSITStore:E:\JavaSE\API\JDK_API7.0.CHM::/java/nio/file/Path.html) path, [Iterable](mk:@MSITStore:E:\JavaSE\API\JDK_API7.0.CHM::/java/lang/Iterable.html)<?extends [CharSequence](mk:@MSITStore:E:\JavaSE\API\JDK_API7.0.CHM::/java/lang/CharSequence.html)> lines,[Charset](mk:@MSITStore:E:\JavaSE\API\JDK_API7.0.CHM::/java/nio/charset/Charset.html) cs, [OpenOption](mk:@MSITStore:E:\JavaSE\API\JDK_API7.0.CHM::/java/nio/file/OpenOption.html)... options):

```java
		ArrayList<String> array = new ArrayList<String>();
		array.add("hello");
		array.add("world");
		array.add("java");
		Files.write(Paths.get("array.txt"), array, Charset.forName("GBK"));
```



