# Javaѧϰ�ʼ�_day23

[TOC]

## ���߳�

���̾����������еĳ��򣬽�����ϵͳ������Դ�͵��õĶ�����λ��ÿһ�����̶����Լ����ڴ�ռ��ϵͳ��Դ

����̿�������CPU��ʹ���ʡ�

�߳����ǳ����ִ�е�Ԫ����ʹ��CPU���������λ��

����̵��������ڶ������֮�䣬���̵߳Ľ����ܹ��и��ߵļ���ǩ��CPU��ִ��Ȩ

### �̵߳���

#### ��ʱ����ģ��

ƽ������ÿ���߳�ռ��CPU��ʱ��Ƭ

#### ���ȵ���ģ��

���������ȼ��ߵ��߳�ʹ��CPU���̼߳�����ͬ�����ѡ��һ����javaʹ�õ������ȵ���ģ��

### �����Ͳ���

��������ĳһ��ʱ���ͬʱ���ж���������߼���ͬʱ����

������ĳһ��ʱ���ͬʱ���ж��������������ͬʱ����

### jvm�Ƿ��ǵ��̣߳�

���Ƕ��̣߳���Ϊ���������߳�ҲҪ���������������ڴ���������һ�Ҫ�������߳�������main�����ȣ����������������̡߳�

### ��Ա����

#### ��ȡ�߳�����

> public final String getName():��ȡ�̵߳����ơ�
>
> public final void setName(String name):�����̵߳�����
>
> public static Thread currentThread():���ص�ǰ����ִ�е��̶߳���

#### ��ȡ�߳����ȼ�

> public final int getPriority():�����̶߳�������ȼ�
>
> public final void setPriority(int newPriority)�������̵߳����ȼ�

ע�⣺�߳�Ĭ�����ȼ���5���߳����ȼ��ķ�Χ�ǣ�1-10���߳����ȼ��߽�����ʾ�̻߳�ȡ�� CPUʱ��Ƭ�ļ��ʸߣ�����Ҫ�ڴ����Ƚ϶࣬���߶�����е�ʱ����ܿ����ȽϺõ�Ч����

#### �߳̿���

> public static void sleep(long millis)�߳�����millis����
>
> public final void join()���ô˷������߳�Ҫ��ִ���꣬Ȼ�����ִ�������߳�
>
> public static void yield()��ͣ��ǰ����ִ�е��̶߳��󣬲�ִ�������̣߳�ע����������͵�һ�������Ǿ�̬���������ڶ�������Ҫͨ���̶߳�����ã��������������һ���̶���ʹ�̵߳�ִ�и��ӻ�����ȣ������Ǿ���
>
> public final void setDaemon(boolean on)�ػ��߳�

```java
/*
 * public final void setDaemon(boolean on):�����̱߳��Ϊ�ػ��̻߳��û��̡߳�
 * ���������е��̶߳����ػ��߳�ʱ��Java ������˳��� �÷��������������߳�ǰ���á� 
 * ���Ե����ػ����߳���ֹʱ���ػ��̲߳��������˳���������ִ����һ��
 */
public class ThreadDaemonDemo {
	public static void main(String[] args) {
		ThreadDaemon td1 = new ThreadDaemon();
		ThreadDaemon td2 = new ThreadDaemon();

		td1.setName("����");
		td2.setName("�ŷ�");

		// �����ջ��߳�
		td1.setDaemon(true);
		td2.setDaemon(true);

		td1.start();
		td2.start();

		Thread.currentThread().setName("����");
		for (int x = 0; x < 5; x++) {
			System.out.println(Thread.currentThread().getName() + ":" + x);
		}
	}
}
```

>public final void stop():���߳�ֹͣ����ʱ�ˣ����ǻ�����ʹ�á�
>
>public void interrupt():�ж��̡߳� ���̵߳�״̬��ֹ�����׳�һ��InterruptedException��

### �̵߳���������

1. �½��������̶߳���
2. �������̶߳����Ѿ���������ִ���ʸ񣬵���û��CPU��ִ��Ȩ
3. ���У���ִ���ʸ���ִ��Ȩ
4. ������û��ִ���ʸ�û��ִ��Ȩ��������������ִ���ʸ�û��ִ��ȨҲ���Ǿ���״̬
5. �������̶߳������������ȴ�����

![��������](http://oaxelf1sk.bkt.clouddn.com/��������.png)



### �������̵߳ķ�ʽ

#### �̳�Thread��

1. �̳�Threadlei
2. ��дrun����
3. ��������
4. ����start�����������߳�

```java
		 MyThread my = new MyThread();
		 // �����߳�
		 my.run();
		 my.run();
//		 IllegalThreadStateException:�Ƿ����߳�״̬�쳣
//		 ��Ϊ����൱����my�̱߳����������Ρ������������߳�������
```

ע�⣺�̲߳��ܱ��������

run()��start()������

```java
		// run():�����Ƿ�װ���߳�ִ�еĴ��룬ֱ�ӵ�������ͨ����
		// start():�����������̣߳�Ȼ������jvmȥ���ø��̵߳�run()������start�������������裬��run����ֻ�ǵײ��װ�����߳�ִ�еĳ���
```

#### ʵ��Runnable�ӿ�

1. ����Runnable��ʵ������
2. ��дrun������Runnableֻ��һ������
3. ��������Ķ���
4. �����̶߳��󣬲��Ұ�runnable������ʵ�ֶ�����

```java
public class MyRunnable implements Runnable {

	@Override
	public void run() {
		for (int x = 0; x < 100; x++) {
			// ����ʵ�ֽӿڵķ�ʽ�Ͳ���ֱ��ʹ��Thread��ķ�����,���ǿ��Լ�ӵ�ʹ��
			System.out.println(Thread.currentThread().getName() + ":" + x);
		}
	}
}
```
ע�⣺���õ�ǰ�̵߳ķ���������ֵķ�����Ҳ�ǵ�������Thread������˼��
```java
	public static void main(String[] args) {
		// ����MyRunnable��Ķ���
		MyRunnable my = new MyRunnable();

		// ����Thread��Ķ��󣬲���C����Ķ�����Ϊ�����������
		// Thread(Runnable target)
		 Thread t1 = new Thread(my);
		 Thread t2 = new Thread(my);
		 t1.setName("����ϼ");
		 t2.setName("����");

		// Thread(Runnable target, String name)
		Thread t1 = new Thread(my, "����ϼ");
		Thread t2 = new Thread(my, "����");

		t1.start();
		t2.start();
	}
```
#### ��ʽ2�ĺô�

1. ���Ա���java���̳е�ȱ��
2. �ʺ϶��������ͬһ����Դ�����

#### �����ַ�ʽ

�������̳߳�

```java
//Callable:�Ǵ����͵Ľӿڡ�
//����ָ���ķ�����ʵ��call()�����ķ���ֵ���͡�
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

�����ǿ��Դ��з���ֵ������������

ʾ�����룺

```java
/*
 * �߳���Ͱ���
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
		// �����̳߳ض���
		ExecutorService pool = Executors.newFixedThreadPool(2);

		// ����ִ��Runnable�������Callable���������߳�
		Future<Integer> f1 = pool.submit(new MyCallable(100));
		Future<Integer> f2 = pool.submit(new MyCallable(200));

		// V get()
		Integer i1 = f1.get();
		Integer i2 = f2.get();

		System.out.println(i1);
		System.out.println(i2);

		// ����
		pool.shutdown();
	}
}

```

### ��ӰƱ��ϰ��

ĳ��ӰԺĿǰ������ӳ�����Ƭ������100��Ʊ��������3����Ʊ������Ʊ�������һ������ģ��õ�ӰԺ��Ʊ

#### ʵ�ֵ�ӰƱ�Ĵ���

```java
public class SellTicket extends Thread {

	// ����100��Ʊ
	// private int tickets = 100;
	// Ϊ���ö���̶߳�������100��Ʊ��������ʵӦ���þ�̬����
	private static int tickets = 100;

	@Override
	public void run() {
		// ����100��Ʊ
		// ÿ���߳̽�����������������Ļ���ÿ���̶߳����൱��������Լ�����100��Ʊ���ⲻ��������Ӧ�ö��嵽����
		// int tickets = 100;

		// ��Ϊ��ģ��һֱ��Ʊ
		while (true) {
			if (tickets > 0) {
				System.out.println(getName() + "���ڳ��۵�" + (tickets--) + "��Ʊ");
			}
		}
	}
}
```

ע�⣺��Ҫÿ����Ķ��󵥶���һ����Ա������ô�Ϳ���ʱprivate int�����Σ��������ô�������ж�����һ����Ա��������ô��Ӧ����privat static int ������

#### ʵ�ֲ�����

```java
public class SellTicketDemo {
	public static void main(String[] args) {
		// ���������̶߳���
		SellTicket st1 = new SellTicket();
		SellTicket st2 = new SellTicket();
		SellTicket st3 = new SellTicket();

		// ���̶߳���������
		st1.setName("����1");
		st2.setName("����2");
		st3.setName("����3");

		// �����߳�
		st1.start();
		st2.start();
		st3.start();
	}
}
```

### ʹ�÷�ʽ����ʵ��

#### ģ����Ʊ��

```java
public class SellTicket implements Runnable {
	// ����100��Ʊ
	private int tickets = 100;

	@Override
	public void run() {
		while (true) {
			if (tickets > 0) {
				System.out.println(Thread.currentThread().getName() + "���ڳ��۵�"
						+ (tickets--) + "��Ʊ");
			}
		}
	}
}
```

ע�⣺������ϸ�������ststic���β�ͬ

#### д������

```java
public class SellTicketDemo {
	public static void main(String[] args) {
		// ������Դ����
		SellTicket st = new SellTicket();

		// ���������̶߳���
		Thread t1 = new Thread(st, "����1");
		Thread t2 = new Thread(st, "����2");
		Thread t3 = new Thread(st, "����3");

		// �����߳�
		t1.start();
		t2.start();
		t3.start();
	}
}
```

### ��ӰƱ�Ľ���

ÿ����Ʊ�ӳ�100����

- ���⣺�������˸�����Ʊ

����Ժ��ӳٵ���

- ������ͬ��Ʊ

CPU��һ�β�������ʱԭ���Ե�

##ͬ������

### �ж��̰߳�ȫ�����ԭ��

1. �Ƿ��Ƕ��̵߳Ļ���
2. �Ƿ��й�������
3. �Ƿ��ж�����������������

> �жϱ�׼ͬʱҲ�ǽ�������˼·����ʹ����������������������

### ͬ�������

```java
  ͬ������飺
  		synchronized(����){
  			��Ҫͬ���Ĵ���;Ҳ���Ƕ����������������ݵĴ����
  		}
```

ע�⣺ͬ����ȫ�Ĺؼ��������Ǹ����󣬶���̱߳�����ͬһ����

```java
	private int tickets = 100;
	//����������
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
							+ "���ڳ��۵�" + (tickets--) + "��Ʊ");
				}
			}
		}
	}
```

### ͬ�����ص�

1. ����߳�
2. ����߳�ʹ�õ���ͬ��������

### ͬ������ȱ��

�ŵ㣺����˶��̵߳İ�ȫ����

ȱ�㣺ÿ���̶߳����ж������ķ���Դ������Ч��

### ͬ������������

- ͬ����������������˭��

�������

- ͬ����������������˭��

this

- ͬ����������������˭��

����ֽ����ļ�

## �ع���ǰ��İ�ȫ

```java
		// �̰߳�ȫ����
		StringBuffer sb = new StringBuffer();
		Vector<String> v = new Vector<String>();
		Hashtable<String, String> h = new Hashtable<String, String>();
```

```java
		// Vector���̰߳�ȫ��ʱ���ȥ����ʹ�õģ������һ�˵����ʹҪ��ȫ����Ҳ������
		// ��ô������˭��?
		// public static <T> List<T> synchronizedList(List<T> list)
		List<String> list1 = new ArrayList<String>();// �̲߳���ȫ
		List<String> list2 = Collections
				.synchronizedList(new ArrayList<String>()); // �̰߳�ȫ
```
