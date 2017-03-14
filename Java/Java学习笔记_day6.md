# Java学习笔记

[TOC]


## 二维数组

### 表示方法

```java
		A:数据类型[][] 数组名 = new 数据类型[m][n];
		B:数据类型[][] 数组名 = new 数据类型[m][];
		C:数据类型[][] 数组名 = new 数据类型[][]{{...},{...},{...}};
		D:数据类型[][] 数组名 = {{...},{...},{...}};
```

### 二维数组的内存示意图

![堆](http://oaxelf1sk.bkt.clouddn.com/堆.png)

> 在这里首先先初始化最外层的一位数组（引用类型，初始值是null），然后内层数组开始初始化（默认是0），并且把内层的地址值给外层的数组，外层再把总地址值交给栈。

**注意**

```java
int[] array = {4,5,6,7,9,8};
```

这里的数组在内存中是在堆中，因为他省略了new。

## 练习题

- 求出下面打印的语句

```java
class ArgsDemo {
	public static void main(String[] args) {
		int a = 10;
		int b = 20;
		System.out.println("a:"+a+",b:"+b); //a:10,b:20
		change(a,b);
		System.out.println("a:"+a+",b:"+b); //???	a:10,b:20

		int[] arr = {1,2,3,4,5}; 
		change(arr);
		System.out.println(arr[1]); //???	4
	}

	public static void change(int a,int b) { //a=10,b=20
		System.out.println("a:"+a+",b:"+b); //a:10,b:20
		a = b;	//a=20
		b = a + b; //b=40
		System.out.println("a:"+a+",b:"+b); //a:20,b:40
	}

	public static void change(int[] arr) { //arr={1,2,3,4,5};
		for(int x=0; x<arr.length; x++) {
			if(arr[x]%2==0) {
				arr[x]*=2;
			}
		}
		//arr={1,4,3,8,5};
	}
}
```

写出各处打印的值是多少？？？？

**参考答案**

> **基本数据类型由实际参数传递给形参时传递的是值，引用数据类型传递的是数据的引用**

我们可以画出它的内存图：

![ice_screenshot_20160726-163627](http://oaxelf1sk.bkt.clouddn.com/ice_screenshot_20160726-163627.png)



每个在栈内的方法有他的数据，区别只是栈内的数据的来源，一个是数值，一个是引用的地址。

> 基本类型：形式参数的改变对实际参数没有影响。	
>
> 引用类型：形式参数的改变直接影响实际参数。

- 某个公司采用公用电话传递数据信息，数据是小于8位的整数，为了确保安全，传递过程中需要加密，加密规则如下：首先将数据倒序，然后将每位数字都加上5，再用和除以10的余数代替该数字，最后将第一位和最后一位数字交换。 请任意给定一个小于8位的整数，然后，把加密后的结果在控制台打印出来。 



> 分析可知：
>
> 首先确定位数，然后把数字转换成数组，在进行操作

```java
	public static void main(String[] args) {
		//定义一个数据
		int number = 123456;
		
		//定义一个数组
		int[] arr = new int[8];
		
		//把数据中每一位上的数据获取到后存储到数组中
		/*
		int index = 0;
		arr[index] = number%10; //arr[0]=6;
		index++;
		arr[index] = number/10%10; //arr[1]=5;
		index++;
		arr[index] = mumber/10/10%10; //arr[2]=4;
		*/
		
		//通过观察这个代码，我们发现应该是可以通过循环改进的
		int index = 0;
		
		while(number > 0) { //number=123456,number=12345,number=1234,number=123,number=12,number=1,number=0
			arr[index] = number%10; //arr[0]=6,arr[1]=5,arr[2]=4,arr[3]=3,arr[4]=2,arr[5]=1
			index++;//index=1,index=2,index=3,index=4,index=5,index=6
			number/=10;//number=12345,number=1234,number=123,number=12,number=1,number=0
		}
		
		//然后将每位数字都加上5，再用和除以10的余数代替该数字
		for(int x=0; x<index; x++) {
			arr[x] += 5;
			arr[x] %= 10;
		}
		
		//最后将第一位和最后一位数字交换
		int temp = arr[0];
		arr[0] = arr[index-1];
		arr[index-1] = temp;
		
		//输出数据
		for(int x=0; x<index; x++) {
			System.out.print(arr[x]);
		}
		System.out.println();
	}
```

这里的重点是把int型的整数每个数字提取出来：

```java
        int num = 12345679;
        int index = 0;
        int[] array = new int[9];
        while (num > 0) {
            array[index] = num % 10;
            num /= 10;
            index++;
        }
```

改进后的代码是：

```java
	public static void main(String[] args) {
		//创建键盘录入对象
		Scanner sc = new Scanner(System.in);
		
		//请输入一个数据
		System.out.println("请输入一个数据(小于8位)：");
		int number = sc.nextInt();
		
		//写功能实现把number进行加密
		//调用
		String result = jiaMi(number);
		System.out.println("加密后的结果是："+result);
	}
	
	/*
		需求：写一个功能，把数据number实现加密。
		两个明确：
			返回值类型：String 做一个字符串的拼接。
			参数列表：int number
	*/
	public static String jiaMi(int number) {
		//定义数组
		int[] arr = new int[8];
		
		//定义索引
		int index = 0;
		
		//把number中的数据想办法放到数组中
		while(number > 0) {
			arr[index] = number%10;
			index++;
			number /= 10;
		}
		
		//把每个数据加5，然后对10取得余数
		for(int x=0; x<index; x++) {
			arr[x] += 5;
			arr[x] %= 10;
		}
		
		//把第一位和最后一位交换
		int temp = arr[0];
		arr[0] = arr[index-1];
		arr[index-1] = temp;
		
		//把数组的元素拼接成一个字符串返回
		//定义一个空内容字符串
		String s = "";
		
		for(int x=0; x<index; x++) {
			s += arr[x];
		}
		
		return s;
	}
```

善于运用while，注意while的条件

## 面向对象

### 面向对象特称

- 封装(encapsulation)
- 继承(inheritance)
- 多态(polymorphism)

### 类和对象的关系

> 类是一组相关的属相（成员属性）和行为（成员行为）的集合，是一个抽象概念，对象是该类的具体表现形式，具体存在的个体。

### 对象的内存图

- 一个对象的内存图

```java
class Phone {
	//品牌
	String brand;
	//价格
	int price;
	//颜色
	String color;
	
	//打电话的方法
	public void call(String name) {
		System.out.println("给"+name+"打电话");
	}
	
	//发短信的方法
	public void sendMessage() {
		System.out.println("群发短信");
	}
	
	//玩游戏的方法
	public void playGame() {
		System.out.println("玩游戏");
	}
}

class PhoneDemo {
	public static void main(String[] args) {
		//创建手机对象
		//类名 对象名 = new 类名();
		Phone p = new Phone();
		
		//直接输出成员变量值
		System.out.println(p.brand+"---"+p.price+"---"+p.color);
		
		//给成员变量赋值
		p.brand = "诺基亚";
		p.price = 100;
		p.color = "灰色";
		//再次输出
		System.out.println(p.brand+"---"+p.price+"---"+p.color);
		
		//调用方法
		p.call("林青霞");
		p.sendMessage();
		p.playGame();
	}
}
```

上图创建两个类，而且都用含有方法，所以应该在方法区开辟空间，开始执行main方法，遇到类名和对象名开始声明变量，所以main方法入栈，在main方法中开辟p对象的空间，而且看到new，所以在堆中开辟new Phone的空间，此时的初始值是brand是null，price是0，color是null，打印结果；

然后由栈中的对象p控制phone的索引值，给堆中的元素值，此时堆中的元素有值，打印输出结果；

p对象调用方法，所以方法区中的方法入栈，执行操作，当方法完成之后，销毁方法，释放内存；

最后mian方法被销毁，内存释放。

![ice_screenshot_20160726-163742](http://oaxelf1sk.bkt.clouddn.com/ice_screenshot_20160726-163742.png)

- 三个对象的内存示意图

```java
//修改上面的代码
class PhoneDemo {
	public static void main(String[] args) {
		//创建手机对象
		//类名 对象名 = new 类名();
		Phone p = new Phone();
		
		//直接输出成员变量值
		System.out.println(p.brand+"---"+p.price+"---"+p.color);
		
		//给成员变量赋值
		p.brand = "诺基亚";
		p.price = 100;
		p.color = "灰色";
		//再次输出
		System.out.println(p.brand+"---"+p.price+"---"+p.color);
		
		//调用方法
		p.call("林青霞");
		p.sendMessage();
		p.playGame();
		
		Phone p2 = p；
		p2.brand = "iphone";
		p2.price = 5000;
		p2.color = "green";
		System.out.println(p.brand+"---"+p.price+"---"+p.color);
	}
}
```

**注意**

只有在上述代码中Phone p2 = p；这样才是吧p的引用传递给p2，加入是：

```java
		p2.brand = p.brand;
```

这样只是传递一个值，而没有把引用传递给p2，当p2操作时，p对象对应堆内的数据值不变
