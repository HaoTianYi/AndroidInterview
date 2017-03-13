# Javaѧϰ�ʼ�

[TOC]


## ��ά����

### ��ʾ����

```java
		A:��������[][] ������ = new ��������[m][n];
		B:��������[][] ������ = new ��������[m][];
		C:��������[][] ������ = new ��������[][]{{...},{...},{...}};
		D:��������[][] ������ = {{...},{...},{...}};
```

### ��ά������ڴ�ʾ��ͼ

![��](http://oaxelf1sk.bkt.clouddn.com/��.png)

> �����������ȳ�ʼ��������һλ���飨�������ͣ���ʼֵ��null����Ȼ���ڲ����鿪ʼ��ʼ����Ĭ����0�������Ұ��ڲ�ĵ�ֵַ���������飬����ٰ��ܵ�ֵַ����ջ��

**ע��**

```java
int[] array = {4,5,6,7,9,8};
```

������������ڴ������ڶ��У���Ϊ��ʡ����new��

## ��ϰ��

- ��������ӡ�����

```java
class ArgsDemo {
	public static void main(String[] args) {
		int a = 10;
		int b = 20;
		System.out.println("a:"+a+",b:"+b); //a:10,b:20
		change(a,b);
		System.out.println("a:"+a+",b:"+b); //???	a:10,b:20

		int[] arr = {1,2,3,4,5}; 
		change(arr);
		System.out.println(arr[1]); //???	4
	}

	public static void change(int a,int b) { //a=10,b=20
		System.out.println("a:"+a+",b:"+b); //a:10,b:20
		a = b;	//a=20
		b = a + b; //b=40
		System.out.println("a:"+a+",b:"+b); //a:20,b:40
	}

	public static void change(int[] arr) { //arr={1,2,3,4,5};
		for(int x=0; x<arr.length; x++) {
			if(arr[x]%2==0) {
				arr[x]*=2;
			}
		}
		//arr={1,4,3,8,5};
	}
}
```

д��������ӡ��ֵ�Ƕ��٣�������

**�ο���**

> **��������������ʵ�ʲ������ݸ��β�ʱ���ݵ���ֵ�������������ʹ��ݵ������ݵ�����**

���ǿ��Ի��������ڴ�ͼ��

![ice_screenshot_20160726-163627](http://oaxelf1sk.bkt.clouddn.com/ice_screenshot_20160726-163627.png)



ÿ����ջ�ڵķ������������ݣ�����ֻ��ջ�ڵ����ݵ���Դ��һ������ֵ��һ�������õĵ�ַ��

> �������ͣ���ʽ�����ĸı��ʵ�ʲ���û��Ӱ�졣	
>
> �������ͣ���ʽ�����ĸı�ֱ��Ӱ��ʵ�ʲ�����

- ĳ����˾���ù��õ绰����������Ϣ��������С��8λ��������Ϊ��ȷ����ȫ�����ݹ�������Ҫ���ܣ����ܹ������£����Ƚ����ݵ���Ȼ��ÿλ���ֶ�����5�����úͳ���10��������������֣���󽫵�һλ�����һλ���ֽ����� ���������һ��С��8λ��������Ȼ�󣬰Ѽ��ܺ�Ľ���ڿ���̨��ӡ������ 



> ������֪��
>
> ����ȷ��λ����Ȼ�������ת�������飬�ڽ��в���

```java
	public static void main(String[] args) {
		//����һ������
		int number = 123456;
		
		//����һ������
		int[] arr = new int[8];
		
		//��������ÿһλ�ϵ����ݻ�ȡ����洢��������
		/*
		int index = 0;
		arr[index] = number%10; //arr[0]=6;
		index++;
		arr[index] = number/10%10; //arr[1]=5;
		index++;
		arr[index] = mumber/10/10%10; //arr[2]=4;
		*/
		
		//ͨ���۲�������룬���Ƿ���Ӧ���ǿ���ͨ��ѭ���Ľ���
		int index = 0;
		
		while(number > 0) { //number=123456,number=12345,number=1234,number=123,number=12,number=1,number=0
			arr[index] = number%10; //arr[0]=6,arr[1]=5,arr[2]=4,arr[3]=3,arr[4]=2,arr[5]=1
			index++;//index=1,index=2,index=3,index=4,index=5,index=6
			number/=10;//number=12345,number=1234,number=123,number=12,number=1,number=0
		}
		
		//Ȼ��ÿλ���ֶ�����5�����úͳ���10���������������
		for(int x=0; x<index; x++) {
			arr[x] += 5;
			arr[x] %= 10;
		}
		
		//��󽫵�һλ�����һλ���ֽ���
		int temp = arr[0];
		arr[0] = arr[index-1];
		arr[index-1] = temp;
		
		//�������
		for(int x=0; x<index; x++) {
			System.out.print(arr[x]);
		}
		System.out.println();
	}
```

������ص��ǰ�int�͵�����ÿ��������ȡ������

```java
        int num = 12345679;
        int index = 0;
        int[] array = new int[9];
        while (num > 0) {
            array[index] = num % 10;
            num /= 10;
            index++;
        }
```

�Ľ���Ĵ����ǣ�

```java
	public static void main(String[] args) {
		//��������¼�����
		Scanner sc = new Scanner(System.in);
		
		//������һ������
		System.out.println("������һ������(С��8λ)��");
		int number = sc.nextInt();
		
		//д����ʵ�ְ�number���м���
		//����
		String result = jiaMi(number);
		System.out.println("���ܺ�Ľ���ǣ�"+result);
	}
	
	/*
		����дһ�����ܣ�������numberʵ�ּ��ܡ�
		������ȷ��
			����ֵ���ͣ�String ��һ���ַ�����ƴ�ӡ�
			�����б�int number
	*/
	public static String jiaMi(int number) {
		//��������
		int[] arr = new int[8];
		
		//��������
		int index = 0;
		
		//��number�е�������취�ŵ�������
		while(number > 0) {
			arr[index] = number%10;
			index++;
			number /= 10;
		}
		
		//��ÿ�����ݼ�5��Ȼ���10ȡ������
		for(int x=0; x<index; x++) {
			arr[x] += 5;
			arr[x] %= 10;
		}
		
		//�ѵ�һλ�����һλ����
		int temp = arr[0];
		arr[0] = arr[index-1];
		arr[index-1] = temp;
		
		//�������Ԫ��ƴ�ӳ�һ���ַ�������
		//����һ���������ַ���
		String s = "";
		
		for(int x=0; x<index; x++) {
			s += arr[x];
		}
		
		return s;
	}
```

��������while��ע��while������

## �������

### ��������س�

- ��װ(encapsulation)
- �̳�(inheritance)
- ��̬(polymorphism)

### ��Ͷ���Ĺ�ϵ

> ����һ����ص����ࣨ��Ա���ԣ�����Ϊ����Ա��Ϊ���ļ��ϣ���һ�������������Ǹ���ľ��������ʽ��������ڵĸ��塣

### ������ڴ�ͼ

- һ��������ڴ�ͼ

```java
class Phone {
	//Ʒ��
	String brand;
	//�۸�
	int price;
	//��ɫ
	String color;
	
	//��绰�ķ���
	public void call(String name) {
		System.out.println("��"+name+"��绰");
	}
	
	//�����ŵķ���
	public void sendMessage() {
		System.out.println("Ⱥ������");
	}
	
	//����Ϸ�ķ���
	public void playGame() {
		System.out.println("����Ϸ");
	}
}

class PhoneDemo {
	public static void main(String[] args) {
		//�����ֻ�����
		//���� ������ = new ����();
		Phone p = new Phone();
		
		//ֱ�������Ա����ֵ
		System.out.println(p.brand+"---"+p.price+"---"+p.color);
		
		//����Ա������ֵ
		p.brand = "ŵ����";
		p.price = 100;
		p.color = "��ɫ";
		//�ٴ����
		System.out.println(p.brand+"---"+p.price+"---"+p.color);
		
		//���÷���
		p.call("����ϼ");
		p.sendMessage();
		p.playGame();
	}
}
```

��ͼ���������࣬���Ҷ��ú��з���������Ӧ���ڷ��������ٿռ䣬��ʼִ��main���������������Ͷ�������ʼ��������������main������ջ����main�����п���p����Ŀռ䣬���ҿ���new�������ڶ��п���new Phone�Ŀռ䣬��ʱ�ĳ�ʼֵ��brand��null��price��0��color��null����ӡ�����

Ȼ����ջ�еĶ���p����phone������ֵ�������е�Ԫ��ֵ����ʱ���е�Ԫ����ֵ����ӡ��������

p������÷��������Է������еķ�����ջ��ִ�в��������������֮�����ٷ������ͷ��ڴ棻

���mian���������٣��ڴ��ͷš�

![ice_screenshot_20160726-163742](http://oaxelf1sk.bkt.clouddn.com/ice_screenshot_20160726-163742.png)

- ����������ڴ�ʾ��ͼ

```java
//�޸�����Ĵ���
class PhoneDemo {
	public static void main(String[] args) {
		//�����ֻ�����
		//���� ������ = new ����();
		Phone p = new Phone();
		
		//ֱ�������Ա����ֵ
		System.out.println(p.brand+"---"+p.price+"---"+p.color);
		
		//����Ա������ֵ
		p.brand = "ŵ����";
		p.price = 100;
		p.color = "��ɫ";
		//�ٴ����
		System.out.println(p.brand+"---"+p.price+"---"+p.color);
		
		//���÷���
		p.call("����ϼ");
		p.sendMessage();
		p.playGame();
		
		Phone p2 = p��
		p2.brand = "iphone";
		p2.price = 5000;
		p2.color = "green";
		System.out.println(p.brand+"---"+p.price+"---"+p.color);
	}
}
```

**ע��**

ֻ��������������Phone p2 = p���������ǰ�p�����ô��ݸ�p2�������ǣ�

```java
		p2.brand = p.brand;
```

����ֻ�Ǵ���һ��ֵ����û�а����ô��ݸ�p2����p2����ʱ��p�����Ӧ���ڵ�����ֵ����
