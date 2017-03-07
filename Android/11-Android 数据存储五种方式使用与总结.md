# 11-Android 数据存储五种方式使用与总结

[TOC]

## 概述

​	Android提供了5种方式来让用户保存持久化应用程序数据。根据自己的需求来做选择，比如数据是否是应用程序私有的，是否能被其他程序访问，需要多少数据存储空间等，分别是：

①　使用SharedPreferences存储数据　

②　文件存储数据

③　 SQLite数据库存储数据

④　使用ContentProvider存储数据

⑤　网络存储数据　

Android提供了一种方式来暴露你的数据（甚至是私有数据）给其他应用程序 - ContentProvider。

## SharedPreferences存储

　　SharedPreference类提供了一个总体框架，使您可以保存和检索的任何基本数据类型（ boolean, float, int, long, string）的持久键-值对（基于XML文件存储的“key-value”键值对数据）。

　　通常用来存储程序的一些配置信息。其存储在“data/data/程序包名/shared_prefs目录下。

　　xml 处理时Dalvik会通过自带底层的本地XML Parser解析，比如XMLpull方式，这样对于内存资源占用比较好。　

### 获取SharedPreferences对象（通过Context）

> ①　getSharedPreferences (String name, int mode)

　　当我们有多个SharedPreferences的时候，根据第一个参数name获得相应的SharedPreferences对象。

> ②　getPreferences (int mode)

　　如果你的Activity中只需要一个SharedPreferences的时候使用。

这里的mode有四个选项：

```
Context.MODE_PRIVATE

```

　　该SharedPreferences数据只能被本应用程序读、写。

```
Context.MODE_WORLD_READABLE

```

　　该SharedPreferences数据能被其他应用程序读，但不能写。

```
Context.MODE_WORLD_WRITEABLE

```

　　该SharedPreferences数据能被其他应用程序读和写。

```
Context.MODE_MULTI_PROCESS

```

　　sdk2.3后添加的选项，当多个进程同时读写同一个SharedPreferences时它会检查文件是否修改。

### 向Shared Preferences中写入值　 

首先要通过 SharedPreferences.Editor获取到Editor对象；

然后通过Editor的putBoolean() 或 putString()等方法存入值；

最后调用Editor的commit()方法提交；

```
//Use 0 or MODE_PRIVATE for the default operation 
SharedPreferences settings = getSharedPreferences("fanrunqi", 0);
SharedPreferences.Editor editor = settings.edit();
editor.putBoolean("isAmazing", true); 

// 提交本次编辑
editor.commit();

```

同时Edit还有两个常用的方法：

> editor.remove(String key) ：下一次commit的时候会移除key对应的键值对
>
> editor.clear()：移除所有键值对

### 从Shared Preferences中读取值

　　读取值使用 SharedPreference对象的getBoolean()或getString()等方法就行了（没Editor 啥子事）。

```
SharedPreferences settings = getSharedPreferences("fanrunqi", 0);
boolean isAmazing= settings.getBoolean("isAmazing",true);

```

### Shared Preferences的优缺点

　　可以看出来Preferences是很轻量级的应用，使用起来也很方便，简洁。但存储数据类型比较单一（只有基本数据类型），无法进行条件查询，只能在不复杂的存储需求下使用，比如保存配置信息等。
## 内部存储
　当文件被保存在内部存储中时，默认情况下，文件是应用程序私有的，其他应用不能访问。当用户卸载应用程序时这些文件也跟着被删除。

　　文件默认存储位置：/data/data/包名/files/文件名。

### 创建和写入一个内部存储的私有文件

①　调用Context的openFileOutput()函数，填入文件名和操作模式，它会返回一个FileOutputStream对象。

②　通过FileOutputStream对象的write()函数写入数据。

③　 FileOutputStream对象的close ()函数关闭流。

例如：

```
        String FILENAME = "a.txt";
        String string = "fanrunqi";

        try {
            FileOutputStream fos = openFileOutput(FILENAME, Context.MODE_PRIVATE);
            fos.write(string.getBytes());
            fos.close();
        } catch (Exception e) {
            e.printStackTrace();
        }

```

在 openFileOutput(String name, int mode)方法中

- name参数:　用于指定文件名称，不能包含路径分隔符“/” ，如果文件不存在，Android 会自动创建它。
- mode参数：用于指定操作模式，分为四种：

> Context.MODE_PRIVATE = 0

　　为默认操作模式，代表该文件是私有数据，只能被应用本身访问，在该模式下，写入的内容会覆盖原文件的内容。

> Context.MODE_APPEND = 32768

　　该模式会检查文件是否存在，存在就往文件追加内容，否则就创建新文件。　

> Context.MODE_WORLD_READABLE = 1

　　表示当前文件可以被其他应用读取。

> MODE_WORLD_WRITEABLE

　　表示当前文件可以被其他应用写入。

### 读取一个内部存储的私有文件

① 调用openFileInput( )，参数中填入文件名，会返回一个FileInputStream对象。

② 使用流对象的 read()方法读取字节

③ 调用流的close()方法关闭流

例如：

```
    String FILENAME = "a.txt";
        try {
            FileInputStream inStream = openFileInput(FILENAME);
            int len = 0;
            byte[] buf = new byte[1024];
            StringBuilder sb = new StringBuilder();
            while ((len = inStream.read(buf)) != -1) {
                sb.append(new String(buf, 0, len));
            }
            inStream.close();
        } catch (Exception e) {
            e.printStackTrace();
        } 

```

其他一些经常用到的方法：

- getFilesDir()：　得到内存储文件的绝对路径
- getDir()：　在内存储空间中创建或打开一个已经存在的目录
- deleteFile()：　删除保存在内部存储的文件。　　
- fileList()：　返回当前由应用程序保存的文件的数组（内存储目录下的全部文件）。　



### 保存内存缓存文件

　　有时候我们只想缓存一些数据而不是持久化保存，可以使用getCacheDir（）去打开一个文件，文件的存储目录（ /data/data/包名/cache ）是一个应用专门来保存临时缓存文件的内存目录。

　　当设备的内部存储空间比较低的时候，Android可能会删除这些缓存文件来恢复空间，但是你不应该依赖系统来回收，要自己维护这些缓存文件把它们的大小限制在一个合理的范围内，比如1ＭＢ．当你卸载应用的时候这些缓存文件也会被移除。

### raw 和assets异同点

- 相同点

两者目录下的文件在打包后会原封不动的保存在apk包中，不会被编译成二进制。

assets和res/raw工程目录下都可以放一些小于1M的文件（2.3版本以前要求，否则将不能读出数据。），这些文件将被打包到APK中应用使用。assets目录下的文件将不做任何处理被打包，，如果你的资源很大，例如视频等等，应该单独存储在文件系统中。

- 不同点

1. res/raw中的文件会被映射到R.java文件中，访问的时候直接使用资源ID即R.id.filename；assets文件夹下的文件不会被映射到R.java中，访问的时候需要AssetManager类。


2. res/raw不可以有目录结构，而assets则可以有目录结构，也就是assets目录下可以再建立文件夹


3. 读取资源的方法不同：

读取res/raw下的文件资源

```
InputStream is = getResources().openRawResource(R.id.filename);
//另外一种方式
InputStream is = this.getResources().openRawResource(R.raw.filename);
```

读取assets下的文件资源:

```
AssetManager am = null;    
am = getAssets();    
InputStream is = am.open("filename");   
//或者通过uri
Uri uri = Uri.parse("android.resource://package_name/raw/sample.png");  
```

## 使用外部存储

### 增加读写权限

```
　<!-- 在SDCard中创建与删除文件权限 --> 
    <uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"/> 

   <!-- 往SDCard写入数据权限 --> 
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```
### 检测外部设备可用性

```
//获取外存储的状态
String state = Environment.getExternalStorageState();
if (Environment.MEDIA_MOUNTED.equals(state)) {
    // 可读可写
    mExternalStorageAvailable = mExternalStorageWriteable = true;
} else if (Environment.MEDIA_MOUNTED_READ_ONLY.equals(state)) {
    // 可读
} else {
    // 可能有很多其他的状态，但是我们只需要知道，不能读也不能写  
}
```
### 访问外部文件
> getExternalFilesDir (String type)

　　该方法打开一个外存储目录，此方法需要一个类型，指定你想要的子目录，如类型参数DIRECTORY_MUSIC和 DIRECTORY_RINGTONES（传null就是你应用程序的文件目录的根目录）。通过指定目录的类型，确保Android的媒体扫描仪将扫描分类系统中的文件（例如，铃声被确定为铃声）。如果用户卸载应用程序，这个目录及其所有内容将被删除。

```
File file = new File(getExternalFilesDir(null), "fanrunqi.jpg");
```


## 参考

http://www.jianshu.com/p/4c020b45f9e3

https://github.com/HaoTianYi/ForAndroidInterview/blob/master/android/Android%20%E6%95%B0%E6%8D%AE%E5%AD%98%E5%82%A8%E4%BA%94%E7%A7%8D%E6%96%B9%E5%BC%8F%E4%BD%BF%E7%94%A8%E4%B8%8E%E6%80%BB%E7%BB%93.md

http://blog.csdn.net/robertcpp/article/details/51628900