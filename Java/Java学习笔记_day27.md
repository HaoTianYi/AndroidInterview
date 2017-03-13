# Java学习笔记_day27

[TOC]

## 反射reflect

在运行状态，通过任意一个对象，都能够获得该类的任意方法和属性，就是通过class文件对象，去使用该文件中的成员变量，构造方法，成员方法

### 类的加载

当程序要使用某个类但是这个类还没有加载到内存，那么系统会通过架子啊/连接/初始化来对这个类进行初始化

- 加载：就是把class文件读入内存，并创建一个对应的class对象，任何类被系统使用时都会创建一个class对象
- 连接：首先验证是否有正确的内部结构，然后是准备，此时给静态成员分配内存，并设置初始值
- 初始化

### 类的加载时机

1. 创建类的实例
2. 访问类的静态变量，或者为静态变量赋值
3. 调用类的静态方法
4. 使用反射方式来强制创建某个类或接口对应的java.lang.Class对象
5. 初始化某个类的子类
6. 直接使用java.exe命令来运行某个主类

### 类加载器

负责将.class文件加载到内在中，并为之生成对应的Class对象

#### Bootstrap ClassLoader 根类加载器

也被称为引导类加载器，负责Java核心类的加载
比如System,String等。在JDK中JRE的lib目录下rt.jar文件中

#### Extension ClassLoader 扩展类加载器

负责JRE的扩展目录中jar包的加载。
在JDK中JRE的lib目录下ext目录

#### Sysetm ClassLoader 系统类加载器

负责在JVM启动时加载来自java命令的class文件，以及classpath环境变量所指定的jar包和类路径，包扩自己写的类

### 获取class文件对象

*   Object类的getClass()方法

*   数据类型的静态属性class

*   Class类中的静态方法

      public static Class forName(String className)要带有包名

## Class类

Class 类的实例表示正在运行的 Java 应用程序中的类和接口

### 成员方法

```java
		public Constructor[] getConstructors():所有公共构造方法
		public Constructor[] getDeclaredConstructors():所有构造方法
```

注意第一个获得公共的构造方法，第二个获得所有构造方法

```java
	public static void main(String[] args) throws Exception {
		// 获取字节码文件对象
		Class c = Class.forName("cn.itcast_01.Person");

		// 获取单个构造方法
		// public Constructor<T> getConstructor(Class<?>... parameterTypes)
		// 参数表示的是：你要获取的构造方法的构造参数个数及数据类型的class字节码文件对象
		Constructor con = c.getConstructor();// 返回的是构造方法对象

		// public T newInstance(Object... initargs)
		// 使用此 Constructor 对象表示的构造方法来创建该构造方法的声明类的新实例，并用指定的初始化参数初始化该实例。
		Object obj = con.newInstance();
		System.out.println(obj);
		// 和下面两行代码含义相同Person p = new Person();
		// System.out.println(p);
	}
```

> Object obj = con.newInstance()本质就是创建一个新的Person对象

### 应用实例

#### 获得构造方法

```java
/*
 * 需求：通过反射去获取该构造方法并使用：
 * public Person(String name, int age, String address)
 * 
 * 相当于下面代码：
 * Person p = new Person("林青霞",27,"北京");
 * System.out.println(p);
 */	
	public static void main(String[] args) throws Exception {
		// 获取字节码文件对象
		Class c = Class.forName("cn.itcast_01.Person");

		// 获取带参构造方法对象
		// public Constructor<T> getConstructor(Class<?>... parameterTypes)
		Constructor con = c.getConstructor(String.class, int.class,
				String.class);

		// 通过带参构造方法对象创建对象
		// public T newInstance(Object... initargs)
		Object obj = con.newInstance("林青霞", 27, "北京");
		
		System.out.println(obj);
	}
```
####获得私有构造方法
```java
/*
 * 需求：通过反射获取私有构造方法并使用
 * private Person(String name){}
 * 
 * Person p = new Person("风清扬");
 * System.out.println(p);
 */
public class ReflectDemo3 {
	public static void main(String[] args) throws Exception {
		// 获取字节码文件对象
		Class c = Class.forName("cn.itcast_01.Person");

		// 获取私有构造方法对象
		// NoSuchMethodException：没有这个方法异常
		// 原因是一开始我们使用的方法只能获取公共的，下面这种方式就可以了。
		Constructor con = c.getDeclaredConstructor(String.class);

		// 用该私有构造方法创建对象
		// IllegalAccessException:非法的访问异常。
		// 暴力访问
		con.setAccessible(true);// 值为true则指示反射的对象在使用时应该取消Java语言访问检查。
		Object obj = con.newInstance("风清扬");

		System.out.println(obj);
	}
}
```
#### 获得成员变量

```java
/*
 * 通过发生获取成员变量并使用
 */
public class ReflectDemo {
	public static void main(String[] args) throws Exception {
		// 获取字节码文件对象
		Class c = Class.forName("cn.itcast_01.Person");

		// 获取所有的成员变量
		// Field[] fields = c.getFields();
		// Field[] fields = c.getDeclaredFields();
		// for (Field field : fields) {
		// System.out.println(field);
		// }

		/*
		 * Person p = new Person(); p.address = "北京"; System.out.println(p);
		 */

		// 通过无参构造方法创建对象
		Constructor con = c.getConstructor();
		Object obj = con.newInstance();
		System.out.println(obj);

		// 获取单个的成员变量
		// 获取address并对其赋值
		Field addressField = c.getField("address");
		// public void set(Object obj,Object value)
		// 将指定对象变量上此 Field 对象表示的字段设置为指定的新值。
		addressField.set(obj, "北京"); // 给obj对象的addressField字段设置值为"北京"
		System.out.println(obj);

		// 获取name并对其赋值
		// NoSuchFieldException
		Field nameField = c.getDeclaredField("name");
		// IllegalAccessException
		nameField.setAccessible(true);
		nameField.set(obj, "林青霞");
		System.out.println(obj);

		// 获取age并对其赋值
		Field ageField = c.getDeclaredField("age");
		ageField.setAccessible(true);
		ageField.set(obj, 27);
		System.out.println(obj);
	}
}

```

#### 获得方法

```java
	public static void main(String[] args) throws Exception {
		// 获取字节码文件对象
		Class c = Class.forName("cn.itcast_01.Person");

		// 获取所有的方法
		// Method[] methods = c.getMethods(); // 获取自己的包括父亲的公共方法
		// Method[] methods = c.getDeclaredMethods(); // 获取自己的所有的方法
		// for (Method method : methods) {
		// System.out.println(method);
		// }

		Constructor con = c.getConstructor();
		Object obj = con.newInstance();

		/*
		 * Person p = new Person(); p.show();
		 */

		// 获取单个方法并使用
		// public void show()
		// public Method getMethod(String name,Class<?>... parameterTypes)
		// 第一个参数表示的方法名，第二个参数表示的是方法的参数的class类型
		Method m1 = c.getMethod("show");
		// obj.m1(); // 错误
		// public Object invoke(Object obj,Object... args)
		// 返回值是Object接收,第一个参数表示对象是谁，第二参数表示调用该方法的实际参数
		m1.invoke(obj); // 调用obj对象的m1方法

		System.out.println("----------");
		// public void method(String s)
		Method m2 = c.getMethod("method", String.class);
		m2.invoke(obj, "hello");
		System.out.println("----------");

		// public String getString(String s, int i)
		Method m3 = c.getMethod("getString", String.class, int.class);
		Object objString = m3.invoke(obj, "hello", 100);
		System.out.println(objString);
		// String s = (String)m3.invoke(obj, "hello",100);
		// System.out.println(s);
		System.out.println("----------");

		// private void function()
		Method m4 = c.getDeclaredMethod("function");
		m4.setAccessible(true);
		m4.invoke(obj);
	}
```

#### 通过配置文件方便升级代码

```java
/*
 * 通过配置文件运行类中的方法
 * 
 * 反射：
 * 		需要有配置文件配合使用。
 * 		用class.txt代替。
 * 		并且你知道有两个键。
 * 			className
 * 			methodName
 */
public class Test {
	public static void main(String[] args) throws Exception {
		// 反射前的做法
		// Student s = new Student();
		// s.love();
		// Teacher t = new Teacher();
		// t.love();
		// Worker w = new Worker();
		// w.love();
		// 反射后的做法

		// 加载键值对数据
		Properties prop = new Properties();
		FileReader fr = new FileReader("class.txt");
		prop.load(fr);
		fr.close();

		// 获取数据
		String className = prop.getProperty("className");
		String methodName = prop.getProperty("methodName");

		// 反射
		Class c = Class.forName(className);

		Constructor con = c.getConstructor();
		Object obj = con.newInstance();

		// 调用方法
		Method m = c.getMethod(methodName);
		m.invoke(obj);
	}
```

#### 越过jdk新特性运行源码

ArrayList<Integer>的一个对象，我想在这个集合中添加一个字符串数据，如何实现呢？

```java
public class ArrayListDemo {
	public static void main(String[] args) throws NoSuchMethodException,
			SecurityException, IllegalAccessException,
			IllegalArgumentException, InvocationTargetException {
		// 创建集合对象
		ArrayList<Integer> array = new ArrayList<Integer>();

		// array.add("hello");
		// array.add(10);

		Class c = array.getClass(); // 集合ArrayList的class文件对象
		Method m = c.getMethod("add", Object.class);

		m.invoke(array, "hello"); // 调用array的add方法，传入的值是hello
		m.invoke(array, "world");
		m.invoke(array, "java");

		System.out.println(array);
	}
}
```

在反编译的源码里，没有泛型，所以可以使用反射机制调用源码

## 动态代理

本来应该自己做的事情，却请了别人来做，被请的人就是代理对象

在Java中java.lang.reflect包下提供了一个Proxy类和一个InvocationHandler接口，通过使用这个类和接口就可以生成动态代理对象。JDK提供的代理只能针对接口做代理。我们有更强大的代理cglib

### 示例代码

假设我有两个类，而且这两个类都要实现一个相同的操作

```java
public class Test {
	public static void main(String[] args) {
		UserDao ud = new UserDaoImpl();
		ud.add();
		ud.delete();
		ud.update();
		ud.find();
		System.out.println("-----------");
		// 我们要创建一个动态代理对象
		// Proxy类中有一个方法可以创建动态代理对象
		// public static Object newProxyInstance(ClassLoader loader,Class<?>[]
		// interfaces,InvocationHandler h)
		// 我准备对ud对象做一个代理对象
		MyInvocationHandler handler = new MyInvocationHandler(ud);
		UserDao proxy = (UserDao) Proxy.newProxyInstance(ud.getClass()
				.getClassLoader(), ud.getClass().getInterfaces(), handler);
		proxy.add();
		proxy.delete();
		proxy.update();
		proxy.find();
		System.out.println("-----------");

		StudentDao sd = new StudentDaoImpl();
		MyInvocationHandler handler2 = new MyInvocationHandler(sd);
		StudentDao proxy2 = (StudentDao) Proxy.newProxyInstance(sd.getClass()
				.getClassLoader(), sd.getClass().getInterfaces(), handler2);
		proxy2.login();
		proxy2.regist();
	}
}

public class MyInvocationHandler implements InvocationHandler {
	private Object target; // 目标对象

	public MyInvocationHandler(Object target) {
		this.target = target;
	}

	@Override
	public Object invoke(Object proxy, Method method, Object[] args)
			throws Throwable {
		System.out.println("权限校验");
		Object result = method.invoke(target, args);
		System.out.println("日志记录");
		return result; // 返回的是代理对象
	}
}

public interface StudentDao {
	public abstract void login();

	public abstract void regist();
}

public class StudentDaoImpl implements StudentDao {

	@Override
	public void login() {
		System.out.println("登录功能");
	}

	@Override
	public void regist() {
		System.out.println("注册功能");
	}
}


public interface UserDao {
	public abstract void add();

	public abstract void delete();

	public abstract void update();

	public abstract void find();
}


public class UserDaoImpl implements UserDao {

	@Override
	public void add() {
		System.out.println("添加功能");
	}

	@Override
	public void delete() {
		System.out.println("删除功能");
	}

	@Override
	public void update() {
		System.out.println("修改功能");
	}

	@Override
	public void find() {
		System.out.println("查找功能");
	}

}
```

## 模板设计模式

模版方法模式就是定义一个算法的骨架，而将具体的算法延迟到子类中来实现

http://meigesir.iteye.com/blog/1506484

## 装饰设计模式

装饰模式就是使用被装饰类的一个子类的实例，在客户端将这个子类的实例交给装饰类。是继承的替代方案

http://jiangnanlove.iteye.com/blog/914120

## JDK新特新

自动装箱和拆箱
泛型
增强for循环
静态导入
可变参数
枚举

### 枚举

枚举类可以有构造器，但必须是private的，它默认的也是private的。枚举项的用法比较特殊：枚举(“”)

#### 实例代码

```java
		Direction3 dd = Direction3.FRONT;
		dd = Direction3.LEFT;

		switch (dd) {
		case FRONT:
			System.out.println("你选择了前");
			break;
		case BEHIND:
			System.out.println("你选择了后");
			break;
		case LEFT:
			System.out.println("你选择了左");
			break;
		case RIGHT:
			System.out.println("你选择了右");
			break;
		}
```

```java
public enum Direction3 {
	FRONT("前") {
		@Override
		public void show() {
			System.out.println("前");
		}
	},
	BEHIND("后") {
		@Override
		public void show() {
			System.out.println("后");
		}
	},
	LEFT("左") {
		@Override
		public void show() {
			System.out.println("左");
		}
	},
	RIGHT("右") {

		@Override
		public void show() {
			// TODO Auto-generated method stub
			
		}
		
	};

	private String name;

	private Direction3(String name) {
		this.name = name;
	}

	public String getName() {
		return name;
	}

	public abstract void show();
}
```

```java
public enum Direction2 {
	FRONT("前"), BEHIND("后"), LEFT("左"), RIGHT("右");

	private String name;

	private Direction2(String name) {
		this.setName(name);
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}
}

public enum Direction {
	FRONT, BEHIND, LEFT, RIGHT;
}
```

#### 枚举的方法

```java
		// <T> T valueOf(Class<T> type,String name)
		Direction2 d = Enum.valueOf(Direction2.class, "FRONT");
		System.out.println(d.getName());
		System.out.println("----------------");
		// values()
		// 此方法虽然在JDK文档中查找不到，但每个枚举类都具有该方法，它遍历枚举类的所有枚举值非常方便
		Direction2[] dirs = Direction2.values();
		for (Direction2 d2 : dirs) {
			System.out.println(d2);
			System.out.println(d2.getName());
		}
```

## JDK新特性

二进制字面量
数字字面量可以出现下划线
switch 语句可以用字符串
泛型简化
异常的多个catch合并
try-with-resources 语句

### 二进制字面值量

只要在二进制数值前面加 0b或者0B
举例：
​	int x = ob110110

### 字面值出现下划线

为了增强对数值的阅读性，如我们经常把数据用逗号分隔一样。JDK7提供了_对数据分隔。
举例：
​	int x = 100_1000;
注意事项：
​	不能出现在进制标识和数值之间
​	不能出现在数值开头和结尾
​	不能出现在小数点旁边

### 泛型简化

```java
ArrayList<String> array = new ArrayList<String>();转化成简化版
ArrayList<String> array = new ArrayList<>();
```

### try-with-resources

```java
		// try(必须是java.lang.AutoCloseable的子类对象){…}

		try {
			FileReader fr = new FileReader("a.txt");
			FileWriter fw = new FileWriter("b.txt");
			int ch = 0;
			while ((ch = fr.read()) != -1) {
				fw.write(ch);
			}
			fw.close();
			fr.close();
		} catch (IOException e) {
			e.printStackTrace();
		}

		// 改进版的代码
		try (FileReader fr = new FileReader("a.txt");
				FileWriter fw = new FileWriter("b.txt");) {
			int ch = 0;
			while ((ch = fr.read()) != -1) {
				fw.write(ch);
			}
		} catch (IOException e) {
			e.printStackTrace();
		}
```

