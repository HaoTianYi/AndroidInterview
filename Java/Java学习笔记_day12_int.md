# Javaѧϰ�ʼ�_day12

[TOC]



## Scanner��

### ���췽��

Scanner��JDK5�Ժ����ڻ�ȡ�û��������룬һ������ʹ��������ʽ�������������ͺ��ַ����ļ��ı�ɨ������Pattern������������ʽ���﷨

```java
	public Scanner��InputStream source����
```

### Scanner��ĳ�Ա����

1. hasNextXXX�����ж���һ�����������ֵ��boolena��
2. nextXxxx������ȡ��һ��������

> InputMismatchException������ĺ�����Ҫ�Ĳ�ƥ��

���õ�����������

1. public int nextInt������ȡһ��int���͵�ֵ��
2. public int nextLine������ȡString���͵�ֵ��

### С����

- �Ȼ�ȡһ��int��double���ڻ�ȡһ��string,�����ȡ�ַ�����ֻ���������һ����ֵ���¿ո񲻻����������⣬ֻ�а��»س��Ż�������⣻
- ��Ҫԭ���ǻ��з�\r������




- ����������Ȼ�ȡһ����ֵ�ڴ���һ���µĶ����ڻ�ȡ��һ����ֵ��������ȫ����Ҫ��ȡ����ֵ��ֵΪString���ڽ�������ת����

## String��

### ����

1. String����һ������
2. String��һ��������

> ֱ�������������һ���������ı�����һ������toString������û����д���ӡ����ϣֵ��

### ���췽��

```java
  		public String():�չ���
 		public String(byte[] bytes):���ֽ�����ת���ַ���
 		public String(byte[] bytes,int index,int length):���ֽ������һ����ת���ַ���
 		public String(char[] value):���ַ�����ת���ַ���
 		public String(char[] value,int index,int count):���ַ������һ����ת���ַ���
 		public String(String original):���ַ�������ֵת���ַ���
```

���췽��һ��

```java
		byte[] bys = { 97, 98, 99, 100, 101 };
		String s2 = new String(bys);
		System.out.println("s2:" + s2);
```

���Ĵ�Ӧ����ǣ�

abcde�������Ȱ���ֵת�����ַ��������ӳ��ַ������飻

```java
 		public String(byte[] bytes,int index,int length):���ֽ������һ����ת���ַ���
```

index��ʾ�ӵڼ�����ֵ��ʼ��length��ʾ��ȡ����

### ��ϰ��

- String s = "hello"; s += "world";s�Ľ���Ƕ��٣�

![ice_screenshot_20160806-081705](http://oaxelf1sk.bkt.clouddn.com/ice_screenshot_20160806-081705.png)

�ַ���һ������ֵ�Ͳ����ı䣬���ܱ��ı����ֵ�������Ƕ���s��

�ǳ���ֵ�ͻᵽ�������ĳ������в��ң�

1. ����string s����һ���µĶ�����ջ��
2. Ȼ�����ڷ������е��ַ��������ز����Ƿ����ַ���hello
3. û�д����ַ���hello
4. ����world�����Ƿ��ڷ���ȥ���У�û�д�����
5. ƴ���ַ�������ֵ������s��

>==:�Ƚ��������ͱȽϵ��ǵ�ֵַ�Ƿ���ͬ
>
>equals:�Ƚ���������Ĭ��Ҳ�ǱȽϵ�ֵַ�Ƿ���ͬ����String����д��equals()�������Ƚϵ��������Ƿ���ͬ��Դ�����һ���ַ�һ���ַ����Ƚ�

- String s = new String(��hello��)��String s = ��hello��;������?

ǰ�߻ᴴ����������һ�����󣬺��߻ᴴ��һ�������������

![ice_screenshot_20160806-083033](http://oaxelf1sk.bkt.clouddn.com/ice_screenshot_20160806-083033.png)

```java
		String s1 = new String("hello");
		String s2 = "hello";
```

1. s1������ջ
2. �ڶ����½�һ���ַ�������
3. �ڷ������в��Ƿ���hello��û�д���һ���µ��ַ���������
4. �ѵ�ֵַ�����еĶ����ٰѶѵĶ���ĵ�ֵַ��ջ�еĶ���

��s2��ֱ�ӵ��������е��ַ����������в����Ƿ����ַ���

- ������д���

```java
		String s1 = new String("hello");
		String s2 = new String("hello");
		System.out.println(s1 == s2);// false
		System.out.println(s1.equals(s2));// true

		String s3 = new String("hello");
		String s4 = "hello";
		System.out.println(s3 == s4);// false
		System.out.println(s3.equals(s4));// true

		String s5 = "hello";
		String s6 = "hello";
		System.out.println(s5 == s6);// true
		System.out.println(s5.equals(s6));// true
```

- д���

```java
		String s1 = "hello";
		String s2 = "world";
		String s3 = "helloworld";
		System.out.println(s3 == s1 + s2);// false
		System.out.println(s3.equals((s1 + s2)));// true

		System.out.println(s3 == "hello" + "world");// true
		System.out.println(s3.equals("hello" + "world"));// true
```

�ַ�������Ǳ�����ӣ���ô���ȿ��ռ���ƴ�ӣ�

�ַ�������ǳ�����ӣ���ô���ȼӣ�Ȼ���ڳ������в��ң�����оͷ��أ�����ʹ���

### �ַ����Ĺ���

#### �жϹ���

* boolean equals(Object obj):�Ƚ��ַ����������Ƿ���ͬ,���ִ�Сд
* boolean equalsIgnoreCase(String str):�Ƚ��ַ����������Ƿ���ͬ,���Դ�Сд
* boolean contains(String str):�жϴ��ַ������Ƿ����С�ַ���
* boolean startsWith(String str):�ж��ַ����Ƿ���ĳ��ָ�����ַ�����ͷ
* boolean endsWith(String str):�ж��ַ����Ƿ���ĳ��ָ�����ַ�����β
* boolean isEmpty():�ж��ַ����Ƿ�Ϊ�ա�

> �ַ�������Ϊ��ʱ��String s = "";
>
> �ַ�������Ϊ�գ�String s = null;��ʱs���󲻴��ڲ��ܵ��÷���**NullPointerException**

#### ��ȡ����

* int length():��ȡ�ַ����ĳ��ȡ�
* char charAt(int index):��ȡָ������λ�õ��ַ�
* int indexOf(int ch):����ָ���ַ��ڴ��ַ����е�һ�γ��ִ���������
    * 	Ϊʲô������int���ͣ�������char����?
        * ԭ���ǣ�'a'��97��ʵ�����Դ���'a'
* int indexOf(String str):����ָ���ַ����ڴ��ַ����е�һ�γ��ִ���������
* int indexOf(int ch,int fromIndex):����ָ���ַ��ڴ��ַ����д�ָ��λ�ú��һ�γ��ִ���������
* int indexOf(String str,int fromIndex):����ָ���ַ����ڴ��ַ����д�ָ��λ�ú��һ�γ��ִ���������
* String substring(int start):��ָ��λ�ÿ�ʼ��ȡ�ַ���,Ĭ�ϵ�ĩβ��
* String substring(int start,int end):��ָ��λ�ÿ�ʼ��ָ��λ�ý�����ȡ�ַ�����

#### ��ϰ-�ַ����ı���

```java
		for (int x = 0; x < s.length(); x++) {
			System.out.println(s.charAt(x));
		}
		//�������Ȱ��ַ����任���ַ�����Ȼ���ڱ����ַ�����
```
#### ��ϰ-�ж��ַ������ж��ٸ���д��Сд�ַ�

```java
  ����ͳ��һ���ַ����д�д��ĸ�ַ���Сд��ĸ�ַ��������ַ����ֵĴ�����(�����������ַ�)
  ������
 		"Hello123World"
  �����
  		��д�ַ���2��
  		Сд�ַ���8��
  		�����ַ���3��
  
 ������
 		ǰ�᣺�ַ���Ҫ����
  		A:��������ͳ�Ʊ���
  			bigCount=0
  			smallCount=0
  			numberCount=0
  		B:�����ַ������õ�ÿһ���ַ���
  			length()��charAt()���
  		C:�жϸ��ַ������������������͵�
  			��bigCount++
  			С��smallCount++
  			���֣�numberCount++
  
  			�����Ŀ���ѵ��������ж�ĳ���ַ��Ǵ�ģ�����С�ģ��������ֵġ�
  			ASCII���
  				0	48
  				A	65
  				a	97
  			��Ȼ�����ǰ������ֵ����ֱȽ��ǿ��Եģ���������ˣ��б��⻹�򵥵�
  				char ch = s.charAt(x);
  
  				if(ch>='0' && ch<='9') numberCount++
  				if(ch>='a' && ch<='z') smallCount++
  				if(ch>='A' && ch<='Z') bigCount++
 		D:��������
```

������ص����ַ���������ʱ���Զ�ת����int���ͣ����Կ���ֱ�ӱȽ϶�û�б�Ҫ��ת�����͡�

```java
public class StringTest2 {
	public static void main(String[] args) {
		//����һ���ַ���
		String s = "Hello123World";
		
		//��������ͳ�Ʊ���
		int bigCount = 0;
		int smallCount = 0;
		int numberCount = 0;
		
		//�����ַ������õ�ÿһ���ַ���
		for(int x=0; x<s.length(); x++){
			char ch = s.charAt(x);
			
			//�жϸ��ַ������������������͵�
			if(ch>='a' && ch<='z'){
				smallCount++;
			}else if(ch>='A' && ch<='Z'){
				bigCount++;
			}else if(ch>='0' && ch<='9'){
				numberCount++;
			}
		}
		
		//��������
		System.out.println("��д��ĸ"+bigCount+"��");
		System.out.println("Сд��ĸ"+smallCount+"��");
		System.out.println("����"+numberCount+"��");
	}
}
```

#### �ַ�����ת������

* byte[] getBytes():���ַ���ת��Ϊ�ֽ����顣
* char[] toCharArray():���ַ���ת��Ϊ�ַ����顣
* static String valueOf(char[] chs):���ַ�����ת���ַ�����
* static String valueOf(int i):��int���͵�����ת���ַ�����
    * 	ע�⣺String���valueOf�������԰��������͵�����ת���ַ�����
* String toLowerCase():���ַ���ת��Сд��
* String toUpperCase():���ַ���ת�ɴ�д��
* String concat(String str):���ַ���ƴ�ӡ�

#### �ַ�������������

* �滻���ܣ�
* String replace(char old,char new)
* String replace(String old,String new) 
* String trim()ȥ���ַ������ո�
* ���ֵ�˳��Ƚ������ַ���  
* int compareTo(String str)�����һλ��ͬ�ͼ����������ͬ����ǰ���ַ���ֵ��ȥ���棨���Ƚ��ߣ���ֵ
* int compareToIgnoreCase(String str)

#### ��ϰcompareTo����

д�����;

```java
   String s1 = "hello";
   String s2 = "hel";
   System.out.println(s1.compareTo(s2)); 
```

�ο��𰸣�2���Ȳ�ͬ��compareTo�����ǳ��������ǰ�涼��ͬ��

#### ��ϰ-�ַ�����ƴ��

  ���󣺰������е����ݰ���ָ������ʽƴ�ӳ�һ���ַ���
  ������
  		int[] arr = {1,2,3};	
  ��������
 		"[1, 2, 3]"
  ������
  		A:����һ���ַ�������ֻ��������Ϊ��
  		B:�Ȱ��ַ���ƴ��һ��"["
  		C:����int���飬�õ�ÿһ��Ԫ��
  		D:���жϸ�Ԫ���Ƿ�Ϊ���һ��
  			�ǣ���ֱ��ƴ��Ԫ�غ�"]"
  			���ǣ���ƴ��Ԫ�غͶ����Լ��ո�
  		E:���ƴ�Ӻ���ַ���

```java
public class StringTest {
	public static void main(String[] args) {
		// ǰ���������Ѿ�����
		int[] arr = { 1, 2, 3 };

		// ����һ���ַ�������ֻ��������Ϊ��
		String s = "";

		// �Ȱ��ַ���ƴ��һ��"["
		s += "[";

		// ����int���飬�õ�ÿһ��Ԫ��
		for (int x = 0; x < arr.length; x++) {
			// ���жϸ�Ԫ���Ƿ�Ϊ���һ��
			if (x == arr.length - 1) {
				// ��ֱ��ƴ��Ԫ�غ�"]"
				s += arr[x];
				s += "]";
			} else {
				// ��ƴ��Ԫ�غͶ����Լ��ո�
				s += arr[x];
				s += ", ";
			}
		}

		// ���ƴ�Ӻ���ַ���
		System.out.println("���յ��ַ����ǣ�" + s);
	}
}
```

#### ��ϰ-�ַ�����ת

  �ַ�����ת
  ����������¼�롱abc��		
  ����������cba��

  ������
  		A:����¼��һ���ַ���
  		B:����һ�����ַ���
  		C:���ű����ַ������õ�ÿһ���ַ�
  			a:length()��charAt()���
  			b:���ַ���ת���ַ�����
  		D:�����ַ�����ÿһ���ַ�ƴ������
  		E:����´�

```java
public class StringTest3 {
	public static void main(String[] args) {
		// ����¼��һ���ַ���
		Scanner sc = new Scanner(System.in);
		System.out.println("������һ���ַ�����");
		String line = sc.nextLine();

		/*
		// ����һ�����ַ���
		String result = "";

		// ���ַ���ת���ַ�����
		char[] chs = line.toCharArray();

		// ���ű����ַ������õ�ÿһ���ַ�
		for (int x = chs.length - 1; x >= 0; x--) {
			// �����ַ�����ÿһ���ַ�ƴ������
			result += chs[x];
		}

		// ����´�
		System.out.println("��ת��Ľ���ǣ�" + result);
		*/

		// �Ľ�Ϊ����ʵ��
		String s = myReverse(line);
		System.out.println("ʵ�ֹ��ܺ�Ľ���ǣ�" + s);
	}

	/*
	 * ������ȷ�� ����ֵ���ͣ�String �����б�String
	 */
	public static  String myReverse(String s) {
		// ����һ�����ַ���
		String result = "";

		// ���ַ���ת���ַ�����
		char[] chs = s.toCharArray();

		// ���ű����ַ������õ�ÿһ���ַ�
		for (int x = chs.length - 1; x >= 0; x--) {
			// �����ַ�����ÿһ���ַ�ƴ������
			result += chs[x];
		}
		return result;
	}
}
```

���Ĵ����ǣ�

```java
		// ���ű����ַ������õ�ÿһ���ַ�
		for (int x = chs.length - 1; x >= 0; x--) {
			// �����ַ�����ÿһ���ַ�ƴ������
			result += chs[x];
		}
```

#### ��ϰ-�ڴ��ַ����в���С�ַ���

  ͳ�ƴ���С�����ֵĴ���
  ������
  		���ַ���"woaijavawozhenaijavawozhendeaijavawozhendehenaijavaxinbuxinwoaijavagun"
  �����
  		java������5��

  ������
  		ǰ�᣺���Ѿ�֪���˴󴮺�С����

  		A:����һ��ͳ�Ʊ�������ʼ��ֵ��0
  		B:���ڴ��в���һ��С����һ�γ��ֵ�λ��
  			a:������-1��˵���������ˣ��ͷ���ͳ�Ʊ���
  			b:��������-1��˵�����ڣ�ͳ�Ʊ���++
  		C:�Ѹղŵ�����+С���ĳ�����Ϊ��ʼλ�ý�ȡ��һ�εĴ󴮣�����һ���µ��ַ��������Ѹ��ַ�����ֵ���¸�ֵ����
  		D:�ص�B

```java
public class StringTest4 {
	public static void main(String[] args) {
		// �����
		String maxString = "woaijavawozhenaijavawozhendeaijavawozhendehenaijavaxinbuxinwoaijavagun";
		// ����С��
		String minString = "java";

		// д����ʵ��
		int count = getCount(maxString, minString);
		System.out.println("Java�ڴ��г����ˣ�" + count + "��");
	}

	/*
	 * ������ȷ�� ����ֵ���ͣ�int �����б������ַ���
	 */
	public static int getCount(String maxString, String minString) {
		// ����һ��ͳ�Ʊ�������ʼ��ֵ��0
		int count = 0;

		// ���ڴ��в���һ��С����һ�γ��ֵ�λ��
		int index = maxString.indexOf(minString);

		// ��������-1��˵�����ڣ�ͳ�Ʊ���++
		while (index != -1) {
			count++;
			// �Ѹղŵ�����+С���ĳ�����Ϊ��ʼλ�ý�ȡ��һ�εĴ󴮣�����һ���µ��ַ��������Ѹ��ַ�����ֵ���¸�ֵ����
			int startIndex = index + minString.length();
			maxString = maxString.substring(startIndex);
			// ������
			index = maxString.indexOf(minString);
		}

		return count;
	}
}
```

�Ľ����룺

```java
	public static int getCount(String maxString, String minString) {
		// ����һ��ͳ�Ʊ�������ʼ��ֵ��0
		int count = 0;		
		int index;
		//�Ȳ飬��ֵ���ж�
		while((index=maxString.indexOf(minString))!=-1){
			count++;
			maxString = maxString.substring(index + minString.length());
		}
		return count;
	}
```
