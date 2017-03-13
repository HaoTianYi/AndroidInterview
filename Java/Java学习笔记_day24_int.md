# Java学习笔记_day24

[TOC]

## Lock接口

Lock 实现提供了比使用 synchronized 方法和语句可获得的更广泛的锁定操作

### 基本用法

```java
 class X {
   private final ReentrantLock lock = new ReentrantLock();
   // ...

   public void m() { 
     lock.lock();  // block until condition holds
     try {
       // ... method body
     } finally {
       lock.unlock()
     }
   }
 }

```

注意：这是一个没有catch的try语句

### 死锁问题

```java
public class Deadlock {
    static class Friend {
        private final String name;
        public Friend(String name) {
            this.name = name;
        }
        public String getName() {
            return this.name;
        }
        public synchronized void bow(Friend bower) {
            System.out.format("%s: %s"
                + "  has bowed to me!%n", 
                this.name, bower.getName());
            bower.bowBack(this);
        }
        public synchronized void bowBack(Friend bower) {
            System.out.format("%s: %s"
                + " has bowed back to me!%n",
                this.name, bower.getName());
        }
    }

    public static void main(String[] args) {
        final Friend alphonse =
            new Friend("Alphonse");
        final Friend gaston =
            new Friend("Gaston");
        new Thread(new Runnable() {
            public void run() { alphonse.bow(gaston); }
        }).start();
        new Thread(new Runnable() {
            public void run() { gaston.bow(alphonse); }
        }).start();
    }
}
```

### 线程之间的通信

由于线程运行的随机性，所以会造成线程通信时数据不匹配的问题，而且还会造成数据个数不同（CPU的一个时间片就会运行好多次）所以应该加锁，而且不同种类的线程要加的锁必须是同一把为了解决次数的问题我们使用java的登台唤醒机制

### 等待唤醒机制

>  Object类中提供了三个方法：
>
>   			wait():等待
>
>   			notify():唤醒单个线程
>
>   			notifyAll():唤醒所有线程

为什么这些方法不定义在Thread类中呢?

> 这些方法的调用必须通过锁对象调用，而我们刚才使用的锁对象是任意锁对象。  			所以，这些方法必须定义在Object类中。

#### 示例代码

模拟两个线程之间的通信，一个是增加学生，一个是读取学生

```java
public class Student {
	String name;
	int age;
	boolean flag; // 默认情况是没有数据，如果是true，说明有数据
}
```

设置学生数据:SetThread(生产者)

```java
public class SetThread implements Runnable {

	private Student s;
	private int x = 0;

	public SetThread(Student s) {
		this.s = s;
	}

	@Override
	public void run() {
		while (true) {
			synchronized (s) {
				//判断有没有
				if(s.flag){
					try {
						s.wait(); //t1等着，释放锁
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
				}
				
				if (x % 2 == 0) {
					s.name = "林青霞";
					s.age = 27;
				} else {
					s.name = "刘意";
					s.age = 30;
				}
				x++; //x=1
				
				//修改标记
				s.flag = true;
				//唤醒线程
				s.notify(); //唤醒t2,唤醒并不表示你立马可以执行，必须还得抢CPU的执行权。
			}
			//t1有，或者t2有
		}
	}
}
```

获取学生数据：GetThread(消费者)

```java
public class GetThread implements Runnable {
	private Student s;

	public GetThread(Student s) {
		this.s = s;
	}

	@Override
	public void run() {
		while (true) {
			synchronized (s) {
				if(!s.flag){
					try {
						s.wait(); //t2就等待了。立即释放锁。将来醒过来的时候，是从这里醒过来的时候
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
				}
				
				System.out.println(s.name + "---" + s.age);
				//林青霞---27
				//刘意---30
				
				//修改标记
				s.flag = false;
				//唤醒线程
				s.notify(); //唤醒t1
			}
		}
	}
}
```

测试类：

```java
public class StudentDemo {
	public static void main(String[] args) {
		//创建资源
		Student s = new Student();
		
		//设置和获取的类
		SetThread st = new SetThread(s);
		GetThread gt = new GetThread(s);

		//线程类
		Thread t1 = new Thread(st);
		Thread t2 = new Thread(gt);

		//启动线程
		t1.start();
		t2.start();
	}
}
```

注意：flag的用法，在修改完数据之后一定要修改flag的标签，并且在使线程等待之后一定要唤醒线程

唤醒并不表示你立马可以执行，必须还得抢CPU的执行权。

第哦啊用wait方法时，会释放锁，使其他线程可以拿到锁

## 线程运行状态图

![线程](http://oaxelf1sk.bkt.clouddn.com/线程.png)

## 线程组

默认情况下所有的线程都属于mian线程组

> 线程类里面的方法：public final ThreadGroup getThreadGroup()

### 创建线程组

```java
public Thread(ThreadGroup group,Runnable target,String name)
分配新的 Thread 对象，以便将 target 作为其运行对象，将指定的 name 作为其名称，并作为 group 所引用的线程组的一员
```

## 线程池

程序新建一个线程浪费资源，所以有线程池，线程池可以提高程序效率，因为线程池会默认创建许多线程，而且这些线程可以回收（重复利用）

### Executors

JDK5新增了一个Executors工厂类来产生线程池

#### 创建线程池的过程

- 创建一个线程池对象，控制要创建几个线程对象。

public static ExecutorService newFixedThreadPool(int nThreads)

- 这种线程池的线程可以执行：

可以执行Runnable对象或者Callable对象。

- 调用如下方法即可

Future<?> submit(Runnable task)

<T> Future<T> submit(Callable<T> task)

- 结束线程池

```java
public class ExecutorsDemo {
	public static void main(String[] args) {
		// 创建一个线程池对象，控制要创建几个线程对象。
		// public static ExecutorService newFixedThreadPool(int nThreads)
		ExecutorService pool = Executors.newFixedThreadPool(2);

		// 可以执行Runnable对象或者Callable对象代表的线程
		pool.submit(new MyRunnable());
		pool.submit(new MyRunnable());

		//结束线程池
		pool.shutdown();
	}
}
```

## 匿名内部类

示例代码：

```java
/*
 * 匿名内部类的格式：
 * 		new 类名或者接口名() {
 * 			重写方法;
 * 		};
 * 		本质：是该类或者接口的子类对象。
 */
public class ThreadDemo {
	public static void main(String[] args) {
		// 继承Thread类来实现多线程
		new Thread() {
			public void run() {
				for (int x = 0; x < 100; x++) {
					System.out.println(Thread.currentThread().getName() + ":"
							+ x);
				}
			}
		}.start();

		// 实现Runnable接口来实现多线程
		new Thread(new Runnable() {
			@Override
			public void run() {
				for (int x = 0; x < 100; x++) {
					System.out.println(Thread.currentThread().getName() + ":"
							+ x);
				}
			}
		}) { }.start();

		// 更有难度的
		new Thread(new Runnable() {
			@Override
			public void run() {
				for (int x = 0; x < 100; x++) {
					System.out.println("hello" + ":" + x);
				}
			}
		}) {
			public void run() {
				for (int x = 0; x < 100; x++) {
					System.out.println("world" + ":" + x);
				}
			}
		}.start();
	}
}
```

第三种方式相当于子类继承了threa类，同时也实行了runnab接口，注意这时候程序走的是thread中的run方法

## Timer

可以让我们在指定的时间做某件事情，还可以重复的做某件事情

- Timer:定时

  		public Timer()
  		public void schedule(TimerTask task,long delay)
  		public void schedule(TimerTask task,long delay,long period)
  		public void cancel()

- TimerTask:任务

  ?	public abstract void run()
  ?	public boolean cancel()

### 示例代码

```java
public class TimerDemo2 {
	public static void main(String[] args) {
		// 创建定时器对象
		Timer t = new Timer();
		// 3秒后执行爆炸任务第一次，如果不成功，每隔2秒再继续炸
		t.schedule(new MyTask2(), 3000, 2000);
	}
}

// 做一个任务
class MyTask2 extends TimerTask {
	@Override
	public void run() {
		System.out.println("beng,爆炸了");
	}
}
```

> public void schedule(TimerTask task,long delay,long period)第二个参数代表的是间隔多少毫秒执行，第三个参数代表间隔多少毫秒再次执行

这样执行完程序，jvm不会退出，因为任务还没有停止，改进版就是加入cancel方法：

```java
public class TimerDemo {
	public static void main(String[] args) {
		// 创建定时器对象
		Timer t = new Timer();
		// 3秒后执行爆炸任务
		// t.schedule(new MyTask(), 3000);
		//结束任务
		t.schedule(new MyTask(t), 3000);
	}
}

// 做一个任务
class MyTask extends TimerTask {

	private Timer t;
	
	public MyTask(){}
	
	public MyTask(Timer t){
		this.t = t;
	}
	
	@Override
	public void run() {
		System.out.println("beng,爆炸了");
		t.cancel();
	}
}
```

### 删除指定目录

```java
/*
 * 需求：在指定的时间删除我们的指定目录(你可以指定c盘，但是我不建议，我使用项目路径下的demo)
 */

class DeleteFolder extends TimerTask {

	@Override
	public void run() {
		File srcFolder = new File("demo");
		deleteFolder(srcFolder);
	}

	// 递归删除目录
	public void deleteFolder(File srcFolder) {
		File[] fileArray = srcFolder.listFiles();
		if (fileArray != null) {
			for (File file : fileArray) {
				if (file.isDirectory()) {
					deleteFolder(file);
				} else {
					System.out.println(file.getName() + ":" + file.delete());
				}
			}
			fileArray.delete();
		}
	}
}

public class TimerTest {
	public static void main(String[] args) throws ParseException {
		Timer t = new Timer();

		String s = "2014-11-27 15:45:00";
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
		Date d = sdf.parse(s);

		t.schedule(new DeleteFolder(), d);
	}
}
```

这样执行完程序，jvm不会退出，因为任务还没有停止

## 面向对象设计思想

单一职责原则；开闭原则；里氏替换原则；依赖注入原则；接口分离原则；迪米特原则

### 单一职责原则

就是开发人员经常说的”高内聚，低耦合”
也就是说，每个类应该只有一个职责，对外只能提供一种功能，而引起类变化的原因应该只有一个。在设计模式中，所有的设计模式都遵循这一原则。

### 开闭原则

核心思想是：一个对象对扩展开放，对修改关闭。
其实开闭原则的意思就是：对类的改动是通过增加代码进行的，而不是修改现有代码。

需要借助于抽象和多态，即把可能变化的内容抽象出来，从而使抽象的部分是相对稳定的，而具体的实现则是可以改变和扩展的

### 里氏替换原则

核心思想：在任何父类出现的地方都可以用它的子类来替代。
其实就是说：同一个继承体系中的对象应该有共同的行为特征。

### 依赖注入原则

核心思想：要依赖于抽象，不要依赖于具体实现

### 接口分离原则

核心思想：不应该强迫程序依赖它们不需要使用的方法。
其实就是说：一个接口不需要提供太多的行为，一个接口应该只提供一种对外的功能，不应该把所有的操作都封装到一个接口中。

### 迪米特原则

核心思想：一个对象应当对其他对象尽可能少的了解
其实就是说：降低各个对象之间的耦合，提高系统的可维护性。在模块之间应该只通过接口编程，而不理会模块的内部工作原理，它可以使各个模块耦合度降到最低，促进软件的复用

## 设计模式

设计模式是为了可重用代码、让代码更容易被他人理解、保证代码可靠性

创建型模式：简单工厂模式，工厂方法模式，抽象工厂模式，建造者模式，原型模式，单例模式。(6个)
结构型模式：外观模式、适配器模式、代理模式、装饰模式、桥接模式、组合模式、享元模式。(7个)
行为型模式：模版方法模式、观察者模式、状态模式、职责链模式、命令模式、访问者模式、策略模式、备忘录模式、迭代器模式、解释器模式。(10个)

### 简单工厂模式

它定义一个具体的工厂类负责对象的创建

#### 优缺点

- 优点：客户端不需要在负责对象的创建，从而明确了各个类的职责
- 缺点：这个静态工厂类负责所有对象的创建，如果有新的对象增加，或者某些对象的创建方式不同，就需要不断的修改工厂类，不利于后期的维护

### 工厂方法模式Factory Method Pattern

```java
public interface Factory {
	public abstract Animal createAnimal();
}

public class CatFactory implements Factory {

	@Override
	public Animal createAnimal() {
		return new Cat();
	}
}

public class DogFactory implements Factory {

	@Override
	public Animal createAnimal() {
		return new Dog();
	}
}

public abstract class Animal {
	public abstract void eat();
}

public class Cat extends Animal {

	@Override
	public void eat() {
		System.out.println("猫吃鱼");
	}
}

public class Dog extends Animal {

	@Override
	public void eat() {
		System.out.println("狗吃肉");
	}
}

public class AnimalDemo {
	public static void main(String[] args) {
		// 需求：我要买只狗
		Factory f = new DogFactory();
		Animal a = f.createAnimal();
		a.eat();
		System.out.println("-------");
		
		//需求：我要买只猫
		f = new CatFactory();
		a = f.createAnimal();
		a.eat();
	}
}
```

### 单例模式

单例模式就是要确保类在内存中只有一个对象，该实例必须自动创建，并且对外提供

* 		饿汉式：类一加载就创建对象，不会出现问题
* 		懒汉式：用的时候，才去创建对象，会出现问题（懒加载不会，线程安全会出现问题）

```java
public class Teacher {
	private Teacher() {
	}

	private static Teacher t = null;

	public synchronized static Teacher getTeacher() {
		// t1,t2,t3
		if (t == null) {
			//t1,t2,t3
			t = new Teacher();
		}
		return t;
	}
}
```

注意懒汉式要加入synchronized

#### 优缺点

- 优点：在系统内存中只存在一个对象，因此可以节约系统资源，对于一些需要频繁创建和销毁的对象单例模式无疑可以提高系统的性能
- 缺点：没有抽象层，因此扩展很难。职责过重，在一定程序上违背了单一职责

#### 保证类在内存中只有一个对象

* 		把构造方法私有
* 		在成员位置自己创建一个对象
* 		通过一个公共的方法提供访问

```java
public class Student {
	//饿汉式：类一加载就创建对象
    // 构造私有
	private Student() {
	}

	// 自己造一个
	// 静态方法只能访问静态成员变量，加静态
	// 为了不让外界直接访问修改这个值，加private
	private static Student s = new Student();

	// 提供公共的访问方式
	// 为了保证外界能够直接使用该方法，加静态
	public static Student getStudent() {
		return s;
	}
}
```

## Runtime

exec(String command)可以直接运行dos命令，应用如打开某个程序

### 源码分析

```java
//饿汉式 
class Runtime {
  		private Runtime() {}
  		private static Runtime currentRuntime = new Runtime();
  		public static Runtime getRuntime() {
        	return currentRuntime;
    	}
  }
```
