# Java学习笔记_day16

[TOC]

## list的子类

### 特点

- ArrayList

底层是数组，查询快，增删慢，线程不安全，效率高

- Vector

底层是数组，查询快，增删慢，线程不安全，效率高

- LinkedList

底层是链表，查询慢，增删快，线程不安全，效率高

### ArrayList

遍历操作（和list一样有两种方式）：

```java
public class ArrayListDemo {
	public static void main(String[] args) {
		// 创建集合对象
		ArrayList array = new ArrayList();

		// 创建元素对象，并添加元素
		array.add("hello");
		array.add("world");
		array.add("java");

		// 遍历
		Iterator it = array.iterator();
		while (it.hasNext()) {
			String s = (String) it.next();
			System.out.println(s);
		}

		System.out.println("-----------");

		for (int x = 0; x < array.size(); x++) {
			String s = (String) array.get(x);
			System.out.println(s);
		}
	}
}
```

### Vector特有功能

#### 添加功能

public void addElement(Object obj)		--	add()

#### 获取功能

public Object elementAt(int index)		--  get()

public Enumeration elements()			--	Iterator iterator()

boolean hasMoreElements()				hasNext()

Object nextElement()					next()

```java
public class VectorDemo {
	public static void main(String[] args) {
		// 创建集合对象
		Vector v = new Vector();

		// 添加功能
		v.addElement("hello");
		v.addElement("world");
		v.addElement("java");

		// 遍历
		for (int x = 0; x < v.size(); x++) {
			String s = (String) v.elementAt(x);
			System.out.println(s);
		}

		System.out.println("------------------");

		Enumeration en = v.elements(); // 返回的是实现类的对象
		while (en.hasMoreElements()) {
			String s = (String) en.nextElement();
			System.out.println(s);
		}
	}
}
```

### LinkedList特有功能

#### 添加功能

public void addFirst(Object e)

public void addLast(Object e)

#### 获取功能

public Object getFirst()

public Obejct getLast()

#### 删除功能

public Object removeFirst()

public Object removeLast()

### 去除ArrayList中的重复元素

```java
public class ArrayListDemo {
	public static void main(String[] args) {
		// 创建集合对象
		ArrayList array = new ArrayList();

		// 添加多个字符串元素(包含内容相同的)
		array.add("hello");
		array.add("world");
		array.add("java");
		array.add("world");
		array.add("java");
		array.add("world");
		array.add("world");
		array.add("world");
		array.add("world");
		array.add("java");
		array.add("world");

		// 创建新集合
		ArrayList newArray = new ArrayList();

		// 遍历旧集合,获取得到每一个元素
		Iterator it = array.iterator();
		while (it.hasNext()) {
			String s = (String) it.next();

			// 拿这个元素到新集合去找，看有没有
			if (!newArray.contains(s)) {
				newArray.add(s);
			}
		}

		// 遍历新集合
		for (int x = 0; x < newArray.size(); x++) {
			String s = (String) newArray.get(x);
			System.out.println(s);
		}
	}
}
```

java.util.ArrayList$Itr@1ee0005其中    

$表示的是内部类

  需求：ArrayList去除集合中字符串的重复值(字符串的内容相同)
  要求：不能创建新的集合，就在以前的集合上做。

```java
public class ArrayListDemo2 {
	public static void main(String[] args) {
		// 创建集合对象
		ArrayList array = new ArrayList();

		// 添加多个字符串元素(包含内容相同的)
		array.add("hello");
		array.add("world");
		array.add("java");
		array.add("world");
		array.add("java");
		array.add("world");
		array.add("world");
		array.add("world");
		array.add("world");
		array.add("java");
		array.add("world");

		// 由选择排序思想引入，我们就可以通过这种思想做这个题目
		// 拿0索引的依次和后面的比较，有就把后的干掉
		// 同理，拿1索引...
		for (int x = 0; x < array.size() - 1; x++) {
			for (int y = x + 1; y < array.size(); y++) {
				if (array.get(x).equals(array.get(y))) {
					array.remove(y);
					y--;
				}
			}
		}

		// 遍历集合
		Iterator it = array.iterator();
		while (it.hasNext()) {
			String s = (String) it.next();
			System.out.println(s);
		}
	}
}
```

重点是：**y--**，因为假如有连续的相同而且都要删除的目标那么就会错过。

### 去除集合中自定义对象的重复值

```java
public class ArrayListDemo3 {
	public static void main(String[] args) {
		// 创建集合对象
		ArrayList array = new ArrayList();

		// 创建学生对象
		Student s1 = new Student("林青霞", 27);
		Student s2 = new Student("林志玲", 40);
		Student s3 = new Student("凤姐", 35);
		Student s4 = new Student("芙蓉姐姐", 18);
		Student s5 = new Student("翠花", 16);
		Student s6 = new Student("林青霞", 27);
		Student s7 = new Student("林青霞", 18);

		// 添加元素
		array.add(s1);
		array.add(s2);
		array.add(s3);
		array.add(s4);
		array.add(s5);
		array.add(s6);
		array.add(s7);

		// 创建新集合
		ArrayList newArray = new ArrayList();

		// 遍历旧集合,获取得到每一个元素
		Iterator it = array.iterator();
		while (it.hasNext()) {
			Student s = (Student) it.next();

			// 拿这个元素到新集合去找，看有没有
			if (!newArray.contains(s)) {
				newArray.add(s);
			}
		}

		// 遍历新集合
		for (int x = 0; x < newArray.size(); x++) {
			Student s = (Student) newArray.get(x);
			System.out.println(s.getName() + "---" + s.getAge());
		}
	}
}
```

  我们按照和字符串一样的操作，发现出问题了。
  为什么呢?
  		我们必须思考哪里会出问题?
  		通过简单的分析，我们知道问题出现在了判断上。
  		而这个判断功能是集合自己提供的，所以我们如果想很清楚的知道它是如何判断的，就应该去看源码。
  contains()方法的底层依赖的是equals()方法。
  而我们的学生类中没有equals()方法，这个时候，默认使用的是它父亲Object的equals()方法
  Object()的equals()默认比较的是地址值，所以，它们进去了。因为new的东西，地址值都不同。
  按照我们自己的需求，比较成员变量的值，重写equals()即可。
  自动生成即可。

### 请用LinkedList模拟栈数据结构的集合，并测试

```java
public class MyStack {
	private LinkedList link;

	public MyStack() {
		link = new LinkedList();
	}

	public void add(Object obj) {
		link.addFirst(obj);
	}

	public Object get() {
		// return link.getFirst();
		return link.removeFirst();
	}

	public boolean isEmpty() {
		return link.isEmpty();
	}
}
```

底层用其他类来封装，而且设置私有，然后在构造方法中初始化的这种封装思想。

## 泛型

是一种把类型明确的工作推迟的对象创建或者调用的时候才去确定的特殊类型，也叫参数化类型，把类型当成参数传递，是jdk5引入。

### 格式

<数据类型>   这里的数据类型只能是引用类型。

### 好处

- 把运行时期的问题提前到来编译时期
- 避免的强制转化
- 优化程序设计

### 泛型方法

格式:public <泛型类型> 返回类型 方法名(泛型类型 .)

接口的实现类名一般是在接口名后面加上**Impl**作为实现类名

### 泛型接口

实现类在实现接口的时候

第一种情况：已经知道该是什么类型的了

```java
Inter<String> i = new InterImpl()
```

第二种情况：还不知道是什么类型的

```java
Inter<String> i = new InterImpl<String>();
```

### 高级

- 泛型通配符<?>表示任意类型，没有明确类型，就是Object，如果泛型左边声明具体的类型，那么右边也一定要声明一样的类型
- ? extends E 向下限定，E及其子类
- ? super E E及其父类

## JDK5新特性

### 自动拆装箱

### 泛型

### 增强for循环

  ####格式
  		for(元素数据类型 变量 : 数组或者Collection集合) {
 			使用变量即可，该变量就是元素

#### 弊端

增强for的目标不能为null。对增强for的目标先进行不为null的判断，然后在使用。否则会报NullPointerException空指针异常

#### 本质

增强for其实是用来替代迭代器的，证明如下：

```java
		for (String s : array) {
			if ("world".equals(s)) {
				array.add("javaee");
			}
		}
```

它会报错ConcurrentModificationException和迭代器同时遍历更改报错一样

### 静态导入

格式：import static 包名….类名.方法名;

#### 注意

1. 方法必须是静态的
2. 如果有多个同名的静态方法，不知道使用谁

### 可变参数

定义方法的时候不知道该定义多少个参数

#### 格式

修饰符 返回值类型 方法名(数据类型…  变量名){}

#### 实质

这里的变量其实是一个数组

#### 注意

如果一个方法有可变参数，并且有多个参数，那么，可变参数肯定是最后一个

### 练习题

ArrayList存储自定义对象并遍历。要求加入泛型，并用增强for遍历。

```java
  A:迭代器
  B:普通for
  C:增强for
  	public static void main(String[] args) {
		// 创建集合对象
		ArrayList<Student> array = new ArrayList<Student>();

		// 创建学生对象
		Student s1 = new Student("林青霞", 27);
		Student s2 = new Student("貂蝉", 22);
		Student s3 = new Student("杨玉环", 24);
		Student s4 = new Student("西施", 21);
		Student s5 = new Student("王昭君", 23);

		// 把学生对象添加到集合中
		array.add(s1);
		array.add(s2);
		array.add(s3);
		array.add(s4);
		array.add(s5);

		// 迭代器
		Iterator<Student> it = array.iterator();
		while (it.hasNext()) {
			Student s = it.next();
			System.out.println(s.getName() + "---" + s.getAge());
		}
		System.out.println("---------------");

		// 普通for
		for (int x = 0; x < array.size(); x++) {
			Student s = array.get(x);
			System.out.println(s.getName() + "---" + s.getAge());
		}
		System.out.println("---------------");

		// 增强for
		for (Student s : array) {
			System.out.println(s.getName() + "---" + s.getAge());
		}
	}
```

## ArrayList的asList方法

可以把数组转换成集合

public static <T> List<T> asList(T... a):把数组转成集合

但是其本质还是一个数组，而且数组的长度不可变所以一切与数组长度有关的操作都会报错**UnsupportedOperationException**（即曾和删不可以）不支持的操作，但是更改操作可以

> ArrayList可以嵌套**ArrayList<ArrayList<Student>>**

