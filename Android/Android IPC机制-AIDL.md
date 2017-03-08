# Android IPC����-AIDL

[TOC]

## ���

AIDL��һ����д��ȫ����[Android](http://lib.csdn.net/base/android)?Interface Definition Language��Ҳ����Android�ӿڶ������ԣ���Ϊ��**ʵ�ֽ��̼�ͨ�ţ����������漰����̲�������µĽ��̼�ͨ��**��

### ��������

AIDLĬ��֧��һЩ�������ͣ���ʹ����Щ�������͵�ʱ���ǲ���Ҫ�����ģ����ǳ�����Щ����֮����������ͣ���ʹ��֮ǰ���뵼����**����Ŀ���ļ��뵱ǰ���ڱ�д�� .aidl �ļ���ͬһ������**

Ĭ��֧�ֵ��������Ͱ�����?
Java�еİ��ֻ����������ͣ����� byte��short��int��long��float��double��boolean��char��

String ���͡�

CharSequence���͡�

List���ͣ�List�е�����Ԫ�ر�����AIDL֧�ֵ�����֮һ��������һ������AIDL���ɵĽӿڣ������Ƕ����parcelable�����Ĺ������������⣩��List����ʹ�÷��͡�

Map���ͣ�Map�е�����Ԫ�ر�����AIDL֧�ֵ�����֮һ��������һ������AIDL���ɵĽӿڣ������Ƕ����parcelable��Map�ǲ�֧�ַ��͵ġ�

**AIDL�еĶ��� tag ��ʾ���ڿ����ͨ�������ݵ��������� in ��ʾ����ֻ���ɿͻ����������ˣ� out ��ʾ����ֻ���ɷ��������ͻ��ˣ��� inout ���ʾ���ݿ��ڷ������ͻ���֮��˫����ͨ��**����tag��aidl�ļ��ķ�����ʶ��

### AIDL�����﷨

```
package com.lypeer.ipcclient;
//��������Ҫʹ�õķ�Ĭ��֧���������͵İ�
import com.lypeer.ipcclient.Book;

interface BookManager {

    //���еķ���ֵǰ������Ҫ���κζ�����������ʲô��������
    List<Book> getBooks();
    Book getBook();
    int getBookCount();

    //����ʱ����Java���������Լ�String��CharSequence֮�������
    //����Ҫ��ǰ����϶���tag�������ʲô�������
    void setBookPrice(in Book book , int price)
    void setBookName(in Book book , String name)
    void addBookIn(in Book book);
    void addBookOut(out Book book);
    void addBookInout(inout Book book);
}
```

ע��asĬ����ɵ�ParcelableҪ����һ��������

```
public void readFromParcel(Parcel dest) {
    //ע�⣬�˴��Ķ�ֵ˳��Ӧ���Ǻ�writeToParcel()������һ�µ�
    name = dest.readString();
    price = dest.readInt();
}
```

### �ƶ��ļ�

��aidl���֮�����ֱ���ƶ��ļ���

![snipaste_20170308_184115](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170308_184115.png)

����ͬ����java�ļ�ֱ���ƶ��ʼۺͶ�Ӧ���ļ��У�

![snipaste_20170308_184226](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170308_184226.png)

д��aidlֱ��rebuild��Ŀ�������ɶ�Ӧ��java�ļ�

## ʵ�ʰ���

### ������

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

    //���еķ���ֵǰ������Ҫ���κζ�����������ʲô��������
    List<Book> getBooks();

    //����ʱ����Java���������Լ�String��CharSequence֮�������
    //����Ҫ��ǰ����϶���tag�������ʲô�������
    void addBook(in Book book);
}
```

Service����Ҫ���Ǿ���д��aidl�ж���ķ�����

```
public class AIDLService extends Service {
    public final String TAG = this.getClass().getSimpleName();

    //����Book�����list
    private List<Book> mBooks = new ArrayList<>();

    //��AIDL�ļ����ɵ�BookManager
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
                //�����޸�book�Ĳ�������Ҫ��Ϊ�˹۲��䵽�ͻ��˵ķ���
                book.setPrice(2333);
                if (!mBooks.contains(book)) {
                    mBooks.add(book);
                }
                //��ӡmBooks�б��۲�ͻ��˴�������ֵ
                Log.e(TAG, "invoking addBooks() method , now the list is : " + mBooks.toString());
            }
        }
    };

    @Override
    public void onCreate() {
        super.onCreate();
        Book book = new Book();
        book.setName("Android��������̽��");
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

### �ͻ���

```
public class MainActivity extends AppCompatActivity {

    //��AIDL�ļ����ɵ�Java��
    private BookManager mBookManager = null;

    //��־��ǰ����������״���Ĳ���ֵ��falseΪδ���ӣ�trueΪ������
    private boolean mBound = false;

    //����Book�����list
    private List<Book> mBooks;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }

    /**
     * ��ť�ĵ���¼������֮����÷���˵�addBookIn����
     *
     * @param view
     */
    public void addBook(View view) {
        //��������˵����Ӵ���δ����״̬����������
        if (!mBound) {
            attemptToBindService();
            Toast.makeText(this, "��ǰ�����˴���δ����״̬�����ڳ������������Ժ�����", Toast.LENGTH_SHORT).show();
            return;
        }
        if (mBookManager == null) return;

        Book book = new Book();
        book.setName("APP�з�¼In");
        book.setPrice(30);
        try {
            mBookManager.addBook(book);
            Log.e(getLocalClassName(), book.toString());
        } catch (RemoteException e) {
            e.printStackTrace();
        }
    }

    /**
     * ���������˽�������
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

## �ο�

http://blog.csdn.net/luoyanglizi/article/details/51980630

http://blog.csdn.net/luoyanglizi/article/details/52029091

http://blog.csdn.net/luoyanglizi/article/details/51958091
