# 09-Android 异步消息处理机制（Handler 、 Looper 、MessageQueue）

[TOC]

## Thread，Looper和Handler的关系

与Windows系统一样，Android也是消息驱动型的系统。引用一下消息驱动机制的四要素：

- 接收消息的“消息队列”
- 阻塞式地从消息队列中接收消息并进行处理的“线程”
- 可发送的“消息的格式”
- “消息发送函数”

与之对应，Android中的实现对应了

- 接收消息的“消息队列” ——【MessageQueue】
- 阻塞式地从消息队列中接收消息并进行处理的“线程” ——【Thread+Looper】
- 可发送的“消息的格式” ——【Message】
- “消息发送函数”——【Handler的post和sendMessage】

一个`Looper`类似一个消息泵。它本身是一个死循环，不断地从`MessageQueue`中提取`Message`或者Runnable。而`Handler`可以看做是一个`Looper`的暴露接口，向外部暴露一些事件，并暴露`sendMessage()`和`post()`函数。

​	一个Handler会允许你发送和处理Message或者Runnable对象关联到一个线程的消息队列MessageQueue中,每一个Handler的实例都会关联一个单一的线程和那个线程的消息队列中。当你创建一个一个新的Handler,它会绑定到你创建的线程和这个线程消息队列中。

  	 对于Handler来说有两种主要的方式: 1. 计划好消息和Runnable将来的某一个时间点来执行它 2. 从一个不同的线程中执行Handler的入队操作。分发消息由下面的几个方法完成:

   1) post(Runnable),
   2) postAtTime(Runnable, long), 
   3) postDelayed(Runnable, long), 
   4) sendEmptyMessage(int), 
   5) sendMessage(Message), 
   6) sendMessageAtTime(Message, long), 
   7) sendMessageDelayed(Message, long)

   post方式的方法可以将一个Runable对象排列到消息队列中。sendMessage方式的方法可以通过 Handler的handleMessage(Message) 方法携带有bundle类型的数据的Message对象到队列中(需要你实现Handler的子类)。

## Message类介绍

message包含描述信息和任意的数据对象发送给Handler。这个对象包含两个额外的int类型的属性和一个Object类型的属性，它可以让你不需要去做一些强制类型的转换的操作

| `public int`       | `arg1`arg1 and arg2 are lower-cost alternatives to using `setData()` if you only need to store a few integer values. |
| ------------------ | ---------------------------------------- |
| `public int`       | `arg2`arg1 and arg2 are lower-cost alternatives to using `setData()` if you only need to store a few integer values. |
| `public Object`    | `obj`An arbitrary object to send to the recipient. |
| `public Messenger` | `replyTo`Optional Messenger where replies to this message can be sent. |
| `public int`       | `sendingUid`Optional field indicating the uid that sent the message. |
| `public int`       | `what`User-defined message code so that the recipient can identify what this message is about. |

 1) **arg1 和 arg2** 都是Message自带的用来传递一些轻量级存储int类型的数据，比如进度条的数据等。通过这个数据是通过Bundle的方式来转载的，读者可以自己查阅源代码研究。

  2) **obj** 是Message自带的Object类型对象，用来传递一些对象。兼容性最高避免对齐进行类型转换等。

  3) **replyTo **是作为线程通信的时候使用.

  4) **what **用户自定义的消息码让接受者识别消息种类,int类型。

> 获得Message的构造方法最好的方式是调用Message.obtain() 和 Handler.obtainMessage()方法,以便能够更好被回收池所回收

### 获得Message的方式

1. 通过 Message.obtain()方式获取Message对象

```
public class MyThread implements Runnable{  
  
    @Override  
    public void run() {  
        // 使用第一种构造方法  
        Message message = Message.obtain();  
        message.what = 1;  
        message.arg1 = 1;  
        message.arg2 = 3;  
        message.obj = "AHuier";  
        handler.sendMessage(message);  
    }          
}  
```

2. 通过 Message.obtain(Handler h)的方式获取Message对象

```
/* 
 *  第二种获取Message对象的方法 
 *  public static Message obtain (Handler h) 
 *  传递一个关联到消息Handler. 
 */  
Message message = Message.obtain(handler);  
message.what = 1;  
message.arg1 = 1;  
message.arg2 = 3;  
message.obj = "AHuier";  
message.sendToTarget(); // 完成发送消息的动作
```

3. 通过 Message.obtain(Handler h)的方式获取Message对象

```
/* 
 * 第三种获取Message对象的方法 
 * public static Message obtain (Handler h, int what) 
 * 关联一个Handler和传递一个what的属性值 
 */  
Message message = Message.obtain(handler, 1);  
message.arg1 = 1;  
message.arg2 = 3;  
message.obj = "AHuier";  
message.sendToTarget();
```

Message中的obtain()的几种重载方法在底层的实现都是大同小异的,他们都是底层都是首先调用obtain()方法来从消息池中获得一个消息的对象的。然后在通过参数传递来封装指定的Handler和需要携带的数据。如果使用这些重载的方法建议完成数据封装之后调用sendToTarget()方法。

> **Message obtain (Message orig)** 它是将原有的消息体作为一个新的消息参数来发送的

### 使用Bundle方式来传递复杂的数据类型

```
/* 
 * public static Message obtain (Handler h, int what, int arg1, int arg2, Object obj) 
 * 关联Handler和传递Message的几种常用属性值 
 */  
Message message = Message.obtain(handler, 1, 1, 3, "AHuier");  
Bundle data = new Bundle();  
data.putStringArray("str", new String[]{"AHui", "AHui1", "AHui2"});  
message.setData(data);  
message.sendToTarget();  
```

​	如果是携带的是轻量级的int类型的数据或者对象的话，我们就用Message构造方法中自带的属性来传递。如果是需要携带上述两种类型之外的数据类型或者一些比较复杂的数据类型建议使用Bundle的方式来封装好后来传递。

## 开启Looper

除了`UI线程`/`主线程`以外，普通的线程(先不提`HandlerThread`)是不自带`Looper`的。想要通过UI线程与子线程通信需要在子线程内自己实现一个`Looper`。开启Looper分**三步走**：

１．调用Looper的prepare()方法为当前线程创建Looper对象，创建Looper对象时，它的构造器会创建与之配套的MessageQueue。 　

２．有了Looper之后，创建Handler子类实例，重写HanderMessage()方法，该方法负责处理来自于其他线程的消息。 　

３．调用Looper的looper()方法启动Looper。

　　然后使用这个handler实例在任何其他线程中发送消息，最终处理消息的代码都会在你创建Handler实例的线程中运行。

由于每一个线程内最多只可以有一个`Looper`，所以一定要在`Looper.prepare()`之前做好判定，否则会抛出`java.lang.RuntimeException: Only one Looper may be created per thread`。为了获取Looper的信息可以使用两个方法：

- Looper.myLooper()
- Looper.getMainLooper()

`Looper.myLooper()`获取当前线程绑定的Looper，如果没有返回`null`。`Looper.getMainLooper()`返回主线程的`Looper`,这样就可以方便的与主线程通信。注意：**在Thread的构造函数中调用Looper.myLooper只会得到主线程的Looper**，因为此时新线程还未构造好

### 实际案例

MainActivity发送信息到handler，并且handler处理，实现自定义的线程并且集成handler：

```
public class MyHandler extends Thread {

    public   Handler mhandler;
        // 实现Runnable接口的线程体 
        @Override  
        public void run() {  

         /*①、调用Looper的prepare()方法为当前线程创建Looper对象并，
          创建Looper对象时，它的构造器会自动的创建相对应的MessageQueue*/
            Looper.prepare();  

            /*.②、创建Handler子类的实例，重写HandleMessage()方法，该方法处理除当前线程以外线程的消息*/
             myHandler = new Handler() {  
                @Override  
                public void handleMessage(Message msg) {  
                    String ms = "";  
                    if (msg.what == 0x777) {  

                    }  
                }  

            };  
            //③、调用Looper的loop()方法来启动Looper让消息队列转动起来
            Looper.loop();  
        }
}
```

MainActivity的实现逻辑：

```
public class MainActivity extends AppCompatActivity {

    private MyHandler handler;
    private final String TAG = "ty";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        handler = new MyHandler();
    }

    public void button1(View view) {
        handler.start();
        Log.d(TAG, "button1: 开启线程");
    }

    public void button2(View view) {
        Message message = handler.mhandler.obtainMessage();
        message.what = 66666;
        handler.mhandler.sendMessage(message);
        Log.d(TAG, "button2: 发送信息");
    }
}
```

> 注意loolper开启：Looper.prepare()和工作Looper.loop()

## handler的sendMessage方法

Android不但可以使用异步任务处理多线程的问题,还可以通过Handler + Message + Thread 的方式进行,例如更新主线程UI等.

![](http://oaxelf1sk.bkt.clouddn.com/20131129165641468.png)

​	android 中发送消息不管是Message中的几种重载的obtain()方式，还是Handler中的几种重载的sendMessage最终都是通过Handler.sendMessage来发送的,而Handler中的几种sendMessage()重载方法最终都会调用到sendMessageAtTime()方法来完成消息的入队操作

## 总结

Handler：发送消息，它能把消息发送给Looper管理的MessageQueue，处理消息，并负责处理Looper分给它的消息。

Message：Handler接收和处理的消息对象。 

Looper：每个线程只有一个Looper，它负责管理对应的MessageQueue，会不断地从MessageQueue取出消息，并将消息分给对应的Hanlder处理。主线程中，系统已经初始化了一个Looper对象，因此可以直接创建Handler即可，就可以通过Handler来发送消息、处理消息。 程序自己启动的子线程，程序必须自己创建一个Looper对象，并启动它，调用Looper.prepare()方法。

> prapare()方法：保证每个线程最多只有一个Looper对象。 　
>
> looper()方法：启动Looper，使用一个死循环不断取出MessageQueue中的消息，并将取出的消息分给对应的Handler进行处理。 　

MessageQueue：由Looper负责管理，它采用先进先出的方式来管理Message。　

Handler的构造方法，会首先得到当前线程中保存的Looper实例，进而与Looper实例中的MessageQueue想关联。Handler的sendMessage方法，会给msg的target赋值为handler自身，然后加入MessageQueue中。

## 参考

https://hit-alibaba.github.io/interview/Android/basic/Android-handler-thread-looper.html

https://github.com/HaoTianYi/ForAndroidInterview/blob/master/android/Android%20%E5%BC%82%E6%AD%A5%E6%B6%88%E6%81%AF%E5%A4%84%E7%90%86%E6%9C%BA%E5%88%B6%EF%BC%88Handler%20%E3%80%81%20Looper%20%E3%80%81MessageQueue%EF%BC%89%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90.md

http://blog.csdn.net/ahuier/article/details/17012005

http://blog.csdn.net/ahuier/article/details/17012923