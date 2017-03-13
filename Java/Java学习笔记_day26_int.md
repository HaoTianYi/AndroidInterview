# Javaѧϰ�ʼ�_day26

[TOC]

## ����

### ����ģ��

![osi](http://oaxelf1sk.bkt.clouddn.com/osi.png)

### ��Ҫ��

ip��ַ���˿ںţ�����Э��

> 127.0.0.1 �ػ���ַ,�����ڲ��Ա����������Ƿ�������. ping 127.0.0.1   

#### �˿ں�

	�������еĳ���ı�ʶ��
	��Ч�˿ڣ�0~65535������0~1024ϵͳʹ�û����˿ڡ�
#### Э��

	UDP��
		�����ݴ��
		����������64k
		����������
		�ٶȿ�
		���ɿ�
	TCP��
		��������ͨ��
		����������
		�ٶ���
		�ɿ�
#### Socket

![Socketͨ��ԭ��ͼ��](http://oaxelf1sk.bkt.clouddn.com/Socketͨ��ԭ��ͼ��.png)

#### ���һ����û�й��췽����

  A:��Աȫ���Ǿ�̬��(Math,Arrays,Collections)
  B:�������ģʽ(Runtime)
  C:�����о�̬�������ظ���Ķ���(InetAddress)
  		class Demo {
  			private Demo(){}

  			public static Demo getXxx() {
  				return new Demo();
  			}
  		}

## InetAddress

public static InetAddress getByName(String host):��������������IP��ַ���ַ�����ʾ�õ�IP��ַ����

### ��Ա����

```java
	public static void main(String[] args) throws UnknownHostException {
		// public static InetAddress getByName(String host)
		// InetAddress address = InetAddress.getByName("liuyi");
		// InetAddress address = InetAddress.getByName("192.168.12.92");
		InetAddress address = InetAddress.getByName("192.168.1.100");

		// ��ȡ������������������IP��ַ
		// public String getHostName()
		String name = address.getHostName();
		// public String getHostAddress()
		String ip = address.getHostAddress();
		System.out.println(name + "---" + ip);
	}
```

## UDP

### ���ͳ���

```java
/*
 * UDPЭ�鷢�����ݣ�
 * A:�������Ͷ�Socket����
 * B:�������ݣ��������ݴ��
 * C:����Socket����ķ��ͷ����������ݰ�
 * D:�ͷ���Դ
 */
	public static void main(String[] args) throws IOException {
		// �������Ͷ�Socket����
		// DatagramSocket()
		DatagramSocket ds = new DatagramSocket();

		// �������ݣ��������ݴ��
		// DatagramPacket(byte[] buf, int length, InetAddress address, int port)
		// ��������
		byte[] bys = "hello,udp,������".getBytes();
		// ����
		int length = bys.length;
		// IP��ַ����
		InetAddress address = InetAddress.getByName("192.168.12.92");
		// �˿�
		int port = 10086;
		DatagramPacket dp = new DatagramPacket(bys, length, address, port);

		// ����Socket����ķ��ͷ����������ݰ�
		// public void send(DatagramPacket p)
		ds.send(dp);

		// �ͷ���Դ
		ds.close();
	}
```

### ���ܳ���

```java
/*
 * UDPЭ��������ݣ�
 * A:�������ն�Socket����
 * B:����һ�����ݰ�(��������)
 * C:����Socket����Ľ��շ�����������
 * D:�������ݰ�������ʾ�ڿ���̨
 * E:�ͷ���Դ
 */
	public static void main(String[] args) throws IOException {
		// �������ն�Socket����
		// DatagramSocket(int port)
		DatagramSocket ds = new DatagramSocket(10086);

		// ����һ�����ݰ�(��������)
		// DatagramPacket(byte[] buf, int length)
		byte[] bys = new byte[1024];
		int length = bys.length;
		DatagramPacket dp = new DatagramPacket(bys, length);

		// ����Socket����Ľ��շ�����������
		// public void receive(DatagramPacket p)
		ds.receive(dp); // ����ʽ

		// �������ݰ�������ʾ�ڿ���̨
		// ��ȡ�Է���ip
		// public InetAddress getAddress()
		InetAddress address = dp.getAddress();
		String ip = address.getHostAddress();
		// public byte[] getData():��ȡ���ݻ�����
		// public int getLength():��ȡ���ݵ�ʵ�ʳ���
		byte[] bys2 = dp.getData();
		int len = dp.getLength();
		String s = new String(bys2, 0, len);
		System.out.println(ip + "���ݵ�������:" + s);

		// �ͷ���Դ
		ds.close();
	}
```

### UDPʵ��������

```java
/*
 * ͨ�����̸߳Ľ��ղŵ�������������ҾͿ���ʵ����һ�����ڷ��ͺͽ���������
 */
public class ChatRoom {
	public static void main(String[] args) throws IOException {
		DatagramSocket dsSend = new DatagramSocket();
		DatagramSocket dsReceive = new DatagramSocket(12306);

		SendThread st = new SendThread(dsSend);
		ReceiveThread rt = new ReceiveThread(dsReceive);

		Thread t1 = new Thread(st);
		Thread t2 = new Thread(rt);

		t1.start();
		t2.start();
	}
}
```

```java
public class ReceiveThread implements Runnable {
	private DatagramSocket ds;

	public ReceiveThread(DatagramSocket ds) {
		this.ds = ds;
	}

	@Override
	public void run() {
		try {
			while (true) {
				// ����һ������
				byte[] bys = new byte[1024];
				DatagramPacket dp = new DatagramPacket(bys, bys.length);

				// ��������
				ds.receive(dp);

				// ��������
				String ip = dp.getAddress().getHostAddress();
				String s = new String(dp.getData(), 0, dp.getLength());
				System.out.println("from " + ip + " data is : " + s);
			}
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
}
```

```java
public class SendThread implements Runnable {

	private DatagramSocket ds;

	public SendThread(DatagramSocket ds) {
		this.ds = ds;
	}

	@Override
	public void run() {
		try {
			// ��װ����¼������
			BufferedReader br = new BufferedReader(new InputStreamReader(
					System.in));
			String line = null;
			while ((line = br.readLine()) != null) {
				if ("886".equals(line)) {
					break;
				}

				// �������ݲ����
				byte[] bys = line.getBytes();
				// DatagramPacket dp = new DatagramPacket(bys, bys.length,
				// InetAddress.getByName("192.168.12.92"), 12345);
				DatagramPacket dp = new DatagramPacket(bys, bys.length,
						InetAddress.getByName("192.168.12.255"), 12306);

				// ��������
				ds.send(dp);
			}

			// �ͷ���Դ
			ds.close();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
}
```

## TCP

### ���ܴ���

```java
/*
 * TCPЭ��������ݣ�
 * A:�������ն˵�Socket����
 * B:�����ͻ������ӡ�����һ����Ӧ��Socket����
 * C:��ȡ����������ȡ������ʾ�ڿ���̨
 * D:�ͷ���Դ
 */
public class ServerDemo {
	public static void main(String[] args) throws IOException {
		// �������ն˵�Socket����
		// ServerSocket(int port)
		ServerSocket ss = new ServerSocket(8888);

		// �����ͻ������ӡ�����һ����Ӧ��Socket����
		// public Socket accept()
		Socket s = ss.accept(); // ���������ܵ����׽��ֵ����ӡ��˷��������Ӵ���֮ǰһֱ������

		// ��ȡ����������ȡ������ʾ�ڿ���̨
		InputStream is = s.getInputStream();

		byte[] bys = new byte[1024];
		int len = is.read(bys); // ����ʽ����
		String str = new String(bys, 0, len);

		String ip = s.getInetAddress().getHostAddress();

		System.out.println(ip + "---" + str);

		// �ͷ���Դ
		s.close();
		// ss.close(); //�����Ӧ�ùر�
	}
}
```

ע�⣺��������Ӧ�ùرգ�������Ӧ�ùر�

����ʽ����:����һֱͣ�ڷ�������ȴ����ݡ�û�����ݾͲ���������ִ�У������õ�����

### ���Ͷ�

```java
/*
 * TCPЭ�鷢�����ݣ�
 * A:�������Ͷ˵�Socket����
 * 		��һ������ɹ�����˵�������Ѿ������ɹ��ˡ�
 * B:��ȡ�������д����
 * C:�ͷ���Դ
 * 
 * ���ӱ��ܾ���TCPЭ��һ��Ҫ�ȿ���������
 * java.net.ConnectException: Connection refused: connect
 */
public class ClientDemo {
	public static void main(String[] args) throws IOException {
		// �������Ͷ˵�Socket����
		// Socket(InetAddress address, int port)
		// Socket(String host, int port)
		// Socket s = new Socket(InetAddress.getByName("192.168.12.92"), 8888);
		Socket s = new Socket("192.168.12.92", 8888);

		// ��ȡ�������д����
		// public OutputStream getOutputStream()
		OutputStream os = s.getOutputStream();
		os.write("hello,tcp,������".getBytes());

		// �ͷ���Դ
		s.close();
	}
}
```

### �������ݵķ��ͺͽ��ܵĿͻ��˺ͷ����

```java
public class ServerDemo {
	public static void main(String[] args) throws IOException {
		// ����������Socket����
		ServerSocket ss = new ServerSocket(9999);

		// �����ͻ��˵�����
		Socket s = ss.accept(); // ����

		// ��ȡ������
		InputStream is = s.getInputStream();
		byte[] bys = new byte[1024];
		int len = is.read(bys); // ����
		String server = new String(bys, 0, len);
		System.out.println("server:" + server);

		// ��ȡ�����
		OutputStream os = s.getOutputStream();
		os.write("�����Ѿ��յ�".getBytes());

		// �ͷ���Դ
		s.close();
		// ss.close();
	}
}


public class ClientDemo {
	public static void main(String[] args) throws IOException {
		// �����ͻ���Socket����
		Socket s = new Socket("192.168.1.100", 9999);

		// ��ȡ�����
		OutputStream os = s.getOutputStream();
		os.write("���������ܺ�,�ʺ�˯��".getBytes());

		// ��ȡ������
		InputStream is = s.getInputStream();
		byte[] bys = new byte[1024];
		int len = is.read(bys);// ����
		String client = new String(bys, 0, len);
		System.out.println("client:" + client);

		// �ͷ���Դ
		s.close();
	}
}
```

### ���̵߳Ŀͻ��ͷ����

```java
public class UploadServer {
	public static void main(String[] args) throws IOException {
		// ����������Socket����
		ServerSocket ss = new ServerSocket(11111);

		while (true) {
			Socket s = ss.accept();
			new Thread(new UserThread(s)).start();
		}
	}
}
```

```java
public class UserThread implements Runnable {
	private Socket s;

	public UserThread(Socket s) {
		this.s = s;
	}

	@Override
	public void run() {
		try {
			// ��װͨ���ڵ���
			BufferedReader br = new BufferedReader(new InputStreamReader(
					s.getInputStream()));
			// ��װ�ı��ļ�
			// BufferedWriter bw = new BufferedWriter(new
			// FileWriter("Copy.java"));

			// Ϊ�˷�ֹ���Ƴ�ͻ
			String newName = System.currentTimeMillis() + ".java";
			BufferedWriter bw = new BufferedWriter(new FileWriter(newName));

			String line = null;
			while ((line = br.readLine()) != null) { // ����
				bw.write(line);
				bw.newLine();
				bw.flush();
			}

			// ��������
			BufferedWriter bwServer = new BufferedWriter(
					new OutputStreamWriter(s.getOutputStream()));
			bwServer.write("�ļ��ϴ��ɹ�");
			bwServer.newLine();
			bwServer.flush();

			// �ͷ���Դ
			bw.close();
			s.close();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}

}
```

```java
public class UploadClient {
	public static void main(String[] args) throws IOException {
		// �����ͻ���Socket����
		Socket s = new Socket("192.168.12.92", 11111);

		// ��װ�ı��ļ�
		// BufferedReader br = new BufferedReader(new FileReader(
		// "InetAddressDemo.java"));
		BufferedReader br = new BufferedReader(new FileReader(
				"ReceiveDemo.java"));
		// ��װͨ������
		BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(
				s.getOutputStream()));

		String line = null;
		while ((line = br.readLine()) != null) { // ����
			bw.write(line);
			bw.newLine();
			bw.flush();
		}

		// Socket�ṩ��һ����ֹ������֪ͨ�����������ˣ���û�����ݹ�����
		s.shutdownOutput();

		// ���շ���
		BufferedReader brClient = new BufferedReader(new InputStreamReader(
				s.getInputStream()));
		String client = brClient.readLine(); // ����
		System.out.println(client);

		// �ͷ���Դ
		br.close();
		s.close();
	}
}
```
