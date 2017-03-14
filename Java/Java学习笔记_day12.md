# Java学习笔记_day12

[TOC]



## Scanner类

### 构造方法

Scanner是JDK5以后用于获取用户键盘输入，一个可以使用正则表达式来解析基本类型和字符串的简单文本扫描器，Pattern类中有正则表达式的语法

```java
	public Scanner（InputStream source）；
```

### Scanner类的成员方法

1. hasNextXXX（）判断下一个输入项，返回值是boolena；
2. nextXxxx（）读取下一个输入项

> InputMismatchException：输入的和你想要的不匹配

常用的两个方法：

1. public int nextInt（）获取一个int类型的值；
2. public int nextLine（）获取String类型的值；

### 小问题

- 先获取一个int（double）在获取一个string,不会获取字符串（只是在输入第一个数值按下空格不会出现这个问题，只有按下回车才会出现问题；
- 主要原因是换行符\r的问题




- 解决方法：先获取一个数值在创建一个新的对象在获取另一个数值；或者先全部把要获取的数值赋值为String类在进行类型转换；

## String类

### 概述

1. String就是一个对象；
2. String是一个常量；

> 直接用输出语句输出一个对象他的本质是一个调用toString方法，没有重写会打印出哈希值；

### 构造方法

```java
  		public String():空构造
 		public String(byte[] bytes):把字节数组转成字符串
 		public String(byte[] bytes,int index,int length):把字节数组的一部分转成字符串
 		public String(char[] value):把字符数组转成字符串
 		public String(char[] value,int index,int count):把字符数组的一部分转成字符串
 		public String(String original):把字符串常量值转成字符串
```

构造方法一：

```java
		byte[] bys = { 97, 98, 99, 100, 101 };
		String s2 = new String(bys);
		System.out.println("s2:" + s2);
```

他的答应结果是：

abcde，他会先把数值转换成字符，在连接成字符串数组；

```java
 		public String(byte[] bytes,int index,int length):把字节数组的一部分转成字符串
```

index表示从第几个数值开始，length表示截取几个

### 练习题

- String s = "hello"; s += "world";s的结果是多少？

![ice_screenshot_20160806-081705](http://oaxelf1sk.bkt.clouddn.com/ice_screenshot_20160806-081705.png)

字符串一旦被赋值就不被改变，不能被改变的是值；而不是对象s；

是常量值就会到方法区的常量池中查找；

1. 首先string s创建一个新的对象，入栈；
2. 然后是在方法区中的字符串常量池查找是否有字符串hello
3. 没有创建字符串hello
4. 在用world查找是否在方法去中有，没有创建；
5. 拼接字符串，赋值给独享s；

>==:比较引用类型比较的是地址值是否相同
>
>equals:比较引用类型默认也是比较地址值是否相同，而String类重写了equals()方法，比较的是内容是否相同，源码就是一个字符一个字符来比较

- String s = new String(“hello”)和String s = “hello”;的区别?

前者会创建两个或者一个对象，后者会创建一个或者零个对象；

![ice_screenshot_20160806-083033](http://oaxelf1sk.bkt.clouddn.com/ice_screenshot_20160806-083033.png)

```java
		String s1 = new String("hello");
		String s2 = "hello";
```

1. s1首先入栈
2. 在堆中新建一个字符串对象；
3. 在方法区中查是否有hello，没有创建一个新的字符串常量；
4. 把地址值给堆中的对象，再把堆的对象的地址值给栈中的对象；

而s2是直接到方法区中的字符串常量池中查找是否有字符串

- 看程序写结果

```java
		String s1 = new String("hello");
		String s2 = new String("hello");
		System.out.println(s1 == s2);// false
		System.out.println(s1.equals(s2));// true

		String s3 = new String("hello");
		String s4 = "hello";
		System.out.println(s3 == s4);// false
		System.out.println(s3.equals(s4));// true

		String s5 = "hello";
		String s6 = "hello";
		System.out.println(s5 == s6);// true
		System.out.println(s5.equals(s6));// true
```

- 写结果

```java
		String s1 = "hello";
		String s2 = "world";
		String s3 = "helloworld";
		System.out.println(s3 == s1 + s2);// false
		System.out.println(s3.equals((s1 + s2)));// true

		System.out.println(s3 == "hello" + "world");// true
		System.out.println(s3.equals("hello" + "world"));// true
```

字符串如果是变量相加，那么是先开空间在拼接；

字符串如果是常量相加，那么是先加，然后在常量池中查找，如果有就返回，否则就创建

### 字符串的功能

#### 判断功能

* boolean equals(Object obj):比较字符串的内容是否相同,区分大小写
* boolean equalsIgnoreCase(String str):比较字符串的内容是否相同,忽略大小写
* boolean contains(String str):判断大字符串中是否包含小字符串
* boolean startsWith(String str):判断字符串是否以某个指定的字符串开头
* boolean endsWith(String str):判断字符串是否以某个指定的字符串结尾
* boolean isEmpty():判断字符串是否为空。

> 字符串内容为空时：String s = "";
>
> 字符串对象为空：String s = null;此时s对象不存在不能调用方法**NullPointerException**

#### 获取功能

* int length():获取字符串的长度。
* char charAt(int index):获取指定索引位置的字符
* int indexOf(int ch):返回指定字符在此字符串中第一次出现处的索引。
    * 	为什么这里是int类型，而不是char类型?
        * 原因是：'a'和97其实都可以代表'a'
* int indexOf(String str):返回指定字符串在此字符串中第一次出现处的索引。
* int indexOf(int ch,int fromIndex):返回指定字符在此字符串中从指定位置后第一次出现处的索引。
* int indexOf(String str,int fromIndex):返回指定字符串在此字符串中从指定位置后第一次出现处的索引。
* String substring(int start):从指定位置开始截取字符串,默认到末尾。
* String substring(int start,int end):从指定位置开始到指定位置结束截取字符串。

#### 练习-字符串的遍历

```java
		for (int x = 0; x < s.length(); x++) {
			System.out.println(s.charAt(x));
		}
		//还可以先把字符串变换成字符数组然后在遍历字符数组
```
#### 练习-判断字符串中有多少个大写、小写字符

```java
  需求：统计一个字符串中大写字母字符，小写字母字符，数字字符出现的次数。(不考虑其他字符)
  举例：
 		"Hello123World"
  结果：
  		大写字符：2个
  		小写字符：8个
  		数字字符：3个
  
 分析：
 		前提：字符串要存在
  		A:定义三个统计变量
  			bigCount=0
  			smallCount=0
  			numberCount=0
  		B:遍历字符串，得到每一个字符。
  			length()和charAt()结合
  		C:判断该字符到底是属于那种类型的
  			大：bigCount++
  			小：smallCount++
  			数字：numberCount++
  
  			这道题目的难点就是如何判断某个字符是大的，还是小的，还是数字的。
  			ASCII码表：
  				0	48
  				A	65
  				a	97
  			虽然，我们按照数字的这种比较是可以的，但是想多了，有比这还简单的
  				char ch = s.charAt(x);
  
  				if(ch>='0' && ch<='9') numberCount++
  				if(ch>='a' && ch<='z') smallCount++
  				if(ch>='A' && ch<='Z') bigCount++
 		D:输出结果。
```

这里的重点是字符串在运算时会自动转换成int类型，所以可以直接比较而没有必要先转换类型。

```java
public class StringTest2 {
	public static void main(String[] args) {
		//定义一个字符串
		String s = "Hello123World";
		
		//定义三个统计变量
		int bigCount = 0;
		int smallCount = 0;
		int numberCount = 0;
		
		//遍历字符串，得到每一个字符。
		for(int x=0; x<s.length(); x++){
			char ch = s.charAt(x);
			
			//判断该字符到底是属于那种类型的
			if(ch>='a' && ch<='z'){
				smallCount++;
			}else if(ch>='A' && ch<='Z'){
				bigCount++;
			}else if(ch>='0' && ch<='9'){
				numberCount++;
			}
		}
		
		//输出结果。
		System.out.println("大写字母"+bigCount+"个");
		System.out.println("小写字母"+smallCount+"个");
		System.out.println("数字"+numberCount+"个");
	}
}
```

#### 字符串的转换功能

* byte[] getBytes():把字符串转换为字节数组。
* char[] toCharArray():把字符串转换为字符数组。
* static String valueOf(char[] chs):把字符数组转成字符串。
* static String valueOf(int i):把int类型的数据转成字符串。
    * 	注意：String类的valueOf方法可以把任意类型的数据转成字符串。
* String toLowerCase():把字符串转成小写。
* String toUpperCase():把字符串转成大写。
* String concat(String str):把字符串拼接。

#### 字符串的其他功能

* 替换功能：
* String replace(char old,char new)
* String replace(String old,String new) 
* String trim()去除字符串两空格
* 按字典顺序比较两个字符串  
* int compareTo(String str)如果第一位相同就继续，如果不同就用前面字符的值减去后面（被比较者）的值
* int compareToIgnoreCase(String str)

#### 练习compareTo方法

写出结果;

```java
   String s1 = "hello";
   String s2 = "hel";
   System.out.println(s1.compareTo(s2)); 
```

参考答案：2长度不同的compareTo方法是长度相减（前面都相同）

#### 练习-字符串的拼接

  需求：把数组中的数据按照指定个格式拼接成一个字符串
  举例：
  		int[] arr = {1,2,3};	
  输出结果：
 		"[1, 2, 3]"
  分析：
  		A:定义一个字符串对象，只不过内容为空
  		B:先把字符串拼接一个"["
  		C:遍历int数组，得到每一个元素
  		D:先判断该元素是否为最后一个
  			是：就直接拼接元素和"]"
  			不是：就拼接元素和逗号以及空格
  		E:输出拼接后的字符串

```java
public class StringTest {
	public static void main(String[] args) {
		// 前提是数组已经存在
		int[] arr = { 1, 2, 3 };

		// 定义一个字符串对象，只不过内容为空
		String s = "";

		// 先把字符串拼接一个"["
		s += "[";

		// 遍历int数组，得到每一个元素
		for (int x = 0; x < arr.length; x++) {
			// 先判断该元素是否为最后一个
			if (x == arr.length - 1) {
				// 就直接拼接元素和"]"
				s += arr[x];
				s += "]";
			} else {
				// 就拼接元素和逗号以及空格
				s += arr[x];
				s += ", ";
			}
		}

		// 输出拼接后的字符串
		System.out.println("最终的字符串是：" + s);
	}
}
```

#### 练习-字符串反转

  字符串反转
  举例：键盘录入”abc”		
  输出结果：”cba”

  分析：
  		A:键盘录入一个字符串
  		B:定义一个新字符串
  		C:倒着遍历字符串，得到每一个字符
  			a:length()和charAt()结合
  			b:把字符串转成字符数组
  		D:用新字符串把每一个字符拼接起来
  		E:输出新串

```java
public class StringTest3 {
	public static void main(String[] args) {
		// 键盘录入一个字符串
		Scanner sc = new Scanner(System.in);
		System.out.println("请输入一个字符串：");
		String line = sc.nextLine();

		/*
		// 定义一个新字符串
		String result = "";

		// 把字符串转成字符数组
		char[] chs = line.toCharArray();

		// 倒着遍历字符串，得到每一个字符
		for (int x = chs.length - 1; x >= 0; x--) {
			// 用新字符串把每一个字符拼接起来
			result += chs[x];
		}

		// 输出新串
		System.out.println("反转后的结果是：" + result);
		*/

		// 改进为功能实现
		String s = myReverse(line);
		System.out.println("实现功能后的结果是：" + s);
	}

	/*
	 * 两个明确： 返回值类型：String 参数列表：String
	 */
	public static  String myReverse(String s) {
		// 定义一个新字符串
		String result = "";

		// 把字符串转成字符数组
		char[] chs = s.toCharArray();

		// 倒着遍历字符串，得到每一个字符
		for (int x = chs.length - 1; x >= 0; x--) {
			// 用新字符串把每一个字符拼接起来
			result += chs[x];
		}
		return result;
	}
}
```

核心代码是：

```java
		// 倒着遍历字符串，得到每一个字符
		for (int x = chs.length - 1; x >= 0; x--) {
			// 用新字符串把每一个字符拼接起来
			result += chs[x];
		}
```

#### 练习-在大字符串中查找小字符串

  统计大串中小串出现的次数
  举例：
  		在字符串"woaijavawozhenaijavawozhendeaijavawozhendehenaijavaxinbuxinwoaijavagun"
  结果：
  		java出现了5次

  分析：
  		前提：是已经知道了大串和小串。

  		A:定义一个统计变量，初始化值是0
  		B:先在大串中查找一次小串第一次出现的位置
  			a:索引是-1，说明不存在了，就返回统计变量
  			b:索引不是-1，说明存在，统计变量++
  		C:把刚才的索引+小串的长度作为开始位置截取上一次的大串，返回一个新的字符串，并把该字符串的值重新赋值给大串
  		D:回到B

```java
public class StringTest4 {
	public static void main(String[] args) {
		// 定义大串
		String maxString = "woaijavawozhenaijavawozhendeaijavawozhendehenaijavaxinbuxinwoaijavagun";
		// 定义小串
		String minString = "java";

		// 写功能实现
		int count = getCount(maxString, minString);
		System.out.println("Java在大串中出现了：" + count + "次");
	}

	/*
	 * 两个明确： 返回值类型：int 参数列表：两个字符串
	 */
	public static int getCount(String maxString, String minString) {
		// 定义一个统计变量，初始化值是0
		int count = 0;

		// 先在大串中查找一次小串第一次出现的位置
		int index = maxString.indexOf(minString);

		// 索引不是-1，说明存在，统计变量++
		while (index != -1) {
			count++;
			// 把刚才的索引+小串的长度作为开始位置截取上一次的大串，返回一个新的字符串，并把该字符串的值重新赋值给大串
			int startIndex = index + minString.length();
			maxString = maxString.substring(startIndex);
			// 继续查
			index = maxString.indexOf(minString);
		}

		return count;
	}
}
```

改进代码：

```java
	public static int getCount(String maxString, String minString) {
		// 定义一个统计变量，初始化值是0
		int count = 0;		
		int index;
		//先查，赋值，判断
		while((index=maxString.indexOf(minString))!=-1){
			count++;
			maxString = maxString.substring(index + minString.length());
		}
		return count;
	}
```
