# Java学习笔记_day15

[TOC]

## 集合框架

面向对象语言对事物的体现都是一对象的形式，所以为了方便操作对象，java提供了集合类。

数组虽然可以存储对象但是长度是固定的，集合长度是可变的，数组可以存储基本数据类型，集合只能存储对象。

### 数组和集合的区别

|      |    数组     |   集合    |
| :--: | :-------: | :-----: |
|  长度  |   长度固定    |  长度可变   |
|  元素  |    同一种    | 可以不同类型  |
| 数据类型 | 基本类型、引用类型 | 只能是引用类型 |

我们得到有个继承体系，在分析问题时我们从具体到抽象，在实现时，我们从抽象到具体，在使用时我们使用具体

### Collection

是集合的顶层接口，他的子体系有重复的也有唯一的，有序的，无序的

#### 添加功能

```java
boolean add(Object obj):添加一个元素
boolean addAll(Collection c):添加一个集合的元素
```

#### 删除功能

```java
void clear():移除所有元素
boolean remove(Object o):移除一个元素
boolean removeAll(Collection c):移除一个集合的元素
```

#### 判断功能

```java
boolean contains(Object o)：判断集合中是否包含指定的元素
boolean containsAll(Collection c)：判断集合中是否包含指定的集合元素(包含全部)
boolean isEmpty()：判断集合是否为空
```

#### 遍历功能

```java
Iterator<E> iterator()(重点)
```

#### 长度功能

```java
int size():元素的个数，数组是length，string是length（）方法
```

#### 交集功能

```java
boolean retainAll(Collection c):最终的结果保存在调用者，boolean表示调用者是否发生改变，可以判断两个集合是否相等，只有两个集合相等时，才返回false
```

#### 把集合转换为数组

```java
Object[] toArray()作用可以实现集合的遍历
```

#### 成员方法iterator

迭代器，集合的专用遍历方式

- Object next():获取元素,并移动到下一个位置。

NoSuchElementException：没有这样的元素，因为你已经找到最后了。

- boolean hasNext():如果仍有元素可以迭代（是不是有下一个）则返回 true。

注意以下两端代码含义相同：

```java
		Collection c = new ArrayList();		
		Iterator it = c.iterator();
		while (it.hasNext()) {
			Student s = (Student) it.next();
			System.out.println(s.getName() + "---" + s.getAge());
		}
```

```java
		for (Iterator it = c.iterator(); it.hasNext();) {
			Student s = (Student) it.next();
			System.out.println(s.getName() + "---" + s.getAge());
		}
```

注意：

不要多次使用it.next()方法，因为每次使用都是访问一个对象，可能出现NoSuchElementException 不要多次使用it.next()方法

```java
			 System.out.println(((Student) it.next()).getName() + "---"
			 + ((Student) it.next()).getAge());
```

打印出的年龄和姓名不是同一个人，因为每次调用next方法，都会向下移动光标。

使用步骤：

1. 通过集合对象获取迭代器对象
2. 通过迭代器的hasNext判断是否有元素
3. 同构迭代器对象的next方法获取元素并移动到下一个位置

#### 迭代器为什么不定义成一个类

java中的集合类有很多，而且他们的数据结构不同，存储方式也不同，遍历方式也不同，所以没有定义成迭代器类。而所有的集合都应该有获取元素的操作，所以把他们提取成接口。**真正的实现类在子类的内部类中**。

## List类

是有序的collection集合（进来有序，出去也有序）可以在每个位置精确控制每个元素，可以使用索引来控制。允许重复的元素。

### 添加功能

void add(int index,Object element):在指定位置添加元素

在元素个数+1可以，超过会报错

```java
		List list = new ArrayList();

		// 添加元素
		list.add("hello");
		list.add("world");
		list.add("java");

		 void add(int index,Object element):在指定位置添加元素
		 list.add(1, "android");//没有问题
		 IndexOutOfBoundsException
		 list.add(11, "javaee");//有问题
		 list.add(3, "javaee"); //没有问题
		 list.add(4, "javaee"); //有问题
```

###获取功能

Object get(int index):获取指定位置的元素

>List集合的特有遍历功能：size()和get()方法结合使用

### 列表迭代器

ListIterator listIterator()：List集合特有的迭代器，该迭代器继承了Iterator迭代器，所以，就可以直接使用hasNext()和next()方法。

> Object previous():获取上一个元素
>
> boolean hasPrevious():判断是否有元素

可能会报错**NoSuchElementException**没有标签，越界

```java
	public static void main(String[] args) {
		// 创建List集合对象
		List list = new ArrayList();
		list.add("hello");
		list.add("world");
		list.add("java");

		// ListIterator listIterator()
		ListIterator lit = list.listIterator(); // 子类对象

		while (lit.hasPrevious()) {
			String s = (String) lit.previous();
			System.out.println(s);
		}
		System.out.println("-----------------");

		// 迭代器
		Iterator it = list.iterator();
		while (it.hasNext()) {
			String s = (String) it.next();
			System.out.println(s);
		}
		System.out.println("-----------------");

	}
```

注意：ListIterator可以实现逆向遍历，但是必须先正向遍历，才能逆向遍历，所以一般无意义，不使用。

### 删除功能

Object remove(int index)：根据索引删除元素,返回被删除的元素

### 修改功能

Object set(int index,Object element):根据索引修改元素，返回被修饰的元素

### 练习题

我有一个集合，如下，请问，我想判断里面有没有"world"这个元素，如果有，我就添加一个"javaee"元素，请写代码实现。

```java
		List list = new ArrayList();
		// 添加元素
		list.add("hello");
		list.add("world");
		list.add("java");

		// 迭代器遍历
		Iterator it = list.iterator();
		while (it.hasNext()) {
			String s = (String) it.next();
			if ("world".equals(s)) {
				list.add("javaee");
			}
		}
```

会报错：ConcurrentModificationException

原因：迭代器是依赖于集合而存在的，在判断成功后，集合的中新添加了元素，而迭代器却不知道，所以就报错了，这个错叫并发修改异常。

其实这个问题描述的是：迭代器遍历元素的时候，通过集合是不能修改元素的。

解决办法：

1. 迭代器迭代元素，迭代器修改元素，**新添加的元素是跟在刚才迭代的元素后面的**
2. 集合遍历元素，集合修改元素(普通for)**新添加的元素在最后添加的**

```java
		// 方式1：迭代器迭代元素，迭代器修改元素
		// 而Iterator迭代器却没有添加功能，所以我们使用其子接口ListIterator
		ListIterator lit = list.listIterator();
		while (lit.hasNext()) {
			String s = (String) lit.next();
			if ("world".equals(s)) {
				lit.add("javaee");
			}
		}
```

```java
		// 方式2：集合遍历元素，集合修改元素(普通for)
		for (int x = 0; x < list.size(); x++) {
			String s = (String) list.get(x);
			if ("world".equals(s)) {
				list.add("javaee");
			}
		}
```