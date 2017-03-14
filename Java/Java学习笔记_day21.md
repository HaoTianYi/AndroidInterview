# Java学习笔记_day21

[TOC]

## 编码表

由现实世界的字符和对应的字符构成

### 常见编码表

|       编码表       |      位数      |
| :-------------: | :----------: |
|      ASCII      | 一个字节的7位最多128 |
| ISO-8859-1拉丁编码表 | 一个字节8位最多256  |
|    GB2312简体     |              |
|   GBK简体中文的升级版   |              |
|    GB18030码表    |   GBK的取代版本   |
|   BIG-5繁体字编码表   |    俗称大五码     |
|  Unicode国际标准码   |     2个字节     |
|      UTF-8      |   最多用3个字节    |

> UTF-8能用一个就用ASCII，两个就用Unicode，最后用三个字节

系统默认字符集是GBK

string和bytes可以使用自定义的字符集来相互转换：

```java
		String s = "你好";
		// String -- byte[]		
		byte[] bys1 = s.getBytes("GBK");// [-60, -29, -70, -61]
		
		System.out.println(Arrays.toString(bys));

		// byte[] -- String
		String ss = new String(bys); // 你好
		String ss1 = new String(bys, "GBK"); // 你好		
```

## 字符流OutputStreamWriter

### 构造方法

```java
OutputStreamWriter(OutputStream out):根据默认编码把字节流的数据转换为字符流
OutputStreamWriter(OutputStream out,String charsetName):根据指定编码把字节流数据转换为字符流
```

### 成员方法

```java
  public void write(int c):写一个字符
  public void write(char[] cbuf):写一个字符数组
  public void write(char[] cbuf,int off,int len):写一个字符数组的一部分
  public void write(String str):写一个字符串
  public void write(String str,int off,int len):写一个字符串的一部分
```

> 一个字符两个字节
>
> 字符流写数据一定要调用flush方法，否则不会写进文件，因为此时字符在缓冲中，文件存储的单位是字符，但是你写的是字节

### close()和flush()的区别

close()关闭流对象，但是先刷新一次缓冲区。关闭之后，流对象不可以继续再使用了

flush()仅仅刷新缓冲区,刷新之后，流对象还可以继续使用

### 实现子类

OutputStreamWriter = FileOutputStream + 编码表(GBK)

FileWriter = FileOutputStream + 编码表(GBK)

## InputStreamReader

### 构造方法

```java
InputStreamReader(InputStream is):用默认的编码读取数据
InputStreamReader(InputStream is,String charsetName):用指定的编码读取数据
```

### 成员方法

```java
int read():一次读取一个字符

int read(char[] chs):一次读取一个字符数组
```

读取数据示例代码：

```java
		// 读取数据
		// 一次读取一个字符
		int ch = 0;
		while ((ch = isr.read()) != -1) {
			System.out.print((char) ch);
		}
		// 一次读取一个字符数组
		char[] chs = new char[1024];
		int len = 0;
		while ((len = isr.read(chs)) != -1) {
			System.out.print(new String(chs, 0, len));
		}
```

### 实现子类

InputStreamReader = FileInputStream + 编码表(GBK)

FileReader = FileInputStream + 编码表(GBK)

## 字符缓冲流
###BufferedReader

```java
/*
 * BufferedReader
 * 从字符输入流中读取文本，缓冲各个字符，从而实现字符、数组和行的高效读取。 
 * 可以指定缓冲区的大小，或者可使用默认的大小。大多数情况下，默认值就足够大了。 
 * 
 * BufferedReader(Reader in)
 */
public class BufferedReaderDemo {
	public static void main(String[] args) throws IOException {
		// 创建字符缓冲输入流对象
		BufferedReader br = new BufferedReader(new FileReader("bw.txt"));

		// 方式1
		int ch = 0;
		while ((ch = br.read()) != -1) {
			System.out.print((char) ch);
		}

		// 方式2
		char[] chs = new char[1024];
		int len = 0;
		while ((len = br.read(chs)) != -1) {
			System.out.print(new String(chs, 0, len));
		}

		// 释放资源
		br.close();
	}
}
```

### BufferedWriter

将文本写入字符输出流，缓冲各个字符，从而提供单个字符、数组和字符串的高效写入。


```java
/*
 * 字符缓冲流的特殊方法：
 * BufferedWriter:
 * 		public void newLine():根据系统来决定换行符
 * BufferedReader:
 * 		public String readLine()：一次读取一行数据
 * 		包含该行内容的字符串，不包含任何行终止符，如果已到达流末尾，则返回 null
 */
public class BufferedDemo {
	public static void main(String[] args) throws IOException {
		// write();
		read();
	}

	private static void read() throws IOException {
		// 创建字符缓冲输入流对象
		BufferedReader br = new BufferedReader(new FileReader("bw2.txt"));

		String line = null;
		while ((line = br.readLine()) != null) {
			System.out.println(line);
		}
		
		//释放资源
		br.close();
	}

	private static void write() throws IOException {
		// 创建字符缓冲输出流对象
		BufferedWriter bw = new BufferedWriter(new FileWriter("bw2.txt"));
		for (int x = 0; x < 10; x++) {
			bw.write("hello" + x);
			// bw.write("\r\n");
			bw.newLine();
			bw.flush();
		}
		bw.close();
	}
}
```
注意：bw.write("hello" + x);不包括换行符号，所以要添加newLine方法，作为换行符号

> newLine是写换行符号

![IO流小结图解](http://oaxelf1sk.bkt.clouddn.com/IO流小结图解.png)

### 字符流复制的练习

```java
/*
 * 复制文本文件
 * 
 * 分析：
 * 		复制数据，如果我们知道用记事本打开并能够读懂，就用字符流，否则用字节流。
 * 		通过该原理，我们知道我们应该采用字符流更方便一些。
 * 		而字符流有5种方式，所以做这个题目我们有5种方式。推荐掌握第5种。
 * 数据源：
 * 		c:\\a.txt -- FileReader -- BufferdReader
 * 目的地：
 * 		d:\\b.txt -- FileWriter -- BufferedWriter
 */
public class CopyFileDemo {
	public static void main(String[] args) throws IOException {
		String srcString = "c:\\a.txt";
		String destString = "d:\\b.txt";
		// method1(srcString, destString);
		// method2(srcString, destString);
		// method3(srcString, destString);
		// method4(srcString, destString);
		method5(srcString, destString);
	}

	// 字符缓冲流一次读写一个字符串
	private static void method5(String srcString, String destString)
			throws IOException {
		BufferedReader br = new BufferedReader(new FileReader(srcString));
		BufferedWriter bw = new BufferedWriter(new FileWriter(destString));

		String line = null;
		while ((line = br.readLine()) != null) {
			bw.write(line);
			bw.newLine();
			bw.flush();
		}

		bw.close();
		br.close();
	}

	// 字符缓冲流一次读写一个字符数组
	private static void method4(String srcString, String destString)
			throws IOException {
		BufferedReader br = new BufferedReader(new FileReader(srcString));
		BufferedWriter bw = new BufferedWriter(new FileWriter(destString));

		char[] chs = new char[1024];
		int len = 0;
		while ((len = br.read(chs)) != -1) {
			bw.write(chs, 0, len);
		}

		bw.close();
		br.close();
	}

	// 字符缓冲流一次读写一个字符
	private static void method3(String srcString, String destString)
			throws IOException {
		BufferedReader br = new BufferedReader(new FileReader(srcString));
		BufferedWriter bw = new BufferedWriter(new FileWriter(destString));

		int ch = 0;
		while ((ch = br.read()) != -1) {
			bw.write(ch);
		}

		bw.close();
		br.close();
	}

	// 基本字符流一次读写一个字符数组
	private static void method2(String srcString, String destString)
			throws IOException {
		FileReader fr = new FileReader(srcString);
		FileWriter fw = new FileWriter(destString);

		char[] chs = new char[1024];
		int len = 0;
		while ((len = fr.read(chs)) != -1) {
			fw.write(chs, 0, len);
		}

		fw.close();
		fr.close();
	}

	// 基本字符流一次读写一个字符
	private static void method1(String srcString, String destString)
			throws IOException {
		FileReader fr = new FileReader(srcString);
		FileWriter fw = new FileWriter(destString);

		int ch = 0;
		while ((ch = fr.read()) != -1) {
			fw.write(ch);
		}

		fw.close();
		fr.close();
	}
}
```

我有一个文本文件中存储了几个名称，请大家写一个程序实现随机获取一个人的名字

```java
public class GetName {
	public static void main(String[] args) throws IOException {
		// 把文本文件中的数据存储到集合中
		BufferedReader br = new BufferedReader(new FileReader("b.txt"));
		ArrayList<String> array = new ArrayList<String>();
		String line = null;
		while ((line = br.readLine()) != null) {
			array.add(line);
		}
		br.close();

		// 随机产生一个索引
		Random r = new Random();
		int index = r.nextInt(array.size());

		// 根据该索引获取一个值
		String name = array.get(index);
		System.out.println("该幸运者是：" + name);
	}
}
```

注意：random类和math中的随机数的区别

复制指定目录下的指定文件，并修改后缀名

```java
/*
 * 需求：复制指定目录下的指定文件，并修改后缀名。
 * 指定的文件是：.java文件。
 * 指定的后缀名是：.jad
 * 指定的目录是：jad
 * 
 * 数据源：e:\\java\\A.java
 * 目的地：e:\\jad\\A.jad
 * 
 * 分析：
 * 		A:封装目录
 * 		B:获取该目录下的java文件的File数组
 * 		C:遍历该File数组，得到每一个File对象
 * 		D:把该File进行复制
 * 		E:在目的地目录下改名
 */
public class CopyFolderDemo {
	public static void main(String[] args) throws IOException {
		// 封装目录
		File srcFolder = new File("e:\\java");
		// 封装目的地
		File destFolder = new File("e:\\jad");
		// 如果目的地目录不存在，就创建
		if (!destFolder.exists()) {
			destFolder.mkdir();
		}

		// 获取该目录下的java文件的File数组
		File[] fileArray = srcFolder.listFiles(new FilenameFilter() {
			@Override
			public boolean accept(File dir, String name) {
				return new File(dir, name).isFile() && name.endsWith(".java");
			}
		});

		// 遍历该File数组，得到每一个File对象
		for (File file : fileArray) {
			// System.out.println(file);
			// 数据源：e:\java\DataTypeDemo.java
			// 目的地：e:\\jad\DataTypeDemo.java
			String name = file.getName();
			File newFile = new File(destFolder, name);
			copyFile(file, newFile);
		}

		// 在目的地目录下改名
		File[] destFileArray = destFolder.listFiles();
		for (File destFile : destFileArray) {
			// System.out.println(destFile);
			// e:\jad\DataTypeDemo.java
			// e:\\jad\\DataTypeDemo.jad
			String name =destFile.getName(); //DataTypeDemo.java
			String newName = name.replace(".java", ".jad");//DataTypeDemo.jad
			
			File newFile = new File(destFolder,newName);
			destFile.renameTo(newFile);
		}
	}

	private static void copyFile(File file, File newFile) throws IOException {
		BufferedInputStream bis = new BufferedInputStream(new FileInputStream(
				file));
		BufferedOutputStream bos = new BufferedOutputStream(
				new FileOutputStream(newFile));

		byte[] bys = new byte[1024];
		int len = 0;
		while ((len = bis.read(bys)) != -1) {
			bos.write(bys, 0, len);
		}

		bos.close();
		bis.close();
	}
}

```

```java
/*
 * 需求：复制多极文件夹
 * 
 * 数据源：E:\JavaSE\day21\code\demos
 * 目的地：E:\\
 * 
 * 分析：
 * 		A:封装数据源File
 * 		B:封装目的地File
 * 		C:判断该File是文件夹还是文件
 * 			a:是文件夹
 * 				就在目的地目录下创建该文件夹
 * 				获取该File对象下的所有文件或者文件夹File对象
 * 				遍历得到每一个File对象
 * 				回到C
 * 			b:是文件
 * 				就复制(字节流)
 */
public class CopyFoldersDemo {
	public static void main(String[] args) throws IOException {
		// 封装数据源File
		File srcFile = new File("E:\\JavaSE\\day21\\code\\demos");
		// 封装目的地File
		File destFile = new File("E:\\");

		// 复制文件夹的功能
		copyFolder(srcFile, destFile);
	}

	private static void copyFolder(File srcFile, File destFile)
			throws IOException {
		// 判断该File是文件夹还是文件
		if (srcFile.isDirectory()) {
			// 文件夹
			File newFolder = new File(destFile, srcFile.getName());
			newFolder.mkdir();

			// 获取该File对象下的所有文件或者文件夹File对象
			File[] fileArray = srcFile.listFiles();
			for (File file : fileArray) {
				copyFolder(file, newFolder);
			}
		} else {
			// 文件
			File newFile = new File(destFile, srcFile.getName());
			copyFile(srcFile, newFile);
		}
	}

	private static void copyFile(File srcFile, File newFile) throws IOException {
		BufferedInputStream bis = new BufferedInputStream(new FileInputStream(
				srcFile));
		BufferedOutputStream bos = new BufferedOutputStream(
				new FileOutputStream(newFile));

		byte[] bys = new byte[1024];
		int len = 0;
		while ((len = bis.read(bys)) != -1) {
			bos.write(bys, 0, len);
		}

		bos.close();
		bis.close();
	}
}
```

```java
/*
 * 键盘录入5个学生信息(姓名,语文成绩,数学成绩,英语成绩),按照总分从高到低存入文本文件
 * 
 * 分析：
 * 		A:创建学生类
 * 		B:创建集合对象
 * 			TreeSet<Student>
 * 		C:键盘录入学生信息存储到集合
 * 		D:遍历集合，把数据写到文本文件
 */
public class StudentDemo {
	public static void main(String[] args) throws IOException {
		// 创建集合对象
		TreeSet<Student> ts = new TreeSet<Student>(new Comparator<Student>() {
			@Override
			public int compare(Student s1, Student s2) {
				int num = s2.getSum() - s1.getSum();
				int num2 = num == 0 ? s1.getChinese() - s2.getChinese() : num;
				int num3 = num2 == 0 ? s1.getMath() - s2.getMath() : num2;
				int num4 = num3 == 0 ? s1.getEnglish() - s2.getEnglish() : num3;
				int num5 = num4 == 0 ? s1.getName().compareTo(s2.getName())
						: num4;
				return num5;
			}
		});

		// 键盘录入学生信息存储到集合
		for (int x = 1; x <= 5; x++) {
			Scanner sc = new Scanner(System.in);
			System.out.println("请录入第" + x + "个的学习信息");
			System.out.println("姓名：");
			String name = sc.nextLine();
			System.out.println("语文成绩：");
			int chinese = sc.nextInt();
			System.out.println("数学成绩：");
			int math = sc.nextInt();
			System.out.println("英语成绩：");
			int english = sc.nextInt();

			// 创建学生对象
			Student s = new Student();
			s.setName(name);
			s.setChinese(chinese);
			s.setMath(math);
			s.setEnglish(english);

			// 把学生信息添加到集合
			ts.add(s);
		}

		// 遍历集合，把数据写到文本文件
		BufferedWriter bw = new BufferedWriter(new FileWriter("students.txt"));
		bw.write("学生信息如下：");
		bw.newLine();
		bw.flush();
		bw.write("姓名,语文成绩,数学成绩,英语成绩");
		bw.newLine();
		bw.flush();
		for (Student s : ts) {
			StringBuilder sb = new StringBuilder();
			sb.append(s.getName()).append(",").append(s.getChinese())
					.append(",").append(s.getMath()).append(",")
					.append(s.getEnglish());
			bw.write(sb.toString());
			bw.newLine();
			bw.flush();
		}
		// 释放资源
		bw.close();
		System.out.println("学习信息存储完毕");
	}
}
```

注意：上面代码的stringBuilder的用法和TreeSet的用法

```java
/*
 * 已知s.txt文件中有这样的一个字符串：“hcexfgijkamdnoqrzstuvwybpl”
 * 请编写程序读取数据内容，把数据排序后写入ss.txt中。
 * 
 * 分析：
 * 		A:把s.txt这个文件给做出来
 * 		B:读取该文件的内容，存储到一个字符串中
 * 		C:把字符串转换为字符数组
 * 		D:对字符数组进行排序
 * 		E:把排序后的字符数组转换为字符串
 * 		F:把字符串再次写入ss.txt中
 */
public class StringDemo {
	public static void main(String[] args) throws IOException {
		// 读取该文件的内容，存储到一个字符串中
		BufferedReader br = new BufferedReader(new FileReader("s.txt"));
		String line = br.readLine();
		br.close();

		// 把字符串转换为字符数组
		char[] chs = line.toCharArray();

		// 对字符数组进行排序
		Arrays.sort(chs);

		// 把排序后的字符数组转换为字符串
		String s = new String(chs);

		// 把字符串再次写入ss.txt中
		BufferedWriter bw = new BufferedWriter(new FileWriter("ss.txt"));
		bw.write(s);
		bw.newLine();
		bw.flush();

		bw.close();
	}
}
```

注意：上面代码的arrays类的用法

```java
/*
 * 用Reader模拟BufferedReader的readLine()功能
 * 
 * readLine():一次读取一行，根据换行符判断是否结束，只返回内容，不返回换行符
 */
public class MyBufferedReader {
	private Reader r;

	public MyBufferedReader(Reader r) {
		this.r = r;
	}

	/*
	 * 思考：写一个方法，返回值是一个字符串。
	 */
	public String readLine() throws IOException {
		/*
		 * 我要返回一个字符串，我该怎么办呢? 我们必须去看看r对象能够读取什么东西呢? 两个读取方法，一次读取一个字符或者一次读取一个字符数组
		 * 那么，我们要返回一个字符串，用哪个方法比较好呢? 我们很容易想到字符数组比较好，但是问题来了，就是这个数组的长度是多长呢?
		 * 根本就没有办法定义数组的长度，你定义多长都不合适。 所以，只能选择一次读取一个字符。
		 * 但是呢，这种方式的时候，我们再读取下一个字符的时候，上一个字符就丢失了 所以，我们又应该定义一个临时存储空间把读取过的字符给存储起来。
		 * 这个用谁比较和是呢?数组，集合，字符串缓冲区三个可供选择。
		 * 数组不知道大小，集合麻烦，经过简单的分析，最终选择使用字符串缓冲区对象。并且使用的是StringBuilder
		 */
		StringBuilder sb = new StringBuilder();

		// 做这个读取最麻烦的是判断结束，但是在结束之前应该是一直读取，直到-1

		int ch = 0;
		while ((ch = r.read()) != -1) { //104,101,108,108,111
			if (ch == '\r') {
				continue;
			}

			if (ch == '\n') {
				return sb.toString(); //hello
			} else {
				sb.append((char)ch); //hello
			}
		}

		// 为了防止数据丢失，就是最后一行没有按下换行直接结束
		if (sb.length() > 0) {
			return sb.toString();
		}

		return null;
	}

	/*
	 * 先写一个关闭方法
	 */
	public void close() throws IOException {
		this.r.close();
	}
}
```

注意:在stringBUilder调用append方法时一定要转换成char否则打印出的是数字

注意这种利用已知类来实现自定义功能的封装方法，有私有变量，构造方法，还有成员方法

```java
//模拟LineNumberReader的获取line行号的功能
public class MyLineNumberReader {
	private Reader r;
	private int lineNumber = 0;

	public MyLineNumberReader(Reader r) {
		this.r = r;
	}

	public int getLineNumber() {
		// lineNumber++;
		return lineNumber;
	}

	public void setLineNumber(int lineNumber) {
		this.lineNumber = lineNumber;
	}

	public String readLine() throws IOException {
		lineNumber++;

		StringBuilder sb = new StringBuilder();

		int ch = 0;
		while ((ch = r.read()) != -1) {
			if (ch == '\r') {
				continue;
			}

			if (ch == '\n') {
				return sb.toString();
			} else {
				sb.append((char) ch);
			}
		}

		if (sb.length() > 0) {
			return sb.toString();
		}

		return null;
	}

	public void close() throws IOException {
		this.r.close();
	}
}
```
