# Android IPC����-���л�

[TOC]

## ���л����

���л� (Serialization)�������״̬��Ϣת��Ϊ���Դ洢�������ʽ�Ĺ��̡������л��ڼ䣬�����䵱ǰ״̬д�뵽��ʱ��־��Դ洢�����Ժ󣬿���ͨ���Ӵ洢���ж�ȡ�����л������״̬�����´����ö���

���л������壺

1. �����Ա�����󣬱��������ֽ����е������ļ����ö���������ObjectOutputStream��������������洢�ڱ��أ�������̣��������л�-�־û����̣���������ʹ������Ѿ������л�-�־û��������ʱ���ö���������ObjectInputStream��ȡ����������ݣ����ص������С�
2. ͨ�����л������������д��ݶ���
3. ͨ�����л������ڽ��̼䴫�ݶ���

### ���л��ķ�ʽ

- ʵ��Serializable�ӿ�
- ʵ��parcelable�ӿ�

### ���ַ�ʽ������

1. Serializeble��java�����л���ʽ��Parcelable��Android���е����л���ʽ
2. ��ʹ���ڴ��ʱ��Parcelable��Serializable���ܸߣ������Ƽ�ʹ��Parcelable��
3. Serializable�����л���ʱ��������������ʱ�������Ӷ�����Ƶ����GC��
4. Parcelable����ʹ����Ҫ�����ݴ洢�ڴ����ϵ��������ΪParcelable���ܺܺõı�֤���ݵĳ�����������б仯������¡�����SerializableЧ�ʵ͵㣬 Ҳ���ᳫ�ã�������������£����ǽ�������Serializable��

## ʹ��Serializeble

ֻ������Ҫ���л�����ʵ��Serializable ���ɣ�ϵͳ���Զ��������л���һ��Ҫ����serialVersionUID��

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

### ��Activity�е�ʹ��

```
// serializable���󴫵ݷ���
	    Student student = new Student();
        student.setAge(18);
        student.setName("����");
        Intent intent = new Intent(MainActivity.this, Main2Activity.class);
        Bundle bundle = new Bundle();
        bundle.putSerializable("ser_key", student);
        intent.putExtras(bundle);
        startActivity(intent);
  //����ֱ�����ö��������      
        intent.putExtra("extra_key",student);
        startActivity(intent);
        
       
// serializable�����ȡ����
Student student1 = (Student) getIntent().getSerializableExtra("ser_key");
```

## Parcelable��ʹ��

��Ҫʵ��������Ҫ�ķ�����һ���ӿڡ�

writeToParcel��describeContents���ֱ����д�����ݵ�Parcel�Լ�����������writeToParcel���ǿ������Ϊ��д��Parcel��describeContents����һ��Ĭ�Ϸ���0���ɡ�

��Ҫʵ�ֽӿڣ�public final static ?Parcelable.Creator<ParcelableClass> CREATOR�����Ǹ����ر�Ľӿڣ���Լ��������ӿڵ������㻹���ܸ��ģ�ֻ����CREATOR������ǰ���������Ҫʱpublic final static��

CREATOR�ӿڵ�ʵ���У�������������createFromParcel�����Կ����Ǵ�Parcel�л�ȡ�������Ϸ���writeToParcel���Ӧ����newArray�����ض������󣩡�

![ecc4da65-1d0d-49bd-bf76-f8c2efe7a62e](http://oaxelf1sk.bkt.clouddn.com/ecc4da65-1d0d-49bd-bf76-f8c2efe7a62e.png)

### ����ʹ��

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

### ��Activity��ʹ��

```
 // parcelable���󴫵ݷ���
	    StudentP studentP = new StudentP();
        studentP.setAge(18);
        studentP.setName("����");
        Intent intent = new Intent(MainActivity.this, Main2Activity.class);
        intent.putExtra("p_key",studentP);
        startActivity(intent);
// parcelable�����ȡ����
studentP studentP1 = (studentP)getIntent().getParcelableExtra("p_key");
```

�����Դ����б�

```
// parcelable����List���ݷ���
public void setParcelableListMethod() {
    ArrayList<Person> personList = new ArrayList<Person>();
    Person person1 = new Person();
    person1.setmName("������");
    person1.setmSex("��");
    person1.setmAge(45);
    personList.add(person1);
    Person person2 = new Person();
    person2.setmName("Ѧ��");
    person2.setmSex("��");
    person2.setmAge(15);
    personList.add(person2);
    Intent intent = new Intent(this, PersonTest.class);
    Bundle bundle = new Bundle();
    bundle.putParcelableArrayList(PAR_LIST_KEY, personList);
    intent.putExtras(bundle);
    startActivity(intent);
}

// parcelable�����ȡ����
public ArrayList<Person> getParcelableMethod(){
    ArrayList<Person> mPersonList = 				getIntent().getParcelableExtra(PAR_LIST_KEY);
	return mPersonList;
}
```

## �ο�

http://blog.csdn.net/qq_23547831/article/details/51779528

http://www.jianshu.com/p/a60b609ec7e7

http://www.cnblogs.com/xyczero/p/4021245.html

blog.csdn.net/yangzhaomuma/article/details/50452651
