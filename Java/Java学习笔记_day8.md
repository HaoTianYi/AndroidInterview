# Java学习笔记

[TOC]

## javaDoc使用

> 说明：使用/**    */ 可以生成说明文档

**制作过程**

		A:写一个工具类
		B:对这个类加入文档注释		
		C:用工具解析文档注释
			javadoc工具
		D:格式
			javadoc -d 目录 -author -version ArrayTool.java
其中文件头是@author 和@version

方法前是@param和@return

> java.lang包不需要导包，看api时要看版本
>
> math.random()方法返回大于等于0小于1

## 代码块

- 局部代码块

用于限定变量的生命周期

- 构造代码块

在类的成员变量的位置，用{}括起来，作用是可以吧多个构造方法的共同代码放到一起，对对象进行初始化，每次新建对象都会执行这个构造代码块。

- 静态代码块

在类的成员变量的位置，用{}括起来，用static修饰，作用是对类进行初始化，因为静态随着类的加载而加载。

> 三者的执行顺序是：静态代码块---》构造代码块-----》**构造方法**

示例：

```java
class Student {
	static {
		System.out.println("Student 静态代码块");
	}
	
	{
		System.out.println("Student 构造代码块");
	}
	
	public Student() {
		System.out.println("Student 构造方法");
	}
}

class StudentDemo {
	static {
		System.out.println("林青霞都60了，我很伤心");
	}
	
	public static void main(String[] args) {
		System.out.println("我是main方法");
		
		Student s1 = new Student();
		Student s2 = new Student();
	}
}
```

内存流程：

1. 加载StudentDemo类，随着类的加载静态代码块执行；
2. 加载main方法，打印；
3. 加载Student类，随着类的加载静态代码块执行；
4. 在栈中为s1开辟内存空间；
5. 在堆中为new Student（）申请空间，同时执行构造代码块；
6. 默认初始化和显示初始化；
7. 调用构造方法，在打印语句；
8. 把堆内存地址赋值给s1；

## 继承

### 优势

- 提高了代码的复用性

  多个类相同的成员可以放到同一个类中

- 提高了代码的维护性

  如果功能的代码需要修改，修改一处即可

- 让类与类之间产生了关系，是多态的前提

  其实这也是继承的一个弊端：类的耦合性很强

> 开发时要低耦合，高内聚

### 特点

1. java只是支持单继承不支持多继承
2. java支持多级继承
3. 只能继承非私有的成员（成员变量和成员方法），其实这也是一个弊端---》打破了封装性；
4. 子类不能继承父类的构造方法，但是可以通过super来访问；

### this和super的用法

1. 访问成员变量：this.成员变量	super.成员变量
2. 访问构造方法：this（参数）       super（参数）
3. 访问成员方法：this.成员方法      super.成员方法

### 继承中构造方法的关系

> 子类所有的构造方法（无论子类有无参数）默认都会先访问父类的无参构造方法
>
> 子类的每一个构造方法默认语句都是：super（）；

### 父类中没有无参构造方法的错误

当类中自己没有写构造方法时，系统会自己添加一个无参构造方法但是当用户自己添加一个带参的构造方法，那么由于子类的所有构造方法都要访问父类的无参构造方法（此时没有），那么编译就会报错。

解决方法：

1. 使用super显示调用父类的带参构造方法；
2. 在父类中添加无参构造方法；
3. 还可以用this（）和super（）结合的方式；

> 在写类时，应该养成良好的编程习惯，主动添加无参构造方法 

**this或者super调用构造方法时必须放在第一条语句，如果没有放在第一条语句就可能对父类的数据进行多次初始化**

### 练习题

####父类和子类的初始化流程

代码如下：

```java
class Fu {
	static {
		System.out.println("静态代码块Fu");
	}

	{
		System.out.println("构造代码块Fu");
	}

	public Fu() {
		System.out.println("构造方法Fu");
	}
}

class Zi extends Fu {
	static {
		System.out.println("静态代码块Zi");
	}

	{
		System.out.println("构造代码块Zi");
	}

	public Zi() {
		System.out.println("构造方法Zi");
	}
}

class ExtendsTest {
	public static void main(String[] args) {
		Zi z = new Zi();
	}
}
```

- 一个类的初始化顺序：静态代码块---》构造代码块---》构造方法；
- 多个类的初始化顺序：

1. 首先是子类加载，但是此时有extends父类所以先加载父类，执行父类的静态代码块；
2. 加载子类，执行子类的静态代码块；
3. 看到new开始在堆中初始化数据；
4. 执行子类的构造方法，但是会默认执行父类的构造方法，在父类的构造方法之前会执行构造代码块；
5. 然后执行父类的构造方法；
6. 回到子类，执行子类的构造代码块，再执行子类的构造方法；

执行结构：

静态代码块Fu
静态代码块Zi
构造代码块Fu
构造方法Fu
构造代码块Zi
构造方法Zi

#### 看程序写结果

```java
class X {
	Y b = new Y();
	X() {
		System.out.print("X");
	}
}

class Y {
	Y() {
		System.out.print("Y");
	}
}

public class Z extends X {
	Y y = new Y();
	Z() {
		//super
		System.out.print("Z");
	}
	public static void main(String[] args) {
		new Z(); 
	}
}
```

一个类的初始化会首先初始化静态代码块，然后初始化成员变量（成员变量有基本变量和引用变量），成员变量的初始化会首先进行默认初始化，然后是显示初始化，最后是构造方法初始化；

分析类的初始化，应该首先从main方法开始：

1. 初始化z，看到z有extends所以应该先初始化x；
2. x初始化成员变量，初始化y；
3. y初始化成员变量打印y；
4. 回到x，打印x；
5. 回到z，看到new y（）；
6. 初始化y，打印语句；
7. 执行构造方法，打印z；

最后结果是：yxyz

### 重写Override

子类中出现和父类一模一样的方法（包扩方法声明和方法参数）

> 方法重载时在本类中出现的方法名一样，参数类列表不同的方法，与返回值无关；
>
> Override：方法重写
> Overload：方法重载

#### 注意事项

- 父类私有的方法不能重写
- 重写父类方法时访问权限不能更低，最好是一致；
- 父类静态方法，子类必须通过静态实现重写

