# Android IPC机制-Messager

[TOC]

## 简介

如果你需要能够编写可以在远程进程客户端执行复杂的通信（不仅仅是简单的使用服务 `Context.startService`将命令发送到它），那么你可以使用`Messenger`类，而不是写全AIDL文件。

### 和Binder以及AIDL区别

a.只有当你需要来自不同应用的客户端通过IPC（进程间通信）通信来访问你的服务时，并且想在服务里处理多线程的业务，这时就需要使用AIDL。

b.如果你不需要同时对几个应用进程IPC操作，你最好通过实现Binder接口来创建你的接口。

c.如果你仍需要执行IPC操作，但不需要处理多线程，使用Messenger来实现接口即可。

d.无论如何，在实现AIDL之前确保你完全理解Bound Service相关知识。

## 使用

```
public class MessengerService extends Service {

    private static final int MSG_SUM = 0x110;

    //最好换成HandlerThread的形式
    private Messenger mMessenger = new Messenger(new Handler() {
        @Override
        public void handleMessage(Message msgfromClient) {
            Message msgToClient = Message.obtain(msgfromClient);//返回给客户端的消息
            switch (msgfromClient.what) {
                //msg 客户端传来的消息
                case MSG_SUM:
                    msgToClient.what = MSG_SUM;
                    try {
                        Log.d(TAG, "handleMessage: 模拟耗时");
                        //模拟耗时
//                        Thread.sleep(2000);
                        msgToClient.arg2 = msgfromClient.arg1 + msgfromClient.arg2;
                        msgfromClient.replyTo.send(msgToClient);
                    } catch (RemoteException e) {
                        e.printStackTrace();
                    }
                    break;
            }

            super.handleMessage(msgfromClient);
        }
    });

    @Override
    public IBinder onBind(Intent intent) {
        return mMessenger.getBinder();
    }
}
```
服务端就一个Service，可以看到代码相当的简单，只需要去声明一个Messenger对象，然后onBind方法返回mMessenger.getBinder()；

然后坐等客户端将消息发送到handleMessage想法，根据message.what去判断进行什么操作，然后做对应的操作，最终将结果通过 msgfromClient.replyTo.send(msgToClient);返回。

replyTo同时把管理信息的messager传递出去，客户端和服务器都可以通过这样来改动数据

​	可以看到我们这里主要是取出客户端传来的两个数字，然后求和返回，添加了sleep(2000)模拟耗时,注意在实际使用过程中，可以换成在独立开辟的线程中完成耗时操作，比如和HandlerThread结合使用。

### 注册文件

注意指定不同的进程，从而实现不同进程之间的通信

```
<service
            android:name=".MessengerService"
            android:enabled="true"
            android:exported="true"
            android:process=":ty">
            <intent-filter>
                <action android:name="com.zhy.aidl.calc"></action>
                <category android:name="android.intent.category.DEFAULT" />
            </intent-filter>
        </service>
```
### Actiivty及布局文件

```
<LinearLayout android:id="@+id/id_ll_container"
              xmlns:android="http://schemas.android.com/apk/res/android"
              xmlns:tools="http://schemas.android.com/tools"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              android:orientation="vertical"
              android:paddingBottom="@dimen/activity_vertical_margin"
              android:paddingLeft="@dimen/activity_horizontal_margin"
              android:paddingRight="@dimen/activity_horizontal_margin"
              android:paddingTop="@dimen/activity_vertical_margin"
              tools:context=".MainActivity">
 
    <TextView
        android:id="@+id/id_tv_callback"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Messenger Test!"/>
 
    <Button android:id="@+id/id_btn_add"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="add"/>
 
</LinearLayout>
```

```
public class MainActivity extends AppCompatActivity {
    private static final String TAG = "MainActivity";
    private static final int MSG_SUM = 0x110;

    private Button mBtnAdd;
    private LinearLayout mLyContainer;
    //显示连接状态
    private TextView mTvState;

    private Messenger mService;
    private boolean isConn;


    private Messenger mMessenger = new Messenger(new Handler()
    {
        @Override
        public void handleMessage(Message msgFromServer)
        {
            switch (msgFromServer.what)
            {
                case MSG_SUM:
                    TextView tv = (TextView) mLyContainer.findViewById(msgFromServer.arg1);
                    tv.setText(tv.getText() + "=>" + msgFromServer.arg2);
                    break;
            }
            super.handleMessage(msgFromServer);
        }
    });


    private ServiceConnection mConn = new ServiceConnection()
    {
        @Override
        public void onServiceConnected(ComponentName name, IBinder service)
        {
            mService = new Messenger(service);
            isConn = true;
            mTvState.setText("connected!");
        }

        @Override
        public void onServiceDisconnected(ComponentName name)
        {
            mService = null;
            isConn = false;
            mTvState.setText("disconnected!");
        }
    };

    private int mA;

    @Override
    protected void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        //开始绑定服务
        bindServiceInvoked();

        System.out.println(getPackageName());

        mTvState = (TextView) findViewById(R.id.id_tv_callback);
        mBtnAdd = (Button) findViewById(R.id.id_btn_add);
        mLyContainer = (LinearLayout) findViewById(R.id.id_ll_container);

        mBtnAdd.setOnClickListener(new View.OnClickListener()
        {
            @Override
            public void onClick(View v)
            {
                try
                {
                    int a = mA++;
                    int b = (int) (Math.random() * 100);

                    //创建一个tv,添加到LinearLayout中
                    TextView tv = new TextView(MainActivity.this);
                    tv.setText(a + " + " + b + " = caculating ...");
                    tv.setId(a);
                    mLyContainer.addView(tv);

                    Message msgFromClient = Message.obtain(null, MSG_SUM, a, b);
                    msgFromClient.replyTo = mMessenger;
                    if (isConn)
                    {
                        //往服务端发送消息
                        mService.send(msgFromClient);
                    }
                } catch (RemoteException e)
                {
                    e.printStackTrace();
                }
            }
        });

    }

    private void bindServiceInvoked()
    {
        Intent intent = new Intent();
        intent.setAction("com.zhy.aidl.calc");
        /*
        从Lollipop开始，service服务必须采用显示方式启动
        Intent mIntent = new Intent();
        mIntent.setAction("XXX.XXX.XXX");//你定义的service的action
        mIntent.setPackage(getPackageName());//这里你需要设置你应用的包名
        context.startService(mIntent);
        */
        intent.setPackage(getPackageName());
        bindService(intent, mConn, Context.BIND_AUTO_CREATE);
        Log.e(TAG, "bindService invoked !");
    }

    @Override
    protected void onDestroy()
    {
        super.onDestroy();
        unbindService(mConn);
    }
}

```

## 参考

http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0723/3216.html

https://developer.android.com/reference/android/app/Service.html#RemoteMessengerServiceSample

http://blog.csdn.net/cs_lht/article/details/6698468