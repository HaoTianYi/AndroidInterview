# Java学习笔记_day17

[TOC]

## 常用快捷键

- ctrl + shift + o 导包
- ctrl + shift + t 快速查找某个类
- 先按ctrl + 2 ,再点L, 创建变量并命名
- ctrl + o , 在当前类中,快速查找某个方法
- ctrl + k, 向下查找某个字符串
- ctrl + shift + k, 向上查找某个字符串
- alt + 左方向键 跳转上一个页面

## toStrring源码分析

```java
代码：
	Collection c = new ArrayList();
	c.add("hello");
	c.add("world");
	c.add("java");
	
	System.out.println(c);
	
为什么c输出的不是地址值呢?
	A:Collection c = new ArrayList();
		这是多态，所以输出c的toString()方法，其实是输出ArrayList的toString()
	B:看ArrayList的toString()
		而我们在ArrayList里面却没有发现toString()。
		以后遇到这种情况，也不要担心，你认为有，它却没有，就应该去它父亲里面看看。
	C:toString()的方法源码
	
		public String toString() {
	        Iterator<E> it = iterator(); //集合本身调用迭代器方法，得到集合迭代器
	        if (! it.hasNext())
	            return "[]";
	
	        StringBuilder sb = new StringBuilder();
	        sb.append('[');
	        for (;;) {
	            E e = it.next(); //e=hello,world,java
	            sb.append(e == this ? "(this Collection)" : e);
	            if (! it.hasNext())
	            	//[hello, world, java]
	                return sb.append(']').toString();
	            sb.append(',').append(' ');
	        }
	    }
```

## 建立一个用户登录案例

### 分包

	A:功能划分
	B:模块划分
	C:先按模块划分，再按功能划分
pojo	   简单的Java对象（Plain Old Java Object）

impl   接口的实现

如果方法的参数小于等于3个那么正常写，如果大于三个就传入一个对象。

### 注意

多个对象共享同一个成员变量，用静态；

循环里面如果有switch，并且在switch里面有break，那么结束的不是循环，而是switch语句，可以使用：

```java
System.exit(0);
```

## Set集合

无序(存储顺序和取出顺序不一致),唯一，有相同的数据存入集合，集合只是存入不同的，**相同的元素存一个**

### HasHSet的set方法源码分析

```java
//首先hashSet继承自set类
class HashSet implements Set {
	private static final Object PRESENT = new Object();
	private transient HashMap<E,Object> map;
	
	public HashSet() {
		map = new HashMap<>();
	}
	
	public boolean add(E e) { //e=hello,world
        return map.put(e, PRESENT)==null;
    }
}
```

他的本质应用就是HashMap，hashMap的源码如下：

```java
class HashMap implements Map {
	public V put(K key, V value) { //key=e=hello,world
	
		//看哈希表是否为空，如果空，就开辟空间
        if (table == EMPTY_TABLE) {
            inflateTable(threshold);
        }
        
        //判断对象是否为null
        if (key == null)
            return putForNullKey(value);
        
        int hash = hash(key); //和对象的hashCode()方法相关
        
        //在哈希表中查找hash值
        int i = indexFor(hash, table.length);
        for (Entry<K,V> e = table[i]; e != null; e = e.next) {
        	//这次的e其实是第一次的world
            Object k;
            if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                V oldValue = e.value;
                e.value = value;
                e.recordAccess(this);
                return oldValue;
                //走这里其实是没有添加元素
            }
        }

        modCount++;
        addEntry(hash, key, value, i); //把元素添加
        return null;
    }
    
    transient int hashSeed = 0;
    
    final int hash(Object k) { //k=key=e=hello,
        int h = hashSeed;
        if (0 != h && k instanceof String) {
            return sun.misc.Hashing.stringHash32((String) k);
        }

        h ^= k.hashCode(); //这里调用的是对象的hashCode()方法

        // This function ensures that hashCodes that differ only by
        // constant multiples at each bit position have a bounded
        // number of collisions (approximately 8 at default load factor).
        h ^= (h >>> 20) ^ (h >>> 12);
        return h ^ (h >>> 7) ^ (h >>> 4);
    }
}
```

> 通过查看add方法的源码，我们知道这个方法底层依赖 两个方法：hashCode()和equals()。

按照方法的步骤来说：	先看hashCode()值是否相同

​	相同:继续走equals()方法

​		返回true：	说明元素重复，就不添加

​		返回false：说明元素不重复，就添加到集合

​	不同：就直接把元素添加到集合

如果类没有重写这两个方法，默认使用的Object()。一般来说不同相同。

而String类重写了hashCode()和equals()方法，所以，它就可以把内容相同的字符串去掉。只留下一个。

### 用hashSet存储引用类型

示例代码：

```java
public class HashSetDemo2 {
	public static void main(String[] args) {
		// 创建集合对象
		HashSet<Student> hs = new HashSet<Student>();

		// 创建学生对象
		Student s1 = new Student("林青霞", 27);
		Student s2 = new Student("柳岩", 22);
		Student s3 = new Student("王祖贤", 30);
		Student s4 = new Student("林青霞", 27);
		Student s5 = new Student("林青霞", 20);
		Student s6 = new Student("范冰冰", 22);

		// 添加元素
		hs.add(s1);
		hs.add(s2);
		hs.add(s3);
		hs.add(s4);
		hs.add(s5);
		hs.add(s6);

		// 遍历集合
		for (Student s : hs) {
			System.out.println(s.getName() + "---" + s.getAge());
		}
	}
}
```

运行结果如下：

```
王祖贤---30
范冰冰---22
林青霞---20
林青霞---27
柳岩---22
林青霞---27
```

没有反映出唯一的性质，但是可以看出它是无序的。

为了使上面的代码达到如下目的：

  需求：存储自定义对象，并保证元素的唯一性
  要求：如果两个对象的成员变量值都相同，则为同一个元素。

改进stutend类，新增两个方法：

```java
	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + age;
		result = prime * result + ((name == null) ? 0 : name.hashCode());
		return result;
	}

	@Override
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

### LinkEdHashSet

LinkedHashSet:底层数据结构由哈希表和链表组成。哈希表保证元素的唯一性。链表保证元素有素。(存储和取出是一致)

### TreeSet

能够对元素按照某种规则进行排序，底层是二叉树结构，是TreeMap。

#### 特点

排序（是指按照某种顺序排列，而不是像list的进出有顺序）和唯一

#### 排列方式

1. 自然排序（无参构造方法默认排序方法）
2. 比较器排序（带参构造函数）

> comparable自然排序

#### 本质比较依赖

真正的比较是依赖于元素的compareTo()方法，而这个方法是定义在 Comparable里面的。
所以，你要想重写该方法，就必须是先实现 Comparable接口。这个接口表示的就是自然排序。

#### 使用TreeSet存储引用类型的数据

**javaBean必须实现comparable接口，同时重写compareTo方法,没有实现会报错**

```java
//目的:TreeSet存储自定义对象并保证排序和唯一
	public int compareTo(Student s) {
		// 这里返回什么，其实应该根据我的排序规则来做
		// 按照年龄排序,主要条件
		int num = this.age - s.age;
		// 次要条件
		// 年龄相同的时候，还得去看姓名是否也相同
		// 如果年龄和姓名都相同，才是同一个元素
		int num2 = num == 0 ? this.name.compareTo(s.name) : num;
		return num2;
	}
```

而且String类也实现了comparable（自然排序）接口，而且重写了compareTo方法

**如果一个类的元素要想能够进行自然排序，就必须实现自然排序接口Comparable**

#### 使用比较器来排序

- 可以使用comparator的实现类来构建treeset

```java
//需求：请按照姓名的长度排序
public class MyComparator implements Comparator<Student> {

	@Override
	public int compare(Student s1, Student s2) {
		// int num = this.name.length() - s.name.length();
		// this -- s1
		// s -- s2
		// 姓名长度
		int num = s1.getName().length() - s2.getName().length();
		// 姓名内容
		int num2 = num == 0 ? s1.getName().compareTo(s2.getName()) : num;
		// 年龄
		int num3 = num2 == 0 ? s1.getAge() - s2.getAge() : num2;
		return num3;
	}
}
```

- 可以使用匿名内部类来创建treeSet

```java
		TreeSet<Student> ts = new TreeSet<Student>(new Comparator<Student>() {
			@Override
			public int compare(Student s1, Student s2) {
				// 姓名长度
				int num = s1.getName().length() - s2.getName().length();
				// 姓名内容
				int num2 = num == 0 ? s1.getName().compareTo(s2.getName())
						: num;
				// 年龄
				int num3 = num2 == 0 ? s1.getAge() - s2.getAge() : num2;
				return num3;
			}
		});
		//比较而言更加简单
```

#### 保证

唯一性：是根据比较的返回是否是0来决定。

 排序：
  	A:自然排序(元素具备比较性)
  		让元素所属的类实现自然排序接口 Comparable
  	B:比较器排序(集合具备比较性)
  		让集合的构造方法接收一个比较器接口的子类对象 Comparator