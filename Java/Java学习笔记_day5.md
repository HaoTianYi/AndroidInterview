# Java学习笔记

[TOC]

## 数组

### 定义格式

- 数据类型[]   数组名
- 数据类型     数组名[ ]

### 数组的初始化

- 动态初始化:只是指定长度由系统给出初始化值

> 数据类型【】 数组名 =  new    数据类型【数组长度】；

- 静态初始化：只是给定初始化值，长度由系统指定

> ```java
> int[] arr = new int[]{1,2,3};	//正常的初始化方式
> int[] arr = {1,2,3};	//简化的初始化方式
> ```

数组的生命有两种，初始化有两种，共有四种

## java中的内存分配

> java为了提高程序的运行效率，就对数据进行不同的空间分配，具体有5个内存空间。

- 栈：存放的是局部变量
- 堆：存放的是所有new出来的变量
- 方法区
- 本地寄存器：系统用
- 寄存器：CPU使用

### 栈

> 局部变量：在方法或者方法声明中的变量都称为局部变量

栈中的数据用完就释放，而且栈内的数据会应用一个堆内存地址（如果有的话），

```java
{
 	int[] arraay = new int[3];
	int code = 100;
	System.out.print(array);
}
```

在上面中有如下的内存图: 

![ice_screenshot_20160726-163022](http://oaxelf1sk.bkt.clouddn.com/ice_screenshot_20160726-163022.png)



所以在打印时会显示array的内存地址，也就是一个16进制的地址。

### 堆

- 每一个new出的数据都有一个内存地址

- 每一个变量都由默认变量（假设数组由动态初始化）

  byte，short，int，long都是0

  float，double是0.0

  char是   \u0000   代表了一个空格

  boolean是false

  所有的引用类型都是 null

- 所有的数据使用完成之后就变成了垃圾，但是没有立即回收，而是在垃圾回收器空闲的时候在回收，和栈不同，栈在使用完成之后会立即回收。

### 栈和堆的联系

	定义第一个数组,定义完毕后，给数组元素赋值。赋值完毕后，在输出数组名称和元素。
	定义第二个数组,定义完毕后，给数组元素赋值。赋值完毕后，在输出数组名称和元素。
	定义第三个数组,把第一个数组的地址值赋值给它。(注意类型一致)，通过第三个数组的名称去把元素重复赋值。
	最后，再次输出第一个数组数组名称和元素。
代码如下：

```java
		//定义第一个数组
		int[] arr = new int[3];
		arr[0] = 88;
		arr[1] = 33;
		arr[2] = 66;
		System.out.println(arr);
		System.out.println(arr[0]);
		System.out.println(arr[1]);
		System.out.println(arr[2]);
		System.out.println("----");
		
		//定义第二个数组
		int[] arr2 = new int[3];
		arr2[0] = 22;
		arr2[1] = 44;
		arr2[2] = 55;
		System.out.println(arr2);
		System.out.println(arr2[0]);
		System.out.println(arr2[1]);
		System.out.println(arr2[2]);
		System.out.println("----");
		
		//定义第三个数组
		int[] arr3 =  arr;
		arr3[0] = 100;
		arr3[1] = 200;
		System.out.println(arr);
		System.out.println(arr[0]);
		System.out.println(arr[1]);
		System.out.println(arr[2]);
```

  ![ice_screenshot_20160726-163318](D:\picture\ice_screenshot_20160726-163318.png)



**栈中的两个引用指向一个内存地址，无论他们谁的操作，都会对堆中的数据进行更改**

## 数组操作的两个常见错误

- ArrayIndexOutOfBoundsException

  访问到了数组中的不存在的索引时发生

  数组索引越界

- NullPointerException

  空指针异常

##数组常见的操作

### 把数组逆序

**方法一**

```java
		for(int i = 0;i < arr.length/2 ; i++){
			int temp = arr[i];
			arr[i] = arr[arr.length-1-i];
			arr[arr.length-1-i] = temp;
		}
```

> 本质就是利用两个数之间的互换算法，利用一个中间数据缓存



**方法二**

```java
		for(int start = 0,end = arr.length-1 ; start <= end ; start++,end-- ){
			int temp = arr[start];
			arr[start] = arr[end];
			arr[end] = temp;
		}
```

> 和上一个只是两者的计算步骤不同，但是还是利用两个数的互换算法

