# 02-说说Service那些事

[TOC]

## 简介

Service本质作用就是一个没有界面的Activity，它可以在后台执行代码永远不用到前台来，所以一个模块只要不想要与用户交互但是还要执行逻辑就可以使用Service。默认运行在程序的主线程之中。

## 重要的方法

```
public class ExampleService extends Service {
    int mStartMode;       // 标识服务被杀死后的处理方式
    IBinder mBinder;      // 用于客户端绑定的接口
    boolean mAllowRebind; // 标识是否使用onRebind

    @Override
    public void onCreate() {
        // 服务正被创建
    }
    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        // 服务正在启动，由startService()调用引发
        return mStartMode;
    }
    @Override
    public IBinder onBind(Intent intent) {
        // 客户端用bindService()绑定服务
        return mBinder;
    }
    @Override
    public boolean onUnbind(Intent intent) {
        // 所有的客户端都用unbindService()解除了绑定
        return mAllowRebind;
    }
    @Override
    public void onRebind(Intent intent) {
        // 某客户端正用bindService()绑定到服务,
        // 而onUnbind()已经被调用过了
    }
    @Override
    public void onDestroy() {
        // 服务用不上了，将被销毁
    }
}
```

请注意onStartCommand()方法必须返回一个整数。这个整数是描述系统在杀死服务之后应该如何继续运行。onStartCommand()的返回值必须是以下常量之一：

START_NOT_STICKY 　 如果系统在onStartCommand()返回后杀死了服务，则不会重建服务了，除非还存在未发送的intent。 当服务不再是必需的，并且应用程序能够简单地重启那些未完成的工作时，这是避免服务运行的最安全的选项。　 　 

START_STICKY 如果系统在onStartCommand()返回后杀死了服务，则将重建服务并调用onStartCommand()，但不会再次送入上一个intent， 而是用null intent来调用onStartCommand() 。除非还有启动服务的intent未发送完，那么这些剩下的intent会继续发送。 这适用于媒体播放器（或类似服务），它们不执行命令，但需要一直运行并随时待命。　

START_REDELIVER_INTENT 如果系统在onStartCommand()返回后杀死了服务，则将重建服务并用上一个已送过的intent调用onStartCommand()。任何未发送完的intent也都会依次送入。这适用于那些需要立即恢复工作的活跃服务，比如下载文件。

## 两种启动模式

### StartService

这类服务由其它组件调用startService()来创建。然后保持运行，且必须通过调用stopSelf()自行终止。其它组件也可通过调用stopService() 终止这类服务。服务终止后，系统会把它销毁。

该Service将会一直在后台运行，而不管对应程序的Activity是否在运行，直到被调用stopService，或自身的stopSelf方法。当然如果系统资源不足，android系统也可能结束服务。

### BindService

　　服务由其它组件（客户端）调用bindService()来创建。然后客户端通过一个IBinder接口与服务进行通信。客户端可以通过调用unbindService()来关闭联接。多个客户端可以绑定到同一个服务上，当所有的客户端都解除绑定后，系统会销毁服务。（服务不需要自行终止。）

　　如果一个Service被某个Activity 调用 Context.bindService 方法绑定启动，不管调用 bindService 调用几次，onCreate方法都只会调用一次，同时onStart方法始终不会被调用。当连接建立之后，Service将会一直运行，除非调用Context.unbindService 断开连接或者之前调用bindService 的 Context 不存在了（如Activity被finish的时候），系统将会自动停止Service，对应onDestroy将被调用，也就是这种启动模式依托于其他的组件。

#### 具体启动过程

- 重写onBind方法并且创建一个类继承自Binder
- 在Activity中初始化ServiceConnection和自己刚刚继承的Binder类
- 调用bindService绑定服务

**全部代码**

```
public class MyService extends Service {
    private int Code = 10;
    MediaPlayer mPlayer;

    public MyService() {
//        mPlayer = MediaPlayer.create(this,R.raw.wu);
    }

    @Override
    public void onCreate() {
        mPlayer = MediaPlayer.create(this,R.raw.wu);
        super.onCreate();
    }

    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        mPlayer.start();
        Log.d("ty","-----onStartCommand------");
        return START_STICKY;
    }


    @Override
    public void onDestroy() {
        mPlayer.stop();
        Log.d("ty","-----onDestroy------");
        super.onDestroy();
    }

    @Override
    public IBinder onBind(Intent intent) {
        Log.d("ty","-----onBind------");
        mPlayer.start();
        return new MyBinder();
    }

    @Override
    public boolean onUnbind(Intent intent) {
        Log.d("ty","-----onUnbind------");
//        mPlayer.stop();
        return super.onUnbind(intent);
    }
```

```
public class MainActivity extends AppCompatActivity {

    private Intent mIntent;
    ServiceConnection mServiceConnection;
    MyService.MyBinder mBinder;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mIntent = new Intent(MainActivity.this, MyService.class);

        mServiceConnection = new ServiceConnection() {
            @Override
            public void onServiceConnected(ComponentName name, IBinder service) {
                mBinder = (MyService.MyBinder) service;
            }

            @Override
            public void onServiceDisconnected(ComponentName name) {

            }
        };
    }

    public void unBind(View view) {
        stopService(mIntent);
        Log.d("ty","-----解绑------");
    }

    public void bind(View view) {
        startService(mIntent);
        Log.d("ty","-----绑定------");
    }

    public void rebind(View view) {
        bindService(mIntent,mServiceConnection,BIND_AUTO_CREATE);
        Log.d("ty","-----使用binder绑定------");
    }

    public void reUnBind(View view) {
        unbindService(mServiceConnection);
        Log.d("ty","-----binder解绑------");
    }
    @Override
    protected void onDestroy() {
        unbindService(mServiceConnection);
        stopService(mIntent);
    }
}
```

> 注意bindService是Context中的方法，当没有Context时传入即可。

在进行服务绑定的时，其flags有：

- Context.BIND_AUTO_CREATE

　　表示收到绑定请求的时候，如果服务尚未创建，则即刻创建，在系统内存不足需要先摧毁优先级组件来释放内存，且只有驻留该服务的进程成为被摧毁对象时，服务才被摧毁　

- Context.BIND_DEBUG_UNBIND 　

　　通常用于调试场景中判断绑定的服务是否正确，但容易引起内存泄漏，因此非调试目的的时候不建议使用

- Context.BIND_NOT_FOREGROUND 　

　　表示系统将阻止驻留该服务的进程具有前台优先级，仅在后台运行。

**注意**

**在Activity中的onDestroy方法中最后一定要要解绑服务**

## 生命周期

![sp161025_204943](http://oaxelf1sk.bkt.clouddn.com/sp161025_204943.png)

正常的生命周期有两种，有一种比较特殊就是onCreat--->onStartCommend----->onBind--->onUnBind

Android官方的生命周期：

![guanfnag](http://oaxelf1sk.bkt.clouddn.com/687474703a2f2f696d672e626c6f672e6373646e2e6e65742f323031363035320172281546.png)

### Start的生命周期

![sp161025_205219](http://oaxelf1sk.bkt.clouddn.com/sp161025_205219.png)

当多次启动Service的时候OnCreate方法只是执行一次，onStartCommand执行多次

### Bind的生命周期

![sp161025_205450](http://oaxelf1sk.bkt.clouddn.com/sp161025_205450.png)

无论绑定多少次都是onCreate然后onBind，onServiceConnected是连接成功时候调用的方法

### 两种模式联系

这两条路径并不是完全隔离的。也就是说，你可以绑定到一个已经用startService()启动的服务上。例如，一个后台音乐服务可以通过调用startService()来启动，传入一个指明所需播放音乐的 Intent。 之后，用户也许需要用播放器进行一些控制，或者需要查看当前歌曲的信息，这时一个activity可以通过调用bindService()与此服务绑定。在类似这种情况下，stopService()或stopSelf()不会真的终止服务，除非所有的客户端都解除了绑定。

> 当在旋转手机屏幕的时候，当手机屏幕在“横”“竖”变换时，此时如果你的 Activity 如果会自动旋转的话，旋转其实是 Activity 的重新创建，因此旋转之前的使用 bindService 建立的连接便会断开（Context 不存在了）。

## Manifest文件中的标签

Service 元素的属性有：

> android:name　　-------------　　服务类名
>
> android:label　　--------------　　服务的名字，如果此项不设置，那么默认显示的服务名则为类名
>
> android:icon　　--------------　　服务的图标
>
> android:permission　　-------　　申明此服务的权限，这意味着只有提供了该权限的应用才能控制或连接此服务
>
> android:process　　----------　　表示该服务是否运行在另外一个进程，如果设置了此项，那么将会在包名后面加上这段字符串表示另一进程的名字
>
> android:enabled　　----------　　如果此项设置为 true，那么 Service 将会默认被系统启动，不设置默认此项为 false
>
> android:exported　　---------　　表示该服务是否能够被其他应用程序所控制或连接，不设置默认此项为 false　

## 服务是否在运行

```
/** 
 * 判断某个服务是否正在运行的方法 
 *  
 * @param mContext 
 * @param serviceName 
 *            是包名+服务的类名（例如：net.loonggg.testbackstage.TestService） 
 * @return true代表正在运行，false代表服务没有正在运行 
 */  
public boolean isServiceWork(Context mContext, String serviceName) {  
    boolean isWork = false;  
    ActivityManager myAM = (ActivityManager) mContext  
            .getSystemService(Context.ACTIVITY_SERVICE);  
    List<RunningServiceInfo> myList = myAM.getRunningServices(40);  
    if (myList.size() <= 0) {  
        return false;  
    }  
    for (int i = 0; i < myList.size(); i++) {  
        String mName = myList.get(i).service.getClassName().toString();  
        if (mName.equals(serviceName)) {  
            isWork = true;  
            break;  
        }  
    }  
    return isWork;  
}  
```

## IntentService

Service中也无法执行大量的耗时操作，所以Android官方推出一个IntentService当使用它是时候系统会自动创建一个进程，不会阻塞主线程，开发者只要重写onHandleIntent方法即可：

### 测试代码

分别再onHandleIntent方法和onStartCommand方法中添加耗时逻辑，看是否弹出ANR:

```
    @Override
    protected void onHandleIntent(Intent intent) {
        System.out.println("---------onHandleIntent----------");
        long endTime = System.currentTimeMillis() + 100 * 1000;
        while (System.currentTimeMillis() < endTime) {
            synchronized (MyIntentService.this) {
                try {
                    wait(endTime - System.currentTimeMillis());
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
```

**onStartCommand**

```
    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        System.out.println("---------onStartCommand----------");
        long endTime = System.currentTimeMillis() + 100 * 1000;
        while (System.currentTimeMillis() < endTime) {
            synchronized (MyService.this) {
                try {
                    wait(endTime - System.currentTimeMillis());
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
        return super.onStartCommand(intent, flags, startId);
    }
```

在onStartCommand中会弹出：

![sp161026_183335](http://oaxelf1sk.bkt.clouddn.com/sp161026_183335.png)

### 和Intent的异同

　　Service不是独立的进程，也不是独立的线程，它是依赖于应用程序的主线程的，不建议在Service中编写耗时的逻辑和操作，否则会引起ANR。

　　IntentService 它创建了一个独立的工作线程来处理所有的通过onStartCommand()传递给服务的intents（把intent插入到工作队列中）。通过工作队列把intent逐个发送给onHandleIntent()。IntentService不需要主动调用stopSelft()来结束服务。因为，在所有的intent被处理完后，系统会自动关闭服务。

　　 默认实现的onBind()返回null。

