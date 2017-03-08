# Android IPC机制-序列化

[TOC]

## 序列化简介

序列化 (Serialization)将对象的状态信息转换为可以存储或传输的形式的过程。在序列化期间，对象将其当前状态写入到临时或持久性存储区。以后，可以通过从存储区中读取或反序列化对象的状态，重新创建该对象。

序列化的意义：

1. 永久性保存对象，保存对象的字节序列到本地文件，用对象处理流（ObjectOutputStream）来把这个类对象存储在本地（这个过程，就是序列化-持久化过程）。当你想使用这个已经被序列化-持久化的类对象时，用对象处理流（ObjectInputStream）取得类对象数据，加载到程序中。
2. 通过序列化对象在网络中传递对象。
3. 通过序列化对象在进程间传递对象。

### 序列化的方式

- 实现Serializable接口
- 实现parcelable接口

### 两种方式的区别

1. Serializeble是java的序列化方式，Parcelable是Android特有的序列化方式
2. 在使用内存的时候，Parcelable比Serializable性能高，所以推荐使用Parcelable。
3. Serializable在序列化的时候会产生大量的临时变量，从而引起频繁的GC。
4. Parcelable不能使用在要将数据存储在磁盘上的情况，因为Parcelable不能很好的保证数据的持续性在外界有变化的情况下。尽管Serializable效率低点， 也不提倡用，但在这种情况下，还是建议你用Serializable。

## 使用Serializeble

只需让需要序列化的类实现Serializable 即可，系统会自动将其序列化，一定要定义serialVersionUID。

```
public class Student implements Serializable {
    private String name;
    private int age;

    private static final long serialVersionUID = 21455356667888L;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

### 在Activity中的使用

```
// serializable对象传递方法
	    Student student = new Student();
        student.setAge(18);
        student.setName("王五");
        Intent intent = new Intent(MainActivity.this, Main2Activity.class);
        Bundle bundle = new Bundle();
        bundle.putSerializable("ser_key", student);
        intent.putExtras(bundle);
        startActivity(intent);
  //或者直接设置额外的数据      
        intent.putExtra("extra_key",student);
        startActivity(intent);
        
       
// serializable对象获取方法
Student student1 = (Student) getIntent().getSerializableExtra("ser_key");
```

## Parcelable的使用

它要实现两个必要的方法和一个接口。

writeToParcel、describeContents。分别代表写入数据到Parcel以及内容描述。writeToParcel我们可以理解为流写入Parcel、describeContents方法一般默认返回0即可。

必要实现接口：public final static ?Parcelable.Creator<ParcelableClass> CREATOR。这是个很特别的接口，按约定，这个接口的名字你还不能更改，只能是CREATOR，并且前面的修饰需要时public final static。

CREATOR接口的实现中，有两个方法，createFromParcel（可以看成是从Parcel中获取流，与上方的writeToParcel相对应），newArray（返回多个类对象）。

![ecc4da65-1d0d-49bd-bf76-f8c2efe7a62e](http://oaxelf1sk.bkt.clouddn.com/ecc4da65-1d0d-49bd-bf76-f8c2efe7a62e.png)

### 基本使用

```
public class StudentP implements Parcelable {

    private String name;
    private int age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    protected StudentP(Parcel in) {
    }

    public static final Creator<StudentP> CREATOR = new Creator<StudentP>() {
        @Override
        public StudentP createFromParcel(Parcel in) {
            StudentP studentP = new StudentP(in);
            studentP.setName(in.readString());
            studentP.setAge(in.readInt());
            return studentP;
        }

        @Override
        public StudentP[] newArray(int size) {
            return new StudentP[size];
        }
    };

    @Override
    public int describeContents() {
        return 0;
    }

    @Override
    public void writeToParcel(Parcel dest, int flags) {
        dest.writeString(name);
        dest.writeInt(age);
    }
}
```

### 在Activity中使用

```
 // parcelable对象传递方法
	    StudentP studentP = new StudentP();
        studentP.setAge(18);
        studentP.setName("王五");
        Intent intent = new Intent(MainActivity.this, Main2Activity.class);
        intent.putExtra("p_key",studentP);
        startActivity(intent);
// parcelable对象获取方法
studentP studentP1 = (studentP)getIntent().getParcelableExtra("p_key");
```

还可以传递列表：

```
// parcelable对象List传递方法
public void setParcelableListMethod() {
    ArrayList<Person> personList = new ArrayList<Person>();
    Person person1 = new Person();
    person1.setmName("王海康");
    person1.setmSex("男");
    person1.setmAge(45);
    personList.add(person1);
    Person person2 = new Person();
    person2.setmName("薛岳");
    person2.setmSex("男");
    person2.setmAge(15);
    personList.add(person2);
    Intent intent = new Intent(this, PersonTest.class);
    Bundle bundle = new Bundle();
    bundle.putParcelableArrayList(PAR_LIST_KEY, personList);
    intent.putExtras(bundle);
    startActivity(intent);
}

// parcelable对象获取方法
public ArrayList<Person> getParcelableMethod(){
    ArrayList<Person> mPersonList = 				getIntent().getParcelableExtra(PAR_LIST_KEY);
	return mPersonList;
}
```

## 参考

http://blog.csdn.net/qq_23547831/article/details/51779528

http://www.jianshu.com/p/a60b609ec7e7

http://www.cnblogs.com/xyczero/p/4021245.html

blog.csdn.net/yangzhaomuma/article/details/50452651
