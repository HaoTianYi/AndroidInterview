# Javaѧϰ�ʼ�_day25

[TOC]

## GUI

Graphical User Interface(ͼ���û��ӿ�)

### API���ڵİ�

java.awt��Abstract Window ToolKit (���󴰿ڹ��߰�)����Ҫ���ñ���ϵͳ����ʵ�ֹ��ܡ����������ؼ���
javax.swing����AWT�Ļ����ϣ�������һ��ͼ�ν���ϵͳ�������ṩ�˸���������������ȫ��Javaʵ�֡���ǿ����ֲ�ԣ����������ؼ���

java��java�Ļ�������javax����չ��

### GUI�̳���ϵ

![gui](http://oaxelf1sk.bkt.clouddn.com/gui.png)

��ߵ����������������ұ��ǵ������

### Frame

```java
		Frame f = new Frame("����ϼ");

		// ���ô������
		f.setTitle("HelloWorld");
		// ���ô����С
		f.setSize(400, 300); // ��λ������
		// ���ô���λ��
		f.setLocation(400, 200);

		// ����һ�������������ô���ɼ�
		f.setVisible(true);

```

setVisible�����ĵ���Ҫ��f.setSize(400, 300)��֮ǰ

```java
		 Dimension d = new Dimension(400, 300);
		 f.setSize(d);
		 // f.setLocation(400, 200);
		 // Point(int x, int y)
		 Point p = new Point(400, 200);
		 f.setLocation(p);
		 //һ�������㶨
		f.setBounds(400, 200, 400, 300);
```

#### �¼�����

```java
		// �����������
		Frame f = new Frame("����رհ���");

		// ���ô�������
		f.setBounds(400, 200, 400, 300);

		// �ô���ر�
		//�¼�Դ
		//�¼����Դ���Ĵ���
		//�¼������رմ���(System.exit(0));
		//�¼�����
//		f.addWindowListener(new WindowListener() {
//			@Override
//			public void windowOpened(WindowEvent e) {
//			}
//			
//			@Override
//			public void windowIconified(WindowEvent e) {
//			}
//			
//			@Override
//			public void windowDeiconified(WindowEvent e) {
//			}
//			
//			@Override
//			public void windowDeactivated(WindowEvent e) {
//			}
//			
//			@Override
//			public void windowClosing(WindowEvent e) {
//				System.exit(0);
//			}
//			
//			@Override
//			public void windowClosed(WindowEvent e) {
//			}
//			
//			@Override
//			public void windowActivated(WindowEvent e) {
//			}
//		});
		
		//����������Ľ�
		f.addWindowListener(new WindowAdapter() {
			@Override
			public void windowClosing(WindowEvent e) {
				System.exit(0);
			}
		});

		// ���ô���ɼ�
		f.setVisible(true);
	}

```
������¼�����������������ģʽ



## ���岼��

![���ı�������ת�Ƶ��ı���](http://oaxelf1sk.bkt.clouddn.com/���ı�������ת�Ƶ��ı���.jpg)



```java
public class FrameDemo {
	public static void main(String[] args) {
		// �����������
		Frame f = new Frame("����ת��");
		// ���ô������ԺͲ���
		f.setBounds(400, 200, 400, 300);
		f.setLayout(new FlowLayout());

		// �����ı���
		final TextField tf = new TextField(20);
		// ������ť
		Button bu = new Button("����ת��");
		// �����ı���
		final TextArea ta = new TextArea(10, 40);

		// �������ӵ�����
		f.add(tf);
		f.add(bu);
		f.add(ta);

		// ���ô���ر�
		f.addWindowListener(new WindowAdapter() {
			@Override
			public void windowClosing(WindowEvent e) {
				System.exit(0);
			}
		});

		// �԰�ť����¼�
		bu.addActionListener(new ActionListener() {
			@Override
			public void actionPerformed(ActionEvent e) {
				// ��ȡ�ı����ֵ
				String tf_str = tf.getText().trim();
				// �������
				tf.setText("");

				// ���ø��ı���
				// ta.setText(tf_str);
				// ׷�Ӻͻ���
				ta.append(tf_str + "\r\n");
				
				//��ȡ���
				tf.requestFocus();
			}
		});

		// ���ô�����ʾ
		f.setVisible(true);
	}
}
```

ע�⣺final TextField tf = new TextField(20);��final���η����ֲ��ڲ�����ʾֲ�����������һ��Ҫʹ��final����

## ������ģʽ

```java
/*
 * ����û����������ֹ���
 */
public interface UserDao {
	public abstract void add();

	public abstract void delete();

	public abstract void update();

	public abstract void find();
}

public abstract class UserAdapter implements UserDao {

	@Override
	public void add() {
	}

	@Override
	public void delete() {
	}

	@Override
	public void update() {
	}

	@Override
	public void find() {
	}
}

public class UserDaoImpl2 extends UserAdapter {
	@Override
	public void add() {
		System.out.println("��ӹ���");
	}
}

/*
 * ���⣺
 * 		�ӿ�(�����Ƚ϶�) -- ʵ����(����ʹ��һ����Ҳ�ð�������ʵ�ָ��ṩ�ˣ������ǿ�ʵ��)
 * 		̫�鷳�ˡ�
 * ���������
 * 		�ӿ�(�����Ƚ϶�) -- ��������(ʵ�ֽӿ�,������ʵ��) -- ʵ����(���ĸ���д�ĸ�)
 */
public class UserDaoDemo {
	public static void main(String[] args) {
		// ��û��˵������Ҫ���ֹ��ܶ�ʵ�ְ���
		UserDao ud2 = new UserDaoImpl2();
		ud2.add();
	}
}
```

## ��ϰ

![qq](http://oaxelf1sk.bkt.clouddn.com/qq.png)

```java
/*
 * �����������Ƿ������ַ�����ȡ�������¼���Ч����
 */
public class FrameDemo {
	public static void main(String[] args) {
		// �������������������
		Frame f = new Frame("��������������ַ�");
		f.setBounds(400, 200, 400, 300);
		f.setLayout(new FlowLayout());

		// ����Label��ǩ����
		Label label = new Label("���������QQ���룬�����Ƿ����֣�����������");
		TextField tf = new TextField(40);

		// ��ӵ�������
		f.add(label);
		f.add(tf);

		// ���ô���ر�
		f.addWindowListener(new WindowAdapter() {
			@Override
			public void windowClosing(WindowEvent e) {
				System.exit(0);
			}
		});

		// ���ı�������¼�
		tf.addKeyListener(new KeyAdapter() {
			@Override
			public void keyPressed(KeyEvent e) {
				// �����ȡ�õ��ַ����������ַ���ȡ���¼�
				// ˼·���Ȼ�ȡ�ַ����ж��ַ���ȡ���¼�
				// char getKeyChar()  
				char ch = e.getKeyChar();
//				System.out.println(ch);
				if(!(ch>='0' && ch<='9')){
					e.consume();
				}
			}
		});

		// ���ô���ɼ�
		f.setVisible(true);
	}
}
```
