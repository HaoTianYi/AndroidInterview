# Java学习笔记_day20

[TOC]

## 递归

方法定义中调用方法本身的现象

### 示例

```java
/*
 * 需求：请用代码实现求5的阶乘。
 * 下面的知识要知道：
 * 		5! = 1*2*3*4*5
 * 		5! = 5*4!
 * 
 * 有几种方案实现呢?
 * 		A:循环实现
 * 		B:递归实现
 * 			a:做递归要写一个方法
 * 			b:出口条件
 * 			c:规律
 */
public class DiGuiDemo {
	public static void main(String[] args) {
		int jc = 1;
		for (int x = 2; x <= 5; x++) {
			jc *= x;
		}
		System.out.println("5的阶乘是：" + jc);
		
		System.out.println("5的阶乘是："+jieCheng(5));
	}
	
	/*
	 * 做递归要写一个方法:
	 * 		返回值类型：int
	 * 		参数列表：int n
	 * 出口条件:
	 * 		if(n == 1) {return 1;}
	 * 规律:
	 * 		if(n != 1) {return n*方法名(n-1);}
	 */
	public static int jieCheng(int n){
		if(n==1){
			return 1;
		}else {
			return n*jieCheng(n-1);
		}
	}
}
```

### 练习题重要

```java
/*
 * 有一对兔子，从出生后第3个月起每个月都生一对兔子，小兔子长到第三个月后每个月又生一对兔子，假如兔子都不死，问第二十个月的兔子对数为多少？
 * 分析：我们要想办法找规律
 * 			兔子对数
 * 第一个月： 	1
 * 第二个月：	1
 * 第三个月：	2
 * 第四个月：	3	
 * 第五个月：	5
 * 第六个月：	8
 * ...
 * 
 * 由此可见兔子对象的数据是：
 * 		1,1,2,3,5,8...
 * 规则：
 * 		A:从第三项开始，每一项是前两项之和
 * 		B:而且说明前两项是已知的
 * 
 * 如何实现这个程序呢?
 * 		A:数组实现
 * 		B:变量的变化实现
 * 		C:递归实现
 * 
 * 假如相邻的两个月的兔子对数是a,b
 * 第一个相邻的数据：a=1,b=1
 * 第二个相邻的数据：a=1,b=2
 * 第三个相邻的数据：a=2,b=3
 * 第四个相邻的数据：a=3,b=5
 * 看到了：下一次的a是以前的b，下一次是以前的a+b	
 */
public class DiGuiDemo2 {
	public static void main(String[] args) {
		// 定义一个数组
		int[] arr = new int[20];
		arr[0] = 1;
		arr[1] = 1;
		// arr[2] = arr[0] + arr[1];
		// arr[3] = arr[1] + arr[2];
		// ...
		for (int x = 2; x < arr.length; x++) {
			arr[x] = arr[x - 2] + arr[x - 1];
		}
		System.out.println(arr[19]);// 6765
		System.out.println("----------------");

		int a = 1;
		int b = 1;
		for (int x = 0; x < 18; x++) {
			// 临时变量存储上一次的a
			int temp = a;
			a = b;
			b = temp + b;
		}
		System.out.println(b);
		System.out.println("----------------");

		System.out.println(fib(20));
	}

	/*
	 * 方法： 返回值类型：int 参数列表：int n 出口条件： 第一个月是1，第二个月是1 规律： 从第三个月开始，每一个月是前两个月之和
	 */
	public static int fib(int n) {
		if (n == 1 || n == 2) {
			return 1;
		} else {
			return fib(n - 1) + fib(n - 2);
		}
	}
}
```

递归要注意：出口条件和重复执行的规律。

递归是简单的算法：只要明白规律即可，比如上面的fib方法，就是阐述如果是第一个月或者第二个月就是1，其他月份是前两个月份的和。

### 练习题二

```java
/*
 * 需求：请大家把E:\JavaSE目录下所有的java结尾的文件的绝对路径给输出在控制台。
 * 
 * 分析：
 * 		A:封装目录
 * 		B:获取该目录下所有的文件或者文件夹的File数组
 * 		C:遍历该File数组，得到每一个File对象
 * 		D:判断该File对象是否是文件夹
 * 			是：回到B
 * 			否：继续判断是否以.java结尾
 * 				是：就输出该文件的绝对路径
 * 				否：不搭理它
 */
public class FilePathDemo {
	public static void main(String[] args) {
		// 封装目录
		File srcFolder = new File("E:");

		// 递归功能实现
		getAllJavaFilePaths(srcFolder);
	}

	private static void getAllJavaFilePaths(File srcFolder) {
		// 获取该目录下所有的文件或者文件夹的File数组
		File[] fileArray = srcFolder.listFiles();

		// 遍历该File数组，得到每一个File对象
		for (File file : fileArray) {
			// 判断该File对象是否是文件夹
			if (file != null && file.isDirectory()) {
				getAllJavaFilePaths(file);
			} else {
				// 继续判断是否以.java结尾
				if (file != null && file.getName().endsWith(".java")) {
					// 就输出该文件的绝对路径
					System.out.println(file.getAbsolutePath());
				}
			}
		}
	}
}
```

### 案例三

```java
/*
 * 需求：递归删除带内容的目录
 * 
 * 目录我已经给定：demo
 * 
 * 分析：
 * 		A:封装目录
 * 		B:获取该目录下的所有文件或者文件夹的File数组
 * 		C:遍历该File数组，得到每一个File对象
 * 		D:判断该File对象是否是文件夹
 * 			是：回到B
 * 			否：就删除
 */
public class FileDeleteDemo {
	public static void main(String[] args) {
		// 封装目录
		File srcFolder = new File("demo");
		// 递归实现
		deleteFolder(srcFolder);
	}

	private static void deleteFolder(File srcFolder) {
		// 获取该目录下的所有文件或者文件夹的File数组
		File[] fileArray = srcFolder.listFiles();

		if (fileArray != null) {
			// 遍历该File数组，得到每一个File对象
			for (File file : fileArray) {
				// 判断该File对象是否是文件夹
				if (file.isDirectory()) {
					deleteFolder(file);
				} else {
					System.out.println(file.getName() + "---" + file.delete());
				}
			}
			System.out.println(srcFolder.getName() + "---" + srcFolder.delete());
		}
	}
}
```

## IO流

lO流用来处理设备之间的数据传输，Java用于操作流的对象都在IO包中，输入输出以程序为本身参考物，所以输入流用来读数据，输出流用来写数据

一个字符两个字节，是为了方便操作文本数据，如果使用windows的记事本可以打开并且看懂，那么就是用字符流，如果什么都不知道就是用字节流

### 分类

- 流向：

  输入流	读取数据
  输出流 写出数据

- 数据类型：

  ```java
  字节流
  	字节输入流	读取数据	InputStream
  	字节输出流	写出数据	OutputStream
  字符流
  	字符输入流	读取数据	Reader
  	字符输出流	写出数据	Writer
  ```

> 默认情况下是按照数据类型来分的

### FileOutputStream

#### 构造方法

```java
FileOutputStream(File file) 

FileOutputStream(String name)
```

```java
		// 创建字节输出流对象
		// FileOutputStream(File file)
		// File file = new File("fos.txt");
		// FileOutputStream fos = new FileOutputStream(file);
		// FileOutputStream(String name)和下面的代码含义相同
		FileOutputStream fos = new FileOutputStream("fos.txt");
```

在创建字节流后台过程：

1. 首先是创建文件
2. 然后是创建File对象
3. 把File对象指向文件

> 把字符串转化成字节"hello,IO".getBytes()

####成员方法

```java
public void write(int b):写一个字节

public void write(byte[] b):写一个字节数组

public void write(byte[] b,int off,int len):写一个字节数组的一部分
```

#### 换行问题

windows是\r\n，FileOutputStream.write("\r\n".getBytes());

#### 数据追加问题

在新建FileOutputStream对象时，第二个参数添加true即可

#### 加入异常处理的FileOutputStream示例代码

```java
		// 为了在finally里面能够看到该对象就必须定义到外面，为了访问不出问题，还必须给初始化值
		FileOutputStream fos = null;
		try {
			// fos = new FileOutputStream("z:\\fos4.txt");
			fos = new FileOutputStream("fos4.txt");
			fos.write("java".getBytes());
		} catch (FileNotFoundException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		} finally {
			// 如果fos不是null，才需要close()
			if (fos != null) {
				// 为了保证close()一定会执行，就放到这里了
				try {
					fos.close();
				} catch (IOException e) {
					e.printStackTrace();
				}
			}
		}
```

注意：close方法是在finaly中调用的而且加入了判断是否为空的操作

### FileInputStream

读取示例代码：

```java
		//读取带有中文字符的格式
		byte[] bys = new byte[1024];
		int len = 0;
		while ((len = fis.read(bys)) != -1) {
			System.out.print(new String(bys, 0, len));
		}
```

```java
		//直接用字节流来操作
		// 封装数据源
		FileInputStream fis = new FileInputStream("e:\\林青霞.jpg");
		// 封装目的地
		FileOutputStream fos = new FileOutputStream("mn.jpg");

		// 复制数据
		int by = 0;
		while ((by = fis.read()) != -1) {
			fos.write(by);
		}
```

以上有两种读取数据的方法：

> int read():一次读取一个字节
>
> int read(byte[] b):一次读取一个字节数组,in返回值是实际读取字符的个数

调用read（bytes[] b) 最后一次调用时假如没有读取完整个数组那么，最后一次会把前一次的n个字符替代，后面的字符n-b还会显示，用第二个方法int read(byte[] b)读取数组会快

### 缓冲区类(高效类)

写数据：BufferedOutputStream，读数据：BufferedInputStream

构造方法可以指定缓冲区的大小，但是我们一般用不上，因为默认缓冲区大小就足够了

#### 为什么不传递一个具体的文件或者文件路径，而是传递一个OutputStream对象呢?

字节缓冲区流仅仅提供缓冲区，为高效而设计的。但是呢，真正的读写操作还得靠基本的流对象实现。

```java
		// FileOutputStream fos = new FileOutputStream("bos.txt");
		// BufferedOutputStream bos = new BufferedOutputStream(fos);
		// 简单写法
		BufferedOutputStream bos = new BufferedOutputStream(
				new FileOutputStream("bos.txt"));

		// 写数据
		bos.write("hello".getBytes());

		// 释放资源
		bos.close();
```

带缓冲的字节读取流

```java
		BufferedInputStream bis = new BufferedInputStream(new FileInputStream("bos.txt"));

		// 读取数据
		// int by = 0;
		// while ((by = bis.read()) != -1) {
		// System.out.print((char) by);
		// }
		// System.out.println("---------");

		// 第二中方式读取数据
		byte[] bys = new byte[1024];
		int len = 0;
		while ((len = bis.read(bys)) != -1) {
			System.out.print(new String(bys, 0, len));
		}

		// 释放资源
		bis.close();
```