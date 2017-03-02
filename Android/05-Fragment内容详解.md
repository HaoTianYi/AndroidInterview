# 05-Fragment内容详解

[TOC]

## 概述

​	**Fragment必须总是被嵌入到一个activity之中**，并且fragment的生命周期直接受其宿主activity的生命周期的影响。

​	应该将每一个fragment设计为模块化的和可复用化的activity组件

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

## Fragment队列和事务的回退栈

　　在调用commit()之前，可以将事务添加到fragment事务后台栈中（通过调用addToBackStatck()）。这个后台栈由activity管理，并且允许用户通过按BACK键回退到前一个fragment状态。

​	Activity中有个FragmentManager，其内部维护fragment队列，以及fragment事务的回退栈。

首先更改Activity布局文件activity_main.xml：

```
<?xml version="1.0" encoding="utf-8"?>
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



## 生命周期

官方生命周期示意图：

![](http://oaxelf1sk.bkt.clouddn.com/687474703a2f2f696d672e626c6f672e6373646e2e6e65742f3230313630343239313334352017321441.jpg)



## 和Activity交互

- 调用fragment的函数findFragmentById()或者findFragmentByTag()

```
ExampleFragment fragment = (ExampleFragment) getFragmentManager().findFragmentById(R.id.example_fragment);
```

- 在Fragment中可以通过getActivity得到当前绑定的Activity的实例。
- 创建activity事件回调函数，在fragment内部定义一个回调接口，宿主activity来实现它。

## 参考文献

http://blog.csdn.net/lmj623565791/article/details/37992017

http://blog.csdn.net/forever_crying/article/details/8238863/

https://github.com/HaoTianYi/ForAndroidInterview/blob/master/android/Fragment%20%E5%85%A8%E8%A7%A3%E6%9E%90.md

23820-23820 W/ty: -------onCreate------
23820-23820 W/ty: ----Fragment---onAttach----
23820-23820 W/ty: ----Fragment---onCreate----
23820-23820 W/ty: ----Fragment---onCreateView----
23820-23820 W/ty: -------onStart------
23820-23820 W/ty: ----Fragment---onActivityCreated----
23820-23820 W/ty: ----Fragment---onStart----
23820-23820 W/ty: -------onResume------
23820-23820 W/ty: ----Fragment---onResume----
23820-23820 W/ty: -------onPause------
23820-23820 W/ty: ----Fragment---onPause----
23820-23820 W/ty: -------onStop------
23820-23820 W/ty: ----Fragment---onStop----
23820-23820 W/ty: -------onDestroy------
23820-23820 W/ty: ----Fragment---onDestroyView----
23820-23820 W/ty: ----Fragment---onDestroy----
23820-23820 W/ty: ----Fragment---onDetach----