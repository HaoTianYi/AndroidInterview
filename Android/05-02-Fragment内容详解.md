# 05-02-Fragment内容详解

[TOC]

## 和Activity交互

- 调用fragment的函数findFragmentById()或者findFragmentByTag()

```
ExampleFragment fragment = (ExampleFragment) getFragmentManager().findFragmentById(R.id.example_fragment);
```

- 在Fragment中可以通过getActivity得到当前绑定的Activity的实例。
- 创建activity事件回调函数，在fragment内部定义一个回调接口，宿主activity来实现它。

### 通信机制

​	考虑Fragment的重复使用，所以必须降低Fragment与Activity的耦合，而且Fragment更不应该直接操作别的Fragment，毕竟Fragment操作应该由它的管理者Activity来决定，所有大部分的逻辑应该放到Activity中

#### 使用回调接口

改进MyFragment代码：

```
    @Override
    public void onClick(View v) {
        
        ((onFragmentClickListener) getActivity()).onClick();
    }

    public interface onFragmentClickListener {
        void onClick();
    }
```

### 使用set方法

```
    onFragmentClickListener mListener;
    
    @Override
    public void onClick(View v) {
        if (mListener != null) {
            mListener.onClick();
        }
    }

    public interface onFragmentClickListener {
        void onClick();
    }    

    public void setFragemntClickListener(onFragmentClickListener listener) {
        mListener = listener;
    }
```

activity在使用时候一定要显示调用setFragemntClickListener方法

## 配置发生变化

运行时配置发生变化，最常见的就是屏幕发生旋转，生命周期：

```
24547-24547 W/ty: ----Fragment---onCreate----
24547-24547 W/ty: -------onCreate------
24547-24547 W/ty: ----Fragment---onCreateView----
24547-24547 W/ty: ----Fragment---onActivityCreated----
24547-24547 W/ty: ----Fragment---onCreate----
24547-24547 W/ty: ----Fragment---onCreateView----
24547-24547 W/ty: ----Fragment---onActivityCreated----
24547-24547 W/ty: -------onStart------
24547-24547 W/ty: ----Fragment---onStart----
24547-24547 W/ty: ----Fragment---onStart----
24547-24547 W/ty: -------onResume------
24547-24547 W/ty: ----Fragment---onResume----
24547-24547 W/ty: ----Fragment---onResume----
```

可以看出当屏幕旋转的时候，系统多创建出一个fragment

> 当屏幕发生旋转，Activity发生重新启动，默认的Activity中的Fragment也会跟着Activity重新创建；这样造成当旋转的时候，本身存在的Fragment会重新启动，然后当执行Activity的onCreate时，又会再次实例化一个新的Fragment

### 解决办法

可以使用actiivty中的savedInstanceState参数来判断是否从新创建fragment：

```
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        Log.w("ty", "-------onCreate------");
        setContentView(R.layout.activity_main);
        if (savedInstanceState==null){
            mFragmentOne = new MyFragment();
            FragmentManager fragmentManager = getFragmentManager();
            mTransaction = fragmentManager.beginTransaction();
            mTransaction.add(R.id.fl,mFragmentOne,"replace");
            mTransaction.commit();
        }
    }
```

判断一下是否有数据就可以了，生命周期：

```
27814-27814 W/ty: ----Fragment---onCreate----
27814-27814 W/ty: -------onCreate------
27814-27814 W/ty: ----Fragment---onCreateView----
27814-27814 W/ty: ----Fragment---onActivityCreated----
27814-27814 W/ty: -------onStart------
27814-27814 W/ty: ----Fragment---onStart----
27814-27814 W/ty: -------onResume------
27814-27814 W/ty: ----Fragment---onResume----
```

## 扩展

### FragmentPagerAdapter与FragmentStatePagerAdapter

主要区别就在与对于fragment是否销毁，下面细说：

FragmentStatePagerAdapter 会销毁不需要的fragment。事务提交后，activity的 Fragment-
Manager 中的fragment会被彻底移除。 FragmentStatePagerAdapter 类名中的“state”表明：在
销毁fragment时，可在 onSaveInstanceState(Bundle) 方法中保存fragment的 Bundle 信息。用户切换回来时，保存的实例状态可用来恢复生成新的fragment

![](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170303_110741.png)

相比之下， FragmentPagerAdapter 有不同的做法。对于不再需要的fragment， Fragment-
PagerAdapter 会选择调用事务的 detach(Fragment) 方法来处理它，而非 remove(Fragment) 方
法。也就是说， FragmentPagerAdapter 只是销毁了fragment的视图，fragment实例还保留在
FragmentManager 中。因此， FragmentPagerAdapter 创建的fragment永远不会被销毁

![](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170303_110750.png)

​	如上所说，使用FragmentStatePagerAdapter当然更省内存，但是销毁新建也是需要时间的。一般情况下，如果你是制作主页面，就3、4个Tab，那么可以选择使用FragmentPagerAdapter，如果你是用于ViewPager展示数量特别多的条目时，那么建议使用FragmentStatePagerAdapter。

### Activity启动fragment

模拟带有参数的启动，也就是数据从activity到fragemnt，只不过发生在fragment创建的过程中，使用newInstance模式：

```
public class ContentFragment extends Fragment  
{  
  
    private String mArgument;  
    public static final String ARGUMENT = "argument";  
  
    @Override  
    public void onCreate(Bundle savedInstanceState)  
    {  
        super.onCreate(savedInstanceState);  
        // mArgument = getActivity().getIntent().getStringExtra(ARGUMENT);  
        Bundle bundle = getArguments();  
        if (bundle != null)  
            mArgument = bundle.getString(ARGUMENT);  
  
    }  
  
    /** 
     * 传入需要的参数，设置给arguments 
     * @param argument 
     * @return 
     */  
    public static ContentFragment newInstance(String argument)  
    {  
        Bundle bundle = new Bundle();  
        bundle.putString(ARGUMENT, argument);  
        ContentFragment contentFragment = new ContentFragment();  
        contentFragment.setArguments(bundle);  
        return contentFragment;  
    }  
```

### fragment启动特定的activity

可以使用startActivityForResult和onActivityResult

```
    @Override
    public void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
    }
```

### BaseFragment抽象标准代码

```
public abstract class BaseFragment extends FragmentActivity  
{  
    protected abstract Fragment createFragment();  
      
    @Override  
    protected void onCreate(Bundle savedInstanceState)  
    {  
  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_single_fragment);  
      
        FragmentManager fm = getSupportFragmentManager();  
        Fragment fragment =fm.findFragmentById(R.id.id_fragment_container);  
          
        if(fragment == null )  
        {  
            fragment = createFragment() ;  
              
            fm.beginTransaction().add(R.id.id_fragment_container,fragment).commit();  
        }  
    }  
      
}  
```

子类：

```
public class ContentActivity extends BaseFragment  
{  
    private ContentFragment mContentFragment;  
  
    @Override  
    protected Fragment createFragment()  
    {  
        String title = getIntent().getStringExtra(ContentFragment.ARGUMENT);  
  
        mContentFragment = ContentFragment.newInstance(title);  
        return mContentFragment;  
    }  
} 
```

## 参考文献

http://blog.csdn.net/lmj623565791/article/details/37992017

http://blog.csdn.net/forever_crying/article/details/8238863/

https://github.com/HaoTianYi/ForAndroidInterview/blob/master/android/Fragment%20%E5%85%A8%E8%A7%A3%E6%9E%90.md

http://blog.csdn.net/lmj623565791/article/details/42628537

感谢以上作者的辛勤劳动

