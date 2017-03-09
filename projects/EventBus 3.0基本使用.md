# EventBus 3.0����ʹ��

[TOC]

## ���

EventBus����һ���Android�ķ���/�����¼����ߡ������������Ǻ����ɵ�ʵ����Android�������֮�䴫����Ϣ�����Ҵ���Ŀɶ��Ը��ã���϶ȸ��͡�

![EventBus-Publish-Subscribe](http://oaxelf1sk.bkt.clouddn.com/EventBus-Publish-Subscribe.png)

## ʹ��

һ���ĸ����裺

### �������

```
compile 'org.greenrobot:eventbus:3.0.0'
```

### �����Զ�����¼�

���ü̳к�ʵ���κ�����߷�����

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

### ����ע����

��������һ����Ϣ�Ĵ����ߣ�ʹ��@Subscribeע��

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

### ������Ϣ

```
EventBus.getDefault().post(new MessageEvent("doubi",12));
```

## �߳�ģʽ

��EventBus�еĹ۲���ͨ���������߳�ģ�ͣ��ֱ���PostThread��Ĭ�ϣ���MainThread��BackgroundThread��Async��

- PostThread�����ʹ���¼�������ָ�����߳�ģ��ΪPostThread����ô���¼����ĸ��̷߳��������ģ��¼��������ͻ�������߳������У�Ҳ����˵�����¼��ͽ����¼���ͬһ���̡߳����߳�ģ��ΪPostThread���¼��������о�������ִ�к�ʱ��������Ϊ���������¼��Ĵ��ݣ������п��ܻ�����ANR��
- MainThread�����ʹ���¼�������ָ�����߳�ģ��ΪMainThread����ô�����¼������ĸ��߳��з��������ģ����¼�������������UI�߳���ִ�С��÷���������������UI�����ǲ��ܴ����ʱ������
- BackgroundThread�����ʹ���¼�������ָ�����߳�ģ��ΪBackgroundThread����ô����¼�����UI�߳��з��������ģ���ô���¼��������ͻ����µ��߳������У�����¼������������߳��з��������ģ���ô���¼�������ֱ���ڷ����¼����߳���ִ�С��ڴ��¼��������н�ֹ����UI���²�����
- Async�����ʹ���¼�������ָ�����߳�ģ��ΪAsync����ô�����¼����ĸ��̷߳��������¼��������������½������߳���ִ�С�ͬ�������¼��������н�ֹ����UI���²�����

## ճ���¼�

�򵥽��������ڷ����¼�֮���ٶ��ĸ��¼�Ҳ���յ����¼����������ͨ���¼�ֻ�Ƿ��ͺʹ�������ͬ��

```
EventBus.getDefault().postSticky(new MessageEvent("test"));
```

��������

```
@Subscribe(sticky = true)
public void XXX(MessageEvent messageEvent) {
    ......
}
```

## �ο�

http://www.liuling123.com/2016/01/EventBus-explain.html

https://segmentfault.com/a/1190000004279679

http://greenrobot.org/eventbus/documentation/how-to-get-started/
