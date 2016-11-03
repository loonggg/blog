---
title: Retrofit初探和使用
date: 2015-12-22 12:39:04
tags:
- Retrofit
categories: 
- 技术博客
---
## Retrofit简单介绍
Retrofit是Square提供的开源产品，为Android平台的应用提供一个类型安全的REST客户端。它是基于注解，提供 JSON to POJO（Plain Ordinary Java Object ，简单 Java 对象），POJO to JSON，网络请求（POST，GET， PUT，DELETE 等）封装。

Retrofit 虽然是一套注解形的网络请求封装库，但是它的强大在于让代码结构更给为清晰，它可以直接解析JSON数据变成JAVA对象，甚至支持回调操作，处理不同的结果。

官方文档地址：http://square.github.io/retrofit/

github地址：https://github.com/square/retrofit


## 基本使用
#### 添加依赖
在AndroidStudio的项目中，在build.gradle文件中添加以下引用：
```
compile 'com.squareup.retrofit2:retrofit:2.0.1'
compile 'com.squareup.retrofit2:converter-gson:2.0.0-beta3'
compile 'com.google.code.gson:gson:2.3'
```
<!--more-->
#### 数据格式的相关使用
今天我们使用聚合数据平台的免费的一个笑话的接口，数据的返回格式是json，所以我们需要根据相应的json数据，建立自己的javabean。

在建立实体类之前，我们截图，先让大家看一下测试的接口。

###### API接口
![](http://7xsgef.com1.z0.glb.clouddn.com/canvas.png)

通过接口我们可以看到以下信息：
主机地址：http://japi.juhe.cn
接口地址：/joke/content/list.from
数据格式为json

###### 根据json数据建立自己的javabean
JokeInfo.class实体类如下：
```
package com.loonggg.retrofitdemo;

import java.util.List;

/**
 * Created by loongggdroid on 2016/4/14.
 */
public class JokeInfo {

    /**
     * error_code : 0
     * reason : Success
     * result : {"data":[{"content":"女生分手的原因有两个，\r\n一个是：闺蜜看不上。另一个是：闺蜜看上了。","hashId":"607ce18b4bed0d7b0012b66ed201fb08","unixtime":1418815439,"updatetime":"2014-12-17 19:23:59"},{"content":"老师讲完课后，问道\r\n\u201c同学们，你们还有什么问题要问吗？\u201d\r\n这时，班上一男同学举手，\r\n\u201c老师，这节什么课？\u201d","hashId":"20670bc096a2448b5d78c66746c930b6","unixtime":1418814837,"updatetime":"2014-12-17 19:13:57"}]}
     */

    private int error_code;
    private String reason;
    private ResultBean result;

    public int getError_code() {
        return error_code;
    }

    public void setError_code(int error_code) {
        this.error_code = error_code;
    }

    public String getReason() {
        return reason;
    }

    public void setReason(String reason) {
        this.reason = reason;
    }

    public ResultBean getResult() {
        return result;
    }

    public void setResult(ResultBean result) {
        this.result = result;
    }

    public static class ResultBean {
        /**
         * content : 女生分手的原因有两个，
         * 一个是：闺蜜看不上。另一个是：闺蜜看上了。
         * hashId : 607ce18b4bed0d7b0012b66ed201fb08
         * unixtime : 1418815439
         * updatetime : 2014-12-17 19:23:59
         */

        private List<DataBean> data;

        public List<DataBean> getData() {
            return data;
        }

        public void setData(List<DataBean> data) {
            this.data = data;
        }

        public static class DataBean {
            private String content;
            private String hashId;
            private int unixtime;
            private String updatetime;

            public String getContent() {
                return content;
            }

            public void setContent(String content) {
                this.content = content;
            }

            public String getHashId() {
                return hashId;
            }

            public void setHashId(String hashId) {
                this.hashId = hashId;
            }

            public int getUnixtime() {
                return unixtime;
            }

            public void setUnixtime(int unixtime) {
                this.unixtime = unixtime;
            }

            public String getUpdatetime() {
                return updatetime;
            }

            public void setUpdatetime(String updatetime) {
                this.updatetime = updatetime;
            }
        }
    }
}

```
#### 定义服务接口
因为是获取笑话的一个接口，我给它命名为：JokeService
```
package com.loonggg.retrofitdemo;

import retrofit2.Call;
import retrofit2.http.GET;
import retrofit2.http.Query;

/**
 * Created by loongggdroid on 2016/4/14.
 */
public interface JokeService {
    @GET("/joke/content/list.from")
    Call<JokeInfo> getJokeInfoList(@Query("sort") String sort, @Query("page") int page, @Query("pagesize") int pagesize, @Query("time") String time, @Query("key") String key);
}
```

注解：
@GET 是get的请求方式
@Query 是接口查询的关键字

#### 封装接口服务的包装类
这个接口服务的包装类使用了单例模式，如下：
```
package com.loonggg.retrofitdemo;

import android.content.Context;

import retrofit2.GsonConverterFactory;
import retrofit2.Retrofit;

/**
 * Created by loongggdroid on 2016/4/13.
 */
public class RetrofitWrapper {
    private static RetrofitWrapper instance;
    private Context mContext;
    private Retrofit retrofit;

    private RetrofitWrapper() {
        retrofit = new Retrofit.Builder().baseUrl(Constant.BASE_URL).addConverterFactory(GsonConverterFactory.create()).build();
    }

    public static RetrofitWrapper getInstance() {
        if (instance == null) {
            synchronized (RetrofitWrapper.class) {
                instance = new RetrofitWrapper();
            }
        }
        return instance;
    }

    public <T> T create(Class<T> service) {
        return retrofit.create(service);
    }

    public class Constant {
        public static final String BASE_URL = "http://api2.juheapi.com";
    }
}
```

#### 如何调用
咱们先看MainActivity中的代码，如下：
```
package com.loonggg.retrofitdemo;

import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.widget.Toast;


import retrofit2.Call;
import retrofit2.Callback;
import retrofit2.Response;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        JokeService jokeService = RetrofitWrapper.getInstance().create(JokeService.class);
        Call<JokeInfo> call = jokeService.getJokeInfoList("desc", 1, 2, "1418816972", "eb46c85bea73462583e38b84c3a25c4b");
        call.enqueue(new Callback<JokeInfo>() {
            @Override
            public void onResponse(Call<JokeInfo> call, Response<JokeInfo> response) {
                JokeInfo ji = response.body();
                //这里显示了列表中的第一条的内容，所以get(0)
                Toast.makeText(MainActivity.this, ji.getResult().getData().get(0).getContent(), Toast.LENGTH_LONG).show();
            }

            @Override
            public void onFailure(Call<JokeInfo> call, Throwable t) {
                Toast.makeText(MainActivity.this, t.getMessage(), Toast.LENGTH_LONG).show();
            }
        });

    }
}
```
###### 进一步解释
1. 首先调用我们封装的网络库，创建retrofit和对应的接口服务jokeService。
2. 然后调用我们的请求接口方法getJokeInfoList（）;
3. 发送请求，并回调处理结果，call.enqueue();

到这里基本结束了，是不是非常简单？赶紧试试去吧。


