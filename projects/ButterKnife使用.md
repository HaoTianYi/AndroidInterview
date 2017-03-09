# ButterKnife的使用

[TOC]

## 基本说明

作者环境：as2.2+gradle-2.14.1

## 配置环境

- 在build.gradle（project）中配置：

```
classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
```

![sp160923_110114](http://oaxelf1sk.bkt.clouddn.com/sp160923_110114.png)

- 在build.gradle（modle：app）中配置：

```
apply plugin: 'android-apt'
```

然后在dependencies中配置：

```
    compile 'com.jakewharton:butterknife:8.4.0'
    apt 'com.jakewharton:butterknife-compiler:8.4.0'
```

然后开始sync，会有错误不用管一路向下，基本成功。

## 基本使用

### 在activity中使用

在setContentView后面加上

```
ButterKnife.bind(this);
```

在前面绑定一些view，来做测试：

```
    @BindView(R.id.imageView)
    ImageView mImageView;
    @BindView(R.id.button)
    Button mButton;
    @BindView(R.id.activity_main)
    RelativeLayout mActivityMain;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ButterKnife.bind(this);
    }


    @OnClick(R.id.button)
    public void onClick() {
        Log.d("tag","success");
    }
```

在as中推荐Android Butterknife Zelezny插件快速生成bingView

### 在fragment中使用

在fragment中的解析view的时候绑定fragment：

```
        View view = inflater.inflate(R.layout.fragment_temp, container, false);
        ButterKnife.bind(this, view);
```

然后bindview和OnClick方法：

```
    @OnClick(R.id.button3)
    public void onClick() {
        Toast.makeText(getActivity(),"success",Toast.LENGTH_SHORT).show();
    }
```

完整代码：

```
    @BindView(R.id.button3)
    Button mButton3;
    @BindView(R.id.button4)
    Button mButton4;

    public TempFragment() {
        // Required empty public constructor
    }


    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
        // Inflate the layout for this fragment
        View view = inflater.inflate(R.layout.fragment_temp, container, false);
        ButterKnife.bind(this, view);
        return view;
    }

    @OnClick(R.id.button3)
    public void onClick() {
        Toast.makeText(getActivity(),"success",Toast.LENGTH_SHORT).show();
    }
```

运行效果：

![sp160923_111715](http://oaxelf1sk.bkt.clouddn.com/sp160923_111715.png)

### 绑定各种视图资源

```
    @BindColor(R.color.colorPrimary)
    int mColor;
    @BindString(R.string.app_name)
    String name;
    @BindDrawable(R.drawable.temp)
    Drawable mDrawable;
    @BindView(R.id.button5)
    Button mButton5;
```

![sp160923_113016](http://oaxelf1sk.bkt.clouddn.com/sp160923_113016.png)

#### 测试代码

```
    @OnClick(R.id.button5)
    public void onClick2() {
        mButton5.setBackground(mDrawable);
    }
```

对应的xml文件：

```
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
<item android:state_checkable="true" android:drawable="@android:color/holo_blue_bright"/>
    <item android:drawable="@android:color/holo_red_light"/>
</selector>
```

**运行结果**

![sp160923_113105](http://oaxelf1sk.bkt.clouddn.com/sp160923_113105.png)

> 注意：ButterKnife.bind(this, view)一定是在使用绑定的view之前否则会报错

## 进阶使用

### 在adapter中使用

在viewholder中的代码：

```
    static class ViewHolder {

        @BindView(R.id.textView)
        TextView mTextView1;
        @BindView(R.id.textView2)
        TextView mTextView2;

        public ViewHolder(View view) {
            ButterKnife.bind(this, view);
        }
    }
```

注意在构造方法中一定要传入一个view

**完整代码**

```
public class TempAdapter<T> extends BaseAdapter {

    private List<T> mList;

    public TempAdapter(List<T> list) {
        mList = list;
    }

    @Override
    public int getCount() {
        if (mList != null) {
            return mList.size();
        }
        return 0;
    }

    @Override
    public Object getItem(int position) {
        return mList.get(position);
    }

    @Override
    public long getItemId(int position) {
        return position;
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        ViewHolder viewHolder;
        if (convertView != null) {
            viewHolder = (ViewHolder) convertView.getTag();
        } else {
            convertView = View.inflate(BaseApplication.getmContext(), R.layout.item, null);
            viewHolder = new ViewHolder(convertView);
            convertView.setTag(viewHolder);
        }
        viewHolder.mTextView1.setText(mList.get(position)+"");
        viewHolder.mTextView2.setText(mList.get(position)+"");

        return convertView;
    }

    static class ViewHolder {

        @BindView(R.id.textView)
        TextView mTextView1;
        @BindView(R.id.textView2)
        TextView mTextView2;

        public ViewHolder(View view) {
            ButterKnife.bind(this, view);
        }
    }
}

```

最后在fragment中给listview添加adapter：

```
mLv.setAdapter(new TempAdapter<String>(mMlist));
```

**item代码**

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="match_parent"
              android:layout_height="match_parent">

    <TextView
        android:text="TextView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" android:id="@+id/textView"/>
    <TextView
        android:text="TextView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" android:id="@+id/textView2"/>
</LinearLayout>
```

**运行结果**

![sp160923_115244](http://oaxelf1sk.bkt.clouddn.com/sp160923_115244.png)

## 最后

在fragment中使用butterknife要注意一定要解绑：

```
    mUnbinder = ButterKnife.bind(this, view);
    
    @Override
    public void onDestroyView() {
        super.onDestroyView();
        mUnbinder.unbind();
    }
```

参考自：[butterKnife官方文档](http://jakewharton.github.io/butterknife/)
