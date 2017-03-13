# Javaѧϰ�ʼ�_day19

[TOC]

## IO





## �����쳣Throwable

![ice_screenshot_20160808-144005](http://oaxelf1sk.bkt.clouddn.com/ice_screenshot_20160808-144005.png)

### ����

1. error�������⣬һ�㲻�������ڴ����
2. exception����

### Exception

����RuntimeException�������ڼ����⣩��������ͷ�RuntimeException���ࣨ�����ڼ����⣩

RuntimeException�����������⣩����Ҫ�޸Ĵ���

��RuntimeException���������쳣����������д���

### �Լ���δ����쳣

- try...catch...finally
- throws �׳�

  try...catch...finally�Ĵ����ʽ��
  		try {

  			���ܳ�������Ĵ���;
  		}catch(�쳣�� ����) {
  			�������Ĵ���;
  		}finally {
  			�ͷ���Դ;
  		}

  ���θ�ʽ��
  		try {
  			���ܳ�������Ĵ���;
  		}catch(�쳣�� ����) {
  			�������Ĵ���;
  		}

  **ע��**��
  		A:try����Ĵ���Խ��Խ��
  		B:catch������������ݣ������Ǹ���һ���򵥵���ʾ

��catch�в����쳣ʱע�⣺�����쳣Ҳ�м̳й�ϵ���Լ����ж���쳣����ôҪע�⸸�쳣д�������档

#### JDK7������

  JDK7������һ���µ��쳣��������
  		try{

  		}catch(�쳣��1 | �쳣��2 | ...  ���� ) {
  			...
  		}

  		ע�⣺���������Ȼ��࣬����Ҳ�����á�
  			A:����ʽ��һ�µġ�(ʵ�ʿ����У��ö�ʱ����ܾ������ͬ���͵����⣬����ͬһ������)
 			B:����쳣�������ƽ����ϵ��

### ����ʱ�ں�����ʱ�ڵ��쳣

�������쳣��Java���������ʾ�����������ͻᷢ�������޷�ͨ������

�������쳣��������ʾ����Ҳ���Ժͱ���ʱ�쳣һ������

### �쳣����

��try���淢�������jvm�����������һ���쳣����Ȼ�����������׳�����catch����������ƥ�䣬����ö�����ĳ�����͵ģ��ͻ�ִ�и�catch����Ĵ�����Ϣ��

```java
public static void main(String[] args) {
		String s = "2014-11-20";
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
		try {
			Date d = sdf.parse(s); // ������һ��ParseException����Ȼ���׳�ȥ����catch�������ƥ��
			System.out.println(d);
		} catch (ParseException e) { // ParseException e = new ParseException();
			// ParseException
			// e.printStackTrace();

			// getMessage()
			// System.out.println(e.getMessage());
			// Unparseable date: "2014-11-20"

			// toString()
			// System.out.println(e.toString());
			// java.text.ParseException: Unparseable date: "2014-11-20"
			
			e.printStackTrace();
			//��ת��ĳ��ָ����ҳ��(index.html)
		}
		
		System.out.println("over");
	}
```

#### �쳣����ķ���

- public String getMessage():�쳣����Ϣ�ַ���
- public String toString():�����쳣�ļ���Ϣ����

> ��ɹ��򣺴˶������� name(ȫ·����)": "��ð�ź�һ���ո񣩵��ô˶��� getLocalizedMessage()�����Ľ�� (Ĭ�Ϸ��ص���getMessage()������)

- printStackTrace() ��ȡ�쳣�������쳣��Ϣ���Լ��쳣�����ڳ����е�λ�á�����ֵvoid������Ϣ����ڿ���̨����jvm��Ĭ�ϴ���ͬ�����쳣����������Ҳ����ִ�У����ӡover

#### �쳣�׳��������ָ�ʽ

- �緽��������throws�쳣��������Ҫ��main�������׳��쳣��throws�������1�Ͷ���쳣�����Ÿ���
- ��throw�׳��쳣����throw new ArithmeticException();

  ####throws��throw������

  throws
  	���ڷ����������棬�������쳣����
  	���Ը�����쳣�������ö��Ÿ���
  	��ʾ�׳��쳣���ɸ÷����ĵ�����������
  	throws��ʾ�����쳣��һ�ֿ����ԣ�����һ���ᷢ����Щ�쳣
  throw
  	���ڷ������ڣ��������쳣������
  	ֻ���׳�һ���쳣������
  	��ʾ�׳��쳣���ɷ������ڵ���䴦��
  	throw�����׳����쳣��ִ��throw��һ���׳���ĳ���쳣
  **����ù����ڲ����Խ����⴦��,��try,���������,���ɵ����ߴ���,������throws**

> ����:����������Ҫ�������о�try������������Ҫ�������о�throws

> try...catch...finally...��finally���Ƶ������һ����ִ�У�����jvm�˳���System.exit(0))

### ��ϰ��

- final,finally��finalize������
- ���catch������return��䣬����finally����Ĵ��뻹��ִ����?
- try...catch...finally�ĸ�ʽ����

```java
/*
 * �����⣺
 * 1:final,finally��finalize������
 * final�����յ���˼�����������࣬��Ա��������Ա����
 * 		�����࣬�಻�ܱ��̳�
 * 		���α����������ǳ���
 * 		���η������������ܱ���д
 * finally�����쳣�����һ���֣������ͷ���Դ��
 * 		һ����˵������϶���ִ�У������������ִ�е�finally֮ǰjvm�˳���
 * finalize����Object���һ��������������������
 * 
 * 2:���catch������return��䣬����finally����Ĵ��뻹��ִ����?
 *   ����ᣬ��������returnǰ������return��
 * 	   �ᡣǰ��
 * 
 * 	 ׼ȷ��˵��Ӧ�������м䡣
 * 
 * 3:try...catch...finally�ĸ�ʽ����
 * 		A:try...catch...finally
 * 		B:try...catch
 * 		C:try...catch...catch...
 * 		D:try...catch...catch...finally
 * 		E:try...finally
 * 			����������Ŀǰ��Ϊ���ͷ���Դ��
 */
public class FinallyDemo2 {
	public static void main(String[] args) {
		System.out.println(getInt());
	}

	public static int getInt() {
		int a = 10;
		try {
			System.out.println(a / 0);
			a = 20;
		} catch (ArithmeticException e) {
			a = 30;
			return a;
			/*
			 * return a�ڳ���ִ�е���һ����ʱ�����ﲻ��return a����return 30;�������·�����γ��ˡ�
			 * �����أ������ֺ��滹��finally�����Լ���ִ��finally�����ݣ�a=40
			 * �ٴλص���ǰ�ķ���·����������return 30;
			 */
		} finally {
			a = 40;
			//return a;//��������������40�ˡ�
		}
		 return a; 		//�����
	}
}
```

### �Զ����쳣

����̳���Exception����RuntimeException

> ����Ǽ̳���RuntimeException��ô��throw  new  �쳣ʱ�ڷ����ϾͲ�����throws�׳������Ǽ����Զ�����쳣�̳���Exception��Ҳ���Ǳ���ʱ�ڵ��쳣��ô��һ��Ҫ�緽�����׳��쳣

�������׳��쳣ʱ�Զ�����һ���ַ�����throw new MyException("����������0-100֮��");�������׳�û�з�Ӧ����ʱֻҪ�ڴ��ι��췽�������super(message)���Ϳ��Է�Ӧ�ڿ���̨�����£�

```java
cn.itcast_08.MyException: ����������0-100֮��
	at cn.itcast_08.Teacher.check(Teacher.java:6)
	at cn.itcast_08.StudentDemo.main(StudentDemo.java:16)
```

�Զ����쳣�̳���Exception���Ǳ���ʱ���쳣���̳���RuntimeException��������ʱ���쳣

�Զ����쳣ֻҪ��д�������췽�����У�

```java
	public MyException() {
		
	}
	public MyException(String string) {
		super(string);		//һ��Ҫsuper���Է�Ӧ�ַ���
	}
```

### ע��

����͸��඼Ҫ�׳��쳣������������д������쳣��������ô�����׳����쳣һ���Ǹ����쳣��������߾��Ǹ����쳣��

��������׳��˶���쳣,������д����ʱ,ֻ���׳���ͬ���쳣�����������Ӽ�,���಻���׳�����û�е��쳣

�������д�ķ���û���쳣�׳�,��ô����ķ������Բ������׳��쳣,������෽�������쳣����,��ô����ֻ��try,����throws

## File��

ʵ����Comparable�ӿڣ���Ȼ���򣩣��ļ���Ŀ¼·�����ĳ����ʾ��ʽ��δ������ʵ���ڵġ�

### ���췽��

File(String pathname)������һ��·���õ�File����

File(String parent, String child):����һ��Ŀ¼��һ�����ļ�/Ŀ¼�õ�File����

File(File parent, String child):����һ����File�����һ�����ļ�/Ŀ¼�õ�File����

ע���ʱ�̷�·����б�����⣨����б�ܣ���

```java
		// File(String pathname)������һ��·���õ�File����
		// ��e:\\demo\\a.txt��װ��һ��File����
		File file = new File("E:\\demo\\a.txt");

// File(String parent, String child):����һ��Ŀ¼��һ�����ļ�/Ŀ¼�õ�File����
		File file2 = new File("E:\\demo", "a.txt");

// File(File parent, String child):����һ����File�����һ�����ļ�/Ŀ¼�õ�File����
		File file3 = new File("e:\\demo");
		File file4 = new File(file3, "a.txt");
```

**�̷���Сд�޹�**

### ��Ա����

#### ��������

```java
 public boolean createNewFile():�����ļ� ��������������ļ����Ͳ�������

 public boolean mkdir():�����ļ��� ��������������ļ��У��Ͳ�������

 public boolean mkdirs():�����༶�ļ���,������ļ��в����ڣ�����㴴������
```

Ҫ����ĳ��Ŀ¼�´������ݣ���Ŀ¼���ȱ�����ڣ������ȵ���mkdir()����mkdirs()����������ֱ�ӵ���createNewFile()����������ᱨ�쳣

```java
Exception in thread "main" java.io.IOException: ϵͳ�Ҳ���ָ����·����
```

> mkdir()���ܴ����༶Ŀ¼
>
> ����㴴���ļ������ļ�������д�̷�·������ô��Ĭ������Ŀ��·���¡�

#### ɾ������

```java
public boolean delete()
```

javaɾ�����ļ������ļ��в����յ�������

> �����ɾ���ļ������ļ�������д�̷�·������ô��Ĭ������Ŀ��·����
>
> ɾ���ļ�����ô����ʼۼб����ǿյ�

#### ����������

```java
public boolean renameTo(File dest)
```

> ���·������ͬ�����Ǹ��������·������ͬ�����Ǹ��������С�

·�����̷���ʼ������·��	c:\\a.txt��·�������̷���ʼ�����·��	a.txt

#### �жϹ���

```java
  public boolean isDirectory():�ж��Ƿ���Ŀ¼
  public boolean isFile():�ж��Ƿ����ļ�
  public boolean exists():�ж��Ƿ����
  public boolean canRead():�ж��Ƿ�ɶ�
  public boolean canWrite():�ж��Ƿ��д
  public boolean isHidden():�ж��Ƿ�����
```

#### ��ȡ����

```java
  public String getAbsolutePath()����ȡ����·��
  public String getPath():��ȡ���·��
  public String getName():��ȡ����
  public long length():��ȡ���ȡ��ֽ���
  public long lastModified():��ȡ���һ�ε��޸�ʱ�䣬����ֵ
    
  public String[] list():��ȡָ��Ŀ¼�µ������ļ������ļ��е���������
  public File[] listFiles():��ȡָ��Ŀ¼�µ������ļ������ļ��е�File����
```

### ��ϰ��

�ж�E��Ŀ¼���Ƿ��к�׺��Ϊ.jpg���ļ�������У���������ļ�����

```java
/*
 * �ж�E��Ŀ¼���Ƿ��к�׺��Ϊ.jpg���ļ�������У���������ļ�����
 * 
 * ������
 * 		A:��װe�ж�Ŀ¼
 * 		B:��ȡ��Ŀ¼�������ļ������ļ��е�File����
 * 		C:������File���飬�õ�ÿһ��File����Ȼ���ж�
 * 		D:�Ƿ����ļ�
 * 			�ǣ������ж��Ƿ���.jpg��β
 * 				�ǣ���������ļ�����
 * 				�񣺲�������
 * 			�񣺲�������
 */
public class FileDemo {
	public static void main(String[] args) {
		// ��װe�ж�Ŀ¼
		File file = new File("e:\\");

		// ��ȡ��Ŀ¼�������ļ������ļ��е�File����
		File[] fileArray = file.listFiles();

		// ������File���飬�õ�ÿһ��File����Ȼ���ж�
		for (File f : fileArray) {
			// �Ƿ����ļ�
			if (f.isFile()) {
				// �����ж��Ƿ���.jpg��β
				if (f.getName().endsWith(".jpg")) {
					// ��������ļ�����
					System.out.println(f.getName());
				}
			}
		}
	}
}
```

��һ��˼·��

```java
/*
 * �ж�E��Ŀ¼���Ƿ��к�׺��Ϊ.jpg���ļ�������У���������ļ�����
 * A:�Ȼ�ȡ���еģ�Ȼ�������ʱ�������жϣ�������������������
 * B:��ȡ��ʱ����Ѿ��������������ˣ�Ȼ��������ɡ�
 * 
 * Ҫ��ʵ�����Ч�����ͱ���ѧϰһ���ӿڣ��ļ����ƹ�����
 * public String[] list(FilenameFilter filter)
 * public File[] listFiles(FilenameFilter filter)
 */
public class FileDemo2 {
	public static void main(String[] args) {
		// ��װe�ж�Ŀ¼
		File file = new File("e:\\");

		// ��ȡ��Ŀ¼�������ļ������ļ��е�String����
		// public String[] list(FilenameFilter filter)
		String[] strArray = file.list(new FilenameFilter() {
			@Override
			public boolean accept(File dir, String name) {
				return new File(dir, name).isFile() && name.endsWith(".jpg");
			}
		});

		// ����
		for (String s : strArray) {
			System.out.println(s);
		}
	}
}

```
�ļ����ƹ�����:

```java
  public String[] list(FilenameFilter filter)

  public File[] listFiles(FilenameFilter filter)
```
### ��ϰ����������

```java
/*
 * ���󣺰�E:\����\���������������Ƶ�����޸�Ϊ
 * 		00?_����.avi
 * 
 * ˼·��
 * 		A:��װĿ¼
 * 		B:��ȡ��Ŀ¼�����е��ļ���File����
 * 		C:������File���飬�õ�ÿһ��File����
 * 		D:ƴ��һ���µ����ƣ�Ȼ�����������ɡ�
 */
public class FileDemo {
	public static void main(String[] args) {
		// ��װĿ¼
		File srcFolder = new File("E:\\����\\��������");

		// ��ȡ��Ŀ¼�����е��ļ���File����
		File[] fileArray = srcFolder.listFiles();

		// ������File���飬�õ�ÿһ��File����
		for (File file : fileArray) {
			// System.out.println(file);
			// E:\����\��������\��������_001_[������-����ܸ���,�����IO��]_��԰������.avi
			// �ĺ�E:\����\��������\001_��԰������.avi
			String name = file.getName(); // ��������_001_[������-����ܸ���,�����IO��]_��԰������.avi

			int index = name.indexOf("_");
			String numberString = name.substring(index + 1, index + 4);
	
			int endIndex = name.lastIndexOf('_');
			String nameString = name.substring(endIndex);

			String newName = numberString.concat(nameString); // 001_��԰������.avi
			// System.out.println(newName);

			File newFile = new File(srcFolder, newName); // E:\\����\\��������\\001_��԰������.avi

			// ����������
			file.renameTo(newFile);
		}
	}
}
```
