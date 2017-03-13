# Java学习笔记_day26

[TOC]

## 网络

### 网络模型

![osi](http://oaxelf1sk.bkt.clouddn.com/osi.png)

### 三要素

ip地址，端口号，传输协议

> 127.0.0.1 回环地址,可用于测试本机的网络是否有问题. ping 127.0.0.1   

#### 端口号

	正在运行的程序的标识。
	有效端口：0~65535，其中0~1024系统使用或保留端口。
#### 协议

	UDP：
		把数据打包
		数据有限制64k
		不建立连接
		速度快
		不可靠
	TCP：
		建立连接通道
		数据无限制
		速度慢
		可靠
#### Socket

![Socket通信原理图解](http://oaxelf1sk.bkt.clouddn.com/Socket通信原理图解.png)

#### 如果一个类没有构造方法：

  A:成员全部是静态的(Math,Arrays,Collections)
  B:单例设计模式(Runtime)
  C:类中有静态方法返回该类的对象(InetAddress)
  		class Demo {
  			private Demo(){}

  			public static Demo getXxx() {
  				return new Demo();
  			}
  		}

## InetAddress

public static InetAddress getByName(String host):根据主机名或者IP地址的字符串表示得到IP地址对象

### 成员方法

```java
	public static void main(String[] args) throws UnknownHostException {
		// public static InetAddress getByName(String host)
		// InetAddress address = InetAddress.getByName("liuyi");
		// InetAddress address = InetAddress.getByName("192.168.12.92");
		InetAddress address = InetAddress.getByName("192.168.1.100");

		// 获取两个东西：主机名，IP地址
		// public String getHostName()
		String name = address.getHostName();
		// public String getHostAddress()
		String ip = address.getHostAddress();
		System.out.println(name + "---" + ip);
	}
```

## UDP

### 发送程序

```java
/*
 * UDP协议发送数据：
 * A:创建发送端Socket对象
 * B:创建数据，并把数据打包
 * C:调用Socket对象的发送方法发送数据包
 * D:释放资源
 */
	public static void main(String[] args) throws IOException {
		// 创建发送端Socket对象
		// DatagramSocket()
		DatagramSocket ds = new DatagramSocket();

		// 创建数据，并把数据打包
		// DatagramPacket(byte[] buf, int length, InetAddress address, int port)
		// 创建数据
		byte[] bys = "hello,udp,我来了".getBytes();
		// 长度
		int length = bys.length;
		// IP地址对象
		InetAddress address = InetAddress.getByName("192.168.12.92");
		// 端口
		int port = 10086;
		DatagramPacket dp = new DatagramPacket(bys, length, address, port);

		// 调用Socket对象的发送方法发送数据包
		// public void send(DatagramPacket p)
		ds.send(dp);

		// 释放资源
		ds.close();
	}
```

### 接受程序

```java
/*
 * UDP协议接收数据：
 * A:创建接收端Socket对象
 * B:创建一个数据包(接收容器)
 * C:调用Socket对象的接收方法接收数据
 * D:解析数据包，并显示在控制台
 * E:释放资源
 */
	public static void main(String[] args) throws IOException {
		// 创建接收端Socket对象
		// DatagramSocket(int port)
		DatagramSocket ds = new DatagramSocket(10086);

		// 创建一个数据包(接收容器)
		// DatagramPacket(byte[] buf, int length)
		byte[] bys = new byte[1024];
		int length = bys.length;
		DatagramPacket dp = new DatagramPacket(bys, length);

		// 调用Socket对象的接收方法接收数据
		// public void receive(DatagramPacket p)
		ds.receive(dp); // 阻塞式

		// 解析数据包，并显示在控制台
		// 获取对方的ip
		// public InetAddress getAddress()
		InetAddress address = dp.getAddress();
		String ip = address.getHostAddress();
		// public byte[] getData():获取数据缓冲区
		// public int getLength():获取数据的实际长度
		byte[] bys2 = dp.getData();
		int len = dp.getLength();
		String s = new String(bys2, 0, len);
		System.out.println(ip + "传递的数据是:" + s);

		// 释放资源
		ds.close();
	}
```

### UDP实现聊天室

```java
/*
 * 通过多线程改进刚才的聊天程序，这样我就可以实现在一个窗口发送和接收数据了
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
				// 创建一个包裹
				byte[] bys = new byte[1024];
				DatagramPacket dp = new DatagramPacket(bys, bys.length);

				// 接收数据
				ds.receive(dp);

				// 解析数据
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
			// 封装键盘录入数据
			BufferedReader br = new BufferedReader(new InputStreamReader(
					System.in));
			String line = null;
			while ((line = br.readLine()) != null) {
				if ("886".equals(line)) {
					break;
				}

				// 创建数据并打包
				byte[] bys = line.getBytes();
				// DatagramPacket dp = new DatagramPacket(bys, bys.length,
				// InetAddress.getByName("192.168.12.92"), 12345);
				DatagramPacket dp = new DatagramPacket(bys, bys.length,
						InetAddress.getByName("192.168.12.255"), 12306);

				// 发送数据
				ds.send(dp);
			}

			// 释放资源
			ds.close();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
}
```

## TCP

### 接受代码

```java
/*
 * TCP协议接收数据：
 * A:创建接收端的Socket对象
 * B:监听客户端连接。返回一个对应的Socket对象
 * C:获取输入流，读取数据显示在控制台
 * D:释放资源
 */
public class ServerDemo {
	public static void main(String[] args) throws IOException {
		// 创建接收端的Socket对象
		// ServerSocket(int port)
		ServerSocket ss = new ServerSocket(8888);

		// 监听客户端连接。返回一个对应的Socket对象
		// public Socket accept()
		Socket s = ss.accept(); // 侦听并接受到此套接字的连接。此方法在连接传入之前一直阻塞。

		// 获取输入流，读取数据显示在控制台
		InputStream is = s.getInputStream();

		byte[] bys = new byte[1024];
		int len = is.read(bys); // 阻塞式方法
		String str = new String(bys, 0, len);

		String ip = s.getInetAddress().getHostAddress();

		System.out.println(ip + "---" + str);

		// 释放资源
		s.close();
		// ss.close(); //这个不应该关闭
	}
}
```

注意：服务器不应该关闭，但是流应该关闭

阻塞式方法:程序一直停在方法这里，等待数据。没有数据就不继续往下执行，至到得到数据

### 发送端

```java
/*
 * TCP协议发送数据：
 * A:创建发送端的Socket对象
 * 		这一步如果成功，就说明连接已经建立成功了。
 * B:获取输出流，写数据
 * C:释放资源
 * 
 * 连接被拒绝。TCP协议一定要先看服务器。
 * java.net.ConnectException: Connection refused: connect
 */
public class ClientDemo {
	public static void main(String[] args) throws IOException {
		// 创建发送端的Socket对象
		// Socket(InetAddress address, int port)
		// Socket(String host, int port)
		// Socket s = new Socket(InetAddress.getByName("192.168.12.92"), 8888);
		Socket s = new Socket("192.168.12.92", 8888);

		// 获取输出流，写数据
		// public OutputStream getOutputStream()
		OutputStream os = s.getOutputStream();
		os.write("hello,tcp,我来了".getBytes());

		// 释放资源
		s.close();
	}
}
```

### 带有数据的发送和接受的客户端和服务端

```java
public class ServerDemo {
	public static void main(String[] args) throws IOException {
		// 创建服务器Socket对象
		ServerSocket ss = new ServerSocket(9999);

		// 监听客户端的连接
		Socket s = ss.accept(); // 阻塞

		// 获取输入流
		InputStream is = s.getInputStream();
		byte[] bys = new byte[1024];
		int len = is.read(bys); // 阻塞
		String server = new String(bys, 0, len);
		System.out.println("server:" + server);

		// 获取输出流
		OutputStream os = s.getOutputStream();
		os.write("数据已经收到".getBytes());

		// 释放资源
		s.close();
		// ss.close();
	}
}


public class ClientDemo {
	public static void main(String[] args) throws IOException {
		// 创建客户端Socket对象
		Socket s = new Socket("192.168.1.100", 9999);

		// 获取输出流
		OutputStream os = s.getOutputStream();
		os.write("今天天气很好,适合睡觉".getBytes());

		// 获取输入流
		InputStream is = s.getInputStream();
		byte[] bys = new byte[1024];
		int len = is.read(bys);// 阻塞
		String client = new String(bys, 0, len);
		System.out.println("client:" + client);

		// 释放资源
		s.close();
	}
}
```

### 多线程的客户和服务端

```java
public class UploadServer {
	public static void main(String[] args) throws IOException {
		// 创建服务器Socket对象
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
			// 封装通道内的流
			BufferedReader br = new BufferedReader(new InputStreamReader(
					s.getInputStream()));
			// 封装文本文件
			// BufferedWriter bw = new BufferedWriter(new
			// FileWriter("Copy.java"));

			// 为了防止名称冲突
			String newName = System.currentTimeMillis() + ".java";
			BufferedWriter bw = new BufferedWriter(new FileWriter(newName));

			String line = null;
			while ((line = br.readLine()) != null) { // 阻塞
				bw.write(line);
				bw.newLine();
				bw.flush();
			}

			// 给出反馈
			BufferedWriter bwServer = new BufferedWriter(
					new OutputStreamWriter(s.getOutputStream()));
			bwServer.write("文件上传成功");
			bwServer.newLine();
			bwServer.flush();

			// 释放资源
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
		// 创建客户端Socket对象
		Socket s = new Socket("192.168.12.92", 11111);

		// 封装文本文件
		// BufferedReader br = new BufferedReader(new FileReader(
		// "InetAddressDemo.java"));
		BufferedReader br = new BufferedReader(new FileReader(
				"ReceiveDemo.java"));
		// 封装通道内流
		BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(
				s.getOutputStream()));

		String line = null;
		while ((line = br.readLine()) != null) { // 阻塞
			bw.write(line);
			bw.newLine();
			bw.flush();
		}

		// Socket提供了一个终止，它会通知服务器你别等了，我没有数据过来了
		s.shutdownOutput();

		// 接收反馈
		BufferedReader brClient = new BufferedReader(new InputStreamReader(
				s.getInputStream()));
		String client = brClient.readLine(); // 阻塞
		System.out.println(client);

		// 释放资源
		br.close();
		s.close();
	}
}
```
