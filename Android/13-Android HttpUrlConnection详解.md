# 13-Android HttpUrlConnection详解

[TOC]

## 介绍

　　HttpUrlConnection是Java.net包中提供的API，我们知道Android SDK是基于Java的，所以当然优先考虑HttpUrlConnection这种最原始最基本的API，其实大多数开源的联网框架基本上也是基于JDK的HttpUrlConnection进行的封装罢了

## 使用

1. 添加权限

```
<uses-permission android:name="android.permission.INTERNET"/>
```

2. 将访问的路径转换成URL

> URL url = new URL("http://www.haotianyi.win/");

3. 通过URL获取连接


> HttpURLConnection httpURLConnection = (HttpURLConnection) url.openConnection()

4. 设置请求方式

> httpURLConnection.setRequestMethod("GET")

5. 设置请求超时

> httpURLConnection.setConnectTimeout(3000)

6. 获得相应码

```
                if (httpURLConnection.getResponseCode() == 200) {
                    System.out.println("----------");
                }
```

### 使用GET方式访问网址

自定义线程访问网络：

```
    class MyThread extends Thread{
        @Override
        public void run() {
            try {
                URL url = new URL("http://www.haotianyi.win/");
                HttpURLConnection httpURLConnection = (HttpURLConnection) url.openConnection();
                httpURLConnection.setRequestMethod("GET");
                httpURLConnection.setConnectTimeout(3000);
                if (httpURLConnection.getResponseCode() == 200) {
                    System.out.println(streamToString(httpURLConnection.getInputStream()));
                }

            } catch (MalformedURLException e) {
                e.printStackTrace();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

#### 转换流的工具方法

```
    /**
     * 将输入流转换成字符串
     * 
     * @param is
     *            从网络获取的输入流
     * @return
     */
    public static String streamToString(InputStream is) {
        try {
            ByteArrayOutputStream baos = new ByteArrayOutputStream();
            byte[] buffer = new byte[1024];
            int len = 0;
            while ((len = is.read(buffer)) != -1) {
                baos.write(buffer, 0, len);
            }
            baos.close();
            is.close();
            byte[] byteArray = baos.toByteArray();
            return new String(byteArray);
        } catch (Exception e) {
            Log.e(tag, e.toString());
            return null;
        }
    }
```

### 使用post方式请求网络

新建net方法

```
    public static String net(){
        HttpURLConnection conn = null;
        URL url = null;

        Map params = new HashMap();//请求参数
        params.put("com","yt");//需要查询的快递公司编号
        params.put("no","884334806804115842");//需要查询的订单号
        params.put("key","聚合数据接口");//应用APPKEY(应用详细页查询)
        params.put("dtype","json");//返回数据的格式,xml或json，默认json

        try {
            url = new URL("http://v.juhe.cn/exp/index");
            conn = (HttpURLConnection) url.openConnection();
            conn.setRequestMethod("POST");
            DataOutputStream out = new DataOutputStream(conn.getOutputStream());
            out.writeBytes(urlencode(params));
            if (conn.getResponseCode()==200){
                System.out.println(streamToString(conn.getInputStream()));
            }
        } catch (MalformedURLException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return null;
    }
```

urlencode方法：

```
    //将map型转为请求参数型
    public static String urlencode(Map<String,Object>data) {
        StringBuilder sb = new StringBuilder();
        for (Map.Entry i : data.entrySet()) {
            try {
                sb.append(i.getKey()).append("=").append(URLEncoder.encode(i.getValue()+"","UTF-8")).append("&");
            } catch (UnsupportedEncodingException e) {
                e.printStackTrace();
            }
        }
        return sb.toString();
    }
```

结果：

```
{"resultcode":"200","reason":"查询成功","result":{"company":"圆通","com":"yt",
```

## WebView详细解释

​	WebView基于webkit引擎展现web页面的控件，WebView可以直接显示和渲染web页面，直接显示网页，webview可以直接用html文件（网络上或本地assets中）作布局，和JavaScript交互调用。

### 相关的类

#### WebSettings

WebSettings用来对WebView的配置进行配置和管理，比如是否可以进行文件操作、缓存的设置、页面是否支持放大和缩小、是否允许使用数据库api、字体及文字编码设置、是否允许js脚本运行、是否允许图片自动加载、是否允许数据及密码保存等等

```
WebSettings webSettings = mWebView.getSettings();
webSettings.setJavaScriptEnabled(true);
webSettings.setCacheMode(WebSettings.LOAD_DEFAULT);
webSettings.setDomStorageEnabled(true);
webSettings.setDatabaseEnabled(true);
webSettings.setAppCacheEnabled(true);
webSettings.setAllowFileAccess(true);
webSettings.setSavePassword(true);
webSettings.setSupportZoom(true);
webSettings.setBuiltInZoomControls(true);
/**
* 用WebView显示图片，可使用这个参数 设置网页布局类型：
* 1、LayoutAlgorithm.NARROW_COLUMNS ：适应内容大小
* 2、LayoutAlgorithm.SINGLE_COLUMN : 适应屏幕，内容将自动缩放
*/
webSettings.setLayoutAlgorithm(WebSettings.LayoutAlgorithm.NARROW_COLUMNS);
webSettings.setUseWideViewPort(true);
 
mWebView.setScrollBarStyle(WebView.SCROLLBARS_OUTSIDE_OVERLAY);
mWebView.setHorizontalScrollbarOverlay(true);
mWebView.setHorizontalScrollBarEnabled(true);
mWebView.requestFocus();
```

####  WebChromeClient

WebChromeClient会在一些影响浏览器ui交互动作发生时被调用，比如WebView关闭和隐藏、页面加载进展、js确认框和警告框、js加载前、js操作超时、webView获得焦点等等

用于设置进度条的显示进度代码：

```
mWebView.setWebChromeClient(new WebChromeClient() {
	public void onProgressChanged(WebView webView, int newProgress) {
		if (newProgress == 100) {
			mProgressBar.setVisibility(View.GONE);
		} else {
			mProgressBar.setVisibility(View.VISIBLE);
			mProgressBar.setProgress(newProgress);
		}	
	}
});
```

#### WebViewClient

WebViewClient会在一些影响内容渲染的动作发生时被调用，比如表单的错误提交需要重新提交、页面开始加载及加载完成、资源加载中、接收到https认证需要处理、页面键盘响应、页面中的url打开处理等等

如果希望点击链接继续在当前browser中响应,而不是新开Android的系统browser中响应该链接,必须覆盖 WebView的WebViewClient对象.

```
mWebView.setWebViewClient(new WebViewClient(){
	public boolean shouldOverrideUrlLoading(WebView view, String url){ 
		view.loadUrl(url);
		return true;
	}
});
```

#### addJavascriptInterface

使Js调用Native本地Java对象,实现本地Java代码和HTML页面进行交互，

注意:因为安全问题的考虑 Google在使用Android API 17以上的版本的时候 需要通过@JavascriptInterface来注解的Java函数才能被识别可以被Js调用。

### 显示百度

```
        mWebView = (WebView) findViewById(R.id.wb);
        mWebView.getSettings().setJavaScriptEnabled(true);
        mWebView.setWebViewClient(new WebViewClient(){
            @Override
            public boolean shouldOverrideUrlLoading(WebView view, WebResourceRequest request) {
                return false;
            }
        });
        mWebView.loadUrl("https://www.baidu.com");
        System.out.println("---------------------");
```

- 增加返回按钮

重写Activity中的按钮返回事件

```
    @Override
    public boolean onKeyDown(int keyCode, KeyEvent event) {
        if (keyCode==KEYCODE_BACK&&mWebView.canGoBack()){
            mWebView.goBack();
            return true;
        }
        return super.onKeyDown(keyCode, event);
    }
```

- 增加进度条

```
    <ProgressBar
        android:id="@+id/pb"
        style="?android:attr/progressBarStyleHorizontal"
        android:background="?attr/colorPrimary"
        android:layout_width="match_parent"
        android:layout_height="20dp"/>       
```

修改Actiivty的代码：

```
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        final ProgressBar progressBar = (ProgressBar) findViewById(R.id.pb);
        progressBar.setMax(100);

        mWebView = (WebView) findViewById(R.id.wb);
        mWebView.getSettings().setJavaScriptEnabled(true);
        mWebView.setWebViewClient(new WebViewClient(){
            @Override
            public boolean shouldOverrideUrlLoading(WebView view, WebResourceRequest request) {
                return false;
            }
        });

        mWebView.setWebChromeClient(new WebChromeClient(){
            @Override
            public void onProgressChanged(WebView view, int newProgress) {
                progressBar.setProgress(newProgress);
            }
        });

        mWebView.loadUrl("https://www.baidu.com");
        System.out.println("---------------------");
```

## 参考

http://www.androidchina.net/1225.html

https://www.kymjs.com/code/2015/05/03/01/

http://blog.csdn.net/jueblog/article/details/12985045

https://developer.android.google.cn/reference/android/webkit/WebView.html