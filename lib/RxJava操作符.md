# RxJava操作符

[TOC]

## 创建操作
### create

使用OnSubscribe从头创建一个Observable，这种方法比较简单。需要注意的是，使用该方法创建时，建议在OnSubscribe#call方法中检查订阅状态，以便及时停止发射数据或者运算。

```java
       Observable.create(new Observable.OnSubscribe<String>(){

            @Override
            public void call(Subscriber<? super String> subscriber) {
                subscriber.onNext("doubi");
            }
        }).subscribe(new Observer<String>() {
            @Override
            public void onCompleted() {

            }

            @Override
            public void onError(Throwable e) {

            }

            @Override
            public void onNext(String s) {
                Log.e(TAG, "onNext: "+s );
            }
        });
```

### from

将一个Iterable, 一个Future, 或者一个数组，内部通过代理的方式转换成一个Observable；遍历ArrayList：

```java
        ArrayList<String> list = new ArrayList<>();
        list.add("doubi");
        list.add("hello");

        Observable.from(list).subscribe(new Action1<String>() {
            @Override
            public void call(String s) {
                Log.e(TAG, "call: "+s );
            }
        });
//或者另外一种方式
       Observable.from(list).subscribe(new Observer<String>() {
            @Override
            public void onCompleted() {

            }

            @Override
            public void onError(Throwable e) {

            }

            @Override
            public void onNext(String s) {
                Log.e(TAG, "onNext: "+s );
            }
        });
```

### just

将一个或多个对象转换成发射这个或这些对象的一个Observable，比如在线程中睡眠5秒：

```java
        Observable.just(null).
                subscribe(new Action1<Object>() {
            @Override
            public void call(Object o) {
                try {
                    Thread.sleep(5000);
                    Log.e(TAG, "call: ----------" );
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
```

### timer

创建一个在给定的延时之后发射数据项为0的`Observable`，延迟5秒发送信息：

```java
        Observable.just(null).timer(5, TimeUnit.SECONDS).subscribe(new Action1<Long>() {
            @Override
            public void call(Long aLong) {
                Log.e(TAG, "call: "+aLong );//结果是0
            }
        });
```
### 其他

Create
通过调用观察者的方法从头创建一个Observable

Empty/Never/Throw
创建行为受限的特殊Observable

Defer
在观察者订阅之前不创建这个Observable，为每一个观察者创建一个新的Observable

Just
将对象或者对象集合转换为一个会发射这些对象的Observable

From
将其它的对象或数据结构转换为Observable

Range
创建发射指定范围的整数序列的Observable,range操作符,发射从start开始的count个数

Interval
间隔一定时间发送一个数字,从0开始.本身运行在Schedulers.computation() 线程内

Repeat
Repeat作用在Observable上,会对其重复发射count次

## 变换操作

### map

用来把一个事件转换为另一个事件。
map()操作符就是用于变换Observable对象的，map操作符返回一个Observable对象，这样就可以实现链式调用，在一个Observable对象上多次使用map操作符，最终将最简洁的数据传递给Subscriber对象。

```java
        Observable.just("hello world").map(new Func1<String, Integer>() {
            @Override
            public Integer call(String s) {
                return 2017;
            }
        }).map(new Func1<Integer, String>() {
            @Override
            public String call(Integer integer) {
                return integer+"----";
            }
        }).subscribe(new Action1<String>() {
            @Override
            public void call(String s) {
                System.out.println(s);
            }
        });
```

**func第一个泛型是输入的类型，第二个是输出的类型**

action中的输入是事件源的输入，而不是监听者的信息，just是构建一个监听者，不参与事件的转换

```java
       Observable.just("hello world").subscribe(new Observer<String>() {
            @Override
            public void onCompleted() {

            }

            @Override
            public void onError(Throwable e) {

            }

            @Override
            public void onNext(String s) {
                Log.e(TAG, "onNext:"+s );
            }
        });
```

上面的just就参与了事件流的转化，运行结果：
![snipaste_20170315_072633](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170315_072633.png)

### flatmap

Observable.flatMap()接收一个Observable的输出作为输入，同时输出另外一个Observable。

```java
    private Observable<List<String>> getStudent() {
        List<String> list = Arrays.asList("123", "456", "doubi");
        return Observable.just(list);
    }
    
    //调用函数
         getStudent().flatMap(new Func1<List<String>, Observable<String>>() {
            @Override
            public Observable<String> call(List<String> strings) {
                return Observable.from(strings);
            }
        }).subscribe(new Action1<String>() {
            @Override
            public void call(String s) {
                Log.e(TAG, "call: " + s);
            }
        });
```

### 其他

Buffer
缓存，可以简单的理解为缓存，它定期从Observable收集数据到一个集合，然后把这些数据集合打包发射，而不是一次发射一个

FlatMap
扁平映射，将Observable发射的数据变换为Observables集合，然后将这些Observable发射的数据平坦化的放进一个单独的Observable，可以认为是一个将嵌套的数据结构展开的过程。

GroupBy
分组，将原来的Observable分拆为Observable集合，将原始Observable发射的数据按Key分组，每一个Observable发射一组不同的数据

Map
映射，通过对序列的每一项都应用一个函数变换Observable发射的数据，实质是对序列中的每一项执行一个函数，函数的参数就是这个数据项

Scan
扫描， 连续地对数据序列的每一项应用一个函数，然后连续发射结果,每一项结果基于之前的结果.累加器函数.

Window
窗口，定期将来自Observable的数据分拆成一些Observable窗口，然后发射这些窗口，而不是每次发射一项。类似于Buffer，但Buffer发射的是数据，Window发射的是Observable，每一个Observable发射原始Observable的数据的一个子集

toList
Returns an Observable that emits a single item, a list composed of all the items emitted by the source Observable. 将一个Observable转换为一个List.

## 过滤操作

### filter

更改刚刚的flatmap的代码，只要含有`doubi`的字符串

```java
        getStudent().flatMap(new Func1<List<String>, Observable<String>>() {
            @Override
            public Observable<String> call(List<String> strings) {
                return Observable.from(strings);
            }
        }).filter(new Func1<String, Boolean>() {
            @Override
            public Boolean call(String s) {
                if (s.contains("12")){
                    return true;
                }
                return false;
            }
        }).subscribe(new Action1<String>() {
            @Override
            public void call(String s) {
                Log.e(TAG, "call: " + s);
            }
        });
```

## 其他

throttleWithTimeout
源Observable每次发射出来一个数据后就会进行计时,如果在设定好的时间结束前源Observable有新的数据发射出来，这个数据就会被丢弃，同时重新开始计时。

Debounce
只有在空闲了一段时间后才发射数据，通俗的说，就是如果一段时间没有操作，就执行一次操作.还可以根据一个函数来进行限流。这个函数的返回值是一个临时Observable，
如果源Observable在发射一个新的数据的时候，上一个数据根据函数所生成的临时Observable还没有结束,没有调用onComplete，那么上一个数据就会被过滤掉。如果是最后一个,还是会发射.

Distinct
去重，过滤掉所有的重复数据项

DistinctUtilChanged
过滤掉相邻的重复项

ElementAt
取值，取特定位置的数据项

Filter
过滤，过滤掉没有通过谓词测试的数据项，只发射通过测试的

First
取满足条件的第一个,如无满足条件数据抛出异常.可以使用takeFisrt(),仅会调动onComplete.
有null数据时会抛空指针异常,要判空处理.
只取满足条件的第一个数据.可以和BlockingObservable连用。可以Observable.toBlocking或者BlockingObservable.from方法来将一个Observable对象转化为BlockingObservable对象

Last
末项，只发射最后一条数据.

IgnoreElements
忽略所有的数据，只保留终止通知(onError或onCompleted)

Sample
取样，定期发射最新的数据，等于是数据抽样，有的实现里叫ThrottleFirst

throttleFirst
会定期发射这个时间段里源Observable发射的第一个数据

Skip
跳过前面的若干项数据

SkipLast
跳过后面的若干项数据

Take
只保留前面的若干项数据

TakeLast
只保留后面的若干项数据

takeFirst
有null数据时会抛空指针异常,要判空处理
和first一样，但在所有数据不满足条件时不会抛出异常，仅仅调用onComplete.

## 参考

http://m.2cto.com/kf/201606/516356.html

http://www.jianshu.com/p/88779bda6691

http://blog.csdn.net/maplejaw_/article/details/52396175

http://blog.csdn.net/job_hesc/article/details/46242117
