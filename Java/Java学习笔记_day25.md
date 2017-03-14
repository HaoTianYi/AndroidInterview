# Java学习笔记_day25

[TOC]

## GUI

Graphical User Interface(图形用户接口)

### API所在的包

java.awt：Abstract Window ToolKit (抽象窗口工具包)，需要调用本地系统方法实现功能。属重量级控件。
javax.swing：在AWT的基础上，建立的一套图形界面系统，其中提供了更多的组件，而且完全由Java实现。增强了移植性，属轻量级控件。

java是java的基本包，javax是扩展包

### GUI继承体系

![gui](http://oaxelf1sk.bkt.clouddn.com/gui.png)

左边的组件是容器组件，右边是单个组件

### Frame

```java
		Frame f = new Frame("林青霞");

		// 设置窗体标题
		f.setTitle("HelloWorld");
		// 设置窗体大小
		f.setSize(400, 300); // 单位：像素
		// 设置窗体位置
		f.setLocation(400, 200);

		// 调用一个方法，设置让窗体可见
		f.setVisible(true);

```

setVisible方法的调用要在f.setSize(400, 300)等之前

```java
		 Dimension d = new Dimension(400, 300);
		 f.setSize(d);
		 // f.setLocation(400, 200);
		 // Point(int x, int y)
		 Point p = new Point(400, 200);
		 f.setLocation(p);
		 //一个方法搞定
		f.setBounds(400, 200, 400, 300);
```

#### 事件监听

```java
		// 创建窗体对象
		Frame f = new Frame("窗体关闭案例");

		// 设置窗体属性
		f.setBounds(400, 200, 400, 300);

		// 让窗体关闭
		//事件源
		//事件：对窗体的处理
		//事件处理：关闭窗口(System.exit(0));
		//事件监听
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
		
		//用适配器类改进
		f.addWindowListener(new WindowAdapter() {
			@Override
			public void windowClosing(WindowEvent e) {
				System.exit(0);
			}
		});

		// 设置窗体可见
		f.setVisible(true);
	}

```
这里的事件监听运用了适配器模式



## 窗体布局

![把文本框数据转移到文本域](http://oaxelf1sk.bkt.clouddn.com/把文本框数据转移到文本域.jpg)



```java
public class FrameDemo {
	public static void main(String[] args) {
		// 创建窗体对象
		Frame f = new Frame("数据转移");
		// 设置窗体属性和布局
		f.setBounds(400, 200, 400, 300);
		f.setLayout(new FlowLayout());

		// 创建文本框
		final TextField tf = new TextField(20);
		// 创建按钮
		Button bu = new Button("数据转移");
		// 创建文本域
		final TextArea ta = new TextArea(10, 40);

		// 把组件添加到窗体
		f.add(tf);
		f.add(bu);
		f.add(ta);

		// 设置窗体关闭
		f.addWindowListener(new WindowAdapter() {
			@Override
			public void windowClosing(WindowEvent e) {
				System.exit(0);
			}
		});

		// 对按钮添加事件
		bu.addActionListener(new ActionListener() {
			@Override
			public void actionPerformed(ActionEvent e) {
				// 获取文本框的值
				String tf_str = tf.getText().trim();
				// 清空数据
				tf.setText("");

				// 设置给文本域
				// ta.setText(tf_str);
				// 追加和换行
				ta.append(tf_str + "\r\n");
				
				//获取光标
				tf.requestFocus();
			}
		});

		// 设置窗体显示
		f.setVisible(true);
	}
}
```

注意：final TextField tf = new TextField(20);的final修饰符，局部内部类访问局部变量，变量一定要使用final修饰

## 适配器模式

```java
/*
 * 针对用户操作的四种功能
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
		System.out.println("添加功能");
	}
}

/*
 * 问题：
 * 		接口(方法比较多) -- 实现类(仅仅使用一个，也得把其他的实现给提供了，哪怕是空实现)
 * 		太麻烦了。
 * 解决方案：
 * 		接口(方法比较多) -- 适配器类(实现接口,仅仅空实现) -- 实现类(用哪个重写哪个)
 */
public class UserDaoDemo {
	public static void main(String[] args) {
		// 我没有说我们需要四种功能都实现啊。
		UserDao ud2 = new UserDaoImpl2();
		ud2.add();
	}
}
```

## 练习

![qq](http://oaxelf1sk.bkt.clouddn.com/qq.png)

```java
/*
 * 你输入的如果是非数字字符，就取消你键盘录入的效果。
 */
public class FrameDemo {
	public static void main(String[] args) {
		// 创建窗体对象并设置属性
		Frame f = new Frame("不能输入非数字字符");
		f.setBounds(400, 200, 400, 300);
		f.setLayout(new FlowLayout());

		// 创建Label标签对象
		Label label = new Label("请输入你的QQ号码，不能是非数字，不信你试试");
		TextField tf = new TextField(40);

		// 添加到窗体上
		f.add(label);
		f.add(tf);

		// 设置窗体关闭
		f.addWindowListener(new WindowAdapter() {
			@Override
			public void windowClosing(WindowEvent e) {
				System.exit(0);
			}
		});

		// 给文本框添加事件
		tf.addKeyListener(new KeyAdapter() {
			@Override
			public void keyPressed(KeyEvent e) {
				// 如果你取得的字符不是数字字符就取消事件
				// 思路：先获取字符，判断字符，取消事件
				// char getKeyChar()  
				char ch = e.getKeyChar();
//				System.out.println(ch);
				if(!(ch>='0' && ch<='9')){
					e.consume();
				}
			}
		});

		// 设置窗体可见
		f.setVisible(true);
	}
}
```
