# SnackBar详细解释

[TOC]

## 说明

SnackBar 是带有动画效果的快速提示栏，它显示在屏幕底部，是用来代替 Toast 的一个全新控件，它基本上继承了 Toast 的属性和方法，用户可以点击按钮执行对应的操作，Snackbar 支持滑动消失，如果没设任何操作，那么到时间自动消失，如下入所示：

![snipaste_20170315_112442](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170315_112442.png)

## 使用

在AS已经默认添加了依赖，直接使用；

Snackbar使用的时候需要一个控件容器用来容纳Snackbar.官方推荐使用CoordinatorLayout

```java
Snackbar.make(view, message_text, duration)
   .setAction(action_text, click_listener)
   .show();
```

显示时间duration有三种类型LENGTH_SHORT、LENGTH_LONG和LENGTH_INDEFINITE

setAction()方法可设置Snackbar右侧按钮，增加进行交互事件。如果不使用setAction()则只显示左侧message。

### 增加事件处理

```java
        Snackbar.make(view, "Replace with your own action", Snackbar.LENGTH_LONG)
                .setAction("Action", new View.OnClickListener() {
                    @Override
                    public void onClick(View v) {
                        Log.d("ty", "onClick: 您点击了");
                    }
                }).show();
```

![snipaste_20170315_112837](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170315_112837.png)

![snipaste_20170315_113015](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170315_113015.png)

### 增加回调方法

调用addCallback方法增加回调，可以在显示和消失的时候执行任务

```java
        Snackbar.make(view, "Replace with your own action", Snackbar.LENGTH_LONG)
                .setAction("Action", new View.OnClickListener() {
                    @Override
                    public void onClick(View v) {
                        Log.d("ty", "onClick: 您点击了");
                    }
                }).addCallback(new BaseTransientBottomBar.BaseCallback<Snackbar>() {
            @Override
            public void onDismissed(Snackbar transientBottomBar, int event) {
                super.onDismissed(transientBottomBar, event);
                Log.d("ty", "onDismissed: 消失了");
            }

            @Override
            public void onShown(Snackbar transientBottomBar) {
                super.onShown(transientBottomBar);
                Log.d("ty", "onDismissed: 显示");
            }
        }).show();
```

## 更改样式

![snipaste_20170315_113550](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170315_113550.png)

Snackbar和Toast的默认样式都很单一，但是有时我们希望把不同类型信息区别显示，Snackbar的官方API只提供了上面的两个方法，使用封装好的utils

```
SnackbarUtil.setSnackbarColor(snackbar,SnackbarUtil.blue,SnackbarUtil.green);
```

![snipaste_20170315_114313](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170315_114313.png)

本质就是通过snackbar拿到他的绑定view，然后再设置背景：

```java
        View view = snackbar.getView();
        if(view!=null){
            view.setBackgroundColor(backgroundColor);
            ((TextView) view.findViewById(R.id.snackbar_text)).setTextColor(messageColor);
        }
```

## 封装Utils

```java
public class SnackbarUtil {

public static final   int Info = 1;
public static final  int Confirm = 2;
public static final  int Warning = 3;
public static final  int Alert = 4;


public static  int red = 0xfff44336;
public static  int green = 0xff4caf50;
public static  int blue = 0xff2195f3;
public static  int orange = 0xffffc107;

/**
 * 短显示Snackbar，自定义颜色
 * @param view
 * @param message
 * @param messageColor
 * @param backgroundColor
 * @return
 */
public static Snackbar ShortSnackbar(View view, String message, int messageColor, int backgroundColor){
    Snackbar snackbar = Snackbar.make(view,message, Snackbar.LENGTH_SHORT);
    setSnackbarColor(snackbar,messageColor,backgroundColor);
    return snackbar;
}

/**
 * 长显示Snackbar，自定义颜色
 * @param view
 * @param message
 * @param messageColor
 * @param backgroundColor
 * @return
 */
public static Snackbar LongSnackbar(View view, String message, int messageColor, int backgroundColor){
    Snackbar snackbar = Snackbar.make(view,message, Snackbar.LENGTH_LONG);
    setSnackbarColor(snackbar,messageColor,backgroundColor);
    return snackbar;
}

/**
 * 自定义时常显示Snackbar，自定义颜色
 * @param view
 * @param message
 * @param messageColor
 * @param backgroundColor
 * @return
 */
public static Snackbar IndefiniteSnackbar(View view, String message,int duration,int messageColor, int backgroundColor){
    Snackbar snackbar = Snackbar.make(view,message, Snackbar.LENGTH_INDEFINITE).setDuration(duration);
    setSnackbarColor(snackbar,messageColor,backgroundColor);
    return snackbar;
}

/**
 * 短显示Snackbar，可选预设类型
 * @param view
 * @param message
 * @param type
 * @return
 */
public static Snackbar ShortSnackbar(View view, String message, int type){
    Snackbar snackbar = Snackbar.make(view,message, Snackbar.LENGTH_SHORT);
    switchType(snackbar,type);
    return snackbar;
}

/**
 * 长显示Snackbar，可选预设类型
 * @param view
 * @param message
 * @param type
 * @return
 */
public static Snackbar LongSnackbar(View view, String message,int type){
    Snackbar snackbar = Snackbar.make(view,message, Snackbar.LENGTH_LONG);
    switchType(snackbar,type);
    return snackbar;
}

/**
 * 自定义时常显示Snackbar，可选预设类型
 * @param view
 * @param message
 * @param type
 * @return
 */
public static Snackbar IndefiniteSnackbar(View view, String message,int duration,int type){
    Snackbar snackbar = Snackbar.make(view,message, Snackbar.LENGTH_INDEFINITE).setDuration(duration);
    switchType(snackbar,type);
    return snackbar;
}

//选择预设类型
private static void switchType(Snackbar snackbar,int type){
    switch (type){
        case Info:
            setSnackbarColor(snackbar,blue);
            break;
        case Confirm:
            setSnackbarColor(snackbar,green);
            break;
        case Warning:
            setSnackbarColor(snackbar,orange);
            break;
        case Alert:
            setSnackbarColor(snackbar,Color.YELLOW,red);
            break;
    }
}

/**
 * 设置Snackbar背景颜色
 * @param snackbar
 * @param backgroundColor
 */
public static void setSnackbarColor(Snackbar snackbar, int backgroundColor) {
    View view = snackbar.getView();
    if(view!=null){
        view.setBackgroundColor(backgroundColor);
    }
}

/**
 * 设置Snackbar文字和背景颜色
 * @param snackbar
 * @param messageColor
 * @param backgroundColor
 */
public static void setSnackbarColor(Snackbar snackbar, int messageColor, int backgroundColor) {
    View view = snackbar.getView();
    if(view!=null){
        view.setBackgroundColor(backgroundColor);
        ((TextView) view.findViewById(R.id.snackbar_text)).setTextColor(messageColor);
    }
}

/**
 * 向Snackbar中添加view
 * @param snackbar
 * @param layoutId
 * @param index 新加布局在Snackbar中的位置
 */
public static void SnackbarAddView( Snackbar snackbar,int layoutId,int index) {
    View snackbarview = snackbar.getView();
    Snackbar.SnackbarLayout snackbarLayout=(Snackbar.SnackbarLayout)snackbarview;

    View add_view = LayoutInflater.from(snackbarview.getContext()).inflate(layoutId,null);

    LinearLayout.LayoutParams p = new LinearLayout.LayoutParams( LinearLayout.LayoutParams.WRAP_CONTENT,LinearLayout.LayoutParams.WRAP_CONTENT);
    p.gravity= Gravity.CENTER_VERTICAL;

    snackbarLayout.addView(add_view,index,p);
}

}
```

## 参考

http://www.jianshu.com/p/cd1e80e64311#

http://www.jcodecraeer.com/plus/view.php?aid=3187

https://mp.weixin.qq.com/s?__biz=MzA5MzI3NjE2MA==&mid=2650238877&idx=1&sn=fe65084035413c0c730c0f34b0781287&pass_ticket=vUJln%2FPT%2FBVKqtX%2BIuMGYCc4wXQ1Smy3W35vgLIpgABOxWF9t9cGrdCJiD7efVUF
