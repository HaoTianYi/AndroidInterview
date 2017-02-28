# 四大组件_Activity

[TOC]

## 简介

Activity就是每一个应用的界面，凡事显示在app中的界面都是Activity或者由Activity启动。

### 基本使用

- 使用as快捷建立Activity

![sp161025_140448](http://oaxelf1sk.bkt.clouddn.com/sp161025_140448.png)

- 常规创建

1. 在app/src/main/AndroidManifest.xml注册
2. 编写java代码继承自Activity或者它的子类

自动创建的Activity如下所示： 

![sp161025_140733](http://oaxelf1sk.bkt.clouddn.com/sp161025_140733.png)

就是一个activity的标签

### 传递数据

我们现在创建两个Activity由MainActivity向SecondActivity传递一个字符串“HelloWorld”

- 在MianActivity中设置带有数据的intent

![sp161025_141217](http://oaxelf1sk.bkt.clouddn.com/sp161025_141217.png)

- startActivityForResult启动activity，第二个参数是请求码
- 重写onActivityResult接受数据

**SecondActivity**

```
        setResult(666);
        String hello = getIntent().getBundleExtra("hello").getString("nihao");
```

设置结果码--->拿到数据

### 完整代码

```
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }
//在按钮中注册的方法
    public void start(View view) {
        Intent intent = new Intent(MainActivity.this, SecondActivity.class);
        Bundle bundle = new Bundle();
        bundle.putString("nihao", "HelloWorld");
        intent.putExtra("hello", bundle);
        startActivityForResult(intent, 0);
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        if (resultCode == 666) {
            System.out.println("世界你好");
        }
    }
}



public class SecondActivity extends AppCompatActivity {


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_second);

        setResult(666);
        String hello = getIntent().getBundleExtra("hello").getString("nihao");

        TextView textView = (TextView) findViewById(R.id.tv);
        textView.setText(hello);
    }

//在按钮中注册的方法
    public void finsh(View view) {
        this.finish();
    }
}
```

## 生命周期

![sp161025_145514](http://oaxelf1sk.bkt.clouddn.com/sp161025_145514.png)

1. onCreate方法负责初始化数据、视图、绑定事件的监听
2. onStart和onStop是从是否可见的角度分析
3. onResume和onPause是从是否在前台的角度分析
4. onStart可见不能获得焦点--->onResume可见能获得焦点---->onPause可见不能获得焦点---->onStop不可见不能获得焦点

**Android官方周期**

![android](http://oaxelf1sk.bkt.clouddn.com/687474703a2f2f696d672e626c6f672e6373646e2e6e65742f3230313630343235313731373131303534.png)

### 正常的生命周期

onCreate---->onStart------>onResume------>onPause----->onStop-----onDestory正好6个生命周期

![sp161025_150341](http://oaxelf1sk.bkt.clouddn.com/sp161025_150341.png)

### 异常生命周期

#### 用户按主页键

![sp161025_150427](http://oaxelf1sk.bkt.clouddn.com/sp161025_150427.png)

然后再次返回这个activity：

![sp161025_150503](http://oaxelf1sk.bkt.clouddn.com/sp161025_150503.png)

#### 突然来一个电话

![sp161025_150607](http://oaxelf1sk.bkt.clouddn.com/sp161025_150607.png)

生命周期和上面的一样

**当要启动的另一个Activity主题色是透明的时候那么这时这个Activity不会调用onStop方法**

也就是说它还可见，符合逻辑

#### 系统配置改变比如说屏幕旋转

![sp161025_151428](http://oaxelf1sk.bkt.clouddn.com/sp161025_151428.png)

当前的Activity被销毁重新创建

在屏幕旋转的时候会调用onSaveInstanceState和onRestoreInstanceState用来保存和恢复组件的状态（注意as默认创建的Activity继承自AppCompatActivity，这里的两个方法要使用使用AppCompatActivity中的）

当状态改变调用onSaveInstanceState保存状态然后调用onRestoreInstanceState恢复状态：

![sp161025_152339](http://oaxelf1sk.bkt.clouddn.com/sp161025_152339.png)

#### 旋转屏幕时状态分析

当屏幕发生旋转后数据会保存到savedInstanceState和onCreate方法中的savedInstanceState

```
protected void onRestoreInstanceState(Bundle savedInstanceState)
```

![sp161025_152628](http://oaxelf1sk.bkt.clouddn.com/sp161025_152628.png)

橙色线表示屏幕旋转

那么会保存view的哪些信息呢？这要看对应的view的onSaveInstanceState方法，比如EditText的正在编辑的信息就会丢失。

那么怎么屏蔽屏幕旋转的信息呢？？？

-----------------看法宝-------------------

![img](http://oaxelf1sk.bkt.clouddn.com/11FF08~1.PNG)

只要在activity注册的时候加入configChanges标签就可以，注意假如是屏蔽旋转的话应该是orientation|screenSize两个属性 

![sp161025_153758](http://oaxelf1sk.bkt.clouddn.com/sp161025_153758.png)

从此妈妈再也不用担心我的学习了，哈哈哈！！！！
####锁屏和屏幕解锁

![lock](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170228_095641.png)

只是调用了onPause和onStop没有销毁Activity


## 启动模式

就是启动Activity的方式：

![sp161025_154035](http://oaxelf1sk.bkt.clouddn.com/sp161025_154035.png)

只要在Activity中指定launchMode属性就可以完成启动模式的改变。

在上面的案例中我们从MainActivity到SecondActivity有木有发现不按下结束键只是返回也能返回到MainActivity，我擦MainActivty那家伙还活着呢？？让我们探究下正常的启动模式

### 正常的启动模式 

![sp161025_154620](http://oaxelf1sk.bkt.clouddn.com/sp161025_154620.png)

就是先启动的Activity先入栈，出栈是最后出栈，所以当SecondActivity死后，可以看见MianActivity，在MainActivity中添加一个不断重新启动自己的button

然后进入Terminal：adb shell 然后dumpsys activity

查看当前运行的activity：

![sp161025_160031](http://oaxelf1sk.bkt.clouddn.com/sp161025_160031.png)

每一个都在栈中。

新建一个Activity2项目在其中只是设置一个按钮，启动Activity项目中的MianActivity： 

![sp161025_160843](http://oaxelf1sk.bkt.clouddn.com/sp161025_160843.png)

发现Activity中的Mian在项目2的栈内，于是有一个结论：

**正常模式启动的Activity，谁启动它，它就在那一个栈**

#### 跨应用启动Activity代码

```
Intent intent = new Intent();     intent.setClassName("win.haotinayi.activity","win.haotinayi.activity.MainActivity");
```

就是设置包名，在设置Activity的全名

### SingleTop

就是当要启动的Activity恰好在栈顶的时候，那么就不再重复创建： 

![sp161025_161412](http://oaxelf1sk.bkt.clouddn.com/sp161025_161412.png)

无论怎么启动都只有一个实例。

### SingleTask

栈内单例模式，分为三种情况：

1. 当有Activity需要的栈时，并且是栈顶，啥也不说了，直接复用
2. 当有Activity需要的栈时，并且不是栈顶，那么在它上面的全部干掉，自己当大哥
3. 当没有Activity需要的栈时，创建栈

把MianActivity设置成SingleTask模式，首先打开Activity项目，让两个Activity同时存在：

![sp161025_161956](http://oaxelf1sk.bkt.clouddn.com/sp161025_161956.png)

并且MianActivity不是栈顶，然后在通过Activity2打开MainActivity： 

![sp161025_162351](http://oaxelf1sk.bkt.clouddn.com/sp161025_162351.png)

SecondActivity被干掉了，大哥是MianActivity，那么什么是Activity的需要栈呢？**默认是包名**

使用属性taskAffinity可以改掉，现在来一个恶作剧： 

![sp161025_162557](http://oaxelf1sk.bkt.clouddn.com/sp161025_162557.png)

这时再次启动项目发现，项目的栈改变了：

![sp161025_162720](http://oaxelf1sk.bkt.clouddn.com/sp161025_162720.png)

### SingleInstance

Activity只能单独存在一个栈中，换句话说就是整个手机就你一个实例

## Activity优先级

从高到低排序：

1. 显示且能获得焦点（前台）的Activity
2. 显示不能获得焦点（非前台），一个打开Dialog的Activity
3. 不可见，已经处于暂停或者直接onStop后的Activity

当内存不足的时候低优先级的Activity就会被杀掉，如果一个组件没有放在四大组件中，那么很快被杀死，所以后台任务最好放到一个四大组件中，防止被干掉。