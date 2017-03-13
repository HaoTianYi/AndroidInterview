# Java学习笔记_day18

[TOC]

## Map集合

将键映射到值的对象。一个映射不能包含重复的键；每个键最多只能映射到一个值。 

### 和Collection的区别

Map存储元素是键值对，Map集合的键是唯一的，值是可重复的

Collection存储的是单个元素，Collection的子类Set是唯一的，List是可重复的。

### 功能

  1:添加功能
  		V put(K key,V value):添加元素。
  			如果键是第一次存储，就直接存储元素，返回null
  			如果键不是第一次存在，就用值把以前的值替换掉，返回以前的值
  2:删除功能
  		void clear():移除所有的键值对元素
  		V remove(Object key)：根据键删除键值对元素，并把值返回
  3:判断功能
  		boolean containsKey(Object key)：判断集合是否包含指定的键
  		boolean containsValue(Object value):判断集合是否包含指定的值
  		boolean isEmpty()：判断集合是否为空
  4:获取功能
  		Set<Map.Entry<K,V>> entrySet():???
  		V get(Object key):根据键获取值
  		Set<K> keySet():获取集合中所有键的集合
  		Collection<V> values():获取集合中所有值的集合
  5：长度功能
  		int size()：返回集合中的键值对的对数

### 遍历

- 思路一：遍历key

```java
		// 获取所有的键
		Set<String> set = map.keySet();
		// 遍历键的集合，获取得到每一个键
		for (String key : set) {
			// 根据键去找值
			String value = map.get(key);
			System.out.println(key + "---" + value);
		}
```

- 思路二：获得所有的键值对

```java
		// 获取所有键值对对象的集合
		Set<Map.Entry<String, String>> set = map.entrySet();
		// 遍历键值对对象的集合，得到每一个键值对对象
		for (Map.Entry<String, String> me : set) {
			// 根据键值对对象获取键和值
			String key = me.getKey();
			String value = me.getValue();
			System.out.println(key + "---" + value);
		}
```

## HashMap

基于哈希表的 Map 接口的实现，哈希表结构保证建的唯一性。哈希结构底层依赖是hashCode和Equals方法

### 键是引用类型的案例

```java
/*
 * HashMap<Student,String>
 * 键：Student
 * 		要求：如果两个对象的成员变量值都相同，则为同一个对象。
 * 值：String
 * 实现：只要在Student类中重写两个方法即可，保证唯一性。hashCode()和equals方法
 */
```

### LinkedHashMap

是Map接口的哈希表和链接列表实现，具有可预知的迭代顺序。由哈希表保证键的唯一性，链表保证键盘的有序(存储和取出的顺序一致)。

### TreeMap

是基于红黑树的Map接口的实现。可以保证键的排序和唯一性

```java
//排序示例如下，他也有两种方式
		TreeMap<Student, String> tm = new TreeMap<Student, String>(
				new Comparator<Student>() {
					@Override
					public int compare(Student s1, Student s2) {
						// 主要条件
						int num = s1.getAge() - s2.getAge();
						// 次要条件
						int num2 = num == 0 ? s1.getName().compareTo(
								s2.getName()) : num;
						return num2;
					}
				});
```

### 练习题

```java
/*
 * 需求 ："aababcabcdabcde",获取字符串中每一个字母出现的次数要求结果:a(5)b(4)c(3)d(2)e(1)
 * 
 * 分析：
 * 		A:定义一个字符串(可以改进为键盘录入)
 * 		B:定义一个TreeMap集合
 * 			键:Character
 * 			值：Integer
 * 		C:把字符串转换为字符数组
 * 		D:遍历字符数组，得到每一个字符
 * 		E:拿刚才得到的字符作为键到集合中去找值，看返回值
 * 			是null:说明该键不存在，就把该字符作为键，1作为值存储
 * 			不是null:说明该键存在，就把值加1，然后重写存储该键和值
 * 		F:定义字符串缓冲区变量
 * 		G:遍历集合，得到键和值，进行按照要求拼接
 * 		H:把字符串缓冲区转换为字符串输出
 * 
 * 录入：linqingxia
 * 结果：result:a(1)g(1)i(3)l(1)n(2)q(1)x(1)
 */
public class TreeMapDemo {
	public static void main(String[] args) {
		// 定义一个字符串(可以改进为键盘录入)
		Scanner sc = new Scanner(System.in);
		System.out.println("请输入一个字符串：");
		String line = sc.nextLine();

		// 定义一个TreeMap集合
		TreeMap<Character, Integer> tm = new TreeMap<Character, Integer>();
		
		//把字符串转换为字符数组
		char[] chs = line.toCharArray();
		
		//遍历字符数组，得到每一个字符
		for(char ch : chs){
			//拿刚才得到的字符作为键到集合中去找值，看返回值
			Integer i =  tm.get(ch);
			
			//是null:说明该键不存在，就把该字符作为键，1作为值存储
			if(i == null){
				tm.put(ch, 1);
			}else {
				//不是null:说明该键存在，就把值加1，然后重写存储该键和值
				i++;
				tm.put(ch,i);
			}
		}
		
		//定义字符串缓冲区变量
		StringBuilder sb=  new StringBuilder();
		
		//遍历集合，得到键和值，进行按照要求拼接
		Set<Character> set = tm.keySet();
		for(Character key : set){
			Integer value = tm.get(key);
			sb.append(key).append("(").append(value).append(")");
		}
		
		//把字符串缓冲区转换为字符串输出
		String result = sb.toString();
		System.out.println("result:"+result);
	}
}
```

### HashMap和Hashtable

  Hashtable:线程安全，效率低。不允许null键和null值
  HashMap:线程不安全，效率高。允许null键和null值

### 习题 List,Set,Map等接口是否都继承子Map接口? 

  List，Set不是继承自Map接口，它们继承自Collection接口
  Map接口本身就是一个顶层接口

### Collections

是针对集合进行操作的工具类，都是静态方法。

#### Collection和Collections的区别?

  Collection:是单列集合的顶层接口，有子接口List和Set。
  Collections:是针对集合操作的工具类，有对集合进行排序和二分查找的方法

#### 常见方法

```java
  public static <T> void sort(List<T> list)：排序 默认情况下是自然顺序。
  public static <T> int binarySearch(List<?> list,T key):二分查找
  public static <T> T max(Collection<?> coll):最大值
  public static void reverse(List<?> list):反转
  public static void shuffle(List<?> list):随
```

其中binarySearch在找不到的时候是最大索引加一然后在取负数。

> 如果同时又比较器和自然排序，那么久采用自然排序

### 练习斗地主洗牌

```java
/*
 * 思路：
 * 		A:创建一个HashMap集合
 * 		B:创建一个ArrayList集合
 * 		C:创建花色数组和点数数组
 * 		D:从0开始往HashMap里面存储编号，并存储对应的牌
 *        同时往ArrayList里面存储编号即可。
 *      E:洗牌(洗的是编号)
 *      F:发牌(发的也是编号，为了保证编号是排序的，就创建TreeSet集合接收)
 *      G:看牌(遍历TreeSet集合，获取编号，到HashMap集合找对应的牌)
 */
public class PokerDemo {
	public static void main(String[] args) {
		// 创建一个HashMap集合
		HashMap<Integer, String> hm = new HashMap<Integer, String>();

		// 创建一个ArrayList集合
		ArrayList<Integer> array = new ArrayList<Integer>();

		// 创建花色数组和点数数组
		// 定义一个花色数组
		String[] colors = { "♠", "♥", "♣", "♦" };
		// 定义一个点数数组
		String[] numbers = { "3", "4", "5", "6", "7", "8", "9", "10", "J", "Q",
				"K", "A", "2", };

		// 从0开始往HashMap里面存储编号，并存储对应的牌,同时往ArrayList里面存储编号即可。
		int index = 0;

		for (String number : numbers) {
			for (String color : colors) {
				String poker = color.concat(number);
				hm.put(index, poker);
				array.add(index);
				index++;
			}
		}
		hm.put(index, "小王");
		array.add(index);
		index++;
		hm.put(index, "大王");
		array.add(index);

		// 洗牌(洗的是编号)
		Collections.shuffle(array);

		// 发牌(发的也是编号，为了保证编号是排序的，就创建TreeSet集合接收)
		TreeSet<Integer> fengQingYang = new TreeSet<Integer>();
		TreeSet<Integer> linQingXia = new TreeSet<Integer>();
		TreeSet<Integer> liuYi = new TreeSet<Integer>();
		TreeSet<Integer> diPai = new TreeSet<Integer>();

		for (int x = 0; x < array.size(); x++) {
			if (x >= array.size() - 3) {
				diPai.add(array.get(x));
			} else if (x % 3 == 0) {
				fengQingYang.add(array.get(x));
			} else if (x % 3 == 1) {
				linQingXia.add(array.get(x));
			} else if (x % 3 == 2) {
				liuYi.add(array.get(x));
			}
		}

		// 看牌(遍历TreeSet集合，获取编号，到HashMap集合找对应的牌)
		lookPoker("风清扬", fengQingYang, hm);
		lookPoker("林青霞", linQingXia, hm);
		lookPoker("刘意", liuYi, hm);
		lookPoker("底牌", diPai, hm);
	}

	// 写看牌的功能
	public static void lookPoker(String name, TreeSet<Integer> ts,
			HashMap<Integer, String> hm) {
		System.out.print(name + "的牌是：");
		for (Integer key : ts) {
			String value = hm.get(key);
			System.out.print(value + " ");
		}
		System.out.println();
	}
}
```

