# Java学习笔记_day11

[TOC]

## eclipse的使用

### 视窗和视图结构

1. outline 显示类的结构，标示c标示构造方法，标示s标示静态方法，绿色表示public，红色标示private，f标示final，sf标示静态和final，
2. hierarchy  显示java继承层次，选中类，点击F4;

### 快捷键

**alt  /**  内容辅助键还可以输入补全已知的类名

**alt   shift  s**   弹出source    o和r

|         快捷键         |        内容        |
| :-----------------: | :--------------: |
|      alt     /      |  内容辅助键（可以帮助起名）   |
| ctrl     shift    F |      格式化代码       |
| ctrl      shift   O |        导包        |
|     ctrl     /      |        注释        |
| ctrl     shift    / |    多行注释（要先选中）    |
|     alt   上 （下）     |       上下移动       |
|         F3          | 查看源码（或者ctrl鼠标点击） |


> ecplise删项目和从硬盘上删除

## debug

| 名称        | 快捷键  | 作用     |
| --------- | ---- | ------ |
| Step Over | F6   | 一次执行一步 |

## 如何去断点

- 再次双击即可


- 找到Debug视图，Variables界面，找到Breakpoints，并点击，然后看到所有的断点，最后点击那个双叉。

## 常用api

1. public int hashCode（）返回一个哈希码值，这个值和地址有关，但不是实际地址。
2. public final Class getClass（）返回此object的运行类，其实返回的是该类的对象。（字节码文件的对象）



==的用法

基本类型：比较的就是值是否相同

引用类型：比较的就是地址值是否相同



## Object类常用API

### equals方法

 equals（只能比引用类型）	引用类型：默认情况下，比较的是地址值。

String的equals()方法是重写自Object类的，比较的是字符串的内容是否相同

```java
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Student other = (Student) obj;
		if (age != other.age)
			return false;
		if (name == null) {
			if (other.name != null)
				return false;
		} else if (!name.equals(other.name))
			return false;
		return true;
	}
```

在比较getClass就是比较两者是否是同样的类型，可以使用如下代码替换：

```java
		if(!(obj instanceof 类名)){
			return false;
		}
```

### getClass方法

getClass返回的是该类的运行时类，也就是该类的对象；

类Class有一个api是getName以string形式返回该Class的对象名；

### clone方法

创建并且返回这个对象的一个副本，注意副本和原来的对象地址不是指向同一个，而且该类一定要实现Cloneable接口，这个接口是标记接口，子类而且要重写object的clone方法，告诉我们实现该接口就可以实现该类对象的克隆。

cloneable接口是一个空接口，只是起到标记作用