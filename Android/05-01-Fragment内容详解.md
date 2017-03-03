# 05--01-Fragment内容详解

[TOC]

## 概述

​	**Fragment必须总是被嵌入到一个activity之中**，并且fragment的生命周期直接受其宿主activity的生命周期的影响。

​	应该将每一个fragment设计为模块化的和可复用化的activity组件

​	两个重要的支持库类，一个是 Fragment 类（ android.support.v4.app.
Fragment ），另一个是 FragmentActivity （ android.support.v4.app.Fragment- Activity ）对应的是`getSupportFragmentManager()`

## 基本使用

两种使用模式，一个是直接当成普通的view来使用，另外一种是用事务的模式

### 静态使用

首先定义好fragment要显示的内容fragment_my.xml：

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              xmlns:tools="http://schemas.android.com/tools"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              tools:context="com.example.simaxiaochen.fragment.MyFragment">

    <!-- TODO: Update blank fragment layout -->
    <Button
        android:id="@+id/btn_my_fragment"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="@string/hello_blank_fragment"
        />

</LinearLayout>
```

然后再fragment中使用这个布局：

```
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        return inflater.inflate(R.layout.fragment_my,null);
    }
```

最后在Activity中的布局中引用这个fragment：

```
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context="com.example.simaxiaochen.fragment.MainActivity">

    <fragment
        android:name="com.example.simaxiaochen.fragment.MyFragment"
        android:layout_width="match_parent"
        android:layout_height="30dp"
        />
</LinearLayout>
```

注意name的属性是你要使用fragment的文件名

### 动态使用

在原Activty的布局中增加一个控制按钮和一个真布局：

```
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context="com.example.simaxiaochen.fragment.MainActivity">

    <fragment
        android:name="com.example.simaxiaochen.fragment.MyFragment"
        android:layout_width="match_parent"
        android:layout_height="60dp"
        />
    <Button
        android:text="显示下一个fragment"
        android:layout_width="match_parent"
        android:layout_height="60dp"
        android:onClick="showFragment"/>
    <FrameLayout
        android:id="@+id/fl"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
</LinearLayout>
```

类似新建一个SecondFragment并且建立布局fragment_second.xml：

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              xmlns:tools="http://schemas.android.com/tools"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              android:background="@color/colorPrimary"
              tools:context="com.example.simaxiaochen.fragment.MyFragment">

    <!-- TODO: Update blank fragment layout -->
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="second fragment"/>

</LinearLayout>
```

在Actiivty中增加控制SecondFragemnt显示的代码，就是开启事务然后再提交

```
    public void showFragment(View view) {
        FragmentManager manager = getFragmentManager();
        FragmentTransaction transaction = manager.beginTransaction();
        transaction.replace(R.id.fl,new SecondFragment());
        transaction.commit();
    }
```

## 相关的类和方法

### 主要的类

android.app.Fragment 主要用于定义Fragment

android.app.FragmentManager 主要用于在Activity中操作Fragment

android.app.FragmentTransaction 保证一些列Fragment操作的原子性

### 主要的方法

FragmentTransaction transaction = fm.benginTransatcion();	//开启一个事务

**transaction.add() **

往Activity中添加一个Fragment

**transaction.remove() **

从Activity中移除一个Fragment，如果被移除的Fragment没有添加到回退栈（回退栈后面会详细说），这个Fragment实例将会被销毁。

**transaction.replace()**

使用另一个Fragment替换当前的，实际上就是remove()然后add()的合体~

**transaction.hide()**

隐藏当前的Fragment，仅仅是设为不可见，并不会销毁

**transaction.show()**

显示之前隐藏的Fragment

**transaction.detach()**

会将view从UI中移除,和remove()不同,此时fragment的状态依然由FragmentManager维护。

**transaction.attach()**

重建view视图，附加到UI上并显示。

transatcion.commit()//提交一个事务

> 发生State loss这样的错误。主要是因为：commit方法一定要在Activity.onSaveInstance()之前调用。

hide和show方法会保留用户正在使用的数据；remove不会保留数据直接销毁。

replace相当于是remove和add的合体

## FragmentManager

![](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170303_105121.png)

　　在调用commit()之前，可以将事务添加到fragment事务后台栈中（通过调用addToBackStatck()）。这个后台栈由activity管理，并且允许用户通过按BACK键回退到前一个fragment状态。

​	Activity中有个FragmentManager，其内部维护fragment队列，以及fragment事务的回退栈。

### 常见使用方式

```
FragmentManager fm = getSupportFragmentManager();
Fragment fragment = fm.findFragmentById(R.id.fragment_container);
if (fragment == null) {
	fragment = new CrimeFragment();
	fm.beginTransaction()
		.add(R.id.fragment_container, fragment)
		.commit();
}
```

> 这里使用FrameLayout组件的ID去得到fragment，注意**判断是否为空**

### 设置回退栈

首先更改Activity布局文件activity_main.xml：

```
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context="com.example.simaxiaochen.fragment.MainActivity">

    <FrameLayout
        android:id="@+id/fl"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>

</LinearLayout>
```

在MainActivity中初始化第一个fragment：

```
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        Log.w("ty", "-------onCreate------");
        setContentView(R.layout.activity_main);
        mFragmentOne = new MyFragment();
        FragmentManager fragmentManager = getFragmentManager();
        mTransaction = fragmentManager.beginTransaction();
        mTransaction.add(R.id.fl,mFragmentOne,"replace");
        mTransaction.commit();
    }
```

fragment对应的布局fragment_my.xml:

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              xmlns:tools="http://schemas.android.com/tools"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              android:orientation="horizontal"
              tools:context="com.example.simaxiaochen.fragment.MyFragment">

    <!-- TODO: Update blank fragment layout -->
    <EditText
        android:layout_width="0dp"
        android:layout_weight="1"
        android:layout_height="80dp"
        />
    <Button
        android:id="@+id/btn_my_fragment"
        android:layout_width="0dp"
        android:layout_weight="1"
        android:layout_height="80dp"
        />

</LinearLayout>
```

修改fragment中的代码，并且假如回退：

```
    @Override
    public void onClick(View v) {
        if (v.getId() == R.id.btn_my_fragment) {
            Log.e("ty", "----Fragment---下一个fragment----");
            SecondFragment secondFragment = new SecondFragment();
            FragmentManager fragmentManager = getFragmentManager();
            FragmentTransaction transaction = fragmentManager.beginTransaction();
            transaction.replace(R.id.fl, secondFragment,"replace");
            transaction.addToBackStack(null);
            transaction.commit();
        }
    }
```

当进入第二个fragment中，在按下返回键显示第一个fragment，但是数据丢失

### 使用hide方法

改进onclick方法：

```
    @Override
    public void onClick(View v) {
        if (v.getId() == R.id.btn_my_fragment) {
            Log.e("ty", "----Fragment---下一个fragment----");
            SecondFragment secondFragment = new SecondFragment();
            FragmentManager fragmentManager = getFragmentManager();
            FragmentTransaction transaction = fragmentManager.beginTransaction();
//            transaction.replace(R.id.fl, secondFragment,"replace");
//           改进之后的代码
            transaction.hide(this);
            transaction.add(R.id.fl,secondFragment);
            transaction.addToBackStack(null);
            transaction.commit();
        }
    }
```

当再次按下返回键之后发现正在编辑的数据没有丢失，生命周期一直在onResume状态，但是replace则调用onPause等生命周期

### 总结

​	当要进行fragment之间的切换时，可以使用transaction.replace也可以使用transaction.hide方法，区别就是hide会保存正在编辑的数据。

## 生命周期

![](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170303_104512.png)

　　如果activity的进程被杀掉了，在activity被重新创建时，你需要恢复fragment状态。可以执行fragment的onSaveInstanceState()来保存状态（注意在fragment是在onCreate()，onCreateView()，或onActvityCreate()中进行恢复）。

　　在生命周期方面,activity与fragment之间一个**很重要的不同**，就是在各自的后台栈中是如何存储的,当activity停止时，**默认**情况下activity被安置在由系统管理的activity后台栈中；　 　　fragment仅当在一个事务被移除时，通过显式调用addToBackStack()请求保存的实例，该fragment才被置于由宿主activity管理的后台栈。　

官方生命周期示意图：

![](http://oaxelf1sk.bkt.clouddn.com/687474703a2f2f696d672e626c6f672e6373646e2e6e65742f3230313630343239313334352017321441.jpg)

### 生命周期方法

![](http://oaxelf1sk.bkt.clouddn.com/1354170682_38242014321825.png)

- onAttach()

　　当fragment被绑定到activity时调用（Activity会被传入）。

- onCreateView()

　　将本身的布局构建到activity中去（fragment作为activity界面的一部分） 　　

- onActivityCreated()

　　当activity的onCreate()函数返回时被调用。

- onDestroyView()

　　当与fragment关联的视图体系正被移除时被调用。

- onDetach()

　　当fragment正与activity解除关联时被调用。

### 完整的生命周期

运行上面的例子，一个Activity中包含一个fragment，正常启动的生命周期：

```
13421-13421 W/ty: -------onCreate------
13421-13421 W/ty: ----Fragment---onCreate----
13421-13421 W/ty: ----Fragment---onCreateView----
13421-13421 W/ty: ----Fragment---onActivityCreated----
13421-13421 W/ty: -------onStart------
13421-13421 W/ty: ----Fragment---onStart----
13421-13421 W/ty: -------onResume------
13421-13421 W/ty: ----Fragment---onResume----
```

​	只有activity是onResume状态的时候，fragemnt才可以自己决定自己的生命周期，否则就是完全依赖activity的生命周期。

### 屏幕锁定的生命周期

```
13421-13421 W/ty: ----Fragment---onPause----
13421-13421 W/ty: -------onPause------
13421-13421 W/ty: ----Fragment---onStop----
13421-13421 W/ty: -------onStop------
```

### 解锁屏幕

```
13421-13421 W/ty: -------onStart------
13421-13421 W/ty: ----Fragment---onStart----
13421-13421 W/ty: -------onResume------
13421-13421 W/ty: ----Fragment---onResume----
```

### 切换到主页

```
13421-13421 W/ty: ----Fragment---onPause----
13421-13421 W/ty: -------onPause------
13421-13421 W/ty: ----Fragment---onStop----
13421-13421 W/ty: -------onStop------
```

### 回到前台

```
13421-13421 W/ty: -------onStart------
13421-13421 W/ty: ----Fragment---onStart----
13421-13421 W/ty: -------onResume------
13421-13421 W/ty: ----Fragment---onResume----
```

### 使用replace切换fragment

```
19864-19864 W/ty: ----Fragment---onPause----
19864-19864 W/ty: ----Fragment---onStop----
19864-19864 W/ty: ----Fragment---onDestroyView----
19864-19864 W/ty: ----SecondFragment---onCreate----
19864-19864 W/ty: ----SecondFragment---onCreateView----
19864-19864 W/ty: ----SecondFragment---onActivityCreated----
19864-19864 W/ty: ----SecondFragment---onStart----
19864-19864 W/ty: ----SecondFragment---onResume----
```

### 退出应用

```
19864-19864 W/ty: ----Fragment---onPause----
19864-19864 W/ty: -------onPause------
19864-19864 W/ty: ----Fragment---onStop----
19864-19864 W/ty: -------onStop------
19864-19864 W/ty: ----Fragment---onDestroyView----
19864-19864 W/ty: ----Fragment---onDestroy----
19864-19864 W/ty: ----Fragment---onDetach----
19864-19864 W/ty: -------onDestroy------
```

## 参考文献

http://blog.csdn.net/lmj623565791/article/details/37992017

http://blog.csdn.net/forever_crying/article/details/8238863/

https://github.com/HaoTianYi/ForAndroidInterview/blob/master/android/Fragment%20%E5%85%A8%E8%A7%A3%E6%9E%90.md

http://blog.csdn.net/lmj623565791/article/details/42628537

感谢以上作者的辛勤劳动

