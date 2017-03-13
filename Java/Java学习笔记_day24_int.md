# Javaѧϰ�ʼ�_day24

[TOC]

## Lock�ӿ�

Lock ʵ���ṩ�˱�ʹ�� synchronized ���������ɻ�õĸ��㷺����������

### �����÷�

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

ע�⣺����һ��û��catch��try���

### ��������

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

### �߳�֮���ͨ��

�����߳����е�����ԣ����Ի�����߳�ͨ��ʱ���ݲ�ƥ������⣬���һ���������ݸ�����ͬ��CPU��һ��ʱ��Ƭ�ͻ����кö�Σ�����Ӧ�ü��������Ҳ�ͬ������߳�Ҫ�ӵ���������ͬһ��Ϊ�˽����������������ʹ��java�ĵ�̨���ѻ���

### �ȴ����ѻ���

>  Object�����ṩ������������
>
>   			wait():�ȴ�
>
>   			notify():���ѵ����߳�
>
>   			notifyAll():���������߳�

Ϊʲô��Щ������������Thread������?

> ��Щ�����ĵ��ñ���ͨ����������ã������Ǹղ�ʹ�õ�������������������  			���ԣ���Щ�������붨����Object���С�

#### ʾ������

ģ�������߳�֮���ͨ�ţ�һ��������ѧ����һ���Ƕ�ȡѧ��

```java
public class Student {
	String name;
	int age;
	boolean flag; // Ĭ�������û�����ݣ������true��˵��������
}
```

����ѧ������:SetThread(������)

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
				//�ж���û��
				if(s.flag){
					try {
						s.wait(); //t1���ţ��ͷ���
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
				}
				
				if (x % 2 == 0) {
					s.name = "����ϼ";
					s.age = 27;
				} else {
					s.name = "����";
					s.age = 30;
				}
				x++; //x=1
				
				//�޸ı��
				s.flag = true;
				//�����߳�
				s.notify(); //����t2,���Ѳ�����ʾ���������ִ�У����뻹����CPU��ִ��Ȩ��
			}
			//t1�У�����t2��
		}
	}
}
```

��ȡѧ�����ݣ�GetThread(������)

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
						s.wait(); //t2�͵ȴ��ˡ������ͷ����������ѹ�����ʱ���Ǵ������ѹ�����ʱ��
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
				}
				
				System.out.println(s.name + "---" + s.age);
				//����ϼ---27
				//����---30
				
				//�޸ı��
				s.flag = false;
				//�����߳�
				s.notify(); //����t1
			}
		}
	}
}
```

�����ࣺ

```java
public class StudentDemo {
	public static void main(String[] args) {
		//������Դ
		Student s = new Student();
		
		//���úͻ�ȡ����
		SetThread st = new SetThread(s);
		GetThread gt = new GetThread(s);

		//�߳���
		Thread t1 = new Thread(st);
		Thread t2 = new Thread(gt);

		//�����߳�
		t1.start();
		t2.start();
	}
}
```

ע�⣺flag���÷������޸�������֮��һ��Ҫ�޸�flag�ı�ǩ��������ʹ�̵߳ȴ�֮��һ��Ҫ�����߳�

���Ѳ�����ʾ���������ִ�У����뻹����CPU��ִ��Ȩ��

��Ŷ����wait����ʱ�����ͷ�����ʹ�����߳̿����õ���

## �߳�����״̬ͼ

![�߳�](http://oaxelf1sk.bkt.clouddn.com/�߳�.png)

## �߳���

Ĭ����������е��̶߳�����mian�߳���

> �߳�������ķ�����public final ThreadGroup getThreadGroup()

### �����߳���

```java
public Thread(ThreadGroup group,Runnable target,String name)
�����µ� Thread �����Ա㽫 target ��Ϊ�����ж��󣬽�ָ���� name ��Ϊ�����ƣ�����Ϊ group �����õ��߳����һԱ
```

## �̳߳�

�����½�һ���߳��˷���Դ���������̳߳أ��̳߳ؿ�����߳���Ч�ʣ���Ϊ�̳߳ػ�Ĭ�ϴ�������̣߳�������Щ�߳̿��Ի��գ��ظ����ã�

### Executors

JDK5������һ��Executors�������������̳߳�

#### �����̳߳صĹ���

- ����һ���̳߳ض��󣬿���Ҫ���������̶߳���

public static ExecutorService newFixedThreadPool(int nThreads)

- �����̳߳ص��߳̿���ִ�У�

����ִ��Runnable�������Callable����

- �������·�������

Future<?> submit(Runnable task)

<T> Future<T> submit(Callable<T> task)

- �����̳߳�

```java
public class ExecutorsDemo {
	public static void main(String[] args) {
		// ����һ���̳߳ض��󣬿���Ҫ���������̶߳���
		// public static ExecutorService newFixedThreadPool(int nThreads)
		ExecutorService pool = Executors.newFixedThreadPool(2);

		// ����ִ��Runnable�������Callable���������߳�
		pool.submit(new MyRunnable());
		pool.submit(new MyRunnable());

		//�����̳߳�
		pool.shutdown();
	}
}
```

## �����ڲ���

ʾ�����룺

```java
/*
 * �����ڲ���ĸ�ʽ��
 * 		new �������߽ӿ���() {
 * 			��д����;
 * 		};
 * 		���ʣ��Ǹ�����߽ӿڵ��������
 */
public class ThreadDemo {
	public static void main(String[] args) {
		// �̳�Thread����ʵ�ֶ��߳�
		new Thread() {
			public void run() {
				for (int x = 0; x < 100; x++) {
					System.out.println(Thread.currentThread().getName() + ":"
							+ x);
				}
			}
		}.start();

		// ʵ��Runnable�ӿ���ʵ�ֶ��߳�
		new Thread(new Runnable() {
			@Override
			public void run() {
				for (int x = 0; x < 100; x++) {
					System.out.println(Thread.currentThread().getName() + ":"
							+ x);
				}
			}
		}) { }.start();

		// �����Ѷȵ�
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

�����ַ�ʽ�൱������̳���threa�࣬ͬʱҲʵ����runnab�ӿڣ�ע����ʱ������ߵ���thread�е�run����

## Timer

������������ָ����ʱ����ĳ�����飬�������ظ�����ĳ������

- Timer:��ʱ

  		public Timer()
  		public void schedule(TimerTask task,long delay)
  		public void schedule(TimerTask task,long delay,long period)
  		public void cancel()

- TimerTask:����

  ?	public abstract void run()
  ?	public boolean cancel()

### ʾ������

```java
public class TimerDemo2 {
	public static void main(String[] args) {
		// ������ʱ������
		Timer t = new Timer();
		// 3���ִ�б�ը�����һ�Σ�������ɹ���ÿ��2���ټ���ը
		t.schedule(new MyTask2(), 3000, 2000);
	}
}

// ��һ������
class MyTask2 extends TimerTask {
	@Override
	public void run() {
		System.out.println("beng,��ը��");
	}
}
```

> public void schedule(TimerTask task,long delay,long period)�ڶ�������������Ǽ�����ٺ���ִ�У��������������������ٺ����ٴ�ִ��

����ִ�������jvm�����˳�����Ϊ����û��ֹͣ���Ľ�����Ǽ���cancel������

```java
public class TimerDemo {
	public static void main(String[] args) {
		// ������ʱ������
		Timer t = new Timer();
		// 3���ִ�б�ը����
		// t.schedule(new MyTask(), 3000);
		//��������
		t.schedule(new MyTask(t), 3000);
	}
}

// ��һ������
class MyTask extends TimerTask {

	private Timer t;
	
	public MyTask(){}
	
	public MyTask(Timer t){
		this.t = t;
	}
	
	@Override
	public void run() {
		System.out.println("beng,��ը��");
		t.cancel();
	}
}
```

### ɾ��ָ��Ŀ¼

```java
/*
 * ������ָ����ʱ��ɾ�����ǵ�ָ��Ŀ¼(�����ָ��c�̣������Ҳ����飬��ʹ����Ŀ·���µ�demo)
 */

class DeleteFolder extends TimerTask {

	@Override
	public void run() {
		File srcFolder = new File("demo");
		deleteFolder(srcFolder);
	}

	// �ݹ�ɾ��Ŀ¼
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

����ִ�������jvm�����˳�����Ϊ����û��ֹͣ

## ����������˼��

��һְ��ԭ�򣻿���ԭ�������滻ԭ������ע��ԭ�򣻽ӿڷ���ԭ�򣻵�����ԭ��

### ��һְ��ԭ��

���ǿ�����Ա����˵�ġ����ھۣ�����ϡ�
Ҳ����˵��ÿ����Ӧ��ֻ��һ��ְ�𣬶���ֻ���ṩһ�ֹ��ܣ���������仯��ԭ��Ӧ��ֻ��һ���������ģʽ�У����е����ģʽ����ѭ��һԭ��

### ����ԭ��

����˼���ǣ�һ���������չ���ţ����޸Ĺرա�
��ʵ����ԭ�����˼���ǣ�����ĸĶ���ͨ�����Ӵ�����еģ��������޸����д��롣

��Ҫ�����ڳ���Ͷ�̬�����ѿ��ܱ仯�����ݳ���������Ӷ�ʹ����Ĳ���������ȶ��ģ��������ʵ�����ǿ��Ըı����չ��

### �����滻ԭ��

����˼�룺���κθ�����ֵĵط������������������������
��ʵ����˵��ͬһ���̳���ϵ�еĶ���Ӧ���й�ͬ����Ϊ������

### ����ע��ԭ��

����˼�룺Ҫ�����ڳ��󣬲�Ҫ�����ھ���ʵ��

### �ӿڷ���ԭ��

����˼�룺��Ӧ��ǿ�ȳ����������ǲ���Ҫʹ�õķ�����
��ʵ����˵��һ���ӿڲ���Ҫ�ṩ̫�����Ϊ��һ���ӿ�Ӧ��ֻ�ṩһ�ֶ���Ĺ��ܣ���Ӧ�ð����еĲ�������װ��һ���ӿ��С�

### ������ԭ��

����˼�룺һ������Ӧ�����������󾡿����ٵ��˽�
��ʵ����˵�����͸�������֮�����ϣ����ϵͳ�Ŀ�ά���ԡ���ģ��֮��Ӧ��ֻͨ���ӿڱ�̣��������ģ����ڲ�����ԭ��������ʹ����ģ����϶Ƚ�����ͣ��ٽ�����ĸ���

## ���ģʽ

���ģʽ��Ϊ�˿����ô��롢�ô�������ױ�������⡢��֤����ɿ���

������ģʽ���򵥹���ģʽ����������ģʽ�����󹤳�ģʽ��������ģʽ��ԭ��ģʽ������ģʽ��(6��)
�ṹ��ģʽ�����ģʽ��������ģʽ������ģʽ��װ��ģʽ���Ž�ģʽ�����ģʽ����Ԫģʽ��(7��)
��Ϊ��ģʽ��ģ�淽��ģʽ���۲���ģʽ��״̬ģʽ��ְ����ģʽ������ģʽ��������ģʽ������ģʽ������¼ģʽ��������ģʽ��������ģʽ��(10��)

### �򵥹���ģʽ

������һ������Ĺ����ฺ�����Ĵ���

#### ��ȱ��

- �ŵ㣺�ͻ��˲���Ҫ�ڸ������Ĵ������Ӷ���ȷ�˸������ְ��
- ȱ�㣺�����̬�����ฺ�����ж���Ĵ�����������µĶ������ӣ�����ĳЩ����Ĵ�����ʽ��ͬ������Ҫ���ϵ��޸Ĺ����࣬�����ں��ڵ�ά��

### ��������ģʽFactory Method Pattern

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
		System.out.println("è����");
	}
}

public class Dog extends Animal {

	@Override
	public void eat() {
		System.out.println("������");
	}
}

public class AnimalDemo {
	public static void main(String[] args) {
		// ������Ҫ��ֻ��
		Factory f = new DogFactory();
		Animal a = f.createAnimal();
		a.eat();
		System.out.println("-------");
		
		//������Ҫ��ֻè
		f = new CatFactory();
		a = f.createAnimal();
		a.eat();
	}
}
```

### ����ģʽ

����ģʽ����Ҫȷ�������ڴ���ֻ��һ�����󣬸�ʵ�������Զ����������Ҷ����ṩ

* 		����ʽ����һ���ؾʹ������󣬲����������
* 		����ʽ���õ�ʱ�򣬲�ȥ�������󣬻�������⣨�����ز��ᣬ�̰߳�ȫ��������⣩

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

ע������ʽҪ����synchronized

#### ��ȱ��

- �ŵ㣺��ϵͳ�ڴ���ֻ����һ��������˿��Խ�Լϵͳ��Դ������һЩ��ҪƵ�����������ٵĶ�����ģʽ���ɿ������ϵͳ������
- ȱ�㣺û�г���㣬�����չ���ѡ�ְ����أ���һ��������Υ���˵�һְ��

#### ��֤�����ڴ���ֻ��һ������

* 		�ѹ��췽��˽��
* 		�ڳ�Աλ���Լ�����һ������
* 		ͨ��һ�������ķ����ṩ����

```java
public class Student {
	//����ʽ����һ���ؾʹ�������
    // ����˽��
	private Student() {
	}

	// �Լ���һ��
	// ��̬����ֻ�ܷ��ʾ�̬��Ա�������Ӿ�̬
	// Ϊ�˲������ֱ�ӷ����޸����ֵ����private
	private static Student s = new Student();

	// �ṩ�����ķ��ʷ�ʽ
	// Ϊ�˱�֤����ܹ�ֱ��ʹ�ø÷������Ӿ�̬
	public static Student getStudent() {
		return s;
	}
}
```

## Runtime

exec(String command)����ֱ������dos���Ӧ�����ĳ������

### Դ�����

```java
//����ʽ 
class Runtime {
  		private Runtime() {}
  		private static Runtime currentRuntime = new Runtime();
  		public static Runtime getRuntime() {
        	return currentRuntime;
    	}
  }
```
