# Java学习笔记_day23

[TOC]

## 多线程

进程就是正在运行的程序，进程是系统运行资源和调用的独立单位，每一个进程都有自己的内存空间和系统资源

多进程可以提升CPU的使用率。

线程是是程序的执行单元。是使用CPU的最基本单位。

多进程的意义是在多个进程之间，多线程的进程能够有更高的几率签到CPU的执行权

### 线程调度

#### 分时调度模型

平均分配每个线程占用CPU的时间片

#### 抢先调度模型

优先让优先级高的线程使用CPU，线程级别相同则随机选择一个，java使用的是抢先调度模型

### 并发和并行

并发是在某一个时间段同时运行多个程序，是逻辑上同时发生

并行是某一个时间点同时运行多个程序，是物理上同时发上

### jvm是否是单线程？

否，是多线程，因为垃圾回收线程也要启动，否则容易内存溢出，而且还要启动主线程来调用main方法等，所以最少是两个线程。

### 成员方法

#### 获取线程名称

> public final String getName():获取线程的名称。
>
> public final void setName(String name):设置线程的名称
>
> public static Thread currentThread():返回当前正在执行的线程对象

#### 获取线程优先级

> public final int getPriority():返回线程对象的优先级
>
> public final void setPriority(int newPriority)：更改线程的优先级

注意：线程默认优先级是5。线程优先级的范围是：1-10。线程优先级高仅仅表示线程获取的 CPU时间片的几率高，但是要在次数比较多，或者多次运行的时候才能看到比较好的效果。

#### 线程控制

> public static void sleep(long millis)线程休眠millis毫秒
>
> public final void join()调用此方法的线程要先执行完，然后才能执行其他线程
>
> public static void yield()暂停当前正在执行的线程对象，并执行其他线程，注意这个方法和第一方法都是静态方法，而第二个方法要通过线程对象调用，这个方法可以在一定程度上使线程的执行更加机会均等，但不是绝对
>
> public final void setDaemon(boolean on)守护线程

```java
/*
 * public final void setDaemon(boolean on):将该线程标记为守护线程或用户线程。
 * 当正在运行的线程都是守护线程时，Java 虚拟机退出。 该方法必须在启动线程前调用。 
 * 所以当被守护的线程终止时，守护线程不会立即退出，而是在执行以一下
 */
public class ThreadDaemonDemo {
	public static void main(String[] args) {
		ThreadDaemon td1 = new ThreadDaemon();
		ThreadDaemon td2 = new ThreadDaemon();

		td1.setName("关羽");
		td2.setName("张飞");

		// 设置收获线程
		td1.setDaemon(true);
		td2.setDaemon(true);

		td1.start();
		td2.start();

		Thread.currentThread().setName("刘备");
		for (int x = 0; x < 5; x++) {
			System.out.println(Thread.currentThread().getName() + ":" + x);
		}
	}
}
```

>public final void stop():让线程停止，过时了，但是还可以使用。
>
>public void interrupt():中断线程。 把线程的状态终止，并抛出一个InterruptedException。

### 线程的生命周期

1. 新建：创建线程对象
2. 就绪：线程对象已经启动，有执行资格，但是没有CPU的执行权
3. 运行：有执行资格有执行权
4. 阻塞：没有执行资格没有执行权；结束阻塞，有执行资格没有执行权也就是就绪状态
5. 死亡：线程对象变成垃圾，等待回收

![生命周期](http://oaxelf1sk.bkt.clouddn.com/生命周期.png)



### 创建多线程的方式

#### 继承Thread类

1. 继承Threadlei
2. 重写run方法
3. 创建对象
4. 调用start方法，启动线程

```java
		 MyThread my = new MyThread();
		 // 启动线程
		 my.run();
		 my.run();
//		 IllegalThreadStateException:非法的线程状态异常
//		 因为这个相当于是my线程被调用了两次。而不是两个线程启动。
```

注意：线程不能被多次启动

run()和start()的区别？

```java
		// run():仅仅是封装被线程执行的代码，直接调用是普通方法
		// start():首先启动了线程，然后再由jvm去调用该线程的run()方法，start方法有两个步骤，而run方法只是底层封装被多线程执行的程序
```

#### 实现Runnable接口

1. 创建Runnable的实现子类
2. 重写run方法，Runnable只有一个方法
3. 创建子类的对象
4. 创建线程对象，并且把runnable的子类实现对象传入

```java
public class MyRunnable implements Runnable {

	@Override
	public void run() {
		for (int x = 0; x < 100; x++) {
			// 由于实现接口的方式就不能直接使用Thread类的方法了,但是可以间接的使用
			System.out.println(Thread.currentThread().getName() + ":" + x);
		}
	}
}
```
注意：调用当前线程的方法获得名字的方法，也是调用其他Thread方法的思想
```java
	public static void main(String[] args) {
		// 创建MyRunnable类的对象
		MyRunnable my = new MyRunnable();

		// 创建Thread类的对象，并把C步骤的对象作为构造参数传递
		// Thread(Runnable target)
		 Thread t1 = new Thread(my);
		 Thread t2 = new Thread(my);
		 t1.setName("林青霞");
		 t2.setName("刘意");

		// Thread(Runnable target, String name)
		Thread t1 = new Thread(my, "林青霞");
		Thread t2 = new Thread(my, "刘意");

		t1.start();
		t2.start();
	}
```
#### 方式2的好处

1. 可以避免java单继承的缺点
2. 适合多个程序处理同一个资源的情况

#### 第三种方式

依赖于线程池

```java
//Callable:是带泛型的接口。
//这里指定的泛型其实是call()方法的返回值类型。
public class MyCallable implements Callable<String> {

	@Override
	public String call() throws Exception {
		for (int x = 0; x < 100; x++) {
			System.out.println(Thread.currentThread().getName() + ":" + x);
		}
		return null;
	}
}
```

这里是可以带有返回值区别上面两种

示例代码：

```java
/*
 * 线程求和案例
 */
public class MyCallable implements Callable<Integer> {

	private int number;

	public MyCallable(int number) {
		this.number = number;
	}

	@Override
	public Integer call() throws Exception {
		int sum = 0;
		for (int x = 1; x <= number; x++) {
			sum += x;
		}
		return sum;
	}
}

public class CallableDemo {
	public static void main(String[] args) throws InterruptedException, ExecutionException {
		// 创建线程池对象
		ExecutorService pool = Executors.newFixedThreadPool(2);

		// 可以执行Runnable对象或者Callable对象代表的线程
		Future<Integer> f1 = pool.submit(new MyCallable(100));
		Future<Integer> f2 = pool.submit(new MyCallable(200));

		// V get()
		Integer i1 = f1.get();
		Integer i2 = f2.get();

		System.out.println(i1);
		System.out.println(i2);

		// 结束
		pool.shutdown();
	}
}

```

### 电影票练习题

某电影院目前正在上映贺岁大片，共有100张票，而它有3个售票窗口售票，请设计一个程序模拟该电影院售票

#### 实现电影票的代码

```java
public class SellTicket extends Thread {

	// 定义100张票
	// private int tickets = 100;
	// 为了让多个线程对象共享这100张票，我们其实应该用静态修饰
	private static int tickets = 100;

	@Override
	public void run() {
		// 定义100张票
		// 每个线程进来都会走这里，这样的话，每个线程对象相当于买的是自己的那100张票，这不合理，所以应该定义到外面
		// int tickets = 100;

		// 是为了模拟一直有票
		while (true) {
			if (tickets > 0) {
				System.out.println(getName() + "正在出售第" + (tickets--) + "张票");
			}
		}
	}
}
```

注意：想要每个类的对象单独有一个成员变量那么就可以时private int来修饰，但是想让此类的所有对象共享一个成员变量，那么就应该用privat static int 来修饰

#### 实现测试类

```java
public class SellTicketDemo {
	public static void main(String[] args) {
		// 创建三个线程对象
		SellTicket st1 = new SellTicket();
		SellTicket st2 = new SellTicket();
		SellTicket st3 = new SellTicket();

		// 给线程对象起名字
		st1.setName("窗口1");
		st2.setName("窗口2");
		st3.setName("窗口3");

		// 启动线程
		st1.start();
		st2.start();
		st3.start();
	}
}
```

### 使用方式二来实现

#### 模拟卖票类

```java
public class SellTicket implements Runnable {
	// 定义100张票
	private int tickets = 100;

	@Override
	public void run() {
		while (true) {
			if (tickets > 0) {
				System.out.println(Thread.currentThread().getName() + "正在出售第"
						+ (tickets--) + "张票");
			}
		}
	}
}
```

注意：这里和上个方法的ststic修饰不同

#### 写测试类

```java
public class SellTicketDemo {
	public static void main(String[] args) {
		// 创建资源对象
		SellTicket st = new SellTicket();

		// 创建三个线程对象
		Thread t1 = new Thread(st, "窗口1");
		Thread t2 = new Thread(st, "窗口2");
		Thread t3 = new Thread(st, "窗口3");

		// 启动线程
		t1.start();
		t2.start();
		t3.start();
	}
}
```

### 电影票改进版

每次卖票延迟100毫秒

- 问题：还出现了负数的票

随机性和延迟导致

- 出现相同的票

CPU的一次操作必须时原子性的

##同步机制

### 判断线程安全问题的原因

1. 是否是多线程的环境
2. 是否有共享数据
3. 是否有多条语句操作共享数据

> 判断标准同时也是解决问题的思路，即使程序不满足条件（第三条）

### 同步代码块

```java
  同步代码块：
  		synchronized(对象){
  			需要同步的代码;也就是多条语句操作共享数据的代码块
  		}
```

注意：同步安全的关键就是在那个对象，多个线程必须是同一把锁

```java
	private int tickets = 100;
	//创建锁对象
	private Object obj = new Object();
	
	@Override
	public void run() {
		while (true) {
			synchronized (obj) {
				if (tickets > 0) {
					try {
						Thread.sleep(100);
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
					System.out.println(Thread.currentThread().getName()
							+ "正在出售第" + (tickets--) + "张票");
				}
			}
		}
	}
```

### 同步的特点

1. 多个线程
2. 多个线程使用的是同意锁对象

### 同步的优缺点

优点：解决了多线程的安全问题

缺点：每个线程都会判断锁，耗费资源，降低效率

### 同步的其他问题

- 同步代码块的锁对象是谁？

任意对象

- 同步方法的锁对象是谁？

this

- 同步方法的锁对象是谁？

类的字节码文件

## 回顾以前类的安全

```java
		// 线程安全的类
		StringBuffer sb = new StringBuffer();
		Vector<String> v = new Vector<String>();
		Hashtable<String, String> h = new Hashtable<String, String>();
```

```java
		// Vector是线程安全的时候才去考虑使用的，但是我还说过即使要安全，我也不用你
		// 那么到底用谁呢?
		// public static <T> List<T> synchronizedList(List<T> list)
		List<String> list1 = new ArrayList<String>();// 线程不安全
		List<String> list2 = Collections
				.synchronizedList(new ArrayList<String>()); // 线程安全
```
