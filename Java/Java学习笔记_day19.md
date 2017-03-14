# Java学习笔记_day19

[TOC]

## IO





## 程序异常Throwable

![ice_screenshot_20160808-144005](http://oaxelf1sk.bkt.clouddn.com/ice_screenshot_20160808-144005.png)

### 分类

1. error严重问题，一般不处理，如内存溢出
2. exception问题

### Exception

包括RuntimeException（运行期间问题）及其子类和非RuntimeException子类（编译期间问题）

RuntimeException（运行期问题）：需要修改代码

非RuntimeException（编译器异常）：必须进行处理

### 自己如何处理异常

- try...catch...finally
- throws 抛出

  try...catch...finally的处理格式：
  		try {

  			可能出现问题的代码;
  		}catch(异常名 变量) {
  			针对问题的处理;
  		}finally {
  			释放资源;
  		}

  变形格式：
  		try {
  			可能出现问题的代码;
  		}catch(异常名 变量) {
  			针对问题的处理;
  		}

  **注意**：
  		A:try里面的代码越少越好
  		B:catch里面必须有内容，哪怕是给出一个简单的提示

在catch中捕获异常时注意：由于异常也有继承关系所以假如有多个异常，那么要注意父异常写在最下面。

#### JDK7新特性

  JDK7出现了一个新的异常处理方案：
  		try{

  		}catch(异常名1 | 异常名2 | ...  变量 ) {
  			...
  		}

  		注意：这个方法虽然简洁，但是也不够好。
  			A:处理方式是一致的。(实际开发中，好多时候可能就是针对同类型的问题，给出同一个处理)
 			B:多个异常间必须是平级关系。

### 编译时期和运行时期的异常

编译期异常：Java程序必须显示处理，否则程序就会发生错误，无法通过编译

运行期异常：无需显示处理，也可以和编译时异常一样处理

### 异常对象

在try里面发现问题后，jvm会帮我们生成一个异常对象，然后把这个对象抛出，和catch里面的类进行匹配，如果该对象是某个类型的，就会执行该catch里面的处理信息。

```java
public static void main(String[] args) {
		String s = "2014-11-20";
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
		try {
			Date d = sdf.parse(s); // 创建了一个ParseException对象，然后抛出去，和catch里面进行匹配
			System.out.println(d);
		} catch (ParseException e) { // ParseException e = new ParseException();
			// ParseException
			// e.printStackTrace();

			// getMessage()
			// System.out.println(e.getMessage());
			// Unparseable date: "2014-11-20"

			// toString()
			// System.out.println(e.toString());
			// java.text.ParseException: Unparseable date: "2014-11-20"
			
			e.printStackTrace();
			//跳转到某个指定的页面(index.html)
		}
		
		System.out.println("over");
	}
```

#### 异常对象的方法

- public String getMessage():异常的消息字符串
- public String toString():返回异常的简单信息描述

> 组成规则：此对象的类的 name(全路径名)": "（冒号和一个空格）调用此对象 getLocalizedMessage()方法的结果 (默认返回的是getMessage()的内容)

- printStackTrace() 获取异常类名和异常信息，以及异常出现在程序中的位置。返回值void。把信息输出在控制台，和jvm的默认处理不同，在异常后面的语句它也可以执行，如打印over

#### 异常抛出的另两种格式

- 早方法声明上throws异常，尽量不要在main方法上抛出异常，throws后面可以1和多个异常，逗号隔开
- 用throw抛出异常对象，throw new ArithmeticException();

  ####throws和throw的区别

  throws
  	用在方法声明后面，跟的是异常类名
  	可以跟多个异常类名，用逗号隔开
  	表示抛出异常，由该方法的调用者来处理
  	throws表示出现异常的一种可能性，并不一定会发生这些异常
  throw
  	用在方法体内，跟的是异常对象名
  	只能抛出一个异常对象名
  	表示抛出异常，由方法体内的语句处理
  	throw则是抛出了异常，执行throw则一定抛出了某种异常
  **如果该功能内部可以将问题处理,用try,如果处理不了,交由调用者处理,这是用throws**

> 区别:后续程序需要继续运行就try，后续程序不需要继续运行就throws

> try...catch...finally...被finally控制的语句体一定会执行（除了jvm退出，System.exit(0))

### 练习题

- final,finally和finalize的区别
- 如果catch里面有return语句，请问finally里面的代码还会执行吗?
- try...catch...finally的格式变形

```java
/*
 * 面试题：
 * 1:final,finally和finalize的区别
 * final：最终的意思，可以修饰类，成员变量，成员方法
 * 		修饰类，类不能被继承
 * 		修饰变量，变量是常量
 * 		修饰方法，方法不能被重写
 * finally：是异常处理的一部分，用于释放资源。
 * 		一般来说，代码肯定会执行，特殊情况：在执行到finally之前jvm退出了
 * finalize：是Object类的一个方法，用于垃圾回收
 * 
 * 2:如果catch里面有return语句，请问finally里面的代码还会执行吗?
 *   如果会，请问是在return前，还是return后。
 * 	   会。前。
 * 
 * 	 准确的说，应该是在中间。
 * 
 * 3:try...catch...finally的格式变形
 * 		A:try...catch...finally
 * 		B:try...catch
 * 		C:try...catch...catch...
 * 		D:try...catch...catch...finally
 * 		E:try...finally
 * 			这种做法的目前是为了释放资源。
 */
public class FinallyDemo2 {
	public static void main(String[] args) {
		System.out.println(getInt());
	}

	public static int getInt() {
		int a = 10;
		try {
			System.out.println(a / 0);
			a = 20;
		} catch (ArithmeticException e) {
			a = 30;
			return a;
			/*
			 * return a在程序执行到这一步的时候，这里不是return a而是return 30;这个返回路径就形成了。
			 * 但是呢，它发现后面还有finally，所以继续执行finally的内容，a=40
			 * 再次回到以前的返回路径，继续走return 30;
			 */
		} finally {
			a = 40;
			//return a;//如果这样结果就是40了。
		}
		 return a; 		//结果是
	}
}
```

### 自定义异常

必须继承自Exception或者RuntimeException

> 如果是继承自RuntimeException那么在throw  new  异常时在方法上就不用再throws抛出，但是假如自定义的异常继承自Exception，也就是编译时期的异常那么就一定要早方法上抛出异常

我们在抛出异常时自定义了一个字符串：throw new MyException("分数必须在0-100之间");但是在抛出没有反应，此时只要在带参构造方法中添加super(message)，就可以反应在控制台，如下：

```java
cn.itcast_08.MyException: 分数必须在0-100之间
	at cn.itcast_08.Teacher.check(Teacher.java:6)
	at cn.itcast_08.StudentDemo.main(StudentDemo.java:16)
```

自定义异常继承自Exception就是编译时期异常，继承自RuntimeException就是运行时期异常

自定义异常只要重写两个构造方法就行：

```java
	public MyException() {
		
	}
	public MyException(String string) {
		super(string);		//一定要super可以反应字符串
	}
```

### 注意

子类和父类都要抛出异常，而且子类重写父类的异常方法，那么子类抛出的异常一定是父类异常的子类或者就是父类异常。

如果父类抛出了多个异常,子类重写父类时,只能抛出相同的异常或者是他的子集,子类不能抛出父类没有的异常

如果被重写的方法没有异常抛出,那么子类的方法绝对不可以抛出异常,如果子类方法内有异常发生,那么子类只能try,不能throws

## File类

实现了Comparable接口（自然排序），文件和目录路径名的抽象表示形式，未必是真实存在的。

### 构造方法

File(String pathname)：根据一个路径得到File对象

File(String parent, String child):根据一个目录和一个子文件/目录得到File对象

File(File parent, String child):根据一个父File对象和一个子文件/目录得到File对象

注意此时盘符路径的斜杠问题（两个斜杠）：

```java
		// File(String pathname)：根据一个路径得到File对象
		// 把e:\\demo\\a.txt封装成一个File对象
		File file = new File("E:\\demo\\a.txt");

// File(String parent, String child):根据一个目录和一个子文件/目录得到File对象
		File file2 = new File("E:\\demo", "a.txt");

// File(File parent, String child):根据一个父File对象和一个子文件/目录得到File对象
		File file3 = new File("e:\\demo");
		File file4 = new File(file3, "a.txt");
```

**盘符大小写无关**

### 成员方法

#### 创建功能

```java
 public boolean createNewFile():创建文件 如果存在这样的文件，就不创建了

 public boolean mkdir():创建文件夹 如果存在这样的文件夹，就不创建了

 public boolean mkdirs():创建多级文件夹,如果父文件夹不存在，会帮你创建出来
```

要想在某个目录下创建内容，该目录首先必须存在，就是先调用mkdir()或者mkdirs()方法而不是直接调用createNewFile()方法，否则会报异常

```java
Exception in thread "main" java.io.IOException: 系统找不到指定的路径。
```

> mkdir()不能创建多级目录
>
> 如果你创建文件或者文件夹忘了写盘符路径，那么，默认在项目根路径下。

#### 删除功能

```java
public boolean delete()
```

java删除的文件或者文件夹不回收到垃圾箱

> 如果你删除文件或者文件夹忘了写盘符路径，那么，默认在项目根路径下
>
> 删除文件夹那么这个问价夹必须是空的

#### 重命名功能

```java
public boolean renameTo(File dest)
```

> 如果路径名相同，就是改名，如果路径名不同，就是改名并剪切。

路径以盘符开始：绝对路径	c:\\a.txt，路径不以盘符开始：相对路径	a.txt

#### 判断功能

```java
  public boolean isDirectory():判断是否是目录
  public boolean isFile():判断是否是文件
  public boolean exists():判断是否存在
  public boolean canRead():判断是否可读
  public boolean canWrite():判断是否可写
  public boolean isHidden():判断是否隐藏
```

#### 获取功能

```java
  public String getAbsolutePath()：获取绝对路径
  public String getPath():获取相对路径
  public String getName():获取名称
  public long length():获取长度。字节数
  public long lastModified():获取最后一次的修改时间，毫秒值
    
  public String[] list():获取指定目录下的所有文件或者文件夹的名称数组
  public File[] listFiles():获取指定目录下的所有文件或者文件夹的File数组
```

### 练习题

判断E盘目录下是否有后缀名为.jpg的文件，如果有，就输出此文件名称

```java
/*
 * 判断E盘目录下是否有后缀名为.jpg的文件，如果有，就输出此文件名称
 * 
 * 分析：
 * 		A:封装e判断目录
 * 		B:获取该目录下所有文件或者文件夹的File数组
 * 		C:遍历该File数组，得到每一个File对象，然后判断
 * 		D:是否是文件
 * 			是：继续判断是否以.jpg结尾
 * 				是：就输出该文件名称
 * 				否：不搭理它
 * 			否：不搭理它
 */
public class FileDemo {
	public static void main(String[] args) {
		// 封装e判断目录
		File file = new File("e:\\");

		// 获取该目录下所有文件或者文件夹的File数组
		File[] fileArray = file.listFiles();

		// 遍历该File数组，得到每一个File对象，然后判断
		for (File f : fileArray) {
			// 是否是文件
			if (f.isFile()) {
				// 继续判断是否以.jpg结尾
				if (f.getName().endsWith(".jpg")) {
					// 就输出该文件名称
					System.out.println(f.getName());
				}
			}
		}
	}
}
```

另一种思路：

```java
/*
 * 判断E盘目录下是否有后缀名为.jpg的文件，如果有，就输出此文件名称
 * A:先获取所有的，然后遍历的时候，依次判断，如果满足条件就输出。
 * B:获取的时候就已经是满足条件的了，然后输出即可。
 * 
 * 要想实现这个效果，就必须学习一个接口：文件名称过滤器
 * public String[] list(FilenameFilter filter)
 * public File[] listFiles(FilenameFilter filter)
 */
public class FileDemo2 {
	public static void main(String[] args) {
		// 封装e判断目录
		File file = new File("e:\\");

		// 获取该目录下所有文件或者文件夹的String数组
		// public String[] list(FilenameFilter filter)
		String[] strArray = file.list(new FilenameFilter() {
			@Override
			public boolean accept(File dir, String name) {
				return new File(dir, name).isFile() && name.endsWith(".jpg");
			}
		});

		// 遍历
		for (String s : strArray) {
			System.out.println(s);
		}
	}
}

```
文件名称过滤器:

```java
  public String[] list(FilenameFilter filter)

  public File[] listFiles(FilenameFilter filter)
```
### 练习题批量改名

```java
/*
 * 需求：把E:\评书\三国演义下面的视频名称修改为
 * 		00?_介绍.avi
 * 
 * 思路：
 * 		A:封装目录
 * 		B:获取该目录下所有的文件的File数组
 * 		C:遍历该File数组，得到每一个File对象
 * 		D:拼接一个新的名称，然后重命名即可。
 */
public class FileDemo {
	public static void main(String[] args) {
		// 封装目录
		File srcFolder = new File("E:\\评书\\三国演义");

		// 获取该目录下所有的文件的File数组
		File[] fileArray = srcFolder.listFiles();

		// 遍历该File数组，得到每一个File对象
		for (File file : fileArray) {
			// System.out.println(file);
			// E:\评书\三国演义\三国演义_001_[评书网-今天很高兴,明天就IO了]_桃园三结义.avi
			// 改后：E:\评书\三国演义\001_桃园三结义.avi
			String name = file.getName(); // 三国演义_001_[评书网-今天很高兴,明天就IO了]_桃园三结义.avi

			int index = name.indexOf("_");
			String numberString = name.substring(index + 1, index + 4);
	
			int endIndex = name.lastIndexOf('_');
			String nameString = name.substring(endIndex);

			String newName = numberString.concat(nameString); // 001_桃园三结义.avi
			// System.out.println(newName);

			File newFile = new File(srcFolder, newName); // E:\\评书\\三国演义\\001_桃园三结义.avi

			// 重命名即可
			file.renameTo(newFile);
		}
	}
}
```
