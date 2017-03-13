# Java学习笔记_day14

[TOC]

## 正则表达式

### 基本规则

A:字符

	x 字符 x。举例：'a'表示字符a
	\\ 反斜线字符。
	\n 新行（换行）符 ('\u000A') 
	\r 回车符 ('\u000D')

B:字符类
	[abc] a、b 或 c（简单类） 三选一
	[^abc] 任何字符，除了 a、b 或 c（否定） 
	[a-zA-Z] a到 z 或 A到 Z，两头的字母包括在内（范围） 
	[0-9] 0到9的字符都包括

C:预定义字符类
	. 任何字符。我的就是.字符本身，怎么表示呢? \.
	\d 数字：[0-9]
	\w 单词字符：[a-zA-Z_0-9]
		在正则表达式里面组成单词的东西必须有这些东西组成

D:边界匹配器
	^ 行的开头 
	$ 行的结尾 
	\b 单词边界
		就是不是单词字符的地方。

E:Greedy 数量词 
	X? X，一次或一次也没有
	X* X，零次或多次
	X+ X，一次或多次
	X{n} X，恰好 n 次 
	X{n,} X，至少 n 次 
	X{n,m} X，至少 n 次，但是不超过 m 次 
### 功能

#### 判断

String类的public boolean matches(String regex)

验证邮箱：

```java
String regex = "\\w+@\\w{2,6}(\\.\\w{2,3})+";
```

#### 分割

String类的public String[] split(String regex)

分割硬盘的路径：

```java
		String s4 = "E:\\JavaSE\\day14\\avi";
		String[] str4Array = s4.split("\\\\");
		for (int x = 0; x < str4Array.length; x++) {
			System.out.println(str4Array[x]);
		}
```

被分割字符串中有空格：

```java
		String s3 = "aa    bb                cc";
		String[] str3Array = s3.split(" +");
		for (int x = 0; x < str3Array.length; x++) {
			System.out.println(str3Array[x]);
		}
```

#### 练习

  我有如下一个字符串:"91 27 46 38 50"
  请写代码实现最终输出结果是："27 38 46 50 91"

```java
public class RegexTest {
	public static void main(String[] args) {
		// 定义一个字符串
		String s = "91 27 46 38 50";

		// 把字符串进行分割，得到一个字符串数组
		String[] strArray = s.split(" ");

		// 把字符串数组变换成int数组
		int[] arr = new int[strArray.length];

		for (int x = 0; x < arr.length; x++) {
			arr[x] = Integer.parseInt(strArray[x]);
		}

		// 对int数组排序
		Arrays.sort(arr);

		// 把排序后的int数组在组装成一个字符串
		StringBuilder sb = new StringBuilder();
		for (int x = 0; x < arr.length; x++) {
			sb.append(arr[x]).append(" ");
		}
		//转化为字符串
		String result = sb.toString().trim();
		
		//输出字符串
		System.out.println("result:"+result);
	}
}
```

#### 替换

String类的public String replaceAll(String regex,String replacement)

使用给定的 replacement 替换此字符串所有匹配给定的正则表达式的子字符串

```java
public class RegexDemo {
	public static void main(String[] args) {
		// 定义一个字符串
		String s = "helloqq12345worldkh622112345678java";

		// 我要去除所有的数字,用*给替换掉
		// String regex = "\\d+"; -------------->所有数字才是一个星
		// String regex = "\\d"; -------->表示一个数字一个星星
		//String ss = "*";
		
		
		// 直接把数字干掉
		String regex = "\\d+";
		String ss = "";

		String result = s.replaceAll(regex, ss);
		System.out.println(result);
	}
}
```

#### 获取

示例代码：

```java
  获取下面这个字符串中由三个字符组成的单词
  da jia ting wo shuo,jin tian yao xia yu,bu shang wan zi xi,gao xing bu?
  
  public class RegexDemo2 {
	public static void main(String[] args) {
		// 定义字符串
		String s = "da jia ting wo shuo,jin tian yao xia yu,bu shang wan zi xi,gao xing bu?";
		// 规则
		String regex = "\\b\\w{3}\\b";

		// 把规则编译成模式对象
		Pattern p = Pattern.compile(regex);
		// 通过模式对象得到匹配器对象
		Matcher m = p.matcher(s);
		// 调用匹配器对象的功能
		// 通过find方法就是查找有没有满足条件的子串
		while (m.find()) {
			System.out.println(m.group());
		}

		// 注意：一定要先find()，然后才能group()
		// IllegalStateException: No match found非法状态
		 String ss = m.group();
		 System.out.println(ss);
	}
}
```

## math类

### 成员方法

```java
  		public static int abs(int a)：绝对值
 		public static double ceil(double a):向上取整
 		public static double floor(double a):向下取整
 		public static int max(int a,int b):最大值 (min自学)
 		public static double pow(double a,double b):a的b次幂
 		public static double random():随机数 [0.0,1.0)
 		public static int round(float a) 四舍五入
 		public static double sqrt(double a):正平方根
```

### 练习题

```java
请设计一个方法，可以实现获取任意范围内的随机数。

	public static int getRandom(int start, int end) {

		int number = (int) (Math.random() * (end - start + 1)) + start;
		return number;
	}
```

## Random类

产生随机数的类，给定种子后，每次得到的随机数是相同的。

### 构造方法

```java
  		public Random():没有给种子，用的是默认种子，是当前时间的毫秒值
 		public Random(long seed):给出指定的种子
```

### 成员方法

```java
  		public int nextInt()：返回的是int范围内的随机数
 		public int nextInt(int n):返回的是[0,n)范围的内随机数
```

## System类

包含一些有用的类字段和方法（就是静态的），他不能被实例化。

### 成员方法

```java
  		public static void gc()：运行垃圾回收器。 会自动调用object的finalize方法
         终止当前正在运行的 Java 虚拟机。参数用作状态码；根据惯例，非 0 的状态码表示异常终止。
 		public static void exit(int status)
		返回以毫秒为单位的当前时间
 		public static long currentTimeMillis()
 		public static void arraycopy(Object src,int srcPos,Object dest,int destPos,int length)指定源数组中复制一个数组，复制从指定的位置开始，到目标数组的指定位置结束。
```

注意：arraycopy的写法是错误的

arraycopy的示例代码：

```java
	public static void main(String[] args) {
		// 定义数组
		int[] arr = { 11, 22, 33, 44, 55 };
		int[] arr2 = { 6, 7, 8, 9, 10 };

		// 请大家看这个代码的意思
		System.arraycopy(arr, 1, arr2, 2, 2);

		System.out.println(Arrays.toString(arr));
		System.out.println(Arrays.toString(arr2));
	}

	//运行结果
	//[11, 22, 33, 44, 55]
	//[6, 7, 22, 33, 10]
```

## BigInteger类

可以让超过Integer范围内的数据进行运算

### 构造方法

BigInteger(String val)

### 成员方法

```java
  public BigInteger add(BigInteger val):加
  public BigInteger subtract(BigInteger val):减
  public BigInteger multiply(BigInteger val):乘
  public BigInteger divide(BigInteger val):除
  public BigInteger[] divideAndRemainder(BigInteger val):返回商和余数的数组
```

## BigDecimal

为了能精确的表示、计算浮点数，Java提供了BigDecimal

### 构造方法

public BigDecimal(String val)其他重载方法1如传入doubble，仍会丢失精度

### 成员方法

```java
  public BigDecimal add(BigDecimal augend)
  public BigDecimal subtract(BigDecimal subtrahend)
  public BigDecimal multiply(BigDecimal multiplicand)
  public BigDecimal divide(BigDecimal divisor)
  public BigDecimal divide(BigDecimal divisor,int scale,int roundingMode):商，几位小数，如何舍取
```

## Date类

表示特定的瞬间，精确到毫秒。 

### 构造方法

Date(long date)：根据给定的毫秒值创建日期对象

```java
		//写出结果		
		long time = 1000 * 60 * 60; // 1小时
		Date d2 = new Date(time);
		System.out.println("d2:" + d2);
```

结果不是1970年1月1日1点而是早上9点，因为时区，差8个小时。

### 成员方法

```java
  public long getTime():获取时间，以毫秒为单位
  public void setTime(long time):设置时间
```

  从Date得到一个毫秒值
  		getTime()
  把一个毫秒值转换为Date
  		构造方法
  		setTime(long time)

## SimpleDateFormat

DateForamt:可以进行日期和字符串的格式化和解析，但是由于是抽象类，所以使用具体子类SimpleDateFormat。

### 构造方法

```java
  		SimpleDateFormat():默认模式
  		SimpleDateFormat(String pattern):给定的模式
  			这个模式字符串该如何写呢?
  			通过查看API，我们就找到了对应的模式
  			年 y
  			月 M	
  			日 d
  			时 H
  			分 m
  			秒 s
          示例：yyyy年MM月dd日 HH:mm:ss
```

日期和字符串的相互转化：

```java
public class DateFormatDemo {
	public static void main(String[] args) throws ParseException {
		// Date -- String
		// 创建日期对象
		Date d = new Date();
		// 创建格式化对象
		// SimpleDateFormat sdf = new SimpleDateFormat();
		// 给定模式
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss");
		// public final String format(Date date)
		String s = sdf.format(d);
		System.out.println(s);
		
		
		//String -- Date
		String str = "2008-08-08 12:12:12";
		//在把一个字符串解析为日期的时候，请注意格式必须和给定的字符串格式匹配
		SimpleDateFormat sdf2 = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
		Date dd = sdf2.parse(str);
		System.out.println(dd);
	}
}
```

## Calendar

Calendar:它为特定瞬间与一组诸如 YEAR、MONTH、DAY_OF_MONTH、HOUR 等 日历字段之间的转换提供了一些方法，并为操作日历字段（例如获得下星期的日期）提供了一些方法。

### 成员方法

public int get(int field):返回给定日历字段的值。日历类中的每个日历字段都是静态的成员变量，并且是int类型。

示例代码：

```java
public class CalendarDemo {
	public static void main(String[] args) {
		// 其日历字段已由当前日期和时间初始化：
		Calendar rightNow = Calendar.getInstance(); // 子类对象

		// 获取年
		int year = rightNow.get(Calendar.YEAR);
		// 获取月
		int month = rightNow.get(Calendar.MONTH);
		// 获取日
		int date = rightNow.get(Calendar.DATE);

		System.out.println(year + "年" + (month + 1) + "月" + date + "日");
	}
}
```

注意：其中getIntance的写法实际就是创建一个Canlendar的子类的实现对象；而且month要加一，month是从0

开始到11。

public void add(int field,int amount):根据给定的日历字段和对应的时间，来对当前的日历进行操作。

public final void set(int year,int month,int date):设置当前日历的年月日

```java
public class CalendarDemo {
	public static void main(String[] args) {
		// 获取当前的日历时间
		Calendar c = Calendar.getInstance();

		// 获取年
		int year = c.get(Calendar.YEAR);
		// 获取月
		int month = c.get(Calendar.MONTH);
		// 获取日
		int date = c.get(Calendar.DATE);
		System.out.println(year + "年" + (month + 1) + "月" + date + "日");

		// // 三年前的今天
		// c.add(Calendar.YEAR, -3);
		// // 获取年
		// year = c.get(Calendar.YEAR);
		// // 获取月
		// month = c.get(Calendar.MONTH);
		// // 获取日
		// date = c.get(Calendar.DATE);
		// System.out.println(year + "年" + (month + 1) + "月" + date + "日");

		// 5年后的10天前
		c.add(Calendar.YEAR, 5);
		c.add(Calendar.DATE, -10);
		// 获取年
		year = c.get(Calendar.YEAR);
		// 获取月
		month = c.get(Calendar.MONTH);
		// 获取日
		date = c.get(Calendar.DATE);
		System.out.println(year + "年" + (month + 1) + "月" + date + "日");
		System.out.println("--------------");

		c.set(2011, 11, 11);
		// 获取年
		year = c.get(Calendar.YEAR);
		// 获取月
		month = c.get(Calendar.MONTH);
		// 获取日
		date = c.get(Calendar.DATE);
		System.out.println(year + "年" + (month + 1) + "月" + date + "日");
	}
}
```

### 练习

获取任意一年的二月有多少天

```java
/*
 * 获取任意一年的二月有多少天
 * 
 * 分析：
 * 		A:键盘录入任意的年份
 * 		B:设置日历对象的年月日
 * 			年就是A输入的数据
 * 			月是2
 * 			日是1
 * 		C:把时间往前推一天，就是2月的最后一天
 * 		D:获取这一天输出即可
 */
public class CalendarTest {
	public static void main(String[] args) {
		// 键盘录入任意的年份
		Scanner sc = new Scanner(System.in);
		System.out.println("请输入年份：");
		int year = sc.nextInt();

		// 设置日历对象的年月日
		Calendar c = Calendar.getInstance();
		c.set(year, 2, 1); // 其实是这一年的3月1日
		// 把时间往前推一天，就是2月的最后一天
		c.add(Calendar.DATE, -1);

		// 获取这一天输出即可
		System.out.println(c.get(Calendar.DATE));
	}
}
```

