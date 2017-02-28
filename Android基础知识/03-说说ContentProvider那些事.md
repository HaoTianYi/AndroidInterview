# 03-说说ContentProvider那些事

[TOC]

## 简介

​        ContentProvider（内容提供者）是Android的四大组件之一，以相对安全的方式封装数据（表）并且提供简易的处理机制和统一的访问接口供**其他程序**调用。　 　　 　　

​        Android的数据存储方式总共有五种，分别是：Shared Preferences、网络存储、文件存储、外储存储、SQLite。但一般这些存储都只是在单独的一个应用程序之中达到一个数据的共享，有时候我们需要操作其他应用程序的一些数据，就会用到ContentProvider。而且Android为常见的一些数据提供了默认的ContentProvider（包括音频、视频、图片和通讯录等）。

​        注意ContentProvider它也只是一个中间人，真正操作的数据源可能是数据库，也可以是文件、xml或网络等其他存储方式。

​        ContentProvider的访问模式和服务器客户端很像，ContentProvider负责提供网站的内容，然后有一个URI来访问，用户还有一个浏览器ContentResolver用于拿取数据。

### 基本使用

​        在as中直接创建就可以了，注意authorities这个属性（稍后讨论），然后我们看到有很多方法，先改写一个insert方法：

```
    @Override
    public Uri insert(Uri uri, ContentValues values) {
        System.out.println("-------insert-------");
        return null;
    }
```

在另一个APP中创建一个ContentResolver用于“浏览数据”

```
        mContentResolver = getContentResolver();
        mContentResolver.insert(Uri.parse("content://hao.win/"), contentValues);
```

并且指定好URI,然后从客户端开始执行，发现在第一个APP中的insert方法被调用了：

![sp161102_034951](http://oaxelf1sk.bkt.clouddn.com/sp161102_034951.png)

这就是基本使用，我们发现其实ContentResolver调用insert本质是调用第一个APP的insert方法

## Uri

常见的Uri：content：//haotianyi.win/word/1/name,下面来探究每一个部分的含义：

- content：//

  类似URL中的互联网协议，ContentProvider默认协议就是content

- haotianyi.win

  就是相当于域名

- word

  代表是资源部分

- 1

  代表在word资源下ID是1的记录

- name

  表示ID是1的记录的name字段

### ContetnUris

ContetnUris包含一个便利的函数withAppendedId()来向URI追加一个id。　

```
Uri uri = Uri.parse("content://cn.scu.myprovider/user")
Uri resultUri = ContentUris.withAppendedId(uri, 7); 

//生成后的Uri为：content://cn.scu.myprovider/user/7

```

同时提供parseId(uri)方法用于从URL中获取ID:

```
Uri uri = Uri.parse("content://cn.scu.myprovider/user/7")
long personid = ContentUris.parseId(uri);
//获取的结果为:7
```

### 通配符

URI模式匹配通配符

`*`匹配的任意长度的任何有效字符的字符串。

`＃`匹配的任意长度的数字字符的字符串。

如：

content://com.example.app.provider/* 匹配provider的任何内容url

content://com.example.app.provider/table3/# 匹配table3的所有行

### UriMatcher

​        UriMatcher本质上是一个文本过滤器，用在contentProvider中帮助我们过滤，分辨出查询者想要查询哪个数据表。

举例说明：

- 第一步，初始化：

```
UriMatcher matcher = new UriMatcher(UriMatcher.NO_MATCH);
//常量UriMatcher.NO_MATCH表示不匹配任何路径的返回码

```

- 第二步，注册需要的Uri：

```
//USER 和 USER_ID是两个int型数据
matcher.addURI("cn.scu.myprovider", "user", USER);
matcher.addURI("cn.scu.myprovider", "user/#",USER_ID);
//如果match()方法匹配content://cn.scu.myprovider/user路径，返回匹配码为USER

```

- 第三部，与已经注册的Uri进行匹配:

```
/* 
     * 如果操作集合，则必须以vnd.android.cursor.dir开头 
     * 如果操作非集合，则必须以vnd.android.cursor.item开头 
     * */  
    @Override  
    public String getType(Uri uri) {  
    Uri uri = Uri.parse("content://" + "cn.scu.myprovider" + "/user");  
        switch(matcher.match(uri)){  
        case USER:  
            return "vnd.android.cursor.dir/user";  
        case USER_ID:  
            return "vnd.android.cursor.item/user";  
        }  
    } 
```

### MIME

　　MIME是指定某个扩展名的文件用一种应用程序来打开，就像你用浏览器查看PDF格式的文件，浏览器会选择合适的应用来打开一样。Android中的工作方式跟HTTP类似，ContentProvider会根据URI来返回MIME类型，ContentProvider会返回一个包含两部分的字符串。MIME类型一般包含两部分，如：　　

> text/html text/css text/xml application/pdf

　　分为类型和子类型，Android遵循类似的约定来定义MIME类型，每个内容类型的Android MIME类型有两种形式：多条记录（集合）和单条记录。

　　集合记录：

```
vnd.android.cursor.dir/自定义

```

　　单条记录：

```
vnd.android.cursor.item/自定义

```

　　vnd表示这些类型和子类型具有非标准的、供应商特定的形式。Android中类型已经固定好了，不能更改，只能区别是集合还是单条具体记录，子类型可以按照格式自己填写。 　　在使用Intent时，会用到MIME，根据Mimetype打开符合条件的活动。

### 具体操作

当Uri可以携带信息，那么就应该有对应的类来处理这个信息，UriMatcher负责管理Uri，它只有两个方法：

![sp161102_180500](http://oaxelf1sk.bkt.clouddn.com/sp161102_180500.png)

1. addURI负责把Uri添加到UriMatcher统一管理，而且可以给不同的Uri打上tag方便switch
2. 根据前面注册的Uri来判断标识码，没有匹配返回-1

### 使用实例

```
        UriMatcher matcher = new UriMatcher(UriMatcher.NO_MATCH);
        matcher.addURI("hao.win","word",0);
        matcher.addURI("hao.win","word/1",1);
//      返回0
        matcher.match(Uri.parse("content://hao.win/word/"));
//      返回值1
        matcher.match(Uri.parse("content://hao.win/word/1/"));
```

注意在Uri.parse要写全称

## ContentProvider详解

### 常见的方法

当继承自ContentProvider类时系统会自动生成一下方法：

![sp161102_175359](http://oaxelf1sk.bkt.clouddn.com/sp161102_175359.png)

> public boolean onCreate()

   　　ContentProvider创建后　或　打开系统后其它应用第一次访问该ContentProvider时调用。

   > public Uri insert(Uri uri, ContentValues values)

   　　外部应用向ContentProvider中添加数据。

   > public int delete(Uri uri, String selection, String[] selectionArgs)

   　　外部应用从ContentProvider删除数据。

   > public int update(Uri uri, ContentValues values, String selection, String[] selectionArgs)：

   　　外部应用更新ContentProvider中的数据。

   > public Cursor query(Uri uri, String[] projection, String selection, String[] selectionArgs, String sortOrder)　

   　　供外部应用从ContentProvider中获取数据。 　

   > public String getType(Uri uri)

   　　该方法用于返回当前Url所代表数据的MIME类型。

## ContentResolver

ContentResolver通过URI来查询ContentProvider中提供的数据。除了URI以 外，还必须知道需要获取的数据段的名称，以及此数据段的数据类型。如果你需要获取一个特定的记录，你就必须知道当前记录的ID，也就是URI中D部分。

　　ContentResolver 类提供了与ContentProvider类相同签名的四个方法：

　　

> public Uri insert(Uri uri, ContentValues values)　//添加
>
> public int delete(Uri uri, String selection, String[] selectionArgs)　//删除
>
> public int update(Uri uri, ContentValues values, String selection, String[] selectionArgs)　//更新
>
> public Cursor query(Uri uri, String[] projection, String selection, String[] selectionArgs, String sortOrder)//获取

实例代码：

```
ContentResolver resolver =  getContentResolver();
Uri uri = Uri.parse("content://cn.scu.myprovider/user");

//添加一条记录
ContentValues values = new ContentValues();
values.put("name", "fanrunqi");
values.put("age", 24);
resolver.insert(uri, values);  

//获取user表中所有记录
Cursor cursor = resolver.query(uri, null, null, null, "userid desc");
while(cursor.moveToNext()){
   //操作
}

//把id为1的记录的name字段值更改新为finch
ContentValues updateValues = new ContentValues();
updateValues.put("name", "finch");
Uri updateIdUri = ContentUris.withAppendedId(uri, 1);
resolver.update(updateIdUri, updateValues, null, null);

//删除id为2的记录
Uri deleteIdUri = ContentUris.withAppendedId(uri, 2);
resolver.delete(deleteIdUri, null, null);
```

## ContentObserver

　　　 ContentObserver(内容观察者)，目的是观察特定Uri引起的数据库的变化，继而做一些相应的处理，它类似于数据库技术中的触发器(Trigger)，当ContentObserver所观察的Uri发生变化时，便会触发它.

```
下面是使用内容观察者监听短信的例子：

```

```
public class MainActivity extends Activity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

//注册观察者Observser    
this.getContentResolver().registerContentObserver(Uri.parse("content://sms"),true,new SMSObserver(new Handler()));

    }

    private final class SMSObserver extends ContentObserver {

        public SMSObserver(Handler handler) {
            super(handler);
        }
        @Override
        public void onChange(boolean selfChange) {

 Cursor cursor = MainActivity.this.getContentResolver().query(
Uri.parse("content://sms/inbox"), null, null, null, null);

            while (cursor.moveToNext()) {
                StringBuilder sb = new StringBuilder();

                sb.append("address=").append(
                        cursor.getString(cursor.getColumnIndex("address")));

                sb.append(";subject=").append(
                        cursor.getString(cursor.getColumnIndex("subject")));

                sb.append(";body=").append(
                        cursor.getString(cursor.getColumnIndex("body")));

                sb.append(";time=").append(
                        cursor.getLong(cursor.getColumnIndex("date")));

                System.out.println("--------has Receivered SMS::" + sb.toString());
            }
        }
    }
}
```

同时可以在ContentProvider发生数据变化时调用 getContentResolver().notifyChange(uri, null)来通知注册在此URI上的访问者。　　

```
public class UserContentProvider extends ContentProvider {
   public Uri insert(Uri uri, ContentValues values) {
      db.insert("user", "userid", values);
      getContext().getContentResolver().notifyChange(uri, null);
   }
}
```

![img](http://oaxelf1sk.bkt.clouddn.com/0451D7~1.GIF)

## 实例-共享数据

建立两个APP一个负责产生数据并且提供ContentProvider，另一个负责调用：

### 创建常量

```
public class Conf {
    // 定义该ContentProvider的Authorities
    public static final String AUTHORITY = "hao.win";
    // 定义一个静态内部类，定义该ContentProvider所包含的数据列的列名
    public static final class Word{
        // 定义Content所允许操作的三个数据列
        public static final String _ID = "_id";
        public static final String WORD = "word";
        public static final String DETAIL = "detail";
        // 定义该Content提供服务的两个Uri
        public static final Uri HAO_CONTENTS = Uri.parse("content://"+AUTHORITY+"/"+"/words");
        public static final Uri HAO_CONTENT = Uri.parse("content://"+AUTHORITY+"/"+"/word");
    }
}
```

注意这个把一个有关的变量在单独放在一个内部类中的写法

### 创建MyDatabaseHelper

```
public class MyDatabaseHelper extends SQLiteOpenHelper {

    public MyDatabaseHelper(Context context) {
        super(context, "test.db", null, 1);
    }

    public MyDatabaseHelper(Context context, String name, SQLiteDatabase.CursorFactory factory, int version) {
        super(context, name, factory, version);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        db.execSQL("create table main(_id integer primary key autoincrement,word,detail)");
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {

    }
}
```

注意第一个构造方法，简化许多

### 完善MyContentProvider

这个有点意思：

1. 注册uri
2. oncreate方法中初始化
3. 重写getType方法解析uri对应的mime
4. 重写对应的方法

#### 注册Uri

```
    private static UriMatcher sMatcher = new UriMatcher(UriMatcher.NO_MATCH);

    private static final int WORDS = 1;
    private static final int WORD = 2;

    static {
        sMatcher.addURI(Conf.AUTHORITY, "words", WORDS);
        sMatcher.addURI(Conf.AUTHORITY, "word/#", WORD);
    }
```

这是一个静态的代码块

#### 在onCreate方法初始化

```
    @Override
    public boolean onCreate() {
        mDatabaseHelper = new MyDatabaseHelper(this.getContext());
        return true;
    }
```

注意返回true

#### 重写getType

```
    @Override
    public String getType(Uri uri) {
        switch (sMatcher.match(uri)) {
            case WORD:
                return "vdn.android.cursor.item/hao.win";
            case WORDS:
                return "vdn.android.cursor.dir/hao.win";
            default:
                throw  new IllegalArgumentException("未知的uri1");
        }
    }
```

在这个过程中把uri和一个int绑定好处明显，在使用swith是特别方便

#### 重写对应的方法

```
    @Override
    public Uri insert(Uri uri, ContentValues values) {
        SQLiteDatabase database = mDatabaseHelper.getWritableDatabase();
        switch (sMatcher.match(uri)){
            case WORDS:
                long rowID = database.insert("main", Conf.Word._ID, values);
                if (rowID>0){
                    Uri withAppendedId = ContentUris.withAppendedId(uri, rowID);                     getContext().getContentResolver().notifyChange(withAppendedId,null);
                    System.out.println("------insert-------");
                    return withAppendedId;
                }
                break;
        }
        return null;
    }
```

注意：getContext().getContentResolver().notifyChange(withAppendedId,null)通知数据的改变，这里可以使用观察者模式

#### 在ContentResolver中调用

```
        ContentValues contentValues = new ContentValues();
        contentValues.put("word","nihao");
        mContentResolver.insert(Uri.parse("content://hao.win/words"), contentValues);
```

注意ContentValues的put方法中的关键字是数据表的列明（你要插入的列）

当这个方法调用时：

![sp161102_194844](http://oaxelf1sk.bkt.clouddn.com/sp161102_194844.png)

## 实战-读取联系人

首先与系统ContenProvider相关的常量和类都在android.provider包中：

### 明确uri

> ContactsContract.Contacts._ID用于获取联系人的id

```
String conatctID = cursor.getString(cursor.getColumnIndex(ContactsContract.Contacts._ID))获得联系人的id
```

> ContactsContract.Contacts.CONTENT_URI管理联系人的uri

```
Cursor cursor = mContentResolver.query(ContactsContract.Contacts.CONTENT_URI, null, null, null, null)查询得到所有的联系人
```

> ContactsContract.Contacts.DISPLAY_NAME联系人的名字

```
String name = cursor.getString(cursor.getColumnIndex(ContactsContract.Contacts.DISPLAY_NAME));
```

> ContactsContract.CommonDataKinds.Phone.NUMBER电话号码

```
// 获得电话号码
String number = phones.getString(phones.getColumnIndex(ContactsContract.CommonDataKinds.Phone.NUMBER));
```

> ContactsContract.CommonDataKinds.Email.DATA邮件地址

```
String emailAdress = emails.getString(emails.getColumnIndex(ContactsContract.CommonDataKinds.Email.DATA))获得联系人的邮件地址
```

### 全部代码

```
public class MainActivity extends AppCompatActivity {

    private ContentResolver mContentResolver;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mContentResolver = getContentResolver();

    }

    public void start(View view) {
        Cursor cursor = mContentResolver.query(ContactsContract.Contacts.CONTENT_URI, null, null, null, null);
        while (cursor.moveToNext()) {
            String conatctID = cursor.getString(cursor.getColumnIndex(ContactsContract.Contacts._ID));
            String name = cursor.getString(cursor.getColumnIndex(ContactsContract.Contacts.DISPLAY_NAME));
//          根据联系人的id获得电话的光标
            Cursor phones = getContentResolver().query(ContactsContract.CommonDataKinds.Phone.CONTENT_URI,
                    null, ContactsContract.CommonDataKinds.Phone.CONTACT_ID + "=" + conatctID, null, null);
//          由于可能有多个电话号码，所以使用一个list
            ArrayList<String> numbers = new ArrayList<>();
            while (phones.moveToNext()) {
//              获得电话号码
                String number = phones.getString(phones.getColumnIndex(ContactsContract.CommonDataKinds.Phone.NUMBER));
                numbers.add(number);
            }
            phones.close();

//           仿照上面使用联系人的id，获得邮件的cursor
            Cursor emails = getContentResolver().query(ContactsContract.CommonDataKinds.Email.CONTENT_URI,
                    null, ContactsContract.CommonDataKinds.Email.CONTACT_ID + "=" + conatctID, null, null);
//          由于可能有多个email，所以使用一个list
            ArrayList<String> emaiList = new ArrayList<>();
            while (emails.moveToNext()) {
                String emailAdress = emails.getString(emails.getColumnIndex(ContactsContract.CommonDataKinds.Email.DATA));
                emaiList.add(emailAdress);
            }

            AlertDialog.Builder builder = new AlertDialog.Builder(MainActivity.this);
            builder.setTitle("联系人").setMessage(name + "----" + printList(emaiList) + "---" + printList(numbers)).show();
        }
    }

    /**
     * 返回这个list中的所有string
     *
     * @param list
     * @return
     */
    private String printList(List list) {
        StringBuilder builder = new StringBuilder();
        for (int i = 0; i < list.size(); i++) {
            String o = (String) list.get(i);
            builder.append(o);
        }
        return builder.toString();
    }
}
```

![sp161102_201834](http://oaxelf1sk.bkt.clouddn.com/sp161102_201834.png)

注意权限：

![sp161102_202523](http://oaxelf1sk.bkt.clouddn.com/sp161102_202523.png)

## 实战-插入联系人

### 思路

就是首先获得要添加联系人的id（通过插入一个空的数据），然后分别插入姓名电话，Email

### 代码

```
   public void insert(View view) {
//       分别存放联系人的姓名等的媒介
        ContentValues values = new ContentValues();
//        插入一个空值获得最新联系人的uri
        Uri rawUri = getContentResolver().insert(ContactsContract.RawContacts.CONTENT_URI, values);
        long rawID = ContentUris.parseId(rawUri);
        /*--------------------名字------------------------*/
//       设置联系人的id
        values.put(ContactsContract.RawContacts.Data.RAW_CONTACT_ID, rawID);
//       设置内容类型是名字
        values.put(ContactsContract.RawContacts.Data.MIMETYPE, ContactsContract.CommonDataKinds.StructuredName.CONTENT_ITEM_TYPE);
//       设置名字的内容
        values.put(ContactsContract.CommonDataKinds.StructuredName.GIVEN_NAME, "黄药师");
//        真正的插入数据
        getContentResolver().insert(ContactsContract.Data.CONTENT_URI, values);
//        之所以陈他做位一个媒介就是要清空
        values.clear();
        /*--------------------第一个电话号码------------------------*/
//        设置具体插入的id
        values.put(ContactsContract.RawContacts.Data.RAW_CONTACT_ID, rawID);
//        设置类型
        values.put(ContactsContract.RawContacts.Data.MIMETYPE, ContactsContract.CommonDataKinds.Phone.CONTENT_ITEM_TYPE);
        values.put(ContactsContract.CommonDataKinds.Phone.NUMBER, "1234567890");
        /*values.put(ContactsContract.CommonDataKinds.Phone.NUMBER, "1000000000");
        加入解锁这句话那么后面这个值会覆盖前面的值，并不会有两个值*/
//        设置成移动电话
        values.put(ContactsContract.CommonDataKinds.Phone.TYPE, ContactsContract.CommonDataKinds.Phone.TYPE_MOBILE);
        getContentResolver().insert(ContactsContract.Data.CONTENT_URI, values);
        values.clear();
//        想要同一个联系人天机两个电话号码只要在复制之分这个代码就ok
        /*--------------------第二个电话号码------------------------*/
//        设置具体插入的id
        values.put(ContactsContract.RawContacts.Data.RAW_CONTACT_ID, rawID);
//        设置类型
        values.put(ContactsContract.RawContacts.Data.MIMETYPE, ContactsContract.CommonDataKinds.Phone.CONTENT_ITEM_TYPE);
        values.put(ContactsContract.CommonDataKinds.Phone.NUMBER, "1000000000");
        /*values.put(ContactsContract.CommonDataKinds.Phone.NUMBER, "1000000000");
        加入解锁这句话那么后面这个值会覆盖前面的值，并不会有两个值*/
//        设置成移动电话
        values.put(ContactsContract.CommonDataKinds.Phone.TYPE, ContactsContract.CommonDataKinds.Phone.TYPE_MOBILE);
        getContentResolver().insert(ContactsContract.Data.CONTENT_URI, values);
        values.clear();
        /*--------------------email------------------------*/
        values.put(ContactsContract.RawContacts.Data.RAW_CONTACT_ID, rawID);
        values.put(ContactsContract.RawContacts.Data.MIMETYPE, ContactsContract.CommonDataKinds.Email.CONTENT_ITEM_TYPE);
        values.put(ContactsContract.CommonDataKinds.Email.DATA, "123456@hao.win");
        getContentResolver().insert(ContactsContract.Data.CONTENT_URI, values);
    }
```

## 实战-管理多媒体

### 获得所有的图片

```
        ArrayList<String> names = new ArrayList<>();
        ArrayList<String> descs = new ArrayList<>();
//        获得所有图片的uri
        Cursor media = getContentResolver().query(MediaStore.Images.Media.EXTERNAL_CONTENT_URI, null, null, null, null);
        while (media.moveToNext()) {
//            获得图片的名字
            String name = media.getString(media.getColumnIndex(MediaStore.Images.Media.DISPLAY_NAME));
            names.add(name);
//            获得图片的描述
            String desc = media.getString(media.getColumnIndex(MediaStore.Images.Media.DESCRIPTION));
            descs.add(desc);
        }
        AlertDialog.Builder builder = new AlertDialog.Builder(MainActivity.this);
        builder.setTitle("图片").setMessage(printList(names) + "---" + printList(descs)).show();
```

### 添加图片

把工程（drawable）中的一个图片添加到系统的picture中：

```
        ContentValues contentValues = new ContentValues();
        contentValues.put(MediaStore.Images.Media.DISPLAY_NAME, "hty");
        contentValues.put(MediaStore.Images.Media.DESCRIPTION, "郝天意");
        contentValues.put(MediaStore.Images.Media.MIME_TYPE, "image/png");
//      只是注册到系统的contentProvider但还是没有文件的读写
        Uri insert = getContentResolver().insert(MediaStore.Images.Media.EXTERNAL_CONTENT_URI, contentValues);
        Bitmap bitmap = BitmapFactory.decodeResource(MainActivity.this.getResources(), R.drawable.hty);
        try {
//            开始真正的把图片写入手机中
            OutputStream outputStream = getContentResolver().openOutputStream(insert);
            bitmap.compress(Bitmap.CompressFormat.PNG, 100, outputStream);
            outputStream.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
```

注意这里的Uri都是Images中的：

![sp161103_001742](http://oaxelf1sk.bkt.clouddn.com/sp161103_001742.png)






