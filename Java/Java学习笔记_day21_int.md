# Javaѧϰ�ʼ�_day21

[TOC]

## �����

����ʵ������ַ��Ͷ�Ӧ���ַ�����

### ���������

|       �����       |      λ��      |
| :-------------: | :----------: |
|      ASCII      | һ���ֽڵ�7λ���128 |
| ISO-8859-1��������� | һ���ֽ�8λ���256  |
|    GB2312����     |              |
|   GBK�������ĵ�������   |              |
|    GB18030���    |   GBK��ȡ���汾   |
|   BIG-5�����ֱ����   |    �׳ƴ�����     |
|  Unicode���ʱ�׼��   |     2���ֽ�     |
|      UTF-8      |   �����3���ֽ�    |

> UTF-8����һ������ASCII����������Unicode������������ֽ�

ϵͳĬ���ַ�����GBK

string��bytes����ʹ���Զ�����ַ������໥ת����

```java
		String s = "���";
		// String -- byte[]		
		byte[] bys1 = s.getBytes("GBK");// [-60, -29, -70, -61]
		
		System.out.println(Arrays.toString(bys));

		// byte[] -- String
		String ss = new String(bys); // ���
		String ss1 = new String(bys, "GBK"); // ���		
```

## �ַ���OutputStreamWriter

### ���췽��

```java
OutputStreamWriter(OutputStream out):����Ĭ�ϱ�����ֽ���������ת��Ϊ�ַ���
OutputStreamWriter(OutputStream out,String charsetName):����ָ��������ֽ�������ת��Ϊ�ַ���
```

### ��Ա����

```java
  public void write(int c):дһ���ַ�
  public void write(char[] cbuf):дһ���ַ�����
  public void write(char[] cbuf,int off,int len):дһ���ַ������һ����
  public void write(String str):дһ���ַ���
  public void write(String str,int off,int len):дһ���ַ�����һ����
```

> һ���ַ������ֽ�
>
> �ַ���д����һ��Ҫ����flush���������򲻻�д���ļ�����Ϊ��ʱ�ַ��ڻ����У��ļ��洢�ĵ�λ���ַ���������д�����ֽ�

### close()��flush()������

close()�ر������󣬵�����ˢ��һ�λ��������ر�֮�������󲻿��Լ�����ʹ����

flush()����ˢ�»�����,ˢ��֮�������󻹿��Լ���ʹ��

### ʵ������

OutputStreamWriter = FileOutputStream + �����(GBK)

FileWriter = FileOutputStream + �����(GBK)

## InputStreamReader

### ���췽��

```java
InputStreamReader(InputStream is):��Ĭ�ϵı����ȡ����
InputStreamReader(InputStream is,String charsetName):��ָ���ı����ȡ����
```

### ��Ա����

```java
int read():һ�ζ�ȡһ���ַ�

int read(char[] chs):һ�ζ�ȡһ���ַ�����
```

��ȡ����ʾ�����룺

```java
		// ��ȡ����
		// һ�ζ�ȡһ���ַ�
		int ch = 0;
		while ((ch = isr.read()) != -1) {
			System.out.print((char) ch);
		}
		// һ�ζ�ȡһ���ַ�����
		char[] chs = new char[1024];
		int len = 0;
		while ((len = isr.read(chs)) != -1) {
			System.out.print(new String(chs, 0, len));
		}
```

### ʵ������

InputStreamReader = FileInputStream + �����(GBK)

FileReader = FileInputStream + �����(GBK)

## �ַ�������
###BufferedReader

```java
/*
 * BufferedReader
 * ���ַ��������ж�ȡ�ı�����������ַ����Ӷ�ʵ���ַ���������еĸ�Ч��ȡ�� 
 * ����ָ���������Ĵ�С�����߿�ʹ��Ĭ�ϵĴ�С�����������£�Ĭ��ֵ���㹻���ˡ� 
 * 
 * BufferedReader(Reader in)
 */
public class BufferedReaderDemo {
	public static void main(String[] args) throws IOException {
		// �����ַ���������������
		BufferedReader br = new BufferedReader(new FileReader("bw.txt"));

		// ��ʽ1
		int ch = 0;
		while ((ch = br.read()) != -1) {
			System.out.print((char) ch);
		}

		// ��ʽ2
		char[] chs = new char[1024];
		int len = 0;
		while ((len = br.read(chs)) != -1) {
			System.out.print(new String(chs, 0, len));
		}

		// �ͷ���Դ
		br.close();
	}
}
```

### BufferedWriter

���ı�д���ַ����������������ַ����Ӷ��ṩ�����ַ���������ַ����ĸ�Чд�롣


```java
/*
 * �ַ������������ⷽ����
 * BufferedWriter:
 * 		public void newLine():����ϵͳ���������з�
 * BufferedReader:
 * 		public String readLine()��һ�ζ�ȡһ������
 * 		�����������ݵ��ַ������������κ�����ֹ��������ѵ�����ĩβ���򷵻� null
 */
public class BufferedDemo {
	public static void main(String[] args) throws IOException {
		// write();
		read();
	}

	private static void read() throws IOException {
		// �����ַ���������������
		BufferedReader br = new BufferedReader(new FileReader("bw2.txt"));

		String line = null;
		while ((line = br.readLine()) != null) {
			System.out.println(line);
		}
		
		//�ͷ���Դ
		br.close();
	}

	private static void write() throws IOException {
		// �����ַ��������������
		BufferedWriter bw = new BufferedWriter(new FileWriter("bw2.txt"));
		for (int x = 0; x < 10; x++) {
			bw.write("hello" + x);
			// bw.write("\r\n");
			bw.newLine();
			bw.flush();
		}
		bw.close();
	}
}
```
ע�⣺bw.write("hello" + x);���������з��ţ�����Ҫ���newLine��������Ϊ���з���

> newLine��д���з���

![IO��С��ͼ��](http://oaxelf1sk.bkt.clouddn.com/IO��С��ͼ��.png)

### �ַ������Ƶ���ϰ

```java
/*
 * �����ı��ļ�
 * 
 * ������
 * 		�������ݣ��������֪���ü��±��򿪲��ܹ������������ַ������������ֽ�����
 * 		ͨ����ԭ������֪������Ӧ�ò����ַ���������һЩ��
 * 		���ַ�����5�ַ�ʽ�������������Ŀ������5�ַ�ʽ���Ƽ����յ�5�֡�
 * ����Դ��
 * 		c:\\a.txt -- FileReader -- BufferdReader
 * Ŀ�ĵأ�
 * 		d:\\b.txt -- FileWriter -- BufferedWriter
 */
public class CopyFileDemo {
	public static void main(String[] args) throws IOException {
		String srcString = "c:\\a.txt";
		String destString = "d:\\b.txt";
		// method1(srcString, destString);
		// method2(srcString, destString);
		// method3(srcString, destString);
		// method4(srcString, destString);
		method5(srcString, destString);
	}

	// �ַ�������һ�ζ�дһ���ַ���
	private static void method5(String srcString, String destString)
			throws IOException {
		BufferedReader br = new BufferedReader(new FileReader(srcString));
		BufferedWriter bw = new BufferedWriter(new FileWriter(destString));

		String line = null;
		while ((line = br.readLine()) != null) {
			bw.write(line);
			bw.newLine();
			bw.flush();
		}

		bw.close();
		br.close();
	}

	// �ַ�������һ�ζ�дһ���ַ�����
	private static void method4(String srcString, String destString)
			throws IOException {
		BufferedReader br = new BufferedReader(new FileReader(srcString));
		BufferedWriter bw = new BufferedWriter(new FileWriter(destString));

		char[] chs = new char[1024];
		int len = 0;
		while ((len = br.read(chs)) != -1) {
			bw.write(chs, 0, len);
		}

		bw.close();
		br.close();
	}

	// �ַ�������һ�ζ�дһ���ַ�
	private static void method3(String srcString, String destString)
			throws IOException {
		BufferedReader br = new BufferedReader(new FileReader(srcString));
		BufferedWriter bw = new BufferedWriter(new FileWriter(destString));

		int ch = 0;
		while ((ch = br.read()) != -1) {
			bw.write(ch);
		}

		bw.close();
		br.close();
	}

	// �����ַ���һ�ζ�дһ���ַ�����
	private static void method2(String srcString, String destString)
			throws IOException {
		FileReader fr = new FileReader(srcString);
		FileWriter fw = new FileWriter(destString);

		char[] chs = new char[1024];
		int len = 0;
		while ((len = fr.read(chs)) != -1) {
			fw.write(chs, 0, len);
		}

		fw.close();
		fr.close();
	}

	// �����ַ���һ�ζ�дһ���ַ�
	private static void method1(String srcString, String destString)
			throws IOException {
		FileReader fr = new FileReader(srcString);
		FileWriter fw = new FileWriter(destString);

		int ch = 0;
		while ((ch = fr.read()) != -1) {
			fw.write(ch);
		}

		fw.close();
		fr.close();
	}
}
```

����һ���ı��ļ��д洢�˼������ƣ�����дһ������ʵ�������ȡһ���˵�����

```java
public class GetName {
	public static void main(String[] args) throws IOException {
		// ���ı��ļ��е����ݴ洢��������
		BufferedReader br = new BufferedReader(new FileReader("b.txt"));
		ArrayList<String> array = new ArrayList<String>();
		String line = null;
		while ((line = br.readLine()) != null) {
			array.add(line);
		}
		br.close();

		// �������һ������
		Random r = new Random();
		int index = r.nextInt(array.size());

		// ���ݸ�������ȡһ��ֵ
		String name = array.get(index);
		System.out.println("���������ǣ�" + name);
	}
}
```

ע�⣺random���math�е������������

����ָ��Ŀ¼�µ�ָ���ļ������޸ĺ�׺��

```java
/*
 * ���󣺸���ָ��Ŀ¼�µ�ָ���ļ������޸ĺ�׺����
 * ָ�����ļ��ǣ�.java�ļ���
 * ָ���ĺ�׺���ǣ�.jad
 * ָ����Ŀ¼�ǣ�jad
 * 
 * ����Դ��e:\\java\\A.java
 * Ŀ�ĵأ�e:\\jad\\A.jad
 * 
 * ������
 * 		A:��װĿ¼
 * 		B:��ȡ��Ŀ¼�µ�java�ļ���File����
 * 		C:������File���飬�õ�ÿһ��File����
 * 		D:�Ѹ�File���и���
 * 		E:��Ŀ�ĵ�Ŀ¼�¸���
 */
public class CopyFolderDemo {
	public static void main(String[] args) throws IOException {
		// ��װĿ¼
		File srcFolder = new File("e:\\java");
		// ��װĿ�ĵ�
		File destFolder = new File("e:\\jad");
		// ���Ŀ�ĵ�Ŀ¼�����ڣ��ʹ���
		if (!destFolder.exists()) {
			destFolder.mkdir();
		}

		// ��ȡ��Ŀ¼�µ�java�ļ���File����
		File[] fileArray = srcFolder.listFiles(new FilenameFilter() {
			@Override
			public boolean accept(File dir, String name) {
				return new File(dir, name).isFile() && name.endsWith(".java");
			}
		});

		// ������File���飬�õ�ÿһ��File����
		for (File file : fileArray) {
			// System.out.println(file);
			// ����Դ��e:\java\DataTypeDemo.java
			// Ŀ�ĵأ�e:\\jad\DataTypeDemo.java
			String name = file.getName();
			File newFile = new File(destFolder, name);
			copyFile(file, newFile);
		}

		// ��Ŀ�ĵ�Ŀ¼�¸���
		File[] destFileArray = destFolder.listFiles();
		for (File destFile : destFileArray) {
			// System.out.println(destFile);
			// e:\jad\DataTypeDemo.java
			// e:\\jad\\DataTypeDemo.jad
			String name =destFile.getName(); //DataTypeDemo.java
			String newName = name.replace(".java", ".jad");//DataTypeDemo.jad
			
			File newFile = new File(destFolder,newName);
			destFile.renameTo(newFile);
		}
	}

	private static void copyFile(File file, File newFile) throws IOException {
		BufferedInputStream bis = new BufferedInputStream(new FileInputStream(
				file));
		BufferedOutputStream bos = new BufferedOutputStream(
				new FileOutputStream(newFile));

		byte[] bys = new byte[1024];
		int len = 0;
		while ((len = bis.read(bys)) != -1) {
			bos.write(bys, 0, len);
		}

		bos.close();
		bis.close();
	}
}

```

```java
/*
 * ���󣺸��ƶ༫�ļ���
 * 
 * ����Դ��E:\JavaSE\day21\code\demos
 * Ŀ�ĵأ�E:\\
 * 
 * ������
 * 		A:��װ����ԴFile
 * 		B:��װĿ�ĵ�File
 * 		C:�жϸ�File���ļ��л����ļ�
 * 			a:���ļ���
 * 				����Ŀ�ĵ�Ŀ¼�´������ļ���
 * 				��ȡ��File�����µ������ļ������ļ���File����
 * 				�����õ�ÿһ��File����
 * 				�ص�C
 * 			b:���ļ�
 * 				�͸���(�ֽ���)
 */
public class CopyFoldersDemo {
	public static void main(String[] args) throws IOException {
		// ��װ����ԴFile
		File srcFile = new File("E:\\JavaSE\\day21\\code\\demos");
		// ��װĿ�ĵ�File
		File destFile = new File("E:\\");

		// �����ļ��еĹ���
		copyFolder(srcFile, destFile);
	}

	private static void copyFolder(File srcFile, File destFile)
			throws IOException {
		// �жϸ�File���ļ��л����ļ�
		if (srcFile.isDirectory()) {
			// �ļ���
			File newFolder = new File(destFile, srcFile.getName());
			newFolder.mkdir();

			// ��ȡ��File�����µ������ļ������ļ���File����
			File[] fileArray = srcFile.listFiles();
			for (File file : fileArray) {
				copyFolder(file, newFolder);
			}
		} else {
			// �ļ�
			File newFile = new File(destFile, srcFile.getName());
			copyFile(srcFile, newFile);
		}
	}

	private static void copyFile(File srcFile, File newFile) throws IOException {
		BufferedInputStream bis = new BufferedInputStream(new FileInputStream(
				srcFile));
		BufferedOutputStream bos = new BufferedOutputStream(
				new FileOutputStream(newFile));

		byte[] bys = new byte[1024];
		int len = 0;
		while ((len = bis.read(bys)) != -1) {
			bos.write(bys, 0, len);
		}

		bos.close();
		bis.close();
	}
}
```

```java
/*
 * ����¼��5��ѧ����Ϣ(����,���ĳɼ�,��ѧ�ɼ�,Ӣ��ɼ�),�����ִܷӸߵ��ʹ����ı��ļ�
 * 
 * ������
 * 		A:����ѧ����
 * 		B:�������϶���
 * 			TreeSet<Student>
 * 		C:����¼��ѧ����Ϣ�洢������
 * 		D:�������ϣ�������д���ı��ļ�
 */
public class StudentDemo {
	public static void main(String[] args) throws IOException {
		// �������϶���
		TreeSet<Student> ts = new TreeSet<Student>(new Comparator<Student>() {
			@Override
			public int compare(Student s1, Student s2) {
				int num = s2.getSum() - s1.getSum();
				int num2 = num == 0 ? s1.getChinese() - s2.getChinese() : num;
				int num3 = num2 == 0 ? s1.getMath() - s2.getMath() : num2;
				int num4 = num3 == 0 ? s1.getEnglish() - s2.getEnglish() : num3;
				int num5 = num4 == 0 ? s1.getName().compareTo(s2.getName())
						: num4;
				return num5;
			}
		});

		// ����¼��ѧ����Ϣ�洢������
		for (int x = 1; x <= 5; x++) {
			Scanner sc = new Scanner(System.in);
			System.out.println("��¼���" + x + "����ѧϰ��Ϣ");
			System.out.println("������");
			String name = sc.nextLine();
			System.out.println("���ĳɼ���");
			int chinese = sc.nextInt();
			System.out.println("��ѧ�ɼ���");
			int math = sc.nextInt();
			System.out.println("Ӣ��ɼ���");
			int english = sc.nextInt();

			// ����ѧ������
			Student s = new Student();
			s.setName(name);
			s.setChinese(chinese);
			s.setMath(math);
			s.setEnglish(english);

			// ��ѧ����Ϣ��ӵ�����
			ts.add(s);
		}

		// �������ϣ�������д���ı��ļ�
		BufferedWriter bw = new BufferedWriter(new FileWriter("students.txt"));
		bw.write("ѧ����Ϣ���£�");
		bw.newLine();
		bw.flush();
		bw.write("����,���ĳɼ�,��ѧ�ɼ�,Ӣ��ɼ�");
		bw.newLine();
		bw.flush();
		for (Student s : ts) {
			StringBuilder sb = new StringBuilder();
			sb.append(s.getName()).append(",").append(s.getChinese())
					.append(",").append(s.getMath()).append(",")
					.append(s.getEnglish());
			bw.write(sb.toString());
			bw.newLine();
			bw.flush();
		}
		// �ͷ���Դ
		bw.close();
		System.out.println("ѧϰ��Ϣ�洢���");
	}
}
```

ע�⣺��������stringBuilder���÷���TreeSet���÷�

```java
/*
 * ��֪s.txt�ļ�����������һ���ַ�������hcexfgijkamdnoqrzstuvwybpl��
 * ���д�����ȡ�������ݣ������������д��ss.txt�С�
 * 
 * ������
 * 		A:��s.txt����ļ���������
 * 		B:��ȡ���ļ������ݣ��洢��һ���ַ�����
 * 		C:���ַ���ת��Ϊ�ַ�����
 * 		D:���ַ������������
 * 		E:���������ַ�����ת��Ϊ�ַ���
 * 		F:���ַ����ٴ�д��ss.txt��
 */
public class StringDemo {
	public static void main(String[] args) throws IOException {
		// ��ȡ���ļ������ݣ��洢��һ���ַ�����
		BufferedReader br = new BufferedReader(new FileReader("s.txt"));
		String line = br.readLine();
		br.close();

		// ���ַ���ת��Ϊ�ַ�����
		char[] chs = line.toCharArray();

		// ���ַ������������
		Arrays.sort(chs);

		// ���������ַ�����ת��Ϊ�ַ���
		String s = new String(chs);

		// ���ַ����ٴ�д��ss.txt��
		BufferedWriter bw = new BufferedWriter(new FileWriter("ss.txt"));
		bw.write(s);
		bw.newLine();
		bw.flush();

		bw.close();
	}
}
```

ע�⣺��������arrays����÷�

```java
/*
 * ��Readerģ��BufferedReader��readLine()����
 * 
 * readLine():һ�ζ�ȡһ�У����ݻ��з��ж��Ƿ������ֻ�������ݣ������ػ��з�
 */
public class MyBufferedReader {
	private Reader r;

	public MyBufferedReader(Reader r) {
		this.r = r;
	}

	/*
	 * ˼����дһ������������ֵ��һ���ַ�����
	 */
	public String readLine() throws IOException {
		/*
		 * ��Ҫ����һ���ַ������Ҹ���ô����? ���Ǳ���ȥ����r�����ܹ���ȡʲô������? ������ȡ������һ�ζ�ȡһ���ַ�����һ�ζ�ȡһ���ַ�����
		 * ��ô������Ҫ����һ���ַ��������ĸ������ȽϺ���? ���Ǻ������뵽�ַ�����ȽϺã������������ˣ������������ĳ����Ƕ೤��?
		 * ������û�а취��������ĳ��ȣ��㶨��೤�������ʡ� ���ԣ�ֻ��ѡ��һ�ζ�ȡһ���ַ���
		 * �����أ����ַ�ʽ��ʱ�������ٶ�ȡ��һ���ַ���ʱ����һ���ַ��Ͷ�ʧ�� ���ԣ�������Ӧ�ö���һ����ʱ�洢�ռ�Ѷ�ȡ�����ַ����洢������
		 * �����˭�ȽϺ�����?���飬���ϣ��ַ��������������ɹ�ѡ��
		 * ���鲻֪����С�������鷳�������򵥵ķ���������ѡ��ʹ���ַ������������󡣲���ʹ�õ���StringBuilder
		 */
		StringBuilder sb = new StringBuilder();

		// �������ȡ���鷳�����жϽ����������ڽ���֮ǰӦ����һֱ��ȡ��ֱ��-1

		int ch = 0;
		while ((ch = r.read()) != -1) { //104,101,108,108,111
			if (ch == '\r') {
				continue;
			}

			if (ch == '\n') {
				return sb.toString(); //hello
			} else {
				sb.append((char)ch); //hello
			}
		}

		// Ϊ�˷�ֹ���ݶ�ʧ���������һ��û�а��»���ֱ�ӽ���
		if (sb.length() > 0) {
			return sb.toString();
		}

		return null;
	}

	/*
	 * ��дһ���رշ���
	 */
	public void close() throws IOException {
		this.r.close();
	}
}
```

ע��:��stringBUilder����append����ʱһ��Ҫת����char�����ӡ����������

ע������������֪����ʵ���Զ��幦�ܵķ�װ��������˽�б��������췽�������г�Ա����

```java
//ģ��LineNumberReader�Ļ�ȡline�кŵĹ���
public class MyLineNumberReader {
	private Reader r;
	private int lineNumber = 0;

	public MyLineNumberReader(Reader r) {
		this.r = r;
	}

	public int getLineNumber() {
		// lineNumber++;
		return lineNumber;
	}

	public void setLineNumber(int lineNumber) {
		this.lineNumber = lineNumber;
	}

	public String readLine() throws IOException {
		lineNumber++;

		StringBuilder sb = new StringBuilder();

		int ch = 0;
		while ((ch = r.read()) != -1) {
			if (ch == '\r') {
				continue;
			}

			if (ch == '\n') {
				return sb.toString();
			} else {
				sb.append((char) ch);
			}
		}

		if (sb.length() > 0) {
			return sb.toString();
		}

		return null;
	}

	public void close() throws IOException {
		this.r.close();
	}
}
```
