# Android IPC机制-AIDL

[TOC]

## 简介

AIDL是一个缩写，全称是[Android](http://lib.csdn.net/base/android)?Interface Definition Language，也就是Android接口定义语言，是为了**实现进程间通信，尤其是在涉及多进程并发情况下的进程间通信**。

### 数据类型

AIDL默认支持一些数据类型，在使用这些数据类型的时候是不需要导包的，但是除了这些类型之外的数据类型，在使用之前必须导包，**就算目标文件与当前正在编写的 .aidl 文件在同一个包下**

默认支持的数据类型包括：?
Java中的八种基本数据类型，包括 byte，short，int，long，float，double，boolean，char。

String 类型。

CharSequence类型。

List类型：List中的所有元素必须是AIDL支持的类型之一，或者是一个其他AIDL生成的接口，或者是定义的parcelable（下文关于这个会有详解）。List可以使用泛型。

Map类型：Map中的所有元素必须是AIDL支持的类型之一，或者是一个其他AIDL生成的接口，或者是定义的parcelable。Map是不支持泛型的。

**AIDL中的定向 tag 表示了在跨进程通信中数据的流向，其中 in 表示数据只能由客户端流向服务端， out 表示数据只能由服务端流向客户端，而 inout 则表示数据可在服务端与客户端之间双向流通。**定向tag在aidl文件的方法标识上

### AIDL基本语法

```
package com.lypeer.ipcclient;
//导入所需要使用的非默认支持数据类型的包
import com.lypeer.ipcclient.Book;

interface BookManager {

    //所有的返回值前都不需要加任何东西，不管是什么数据类型
    List<Book> getBooks();
    Book getBook();
    int getBookCount();

    //传参时除了Java基本类型以及String，CharSequence之外的类型
    //都需要在前面加上定向tag，具体加什么量需而定
    void setBookPrice(in Book book , int price)
    void setBookName(in Book book , String name)
    void addBookIn(in Book book);
    void addBookOut(out Book book);
    void addBookInout(inout Book book);
}
```

注意as默认完成的Parcelable要增加一个方法：

```
public void readFromParcel(Parcel dest) {
    //注意，此处的读值顺序应当是和writeToParcel()方法中一致的
    name = dest.readString();
    price = dest.readInt();
}
```

### 移动文件

当aidl完成之后可以直接移动文件：

![snipaste_20170308_184115](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170308_184115.png)

其中同名的java文件直接移动问价和对应的文件夹：

![snipaste_20170308_184226](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170308_184226.png)

写完aidl直接rebuild项目即可生成对应的java文件

## 实际案例

### 服务器

book.aidl

```
package com.example.simaxiaochen.myapplication;

// Declare any non-default types here with import statements
parcelable Book;
```

BookManager.aidl

```
// BookManager.aidl
package com.example.simaxiaochen.myapplication;

// Declare any non-default types here with import statements
import com.example.simaxiaochen.myapplication.Book;
interface BookManager {

    //所有的返回值前都不需要加任何东西，不管是什么数据类型
    List<Book> getBooks();

    //传参时除了Java基本类型以及String，CharSequence之外的类型
    //都需要在前面加上定向tag，具体加什么量需而定
    void addBook(in Book book);
}
```

Service，主要就是具体写完aidl中定义的方法：

```
public class AIDLService extends Service {
    public final String TAG = this.getClass().getSimpleName();

    //包含Book对象的list
    private List<Book> mBooks = new ArrayList<>();

    //由AIDL文件生成的BookManager
    private final BookManager.Stub mBookManager = new BookManager.Stub() {
        @Override
        public List<Book> getBooks() throws RemoteException {
            synchronized (this) {
                Log.e(TAG, "invoking getBooks() method , now the list is : " + mBooks.toString());
                if (mBooks != null) {
                    return mBooks;
                }
                return new ArrayList<>();
            }
        }


        @Override
        public void addBook(Book book) throws RemoteException {
            synchronized (this) {
                if (mBooks == null) {
                    mBooks = new ArrayList<>();
                }
                if (book == null) {
                    Log.e(TAG, "Book is null in In");
                    book = new Book();
                }
                //尝试修改book的参数，主要是为了观察其到客户端的反馈
                book.setPrice(2333);
                if (!mBooks.contains(book)) {
                    mBooks.add(book);
                }
                //打印mBooks列表，观察客户端传过来的值
                Log.e(TAG, "invoking addBooks() method , now the list is : " + mBooks.toString());
            }
        }
    };

    @Override
    public void onCreate() {
        super.onCreate();
        Book book = new Book();
        book.setName("Android开发艺术探索");
        book.setPrice(28);
        mBooks.add(book);
    }

    @Nullable
    @Override
    public IBinder onBind(Intent intent) {
        Log.e(getClass().getSimpleName(), String.format("on bind,intent = %s", intent.toString()));
        return mBookManager;
    }
}
```

### 客户端

```
public class MainActivity extends AppCompatActivity {

    //由AIDL文件生成的Java类
    private BookManager mBookManager = null;

    //标志当前与服务端连接状况的布尔值，false为未连接，true为连接中
    private boolean mBound = false;

    //包含Book对象的list
    private List<Book> mBooks;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }

    /**
     * 按钮的点击事件，点击之后调用服务端的addBookIn方法
     *
     * @param view
     */
    public void addBook(View view) {
        //如果与服务端的连接处于未连接状态，则尝试连接
        if (!mBound) {
            attemptToBindService();
            Toast.makeText(this, "当前与服务端处于未连接状态，正在尝试重连，请稍后再试", Toast.LENGTH_SHORT).show();
            return;
        }
        if (mBookManager == null) return;

        Book book = new Book();
        book.setName("APP研发录In");
        book.setPrice(30);
        try {
            mBookManager.addBook(book);
            Log.e(getLocalClassName(), book.toString());
        } catch (RemoteException e) {
            e.printStackTrace();
        }
    }

    /**
     * 尝试与服务端建立连接
     */
    private void attemptToBindService() {
        Intent intent = new Intent();
        intent.setAction("com.lypeer.aidl");
        intent.setPackage("com.example.simaxiaochen.myapplication");
        bindService(intent, mServiceConnection, Context.BIND_AUTO_CREATE);
    }

    @Override
    protected void onStart() {
        super.onStart();
        if (!mBound) {
            attemptToBindService();
            Log.e("ty", "onStart: attemptToBindService");
        }
    }

    @Override
    protected void onStop() {
        super.onStop();
        if (mBound) {
            unbindService(mServiceConnection);
            mBound = false;
        }
    }

    private ServiceConnection mServiceConnection = new ServiceConnection() {
        @Override
        public void onServiceConnected(ComponentName name, IBinder service) {
            Log.e(getLocalClassName(), "service connected");
            mBookManager = BookManager.Stub.asInterface(service);
            mBound = true;

            if (mBookManager != null) {
                try {
                    mBooks = mBookManager.getBooks();
                    Log.e(getLocalClassName(), mBooks.toString());
                } catch (RemoteException e) {
                    e.printStackTrace();
                }
            }
        }

        @Override
        public void onServiceDisconnected(ComponentName name) {
            Log.e(getLocalClassName(), "service disconnected");
            mBound = false;
        }
    };
}
```

## 参考

http://blog.csdn.net/luoyanglizi/article/details/51980630

http://blog.csdn.net/luoyanglizi/article/details/52029091

http://blog.csdn.net/luoyanglizi/article/details/51958091
