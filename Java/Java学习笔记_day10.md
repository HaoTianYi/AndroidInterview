# Java学习笔记_day10

[TOC]

## 形式参数和返回值

- 形式参数：

1. 基本数据类型

2. 引用数据类型

   1. 类：要求是该类的对象
   2. 接口：接口也可以new出对象，所以可以作为形式参数传递给方法，一般是传入一个该接口的实现类的对象，同样利用了多态；
   3. 抽象类：类可以new出对象，所以可以作为形式参数传递给方法，一抽般用法是把抽象类作为形式参数，但是传入**一个抽象类的实现类**的对象；（利用了多态）

   示例代码：

   ```java
   interface Love {
   	public abstract void love();
   }

   class LoveDemo {
   	public void method(Love l) { //l; l = new Teacher();  Love l = new Teacher(); 多态
   		l.love();
   	}
   }

   //定义具体类实现接口
   class Teacher implements Love {
   	public void love() {
   		System.out.println("老师爱学生,爱Java,爱林青霞");
   	}
   }

   class TeacherTest {
   	public static void main(String[] args) {
   		//需求：我要测试LoveDemo类中的love()方法
   		LoveDemo ld = new LoveDemo();
   		Love l = new Teacher();
   		ld.method(l);
   	}
   }
   ```

- 返回值

1. 基本数据类型
2. 引用数据类型
   1. 类：返回的是该类的对象
   2. 抽象类：返回的是该类的实现类的对象
   3. 接口：返回的是该类的子类的实现对象

示例代码：

```java
interface Love {
	public abstract void love();
}

class LoveDemo {
	public Love getLove() {
		//Love l = new Teacher();
		//return l;
		
		return new Teacher();
	}
}

class Teacher1 implements Love {
	public void love() {
		System.out.println("老师爱学生,爱Java,爱林青霞1111111111");
	}
}
//定义具体类实现接口
class Teacher implements Love {
	public void love() {
		System.out.println("老师爱学生,爱Java,爱林青霞2222222");
	}
}

class ExtendsTest {
	public static void main(String[] args) {
		//如何测试呢?
		LoveDemo ld = new LoveDemo();
		Love l = ld.getLove(); //new Teacher(); Love l = new Teacher(); 多态
		l.love();
	}
}
```

这里由于LoveDemo的getLove方法返回的是Teacher类型的对象，所以多态的l对象会自动调用love的实现类Teacher；

如果在getLove方法返回的是new Love那么系统会自动帮你创建一个匿名的实现类的对象；

## 包

> package语句必须是程序的第一条可执行的代码，package语句在一个class文件中只能有一个，如果没有package语句标示默认无包；

### 带有包的java文件的运行

- 手动
  1. 编写带有package语句的java文件
  2. javac编译，并且手动创建包
  3. 把class放进包中，回到最顶层包的根目录
  4. java运行**是java 包名 文件名**
- 自动
  1. javac编译时加入**javac  -d   .  文件名.java**
  2. 只是自动创建包然后在**java 包名  文件名**运行

 ## 权限修饰符

![Java学习笔记_day10_权限修饰符](http://oaxelf1sk.bkt.clouddn.com/Java学习笔记_day10_权限修饰符.png)

权限修饰符：public protect  默认  private

状态修饰符：static  final 

抽象修饰符：abstract

类：

​	权限修饰符：默认，public（private不可以）**内部类可以用private修饰**

​	状态修饰符：final（static不可以）**内部类也可以用static**

​	抽象修饰符：abstract 

​	内部类就相当于外部类的一个成员变量，所以可以使用private和static修饰

成员变量

​	权限修饰符：都可以

​	状态修饰符：都可以

​	抽象修饰符：不可以

成员方法

​	权限修饰符：都可以

​	状态修饰符：都可以

​	抽象修饰符：都可以

## 内部类

### 访问特点

1. 内部类可以直接访问外部类的成员变量，包括私有变量；
2. 外部类不可以直接访问内部类的成员，一定要创建对象；

### 内部类的位置

1. 成员内部类
2. 局部内部类（在方法里定义的内部类）

### 访问不同内部类内的方法

- 成员内部类：

```java
	//外部类名.内部类名 对象名 = 外部类对象.内部类对象;
	Outer.Inner oi = new Outer().new Inner();

	//成员内部类被static修饰后的访问方式是：
	//外部类名.内部类名 对象名 = 外部类名.内部类名();
	Outer.Inner oi = new Outor.Inner();
	//或者是外部类名.内部类名.方法名
	Outer.Inner.show();
```

- 局部内部类：

```java
	//在局部位置，可以创建局部类对象，通过对象调用内部类方法
	//局部内部类访问局部变量，局部变量一定要使用fianl修饰局部变量
```

使用final的原因：

​	因为局部变量会随着方法的调用完毕而销毁，这时候局部对象没有立马从堆内存中消失，还会用到局部变量，所以为了能让局部变量继续使用，就用fianl修饰，这样在堆内存中其实存储的是一个常量值。

### 不同内部类的修饰符

- 成员内部类

  成员内部类可以用static修饰，此时常用是public static，相当于外部类的一个局部变量；

  局部内部类可以用private修饰，相当于外部类的私有成员变量；

  被静态修饰的成员内部类只能访问外部类的静态成员

### 内部类的面试题

- 局部内部类

```java
补齐程序(注意:内部类和外部类没有继承关系)
	class Outer {
		public int num = 10;
		class Inner {
			public int num = 20;
			public void show() {
				int num = 30;
				System.out.println(?);
				System.out.println(??);
				System.out.println(???);
			}
		}
	}
在控制分别输出：30，20，10
```

参考答案：

```java
			System.out.println(num);
			System.out.println(this.num);
			//System.out.println(new Outer().num);
			System.out.println(Outer.this.num);
```

注意：**通过外部类名限制this**

- 匿名内部类

```java
按照要求，补齐代码
	interface Inter { void show(); }
	class Outer { //补齐代码 }
	class OuterDemo {
	    public static void main(String[] args) {
		      Outer.method().show();
		  }
	}
要求在控制台输出”HelloWorld”
```

参考答案：

```java
interface Inter { 
	void show(); 
	//public abstract
}

class Outer { 
	//补齐代码
	public static Inter method() {
		//子类对象 -- 子类匿名对象
		return new Inter() {
			public void show() {
				System.out.println("HelloWorld");
			}
		};
	}
}

class OuterDemo {
	public static void main(String[] args) {
		Outer.method().show();
		/*
			1:Outer.method()可以看出method()应该是Outer中的一个静态方法。
			2:Outer.method().show()可以看出method()方法的返回值是一个对象。
				又由于接口Inter中有一个show()方法,所以我认为method()方法的返回值类型是一个接口。
		*/
	}
}
```

### 匿名内部类

- 前提：存在一个类或者一个叫接口
- 格式是：new 类名或者接口名（）{重写方法；}
- 本质：**是一个继承了该类或者实现该接口的子类的匿名对象**
- 匿名内部类方法的调用：

```java
interface Inter {
	public abstract void show();
	public abstract void show2();
}

class Outer {
	public void method() {
		//一个方法的时候
		/*
		new Inter() {
			public void show() {
				System.out.println("show");
			}
		}.show();
		*/
		
		//二个方法的时候
		/*
		new Inter() {
			public void show() {
				System.out.println("show");
			}
			
			public void show2() {
				System.out.println("show2");
			}
		}.show();
		
		new Inter() {
			public void show() {
				System.out.println("show");
			}
			
			public void show2() {
				System.out.println("show2");
			}
		}.show2();
		*/
		
		//如果我是很多个方法，就很麻烦了
		//那么，我们有没有改进的方案呢?
		Inter i = new Inter() { //多态
			public void show() {
				System.out.println("show");
			}
			
			public void show2() {
				System.out.println("show2");
			}
		};
		
		i.show();
		i.show2();
	}
}

class InnerClassDemo6 {
	public static void main(String[] args) {
		Outer o = new Outer();
		o.method();
	}
}
```

匿名内部类的应用：

```java
interface Person {
	public abstract void study();
}

class PersonDemo {
	//接口名作为形式参数
	//其实这里需要的不是接口，而是该接口的实现类的对象
	public void method(Person p) {
		p.study();
	}
}

//实现类
class Student implements Person {
	public void study() {
		System.out.println("好好学习,天天向上");
	}
}

class InnerClassTest2 {
	public static void main(String[] args) {
		//测试
		PersonDemo pd = new PersonDemo();
		Person p = new Student();
		pd.method(p);
		System.out.println("--------------------");
		
		//匿名内部类在开发中的使用
		//匿名内部类的本质是继承类或者实现了接口的子类匿名对象
		pd.method(new Person(){
			public void study() {
				System.out.println("好好学习,天天向上");
			}
		});
	}
}
```

使用接口名作为形式参数，本质是想要一个接口的实现类对象，而匿名内部类的本质是一个实现了接口的匿名**对象**。
