# EventBus 3.0基本使用

[TOC]

## 简介

EventBus是针一款对Android的发布/订阅事件总线。它可以让我们很轻松的实现在Android各个组件之间传递消息，并且代码的可读性更好，耦合度更低。

![EventBus-Publish-Subscribe](C:\Users\SiMaXiaoChen\Desktop\EventBus-Publish-Subscribe.png)

## 使用

一共四个步骤：

### 添加依赖

```
compile 'org.greenrobot:eventbus:3.0.0'
```

### 增加自定义的事件

不用继承和实现任何类或者方法：

```
public class MessageEvent {
    public String name;
    public int price;

    public MessageEvent(String name, int price) {
        this.name = name;
        this.price = price;
    }
}
```

### 增加注册者

并且增加一个消息的处理者，使用@Subscribe注解

```
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        TAG = getLocalClassName();
        EventBus.getDefault().register(MainActivity.this);
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        EventBus.getDefault().unregister(MainActivity.this);
    }
    @Subscribe(threadMode = ThreadMode.MAIN)
    public void postEvent(MessageEvent messageEvent){
        Log.e(TAG, "postEvent: post"+messageEvent.name);
    }
```

### 发布信息

```
EventBus.getDefault().post(new MessageEvent("doubi",12));
```

## 线程模式

在EventBus中的观察者通常有四种线程模型，分别是PostThread（默认）、MainThread、BackgroundThread与Async。

- PostThread：如果使用事件处理函数指定了线程模型为PostThread，那么该事件在哪个线程发布出来的，事件处理函数就会在这个线程中运行，也就是说发布事件和接收事件在同一个线程。在线程模型为PostThread的事件处理函数中尽量避免执行耗时操作，因为它会阻塞事件的传递，甚至有可能会引起ANR。
- MainThread：如果使用事件处理函数指定了线程模型为MainThread，那么不论事件是在哪个线程中发布出来的，该事件处理函数都会在UI线程中执行。该方法可以用来更新UI，但是不能处理耗时操作。
- BackgroundThread：如果使用事件处理函数指定了线程模型为BackgroundThread，那么如果事件是在UI线程中发布出来的，那么该事件处理函数就会在新的线程中运行，如果事件本来就是子线程中发布出来的，那么该事件处理函数直接在发布事件的线程中执行。在此事件处理函数中禁止进行UI更新操作。
- Async：如果使用事件处理函数指定了线程模型为Async，那么无论事件在哪个线程发布，该事件处理函数都会在新建的子线程中执行。同样，此事件处理函数中禁止进行UI更新操作。

## 粘性事件

简单讲，就是在发送事件之后再订阅该事件也能收到该事件，相比于普通的事件只是发送和处理函数不同：

```
EventBus.getDefault().postSticky(new MessageEvent("test"));
```

处理函数：

```
@Subscribe(sticky = true)
public void XXX(MessageEvent messageEvent) {
    ......
}
```

## 参考

http://www.liuling123.com/2016/01/EventBus-explain.html

https://segmentfault.com/a/1190000004279679

http://greenrobot.org/eventbus/documentation/how-to-get-started/