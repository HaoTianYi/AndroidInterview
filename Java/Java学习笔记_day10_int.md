# Javaѧϰ�ʼ�_day10

[TOC]

## ��ʽ�����ͷ���ֵ

- ��ʽ������

1. ������������

2. ������������

   1. �ࣺҪ���Ǹ���Ķ���
   2. �ӿڣ��ӿ�Ҳ����new���������Կ�����Ϊ��ʽ�������ݸ�������һ���Ǵ���һ���ýӿڵ�ʵ����Ķ���ͬ�������˶�̬��
   3. �����ࣺ�����new���������Կ�����Ϊ��ʽ�������ݸ�������һ����÷��ǰѳ�������Ϊ��ʽ���������Ǵ���**һ���������ʵ����**�Ķ��󣻣������˶�̬��

   ʾ�����룺

   ```java
   interface Love {
   	public abstract void love();
   }

   class LoveDemo {
   	public void method(Love l) { //l; l = new Teacher();  Love l = new Teacher(); ��̬
   		l.love();
   	}
   }

   //���������ʵ�ֽӿ�
   class Teacher implements Love {
   	public void love() {
   		System.out.println("��ʦ��ѧ��,��Java,������ϼ");
   	}
   }

   class TeacherTest {
   	public static void main(String[] args) {
   		//������Ҫ����LoveDemo���е�love()����
   		LoveDemo ld = new LoveDemo();
   		Love l = new Teacher();
   		ld.method(l);
   	}
   }
   ```

- ����ֵ

1. ������������
2. ������������
   1. �ࣺ���ص��Ǹ���Ķ���
   2. �����ࣺ���ص��Ǹ����ʵ����Ķ���
   3. �ӿڣ����ص��Ǹ���������ʵ�ֶ���

ʾ�����룺

```java
interface Love {
	public abstract void love();
}

class LoveDemo {
	public Love getLove() {
		//Love l = new Teacher();
		//return l;
		
		return new Teacher();
	}
}

class Teacher1 implements Love {
	public void love() {
		System.out.println("��ʦ��ѧ��,��Java,������ϼ1111111111");
	}
}
//���������ʵ�ֽӿ�
class Teacher implements Love {
	public void love() {
		System.out.println("��ʦ��ѧ��,��Java,������ϼ2222222");
	}
}

class ExtendsTest {
	public static void main(String[] args) {
		//��β�����?
		LoveDemo ld = new LoveDemo();
		Love l = ld.getLove(); //new Teacher(); Love l = new Teacher(); ��̬
		l.love();
	}
}
```

��������LoveDemo��getLove�������ص���Teacher���͵Ķ������Զ�̬��l������Զ�����love��ʵ����Teacher��

�����getLove�������ص���new Love��ôϵͳ���Զ����㴴��һ��������ʵ����Ķ���

## ��

> package�������ǳ���ĵ�һ����ִ�еĴ��룬package�����һ��class�ļ���ֻ����һ�������û��package����ʾĬ���ް���

### ���а���java�ļ�������

- �ֶ�
  1. ��д����package����java�ļ�
  2. javac���룬�����ֶ�������
  3. ��class�Ž����У��ص������ĸ�Ŀ¼
  4. java����**��java ���� �ļ���**
- �Զ�
  1. javac����ʱ����**javac  -d   .  �ļ���.java**
  2. ֻ���Զ�������Ȼ����**java ����  �ļ���**����

 ## Ȩ�����η�

![Javaѧϰ�ʼ�_day10_Ȩ�����η�](http://oaxelf1sk.bkt.clouddn.com/Javaѧϰ�ʼ�_day10_Ȩ�����η�.png)

Ȩ�����η���public protect  Ĭ��  private

״̬���η���static  final 

�������η���abstract

�ࣺ

?	Ȩ�����η���Ĭ�ϣ�public��private�����ԣ�**�ڲ��������private����**

?	״̬���η���final��static�����ԣ�**�ڲ���Ҳ������static**

?	�������η���abstract 

?	�ڲ�����൱���ⲿ���һ����Ա���������Կ���ʹ��private��static����

��Ա����

?	Ȩ�����η���������

?	״̬���η���������

?	�������η���������

��Ա����

?	Ȩ�����η���������

?	״̬���η���������

?	�������η���������

## �ڲ���

### �����ص�

1. �ڲ������ֱ�ӷ����ⲿ��ĳ�Ա����������˽�б�����
2. �ⲿ�಻����ֱ�ӷ����ڲ���ĳ�Ա��һ��Ҫ��������

### �ڲ����λ��

1. ��Ա�ڲ���
2. �ֲ��ڲ��ࣨ�ڷ����ﶨ����ڲ��ࣩ

### ���ʲ�ͬ�ڲ����ڵķ���

- ��Ա�ڲ��ࣺ

```java
	//�ⲿ����.�ڲ����� ������ = �ⲿ�����.�ڲ������;
	Outer.Inner oi = new Outer().new Inner();

	//��Ա�ڲ��౻static���κ�ķ��ʷ�ʽ�ǣ�
	//�ⲿ����.�ڲ����� ������ = �ⲿ����.�ڲ�����();
	Outer.Inner oi = new Outor.Inner();
	//�������ⲿ����.�ڲ�����.������
	Outer.Inner.show();
```

- �ֲ��ڲ��ࣺ

```java
	//�ھֲ�λ�ã����Դ����ֲ������ͨ����������ڲ��෽��
	//�ֲ��ڲ�����ʾֲ��������ֲ�����һ��Ҫʹ��fianl���ξֲ�����
```

ʹ��final��ԭ��

?	��Ϊ�ֲ����������ŷ����ĵ�����϶����٣���ʱ��ֲ�����û������Ӷ��ڴ�����ʧ�������õ��ֲ�����������Ϊ�����þֲ���������ʹ�ã�����fianl���Σ������ڶ��ڴ�����ʵ�洢����һ������ֵ��

### ��ͬ�ڲ�������η�

- ��Ա�ڲ���

  ��Ա�ڲ��������static���Σ���ʱ������public static���൱���ⲿ���һ���ֲ�������

  �ֲ��ڲ��������private���Σ��൱���ⲿ���˽�г�Ա������

  ����̬���εĳ�Ա�ڲ���ֻ�ܷ����ⲿ��ľ�̬��Ա

### �ڲ����������

- �ֲ��ڲ���

```java
�������(ע��:�ڲ�����ⲿ��û�м̳й�ϵ)
	class Outer {
		public int num = 10;
		class Inner {
			public int num = 20;
			public void show() {
				int num = 30;
				System.out.println(?);
				System.out.println(??);
				System.out.println(???);
			}
		}
	}
�ڿ��Ʒֱ������30��20��10
```

�ο��𰸣�

```java
			System.out.println(num);
			System.out.println(this.num);
			//System.out.println(new Outer().num);
			System.out.println(Outer.this.num);
```

ע�⣺**ͨ���ⲿ��������this**

- �����ڲ���

```java
����Ҫ�󣬲������
	interface Inter { void show(); }
	class Outer { //������� }
	class OuterDemo {
	    public static void main(String[] args) {
		      Outer.method().show();
		  }
	}
Ҫ���ڿ���̨�����HelloWorld��
```

�ο��𰸣�

```java
interface Inter { 
	void show(); 
	//public abstract
}

class Outer { 
	//�������
	public static Inter method() {
		//������� -- ������������
		return new Inter() {
			public void show() {
				System.out.println("HelloWorld");
			}
		};
	}
}

class OuterDemo {
	public static void main(String[] args) {
		Outer.method().show();
		/*
			1:Outer.method()���Կ���method()Ӧ����Outer�е�һ����̬������
			2:Outer.method().show()���Կ���method()�����ķ���ֵ��һ������
				�����ڽӿ�Inter����һ��show()����,��������Ϊmethod()�����ķ���ֵ������һ���ӿڡ�
		*/
	}
}
```

### �����ڲ���

- ǰ�᣺����һ�������һ���нӿ�
- ��ʽ�ǣ�new �������߽ӿ�������{��д������}
- ���ʣ�**��һ���̳��˸������ʵ�ָýӿڵ��������������**
- �����ڲ��෽���ĵ��ã�

```java
interface Inter {
	public abstract void show();
	public abstract void show2();
}

class Outer {
	public void method() {
		//һ��������ʱ��
		/*
		new Inter() {
			public void show() {
				System.out.println("show");
			}
		}.show();
		*/
		
		//����������ʱ��
		/*
		new Inter() {
			public void show() {
				System.out.println("show");
			}
			
			public void show2() {
				System.out.println("show2");
			}
		}.show();
		
		new Inter() {
			public void show() {
				System.out.println("show");
			}
			
			public void show2() {
				System.out.println("show2");
			}
		}.show2();
		*/
		
		//������Ǻܶ���������ͺ��鷳��
		//��ô��������û�иĽ��ķ�����?
		Inter i = new Inter() { //��̬
			public void show() {
				System.out.println("show");
			}
			
			public void show2() {
				System.out.println("show2");
			}
		};
		
		i.show();
		i.show2();
	}
}

class InnerClassDemo6 {
	public static void main(String[] args) {
		Outer o = new Outer();
		o.method();
	}
}
```

�����ڲ����Ӧ�ã�

```java
interface Person {
	public abstract void study();
}

class PersonDemo {
	//�ӿ�����Ϊ��ʽ����
	//��ʵ������Ҫ�Ĳ��ǽӿڣ����Ǹýӿڵ�ʵ����Ķ���
	public void method(Person p) {
		p.study();
	}
}

//ʵ����
class Student implements Person {
	public void study() {
		System.out.println("�ú�ѧϰ,��������");
	}
}

class InnerClassTest2 {
	public static void main(String[] args) {
		//����
		PersonDemo pd = new PersonDemo();
		Person p = new Student();
		pd.method(p);
		System.out.println("--------------------");
		
		//�����ڲ����ڿ����е�ʹ��
		//�����ڲ���ı����Ǽ̳������ʵ���˽ӿڵ�������������
		pd.method(new Person(){
			public void study() {
				System.out.println("�ú�ѧϰ,��������");
			}
		});
	}
}
```

ʹ�ýӿ�����Ϊ��ʽ��������������Ҫһ���ӿڵ�ʵ������󣬶������ڲ���ı�����һ��ʵ���˽ӿڵ�����**����**��
