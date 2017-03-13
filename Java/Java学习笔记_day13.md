# Java学习笔记_day13

[TOC]

## StringBuffer

### 概述

是一个线程安全的可变字符序列（所以在字符串拼接时不会有空间的浪费）

### 构造方法

```java
  		public StringBuffer():无参构造方法
 		public StringBuffer(int capacity):指定容量的字符串缓冲区对象
 		public StringBuffer(String str):指定字符串内容的字符串缓冲区对象
```

> stringBuffer重写了toString方法
>
> public int capacity（）返回当前的容量，理论值
>
> public int length（）返回长度字符数，实际值

示例代码：

```java
public class StringBufferDemo {
	public static void main(String[] args) {
		// public StringBuffer():无参构造方法
		StringBuffer sb = new StringBuffer();
		System.out.println("sb:" + sb);
		System.out.println("sb.capacity():" + sb.capacity());
		System.out.println("sb.length():" + sb.length());
		System.out.println("--------------------------");

		// public StringBuffer(int capacity):指定容量的字符串缓冲区对象
		StringBuffer sb2 = new StringBuffer(50);
		System.out.println("sb2:" + sb2);
		System.out.println("sb2.capacity():" + sb2.capacity());
		System.out.println("sb2.length():" + sb2.length());
		System.out.println("--------------------------");

		// public StringBuffer(String str):指定字符串内容的字符串缓冲区对象
		StringBuffer sb3 = new StringBuffer("hello");
		System.out.println("sb3:" + sb3);
		System.out.println("sb3.capacity():" + sb3.capacity());
		System.out.println("sb3.length():" + sb3.length());
	}
}
```

打印结果：

```
sb:
sb.capacity():16
sb.length():0
--------------------------
sb2:
sb2.capacity():50
sb2.length():0
--------------------------
sb3:hello
sb3.capacity():21
sb3.length():5
```

### 成员方法

- public StringBuffer append(XXXX xxx)

  参数将被转换成字符串，就好象使用了 String.valueOf 方法一样。然后，将所得字符串中的字符追加到此序列。

注意：此时返回的是被操作（被添加）的字符串本身

​	可以把任意类型数据添加到字符串缓冲区里面,并返回字符串缓冲区本身

```java
		StringBuffer sb = new StringBuffer();
		// public StringBuffer append(String str)
		 StringBuffer sb2 = sb.append("hello");
		 System.out.println("sb:" + sb);
		 System.out.println("sb2:" + sb2);
		 System.out.println(sb == sb2); // true

		// 链式编程
		sb.append("hello").append(true).append(12).append(34.56);
		System.out.println("sb:" + sb);
```

- public StringBuffer insert(int offset,String str)

  在指定位置把任意类型的数据插入到字符串缓冲区里面,并返回字符串缓冲区本身

- public StringBuffer deleteCharAt(int index)

  删除指定位置的字符，并返回本身

- public StringBuffer delete(int start,int end)

  删除从指定位置开始指定位置结束的内容，并返回本身，**包含开始不包含结束**

  删除所有的数据：sb.delete(0, sb.length());

- public StringBuffer replace(int start,int end,String str)从start开始到end用str替换

- public StringBuffer reverse()反转功能，返回的是数据本身，本身被改变

- public String substring(int start)返回的是从start到结束，此时它**本身不变**

- public String substring(int start,int end)注意：以上两个都是返回**string而不是StringBuffer**

### 练习

- String和StringBuffer的转换

```java
		//string----------->stringBuffer
		// StringBuffer sb = "hello";
		// StringBuffer sb = s;
		// 方式1:通过构造方法
		StringBuffer sb = new StringBuffer(s);
		// 方式2：通过append()方法
		StringBuffer sb2 = new StringBuffer();
		sb2.append(s);

		// StringBuffer --------> String
		StringBuffer buffer = new StringBuffer("java");
		// String(StringBuffer buffer)
		// 方式1:通过构造方法
		String str = new String(buffer);
		// 方式2：通过toString()方法
		String str2 = buffer.toString();
```

- 把数组拼接成一个字符串

```java
public class StringBufferTest2 {
	public static void main(String[] args) {
		// 定义一个数组
		int[] arr = { 44, 33, 55, 11, 22 };

		// 定义功能
		// 方式1：用String做拼接的方式
		String s1 = arrayToString(arr);
		System.out.println("s1:" + s1);

		// 方式2:用StringBuffer做拼接的方式
		String s2 = arrayToString2(arr);
		System.out.println("s2:" + s2);
	}

	// 用StringBuffer做拼接的方式
	public static String arrayToString2(int[] arr) {
		StringBuffer sb = new StringBuffer();

		sb.append("[");
		for (int x = 0; x < arr.length; x++) {
			if (x == arr.length - 1) {
				sb.append(arr[x]);
			} else {
				sb.append(arr[x]).append(", ");
			}
		}
		sb.append("]");

		return sb.toString();
	}

	// 用String做拼接的方式
	public static String arrayToString(int[] arr) {
		String s = "";

		s += "[";
		for (int x = 0; x < arr.length; x++) {
			if (x == arr.length - 1) {
				s += arr[x];
			} else {
				s += arr[x];
				s += ", ";
			}
		}
		s += "]";

		return s;
	}
}
```

- 字符串的反转

```java
public class StringBufferTest3 {
	public static void main(String[] args) {
		// 键盘录入数据
		Scanner sc = new Scanner(System.in);
		System.out.println("请输入数据：");
		String s = sc.nextLine();

		// 方式1：用String做拼接
		String s1 = myReverse(s);
		System.out.println("s1:" + s1);
		// 方式2：用StringBuffer的reverse()功能
		String s2 = myReverse2(s);
		System.out.println("s2:" + s2);
	}

	// 用StringBuffer的reverse()功能
	public static String myReverse2(String s) {
		// StringBuffer sb = new StringBuffer();
		// sb.append(s);

		// StringBuffer sb = new StringBuffer(s);
		// sb.reverse();
		// return sb.toString();

		// 简易版
		return new StringBuffer(s).reverse().toString();
	}

	// 用String做拼接
	public static String myReverse(String s) {
		String result = "";

		char[] chs = s.toCharArray();
		for (int x = chs.length - 1; x >= 0; x--) {
			// char ch = chs[x];
			// result += ch;
			result += chs[x];
		}

		return result;
	}
}
```

- 判断一个字符串是否是对称的字符串

```java
public class StringBufferTest4 {
	public static void main(String[] args) {
		// 创建键盘录入对象
		Scanner sc = new Scanner(System.in);
		System.out.println("请输入一个字符串：");
		String s = sc.nextLine();

		// 一个一个的比较
		boolean b = isSame(s);
		System.out.println("b:" + b);
		
		//用字符串缓冲区的反转功能
		boolean b2 = isSame2(s);
		System.out.println("b2:"+b2);
	}
	
	public static boolean isSame2(String s) {
		return new StringBuffer(s).reverse().toString().equals(s);
	}
	

	// public static boolean isSame(String s) {
	// // 把字符串转成字符数组
	// char[] chs = s.toCharArray();
	//
	// for (int start = 0, end = chs.length - 1; start <= end; start++, end--) {
	// if (chs[start] != chs[end]) {
	// return false;
	// }
	// }
	//
	// return true;
	// }

	public static boolean isSame(String s) {
		boolean flag = true;

		// 把字符串转成字符数组
		char[] chs = s.toCharArray();

		for (int start = 0, end = chs.length - 1; start <= end; start++, end--) {
			if (chs[start] != chs[end]) {
				flag = false;
				break;
			}
		}

		return flag;
	}
}
```

注意：在isSame方法中的flag标签的思想，而不是直接返回一个boolean值。

> StringBuffer和StringBuilder区别是：builder不安全

- String,StringBuffer,StringBuilder的区别?

> A:String是内容不可变的，而StringBuffer,StringBuilder都是内容可变的。
>
> B:StringBuffer是同步的，数据安全,效率低;StringBuilder是不同步的,数据不安全,效率高
>
> 安全不安全指的是多线程的情况

- StringBuffer和数组的区别?

>  二者都可以看出是一个容器，装其他的数据。
>
>   但是呢,StringBuffer的数据最终是一个字符串数据。
>
>   而数组可以放置多种数据，但必须是同一种数据类型的。

- 写出运行结果

```java
public class StringBufferDemo {
	public static void main(String[] args) {
		String s1 = "hello";
		String s2 = "world";
		System.out.println(s1 + "---" + s2);// hello---world
		change(s1, s2);
		System.out.println(s1 + "---" + s2);// hello---world

		StringBuffer sb1 = new StringBuffer("hello");
		StringBuffer sb2 = new StringBuffer("world");
		System.out.println(sb1 + "---" + sb2);// hello---world
		change(sb1, sb2);
		System.out.println(sb1 + "---" + sb2);// hello---worldworld

	}

	public static void change(StringBuffer sb1, StringBuffer sb2) {
		sb1 = sb2;
		sb2.append(sb1);
	}

	public static void change(String s1, String s2) {
		s1 = s2;
		s2 = s1 + s2;
	}
}
```

>  形式参数：
>
>   		基本类型：形式参数的改变不影响实际参数
>
>   		引用类型：形式参数的改变直接影响实际参数

而string是不可变的，虽然是引用类型但是在做形式参数是当成基本类型来看；String作为参数传递，效果和基本类型作为参数传递是一样的。

而StringBuffer作为参数传递，基本赋值操作不会改变数值，但是调用方法时会改变数值。

## Array高级

### 冒泡算法

```java
		for (int x = 0; x < arr.length - 1; x++) {
			for (int y = 0; y < arr.length - 1 - x; y++) {
				if (arr[y] > arr[y + 1]) {
					int temp = arr[y];
					arr[y] = arr[y + 1];
					arr[y + 1] = temp;
				}
			}
		}
```

### 选择排序

```java
		for(int x=0; x<arr.length-1; x++){
			for(int y=x+1; y<arr.length; y++){
				if(arr[y] <arr[x]){
					int temp = arr[x];
					arr[x] = arr[y];
					 arr[y] = temp;
				}
			}
		}
```

### 练习

把字符串中的字符进行排序。举例："dacgebf" 结果："abcdefg" 

```java
public class ArrayTest {
	public static void main(String[] args) {
		// 定义一个字符串
		String s = "dacgebf";

		// 把字符串转换为字符数组
		char[] chs = s.toCharArray();

		// 把字符数组进行排序
		bubbleSort(chs);

		//把排序后的字符数组转成字符串
		String result = String.valueOf(chs);
		
		//输出最后的字符串
		System.out.println("result:"+result);
	}

	// 冒泡排序
	public static void bubbleSort(char[] chs) {
		for (int x = 0; x < chs.length - 1; x++) {
			for (int y = 0; y < chs.length - 1 - x; y++) {
				if (chs[y] > chs[y + 1]) {
					char temp = chs[y];
					chs[y] = chs[y + 1];
					chs[y + 1] = temp;
				}
			}
		}
	}
}
```

### 二分查找

> 数组元素有序

```java
public class ArrayDemo {
	public static void main(String[] args) {
		//定义一个数组
		int[] arr = {11,22,33,44,55,66,77};
		
		//写功能实现
		int index = getIndex(arr, 33);
		System.out.println("index:"+index);
		
		//假如这个元素不存在后有什么现象呢?
		index = getIndex(arr, 333);
		System.out.println("index:"+index);
	}
	
	/*
	 * 两个明确：
	 * 返回值类型：int
	 * 参数列表：int[] arr,int value
	 */
	public static int getIndex(int[] arr,int value){
		//定义最大索引，最小索引
		int max = arr.length -1;
		int min = 0;
		
		//计算出中间索引
		int mid = (max +min)/2;
		
		//拿中间索引的值和要查找的值进行比较
		while(arr[mid] != value){
			if(arr[mid]>value){
				max = mid - 1;
			}else if(arr[mid]<value){
				min = mid + 1;
			}
			
			//加入判断
			if(min > max){
				return -1;
			}
			
			mid = (max +min)/2;
		}
		
		return mid;
	}
}
```

## Arrays类

### 方法

- public static String toString(Object[] a)把任意类型转换成string类型
- public static void sort(Object[] a)对任意数组进行升序排序，底层是快速排序
- public static int binarySearch(Object[] a,Object key)二分查找

>开发原则：只要是对象，我们就要判断该对象是否为null。
>
>​	if (a == null)
>​		return "null";

### 源码

- toString源码:

```java
    public static String toString(int[] a) {
        if (a == null)
            return "null";
        int iMax = a.length - 1;
        if (iMax == -1)
            return "[]";

        StringBuilder b = new StringBuilder();
        b.append('[');
        for (int i = 0; ; i++) {
            b.append(a[i]);
            if (i == iMax)
                return b.append(']').toString();
            b.append(", ");
        }
    }
```

在for循环中不写条件那么永远是true；

- binarySearch二分查找法源码：

```java
    private static int binarySearch0(int[] a, int fromIndex, int toIndex,
                                     int key) {
        int low = fromIndex;
        int high = toIndex - 1;

        while (low <= high) {
            int mid = (low + high) >>> 1;
            int midVal = a[mid];

            if (midVal < key)
                low = mid + 1;
            else if (midVal > key)
                high = mid - 1;
            else
                return mid; // key found
        }
        return -(low + 1);  // key not found.
    }
```

## 基本类型的包装类

把基本数据类型封装成对象的好处是可以在对象中定义更多的方法操作该数据

| 基本数据类型  |  包装数据类型   |
| :-----: | :-------: |
|  byte   |   Byte    |
|  short  |   Short   |
|   int   |  Integer  |
|  long   |   Long    |
| double  |  Double   |
|  float  |   Float   |
|  char   | Character |
| boolean |  Boolean  |

用于基本数据类型与字符串之间的相互转换

### Integer类

#### 构造方法

- public Integer(int value)
- public Integer(String s)**这个字符串必须是由数字字符组成**

#### 成员方法

- int到String的方法

```java
		// int -- String
		int number = 100;
		// 方式1
		String s1 = "" + number;
		System.out.println("s1:" + s1);
		// 方式2
		String s2 = String.valueOf(number);
		System.out.println("s2:" + s2);
		// 方式3
		// int -- Integer -- String
		Integer i = new Integer(number);
		String s3 = i.toString();
		System.out.println("s3:" + s3);
		// 方式4
		// public static String toString(int i)
		String s4 = Integer.toString(number);
		System.out.println("s4:" + s4);
		System.out.println("-----------------");
```

- string到int

```java
		String s = "100";
		// 方式1
		// String -- Integer -- int
		Integer ii = new Integer(s);
		// public int intValue()
		int x = ii.intValue();
		System.out.println("x:" + x);
		//方式2
		//public static int parseInt(String s)
		int y = Integer.parseInt(s);
		System.out.println("y:"+y);
```

```java
推荐使用：  
int -- String
  		String.valueOf(number)
  
  String -- int
  		Integer.parseInt(s)
```

- 进制转换

```java
  public static String toBinaryString(int i)
  public static String toOctalString(int i)
  public static String toHexString(int i)
  
  十进制到其他进制
  public static String toString(int i,int radix)
  由这个我们也看到了进制的范围：2-36
  为什么呢?0,...9,a...z相加共36个
  
  其他进制到十进制
  public static int parseInt(String s,int radix)
```

#### JDK5的新特性

- Integer x = new Integer(4);可以直接写成
- Integer x = 4;//自动装箱。
- x = x + 5;//自动拆箱。通过intValue方法。

>  自动装箱：把基本类型转换为包装类类型
>
>   自动拆箱：把包装类类型转换为基本类型

  注意一个小问题：
  		在使用时，Integer  x = null;代码就会出现NullPointerException。因为会自动包装成一个对象
  		建议先判断是否为null，然后再使用。

```java
		// 定义了一个int类型的包装类类型变量i
		// Integer i = new Integer(100);
		Integer ii = 100;
		ii += 200;
		System.out.println("ii:" + ii);

		// 通过反编译后的代码
		// Integer ii = Integer.valueOf(100); //自动装箱
		// ii = Integer.valueOf(ii.intValue() + 200); //自动拆箱，再自动装箱
		// System.out.println((new StringBuilder("ii:")).append(ii).toString());
```

#### 练习题>

```java
		Integer i1 = new Integer(127);
		Integer i2 = new Integer(127);
		System.out.println(i1 == i2);
		System.out.println(i1.equals(i2));
		System.out.println("-----------");

		Integer i3 = new Integer(128);
		Integer i4 = new Integer(128);
		System.out.println(i3 == i4);
		System.out.println(i3.equals(i4));
		System.out.println("-----------");

		Integer i5 = 128;
		Integer i6 = 128;
		System.out.println(i5 == i6);
		System.out.println(i5.equals(i6));
		System.out.println("-----------");

		Integer i7 = 127;
		Integer i8 = 127;
		System.out.println(i7 == i8);
		System.out.println(i7.equals(i8));
```

> 注意：Integer的数据直接赋值，如果在-128到127之间，会直接从缓冲池里获取数据，也就是引用相的地址

## Character

### 构造方法

> Character(char value). 

### 常用方法

* public static boolean isUpperCase(char ch):判断给定的字符是否是大写字符
* public static boolean isLowerCase(char ch):判断给定的字符是否是小写字符
* public static boolean isDigit(char ch):判断给定的字符是否是数字字符
* public static char toUpperCase(char ch):把给定的字符转换为大写字符
* public static char toLowerCase(char ch):把给定的字符转换为小写字符

### 练习

统计一个字符串中大写字母字符，小写字母字符，数字字符出现的次数。(不考虑其他字符)

```java
public class CharacterTest {
	public static void main(String[] args) {
		// 定义三个统计变量。
		int bigCount = 0;
		int smallCount = 0;
		int numberCount = 0;

		// 键盘录入一个字符串。
		Scanner sc = new Scanner(System.in);
		System.out.println("请输入一个字符串：");
		String line = sc.nextLine();

		// 把字符串转换为字符数组。
		char[] chs = line.toCharArray();

		// 历字符数组获取到每一个字符
		for (int x = 0; x < chs.length; x++) {
			char ch = chs[x];

			// 判断该字符
			if (Character.isUpperCase(ch)) {
				bigCount++;
			} else if (Character.isLowerCase(ch)) {
				smallCount++;
			} else if (Character.isDigit(ch)) {
				numberCount++;
			}
		}

		// 输出结果即可
		System.out.println("大写字母：" + bigCount + "个");
		System.out.println("小写字母：" + smallCount + "个");
		System.out.println("数字字符：" + numberCount + "个");
	}
}

```