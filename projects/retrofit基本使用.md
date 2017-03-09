# retrofit基本使用

[TOC]

## 简介

Retrofit是Square公司开发的一款针对Android网络请求的框架，Retrofit2底层基于OkHttp实现的

使用注解的方式来进行数据的绑定，它有几个重要的概念：

- 自定义的接口文件

```
    @GET("get")
    Call<Phone> getPhone(@Query("phone") int phone,@Query("key") String key);
```

它的作用大致就是：http://apis.juhe.cn/mobile/get&phone=1363246&dtype=&key=d8bdd88f4a5cff5bf23a3577ebd31339

这个网络请求中get后面的参数&的具体数据，通过键值对的方式完成get请求

- javaBean文件

用于解析返回的json文件，从而得到具体的数据

## 使用

它内部有自己的gson和封装好的框架，基本实现只要两个：

```
    compile 'com.squareup.retrofit2:retrofit:2.2.0'
    compile 'com.squareup.retrofit2:converter-gson:2.2.0'
```

### 指定接口文件和bean

```
public interface IBook {
    @GET("get")
    Call<Phone> getPhone(@Query("phone") int phone,@Query("key") String key);
}

----------------

public class Phone {

    /*
    "resultcode":"200",
"reason":"Return Successd!",
"result":{
    "province":"浙江",
    "city":"杭州",
    "areacode":"0571",
    "zip":"310000",
    "company":"中国移动",
    "card":"移动动感地带卡"
}
    */

    private String resultcode;
    private String reason;
    private Result result;

    public String getResultcode() {
        return resultcode;
    }

    public void setResultcode(String resultcode) {
        this.resultcode = resultcode;
    }

    public String getReason() {
        return reason;
    }

    public void setReason(String reason) {
        this.reason = reason;
    }

    public Result getResult() {
        return result;
    }

    public void setResult(Result result) {
        this.result = result;
    }

    public class Result {
        private String province;
        private String city;
        private String areacode;
        private String zip;
        private String company;
        private String card;

        public String getProvince() {
            return province;
        }

        public void setProvince(String province) {
            this.province = province;
        }

        public String getCity() {
            return city;
        }

        public void setCity(String city) {
            this.city = city;
        }

        public String getAreacode() {
            return areacode;
        }

        public void setAreacode(String areacode) {
            this.areacode = areacode;
        }

        public String getZip() {
            return zip;
        }

        public void setZip(String zip) {
            this.zip = zip;
        }

        public String getCompany() {
            return company;
        }

        public void setCompany(String company) {
            this.company = company;
        }

        public String getCard() {
            return card;
        }

        public void setCard(String card) {
            this.card = card;
        }
    }
}
```

### 主体请求

```
       Retrofit retrofit = new Retrofit.Builder().baseUrl("http://apis.juhe.cn/mobile/")
                .addConverterFactory(GsonConverterFactory.create())
                .build();
        IBook iBook = retrofit.create(IBook.class);
        Call<Phone> phoneCall = iBook.getPhone(1363246, "d8bdd88f4a5cff5bf23a3577ebd31339");
        phoneCall.enqueue(new Callback<Phone>() {
            @Override
            public void onResponse(Call<Phone> call, Response<Phone> response) {
                System.out.println(response.body().getResultcode());
                Log.e("ty", "onResponse: " + response.body().getResult().getCity());
            }

            @Override
            public void onFailure(Call<Phone> call, Throwable t) {
                Log.e("ty", "onResponse: ", t);
            }
        });
```

