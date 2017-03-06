# 07-01自定义ViewGroup基本概念

[TOC]

## 构造函数的调用流程

```
public class MyViewGroup extends ViewGroup {

    public MyViewGroup(Context context) {
        super(context);
    }

    public MyViewGroup(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    public MyViewGroup(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }
```

1. 在代码中直接new一个MyViewGroup实例的时候,会调用第一个构造函数.这个没有任何争议.
2. 在xml布局文件中调用MyViewGroup的时候,会调用第二个构造函数.这个也没有争议.
3. 在xml布局文件中调用MyViewGroup,并且MyViewGroup标签中还有自定义属性时,**这里调用的还是第二个构造函数**.

也就是说,系统默认只会调用MyViewGroup的前两个构造函数,至于第三个构造函数的调用,通常是我们自己在构造函数中主动调用的，defStyleAttr参数就是控件的样式style。

## 绘制流程

![](http://oaxelf1sk.bkt.clouddn.com/687474703a2f2f696d672e626c6f672e6373646e2e6e65742f3230313630363137313830393333353235.png)

​	其中measure()是被final修饰的，这是不可被重写。onMeasure在measure方法中调用的，当我们继承View的时候通过重写onMeasure方法来测量控件大小，layout()和onLayout(),draw()和onDraw()类似。

## 重要的方法

### onMeasure

在onMeasure中计算childView的测量值以及模式，以及设置自己的宽和高

#### ViewGroup.LayoutParams

​	ViewGroup 自身的布局参数，用来指定视图的高度和宽度等参数，使用 view.getLayoutParams() 方法获取一个视图LayoutParams，该方法得到的就是其所在父视图类型的LayoutParams，比如View的父控件为RelativeLayout，那么得到的 LayoutParams 类型就为RelativeLayoutParams。

> ①具体值
>
> ②MATCH_PARENT 表示子视图希望和父视图一样大(不包含 padding 值) 
>
> ③WRAP_CONTENT 表示视图为正好能包裹其内容大小(包含 padding 值) 

#### MeasureSpecs

测量规格，包含测量要求和尺寸的信息，有三种模式:

- UNSPECIFIED

> 父视图不对子视图有任何约束，它可以达到所期望的任意尺寸。比如 ListView、ScrollView，一般自定义 View 中用不到 　　 

- EXACTLY　

> 父视图为子视图指定一个确切的尺寸，而且无论子视图期望多大，它都必须在该指定大小的边界内，对应的属性为 match_parent 或具体值，比如 100dp，父控件可以通过MeasureSpec.getSize(measureSpec)直接得到子控件的尺寸。

- AT_MOST　

> 父视图为子视图指定一个最大尺寸。子视图必须确保它自己所有子视图可以适应在该尺寸范围内，对应的属性为 wrap_content，这种模式下，父控件无法确定子 View 的尺寸，只能由子控件自己根据需求去计算自己的尺寸，这种模式就是我们自定义视图需要实现测量逻辑的情况。　

#### 对wrap_content的支持

在onMeasure()方法中实现对wrap_content的支持

```
  protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
      super.onMeasure(widthMeasureSpec, heightMeasureSpec);
      int widthSpecMode = MeasureSpec.getMode(widthMeasureSpec);
      int widthSpecSize= MeasureSpec.getSize(widthMeasureSpec);
      int heightSpectMode = MeasureSpec.getMode(heightMeasureSpec);
      int heightSpecSize=MeasureSpec.getSize(heightMeasureSpec);
      //这里就是对wrap_content的支持
      if(widthSpecMode==MeasureSpec.AT_MOST&&heightSpectMode==MeasureSpec.AT_MOST){
          //这里设定的根据你自己自定义View的情况而定
          setMeasuredDimension(200,200);
      }else if(widthSpecMode==MeasureSpec.AT_MOST){
          setMeasuredDimension(200,heightSpecSize);
      }else if (heightSpectMode==MeasureSpec.AT_MOST){
          setMeasuredDimension(widthSpecSize,200);
      }
  }
```

也就是自己指定View的大小，也可以由childView来自己确定大小

#### 对padding的支持

```
//在onDraw()方法中实现对padding的支持，其实就是在或控件时考虑到就padding就好了。如果不自己实现那么你对该自定义View设置padding将是无效的！

  @Override
     protected void onDraw(Canvas canvas) {
      super.onDraw(canvas);
      //这里是对画一个圆形的View的padding支持
      final int paddingLeft = getPaddingLeft();
      final int paddingRight = getPaddingRight();
      final int paddingTop = getPaddingTop();
      final int paddingBottom=getPaddingBottom();
      int width = getWidth()-paddingLeft-paddingRight;
      int height = getHeight()-paddingBottom-paddingTop;
      int radius = Math.min(width,height)/2;
      canvas.drawCircle(paddingLeft+width/2,paddingTop+height/2,radius,mPaint_while);
  }
```

对于margin的支持可以使用MarginLayoutParams，重新ViewGroup中的generateLayoutParams方法即可：

```
    @Override
    public LayoutParams generateLayoutParams(AttributeSet attrs) {
        return new MarginLayoutParams(getContext(),attrs);
    }
```

#### 测量子视图

measureChild()方法来测量出子视图的大小，getChildCount()获得孩子view的个数

### onLayout

​	measure过程结束后，视图的大小就已经测量好了，接下来就是layout的过程了。这个方法是用于给视图进行布局的，也就是确定视图的位置。

​	在 layout 过程中，子视图会调用getMeasuredWidth()和getMeasuredHeight()方法获取到 measure 过程得到的 mMeasuredWidth 和 mMeasuredHeight，作为自己的 width 和 height。然后调用每一个子视图的layout(l, t, r, b)函数，来确定每个子视图在父视图中的位置。

> getMeasureWidth()方法在measure()过程结束后就可以获取到了，而getWidth()方法要在layout()过程结束后才能获取到。另外，getMeasureWidth()方法中的值是通过setMeasuredDimension()方法来进行设置的，而getWidth()方法中的值则是通过视图右边的坐标减去左边的坐标计算出来的。

简单的实例，自定义的这个布局目标很简单，只要能够包含一个子视图，并且让子视图正常显示出来就可以了：

```
public class SimpleView extends ViewGroup {

    public SimpleView(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
        if (getChildCount() > 0) {
            View view = getChildAt(0);
            measureChild(view, widthMeasureSpec, heightMeasureSpec);
        }
    }

    @Override
    protected void onLayout(boolean changed, int l, int t, int r, int b) {
        if (getChildCount() > 0) {
            View view = getChildAt(0);
            view.layout(0, 0, view.getMeasuredWidth(), view.getMeasuredHeight());
        }
    }
}
```

对应的布局：

```
<com.example.simaxiaochen.viewgroup.SimpleView xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context="com.example.simaxiaochen.viewgroup.MainActivity">

   <ImageView
       android:layout_width="match_parent"
       android:layout_height="match_parent"
       android:src="@mipmap/ic_launcher"
       android:scaleType="fitCenter"/>
</com.example.simaxiaochen.viewgroup.SimpleView>
```

![](http://oaxelf1sk.bkt.clouddn.com/Screenshot_1488766302.png)

## selector

selector中view的多种状态：

**1. enabled**

表示当前视图是否可用。可以调用setEnable()方法来改变视图的可用状态，传入true表示可用，传入false表示不可用。它们之间最大的区别在于，不可用的视图是无法响应onTouch事件的。

**2. focused**

表示当前视图是否获得到焦点。通常情况下有两种方法可以让视图获得焦点，即通过键盘的上下左右键切换视图，以及调用requestFocus()方法。而现在的Android手机几乎都没有键盘了，因此基本上只可以使用requestFocus()这个办法来让视图获得焦点了。而requestFocus()方法也不能保证一定可以让视图获得焦点，它会有一个布尔值的返回值，如果返回true说明获得焦点成功，返回false说明获得焦点失败。一般只有视图在focusable和focusable in touch mode同时成立的情况下才能成功获取焦点，比如说EditText。

**3. window_focused**

表示当前视图是否处于正在交互的窗口中，这个值由系统自动决定，应用程序不能进行改变。

**4. selected**

表示当前视图是否处于选中状态。一个界面当中可以有多个视图处于选中状态，调用setSelected()方法能够改变视图的选中状态，传入true表示选中，传入false表示未选中。

**5. pressed**

表示当前视图是否处于按下状态。可以调用setPressed()方法来对这一状态进行改变，传入true表示按下，传入false表示未按下。通常情况下这个状态都是由系统自动赋值的，但开发者也可以自己调用这个方法来进行改变。

## 流布局

![](http://oaxelf1sk.bkt.clouddn.com/snipaste_20170306_144115.png)

- 设置支持margin的布局参数

```
	@Override
	protected ViewGroup.LayoutParams generateDefaultLayoutParams()
	{
		return new MarginLayoutParams(LayoutParams.MATCH_PARENT,
				LayoutParams.MATCH_PARENT);
	}
```

- 重写onMeasure方法

```
	/**
	 * 负责设置子控件的测量模式和大小 根据所有子控件设置自己的宽和高
	 */
	@Override
	protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec)
	{
		super.onMeasure(widthMeasureSpec, heightMeasureSpec);
		// 获得它的父容器为它设置的测量模式和大小
		int sizeWidth = MeasureSpec.getSize(widthMeasureSpec);
		int sizeHeight = MeasureSpec.getSize(heightMeasureSpec);
		int modeWidth = MeasureSpec.getMode(widthMeasureSpec);
		int modeHeight = MeasureSpec.getMode(heightMeasureSpec);

		Log.e(TAG, sizeWidth + "," + sizeHeight);

		// 如果是warp_content情况下，记录宽和高
		int width = 0;
		int height = 0;
		/**
		 * 记录每一行的宽度，width不断取最大宽度
		 */
		int lineWidth = 0;
		/**
		 * 每一行的高度，累加至height
		 */
		int lineHeight = 0;

		int cCount = getChildCount();

		// 遍历每个子元素
		for (int i = 0; i < cCount; i++)
		{
			View child = getChildAt(i);
			// 测量每一个child的宽和高
			measureChild(child, widthMeasureSpec, heightMeasureSpec);
			// 得到child的lp
			MarginLayoutParams lp = (MarginLayoutParams) child
					.getLayoutParams();
			// 当前子空间实际占据的宽度
			int childWidth = child.getMeasuredWidth() + lp.leftMargin
					+ lp.rightMargin;
			// 当前子空间实际占据的高度
			int childHeight = child.getMeasuredHeight() + lp.topMargin
					+ lp.bottomMargin;
			/**
			 * 如果加入当前child，则超出最大宽度，则的到目前最大宽度给width，类加height 然后开启新行
			 */
			if (lineWidth + childWidth > sizeWidth)
			{
				width = Math.max(lineWidth, childWidth);// 取最大的
				lineWidth = childWidth; // 重新开启新行，开始记录
				// 叠加当前高度，
				height += lineHeight;
				// 开启记录下一行的高度
				lineHeight = childHeight;
			} else
			// 否则累加值lineWidth,lineHeight取最大高度
			{
				lineWidth += childWidth;
				lineHeight = Math.max(lineHeight, childHeight);
			}
			// 如果是最后一个，则将当前记录的最大宽度和当前lineWidth做比较
			//if (i == cCount - 1)
			//{
			//	width = Math.max(width, lineWidth);
			//	height += lineHeight;
			//}

		}
		setMeasuredDimension((modeWidth == MeasureSpec.EXACTLY) ? sizeWidth
				: width, (modeHeight == MeasureSpec.EXACTLY) ? sizeHeight
				: height);

	}
```

> setMeasuredDimension用于设置ViewGroup的宽和高

- 重写onLayout方法

```
	/**
	 * 存储所有的View，按行记录
	 */
	private List<List<View>> mAllViews = new ArrayList<List<View>>();
	/**
	 * 记录每一行的最大高度
	 */
	private List<Integer> mLineHeight = new ArrayList<Integer>();
	@Override
	protected void onLayout(boolean changed, int l, int t, int r, int b)
	{
		mAllViews.clear();
		mLineHeight.clear();

		int width = getWidth();

		int lineWidth = 0;
		int lineHeight = 0;
		// 存储每一行所有的childView
		List<View> lineViews = new ArrayList<View>();
		int cCount = getChildCount();
		// 遍历所有的孩子
		for (int i = 0; i < cCount; i++)
		{
			View child = getChildAt(i);
			MarginLayoutParams lp = (MarginLayoutParams) child
					.getLayoutParams();
			int childWidth = child.getMeasuredWidth();
			int childHeight = child.getMeasuredHeight();

			// 如果已经需要换行
			if (childWidth + lp.leftMargin + lp.rightMargin + lineWidth > width)
			{
				// 记录这一行所有的View以及最大高度
				mLineHeight.add(lineHeight);
				// 将当前行的childView保存，然后开启新的ArrayList保存下一行的childView
				mAllViews.add(lineViews);
				lineWidth = 0;// 重置行宽
				lineViews = new ArrayList<View>();
			}
			/**
			 * 如果不需要换行，则累加
			 */
			lineWidth += childWidth + lp.leftMargin + lp.rightMargin;
			lineHeight = Math.max(lineHeight, childHeight + lp.topMargin
					+ lp.bottomMargin);
			lineViews.add(child);
		}
		// 记录最后一行
		mLineHeight.add(lineHeight);
		mAllViews.add(lineViews);

		int left = 0;
		int top = 0;
		// 得到总行数
		int lineNums = mAllViews.size();
		for (int i = 0; i < lineNums; i++)
		{
			// 每一行的所有的views
			lineViews = mAllViews.get(i);
			// 当前行的最大高度
			lineHeight = mLineHeight.get(i);

			Log.e(TAG, "第" + i + "行 ：" + lineViews.size() + " , " + lineViews);
			Log.e(TAG, "第" + i + "行， ：" + lineHeight);

			// 遍历当前行所有的View
			for (int j = 0; j < lineViews.size(); j++)
			{
				View child = lineViews.get(j);
				if (child.getVisibility() == View.GONE)
				{
					continue;
				}
				MarginLayoutParams lp = (MarginLayoutParams) child
						.getLayoutParams();

				//计算childView的left,top,right,bottom
				int lc = left + lp.leftMargin;
				int tc = top + lp.topMargin;
				int rc =lc + child.getMeasuredWidth();
				int bc = tc + child.getMeasuredHeight();

				Log.e(TAG, child + " , l = " + lc + " , t = " + t + " , r ="
						+ rc + " , b = " + bc);

				child.layout(lc, tc, rc, bc);
				
				left += child.getMeasuredWidth() + lp.rightMargin
						+ lp.leftMargin;
			}
			left = 0;
			top += lineHeight;
		}

	}
```

> child.layout主要就是每一个孩子都要设置layout方法

## 参考

http://blog.csdn.net/guolin_blog/article/details/17045157

http://blog.csdn.net/lmj623565791/article/details/38352503

http://blog.csdn.net/guolin_blog/article/details/17357967

http://blog.csdn.net/guolin_blog/article/details/16330267

http://blog.csdn.net/lmj623565791/article/details/38339817