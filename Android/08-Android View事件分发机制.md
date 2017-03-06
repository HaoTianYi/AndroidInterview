# 08-Android View事件分发机制

[TOC]

## 相关方法

![](http://oaxelf1sk.bkt.clouddn.com/687474703a2f2f696d672e626c6f672e6373646e2e6e65742f3230313630343238313631313034333339.png)

> **事件分发：public boolean dispatchTouchEvent(MotionEvent ev)**

　　　当有监听到事件时，首先由Activity进行捕获，进入事件分发处理流程。（因为activity没有事件拦截，View和ViewGroup有）会将事件传递给最外层View的dispatchTouchEvent(MotionEvent ev)方法，该方法对事件进行分发。 　　

- return true ：表示该View内部消化掉了所有事件。
- return false ：事件在本层不再继续进行分发，并交由**上层**控件的onTouchEvent方法进行消费（如果本层控件已经是Activity，那么事件将被系统消费或处理）。　
- 如果事件分发返回系统默认的 super.dispatchTouchEvent(ev)，事件将分发给本层的事件拦截onInterceptTouchEvent 方法进行处理

> **事件拦截：public boolean onInterceptTouchEvent(MotionEvent ev)**

- return true ：表示将事件进行拦截，并将拦截到的事件交由本层控件 的 onTouchEvent 进行处理；
- return false ：则表示不对事件进行拦截，事件得以成功分发到子View。并由子View的dispatchTouchEvent进行处理。　
- 如果返回super.onInterceptTouchEvent(ev)，默认表示拦截该事件，并将事件传递给当前View的onTouchEvent方法，和return true一样。

> **事件响应：public boolean onTouchEvent(MotionEvent ev)**

　　在dispatchTouchEvent（事件分发）返回super.dispatchTouchEvent(ev)并且onInterceptTouchEvent（事件拦截返回true或super.onInterceptTouchEvent(ev)的情况下，那么事件会传递到onTouchEvent方法，该方法对事件进行响应。　

- 如果return true，表示onTouchEvent处理完事件后消费了此次事件。此时事件终结；
- 如果return fasle，则表示不响应事件，那么该事件将会不断向上层View的onTouchEvent方法传递，直到某个View的onTouchEvent方法返回true，如果到了最顶层View还是返回false，那么认为该事件不消耗，则在同一个事件系列中，当前View无法再次接收到事件，该事件会交由Activity的onTouchEvent进行处理；　　
- 如果return super.dispatchTouchEvent(ev)，则表示不响应事件，结果与return false一样。

> 从以上过程中可以看出，dispatchTouchEvent无论返回true还是false，事件都不再进行分发，只有当其返回super.dispatchTouchEvent(ev)，才表明其具有向下层分发的愿望，但是是否能够分发成功，则需要经过事件拦截onInterceptTouchEvent的审核。事件是否向上传递处理是由onTouchEvent的返回值决定的。

## 分发机制

| 类型   | 相关方法                  | Activity | ViewGroup | View |
| ---- | --------------------- | -------- | --------- | ---- |
| 事件分发 | dispatchTouchEvent    | √        | √         | √    |
| 事件拦截 | onInterceptTouchEvent | X        | √         | X    |
| 事件消费 | onTouchEvent          | √        | √         | √    |

Activity 和 View 都是没有事件拦截的，这是因为：

> Activity 作为原始的事件分发者，如果 Activity 拦截了事件会导致整个屏幕都无法响应事件，这肯定不是我们想要的效果。
>
> View最为事件传递的最末端，要么消费掉事件，要么不处理进行回传，根本没必要进行事件拦截。

事件收集之后最先传递给 Activity， 然后依次向下传递，大致如下：

```
Activity －> PhoneWindow －> DecorView －> ViewGroup －> ... －> View
```

没有任何View消费掉事件，那么这个事件会按照反方向回传，最终传回给Activity，如果最后 Activity 也没有处理，本次事件才会被抛弃:

```
Activity <－ PhoneWindow <－ DecorView <－ ViewGroup <－ ... <－ View
```

**这是一个非常经典的责任链模式**

![](http://oaxelf1sk.bkt.clouddn.com/005Xtdi2jw1f88i0q8uozj30nm0kqwhm.jpg)

1. 事件返回时 `dispatchTouchEvent` 直接指向了父View的 `onTouchEvent` 这一部分是不合理的，实际上它仅仅是给了父View的 `dispatchTouchEvent` 一个 false 返回值，父View根据返回值来调用自身的 `onTouchEvent`。
2. ViewGroup 是根据 `onInterceptTouchEvent` 的返回值来确定是调用子View的 `dispatchTouchEvent` 还是自身的 `onTouchEvent`， 并没有将调用交给 `onInterceptTouchEvent`。

## 具体流程

当view1自己处理事件时，onTouchEvent返回true，事件不在向上传递：

![](http://oaxelf1sk.bkt.clouddn.com/005Xtdi2jw1f88ll27wv9j30nm0kqtbo2017361648.jpg)

当viewGroup自己拦截事件，并且自己处理事件时，onInterceptTouchEvent返回true，并且onTouchEvent返回true：
![](http://oaxelf1sk.bkt.clouddn.com/005Xtdi2jw1f88p3r45vfj30nm0kqn00201736161815.jpg)

## 参考

http://www.gcssloop.com/customview/dispatch-touchevent-theory

https://github.com/HaoTianYi/ForAndroidInterview/blob/master/android/Android%20View%E4%BA%8B%E4%BB%B6%E5%88%86%E5%8F%91%E6%9C%BA%E5%88%B6%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90.md