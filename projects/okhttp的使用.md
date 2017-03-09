# okHttp3.4.1的使用

[TOC]

## 说明

整理自：http://blog.csdn.net/iispring/article/details/51661195

Okhttp请求的数据会进行相应的缓存处理，下次再进行请求时，如果服务器告知304（表明数据没有发生变化），那么就直接从缓存中读取数据，降低了重复请求的数量

### 基本使用

在gradle中添加：

```
compile 'com.squareup.okhttp3:okhttp:3.3.1'
```

## 核心类

我们在使用Okhttp进行开发的时候，主要牵扯到以下几个核心类：OkHttpClient、Request、Call 和 Response。

### okhttpclient

OkHttpClient表示了HTTP请求的客户端类，在绝大多数的App中，我们只应该执行一次new OkHttpClient()，将其作为全局的实例进行保存，从而在App的各处都只使用这一个实例对象，这样所有的HTTP请求都可以共用Response缓存、共用线程池以及共用连接池。

默认情况下，直接执行OkHttpClient client = new OkHttpClient()就可以实例化一个OkHttpClient对象。

可以配置OkHttpClient的一些参数，比如超时时间、缓存目录、代理、Authenticator等，那么就需要用到内部类OkHttpClient.Builder，设置如下所示：

```
OkHttpClient client = new OkHttpClient.Builder().
        readTimeout(30, TimeUnit.SECONDS).
        cache(cache).
        proxy(proxy).
        authenticator(authenticator).
        build();
```

OkHttpClient本身不能设置参数，需要借助于其内部类Builder设置参数，参数设置完成后，调用Builder的build方法得到一个配置好参数的OkHttpClient对象。这些配置的参数会对该OkHttpClient对象所生成的所有HTTP请求都有影响。

有时候我们想单独给某个网络请求设置特别的几个参数，比如只想让某个请求的超时时间设置为一分钟，但是还想保持OkHttpClient对象中的其他的参数设置，那么可以调用OkHttpClient对象的`newBuilder()`方法，代码如下所示：

```
OkHttpClient client = ...

OkHttpClient clientWith60sTimeout = client.newBuilder().
        readTimeout(60, TimeUnit.SECONDS).
        build();
```

就是在前一个的client中再次设置builder

### requset

Request类封装了请求报文信息：请求的Url地址、请求的方法（如GET、POST等）、各种请求头（如Content-Type、Cookie）以及可选的请求体。一般通过内部类Request.Builder的链式调用生成Request对象。

### call

Call代表了一个实际的HTTP请求，它是连接Request和Response的桥梁，通过Request对象的`newCall()`方法可以得到一个Call对象。Call对象既支持同步获取数据，也可以异步获取数据。

- 执行Call对象的`execute()`方法，会阻塞当前线程去获取数据，该方法返回一个Response对象。
- 执行Call对象的`enqueue()`方法，不会阻塞当前线程，该方法接收一个Callback对象，当异步获取到数据之后，会回调执行Callback对象的相应方法。如果请求成功，则执行Callback对象的onResponse方法，并将Response对象传入该方法中；如果请求失败，则执行Callback对象的onFailure方法。

### Response 

Response类封装了响应报文信息：状态吗（200、404等）、响应头（Content-Type、Server等）以及可选的响应体。可以通过Call对象的`execute()`方法获得Response对象，异步回调执行Callback对象的onResponse方法时也可以获取Response对象。

## 同步get请求

```
    /**
     * 同步的get请求，不常用
     *
     * @param url
     * @return
     */
    public String syncGetByUrl(String url) {
        Request request = new Request.Builder().url(url).build();
        Response response = null;
        try {
            response = mOkHttpClient.newCall(request).execute();
            if (response.isSuccessful()) {
                return response.body().string();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        return null;
    }
```

核心就是调用execute方法

下面对以上代码进行简单说明：

- client执行newCall方法会得到一个Call对象，表示一个新的网络请求。

- Call对象的execute方法是同步方法，会阻塞当前线程，其返回Response对象。

- 通过Response对象的`isSuccessful()`方法可以判断请求是否成功。

- 通过Response的`headers()`方法可以得到响应头[Headers](http://square.github.io/okhttp/3.x/okhttp/okhttp3/Headers.html)对象，可以通过for循环索引遍历所有的响应头的名称和值。可以通过`Headers.name(index)`方法获取响应头的名称，通过`Headers.value(index)`方法获取响应头的值。

- 除了索引遍历，通过`Headers.get(headerName)`方法也可以获取某个响应头的值，比如通过headers.get(“Content-Type”)获得服务器返回给客户端的数据类型。但是服务器返回给客户端的响应头中有可能有多个重复名称的响应头，比如在某个请求中，服务器要向客户端设置多个Cookie，那么会写入多个`Set-Cookie`响应头，且这些`Set-Cookie`响应头的值是不同的，访问百度首页，可以看到有7个`Set-Cookie`的响应头，如下图所示：

  ![这里写图片描述](http://img.blog.csdn.net/20160614225714171)

  为了解决同时获取多个name相同的响应头的值，`Headers`中提供了一个`public List values(String name)`方法，该方法会返回一个`List`对象，所以此处通过Headers对象的values(‘Set-Cookie’)可以获取全部的Cookie信息，如果调用Headers对象的get(‘Set-Cookie’)方法，那么只会获取最后一条Cookie信息。

- 通过Response对象的`body()`方法可以得到响应体[ResponseBody](http://square.github.io/okhttp/3.x/okhttp/okhttp3/ResponseBody.html)对象，调用其`string()`方法可以很方便地将响应体中的数据转换为字符串，该方法会将所有的数据放入到内存之中，所以如果数据超过1M，最好不要调用`string()`方法以避免占用过多内存，这种情况下可以考虑将数据当做Stream流处理。

## 异步get请求

```
  /**
     * 异步请求指定的url，并且返回的是json字符串
     *
     * @param url
     * @param callBack
     */
    public void asyncGetJsonStringByUrl(String url, final SuccessJsonString callBack) {
        final Request request = new Request.Builder().url(url).build();
        mOkHttpClient.newCall(request).enqueue(new Callback() {
            @Override
            public void onFailure(Call call, IOException e) {
                e.printStackTrace();
            }

            @Override
            public void onResponse(Call call, Response response) throws IOException {
                if (response != null && response.isSuccessful()) {
                    onSuccessJsonString(response.body().string(), callBack);
                }
            }
        });
    }
```

就是调用enquence方法

## okhttp的封装

```
package win.haotinayi.okhttp;

import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.os.Handler;
import android.os.Looper;
import android.view.View;

import org.json.JSONObject;

import java.io.IOException;
import java.util.Map;

import okhttp3.Call;
import okhttp3.Callback;
import okhttp3.FormBody;
import okhttp3.MediaType;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.RequestBody;
import okhttp3.Response;

/**
 * @author HaoTianYi hao.ty@haotianyi.win
 * @version v1.0
 * @des 实现上传可下载的工具类
 * @time 2016/10/1 15:51
 */

public class OkManger {
    private OkHttpClient mOkHttpClient;
    //  防止多个线程同时访问造成异常,并且使用单例模式
    private volatile static OkManger mOkManger;
    private Handler mHandler;
    //  提交json数据
    private static final MediaType JSON = MediaType.parse("application/json;charset=utf-8");
    //  提交字符串
    private static final MediaType MEDIA_TYPE_MARKDOWN = MediaType.parse("text/x-markdwon;charset=utf-8");

    private OkManger() {
        mOkHttpClient = new OkHttpClient();
        mHandler = new Handler(Looper.getMainLooper());
    }

    //使用单例模式，注意私有的构造方法，公共的获取实例方法
    public static OkManger getInstance() {
        OkManger instance = null;
        if (mOkManger == null) {
            synchronized (OkManger.class) {
                if (instance == null) {
                    instance = new OkManger();
                    mOkManger = instance;
                }
            }
        }
        return instance;
    }

    /**
     * 同步的get请求，不常用
     *
     * @param url
     * @return
     */
    public String syncGetByUrl(String url) {
        Request request = new Request.Builder().url(url).build();
        Response response = null;
        try {
            response = mOkHttpClient.newCall(request).execute();
            if (response.isSuccessful()) {
                return response.body().string();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        return null;
    }

    /**
     * 返回的结果是json字符串
     *
     * @param jsonValue
     * @param callBack
     */
    private void onSuccessJsonString(final String jsonValue, final SuccessJsonString callBack) {
        mHandler.post(new Runnable() {
            @Override
            public void run() {
                if (callBack != null) {
                    try {
                        callBack.onResponse(jsonValue);
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                }
            }
        });
    }

    /**
     * 异步请求指定的url，并且返回的是json字符串
     *
     * @param url
     * @param callBack
     */
    public void asyncGetJsonStringByUrl(String url, final SuccessJsonString callBack) {
        final Request request = new Request.Builder().url(url).build();
        mOkHttpClient.newCall(request).enqueue(new Callback() {
            @Override
            public void onFailure(Call call, IOException e) {
                e.printStackTrace();
            }

            @Override
            public void onResponse(Call call, Response response) throws IOException {
                if (response != null && response.isSuccessful()) {
                    onSuccessJsonString(response.body().string(), callBack);
                }
            }
        });
    }

    /**
     * 异步请求指定的url，并且返回的是jsonObject
     *
     * @param url
     * @param callBack
     */
    public void asyncGetJsonObjectByUrl(String url, final SuccessJsonObject callBack) {
        final Request request = new Request.Builder().url(url).build();
        mOkHttpClient.newCall(request).enqueue(new Callback() {
            @Override
            public void onFailure(Call call, IOException e) {
                e.printStackTrace();
            }

            @Override
            public void onResponse(Call call, Response response) throws IOException {
                if (response != null && response.isSuccessful()) {
                    onSuccessJosnObject(response.body().string(), callBack);
                }
            }
        });
    }

    /**
     * 返回值是jsonObject
     *
     * @param josnValue
     * @param callBack
     */
    private void onSuccessJosnObject(final String josnValue, final SuccessJsonObject callBack) {
        mHandler.post(new Runnable() {
            @Override
            public void run() {
                if (callBack != null) {
                    try {
                        callBack.onResponse(new JSONObject(josnValue));
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                }
            }
        });
    }

    /**
     * 返回的结果是byte[]字符串
     *
     * @param data
     * @param callBack
     */
    private void onSuccessBytes(final byte[] data, final SuccessBytes callBack) {
        mHandler.post(new Runnable() {
            @Override
            public void run() {
                if (callBack != null) {
                    try {
                        callBack.onResponse(data);
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                }
            }
        });
    }

    /**
     * 异步请求指定的url，并且返回的是byte[]
     *
     * @param url
     * @param callBack
     */
    public void asyncGetByteByUrl(String url, final SuccessBytes callBack) {
        final Request request = new Request.Builder().url(url).build();
        mOkHttpClient.newCall(request).enqueue(new Callback() {
            @Override
            public void onFailure(Call call, IOException e) {
                e.printStackTrace();
            }

            @Override
            public void onResponse(Call call, Response response) throws IOException {
                if (response != null && response.isSuccessful()) {
                    onSuccessBytes(response.body().bytes(), callBack);
                }
            }
        });
    }

    /**
     * 异步请求指定的url，并且返回的是bitmap
     *
     * @param url
     * @param callBack
     */
    public void asyncGetImageByUrl(String url, final SuccessBitmap callBack) {
        final Request request = new Request.Builder().url(url).build();
        mOkHttpClient.newCall(request).enqueue(new Callback() {
            @Override
            public void onFailure(Call call, IOException e) {
                e.printStackTrace();
            }

            @Override
            public void onResponse(Call call, Response response) throws IOException {
                if (response != null && response.isSuccessful()) {
                    Bitmap bitmap = BitmapFactory.decodeByteArray(response.body().bytes(), 0, response.body().bytes().length);
                    callBack.onResponse(bitmap);
                }
            }
        });
    }

    /**
     * 异步请求指定的url，并且返回的是post
     *
     * @param url
     * @param params
     * @param callBack
     */
    public void sendForm(String url, Map<String, String> params, final SuccessJsonObject callBack) {
        FormBody.Builder formBody = new FormBody.Builder();
        if (params != null && !params.isEmpty()) {
            for (Map.Entry<String, String> param : params.entrySet()) {
                formBody.add(param.getKey(), param.getValue());
            }
        }
        RequestBody requsetBody = formBody.build();
        Request request = new Request.Builder().url(url).post(requsetBody).build();
        mOkHttpClient.newCall(request).enqueue(new Callback() {
            @Override
            public void onFailure(Call call, IOException e) {
                e.printStackTrace();
            }

            @Override
            public void onResponse(Call call, Response response) throws IOException {
                if (response != null && response.isSuccessful()) {
                    onSuccessJosnObject(response.body().string(), callBack);
                }
            }
        });

    }


    //真正去处理json的接口
    public interface SuccessJsonString {
        void onResponse(String result);
    }

    //真正去处理返回值是jsonObject的接口
    public interface SuccessJsonObject {
        void onResponse(JSONObject result);
    }

    //真正去处理返回值是byte[]的接口
    public interface SuccessBytes {
        void onResponse(byte[] result);
    }

    public interface SuccessBitmap {
        void onResponse(Bitmap bitmap);
    }
}

```