---
title: 玩转EventBus,详解其使用
date: 2015-11-18 12:39:04
tags:
- EventBus
categories: 
- 技术博客
---
### 概述
**EventBus**是一款针对Android优化的发布/订阅（publish/subscribe）事件总线。主要功能是替代Intent,Handler,BroadCast在Fragment，Activity，Service，线程之间传递消息。简化了应用程序内各组件间、组件与后台线程间的通信。优点是开销小，代码更优雅。以及将发送者和接收者解耦。比如请求网络，等网络返回时通过Handler或Broadcast通知UI，两个Fragment之间需要通过Listener通信，这些需求都可以通过**EventBus**实现。

**EventBus**作为一个消息总线，有三个主要的元素：

* Event：事件。可以是任意类型的对象

* Subscriber：事件订阅者，接收特定的事件。在EventBus中，使用约定来指定事件订阅者以简化使用。即所有事件订阅都都是以onEvent开头的函数，具体来说，函数的名字是onEvent，onEventMainThread，onEventBackgroundThread，onEventAsync这四个，这个和ThreadMode（下面讲）有关。

* Publisher：事件发布者，用于通知 Subscriber 有事件发生。可以在任意线程任意位置发送事件，直接调用 eventBus.post(Object) 方法，可以自己实例化 EventBus 对象，但一般使用默认的单例就好了：EventBus.getDefault()， 根据 post 函数参数的类型，会自动调用订阅相应类型事件的函数。

### 关于ThreadMode
前面说了，Subscriber的函数只能是那4个，因为每个事件订阅函数都是和一个**ThreadMode**相关联的，ThreadMode指定了会调用的函数。有以下四个ThreadMode：

* PostThread：事件的处理在和事件的发送在相同的进程，所以事件处理时间不应太长，不然影响事件的发送线程，而这个线程可能是UI线程。对应的函数名是onEvent。

* MainThread: 事件的处理会在UI线程中执行。事件处理时间不能太长，这个不用说的，长了会ANR的，对应的函数名是onEventMainThread。

* BackgroundThread：事件的处理会在一个后台线程中执行，对应的函数名是onEventBackgroundThread，虽然名字是BackgroundThread，事件处理是在后台线程，但事件处理时间还是不应该太长，因为如果发送事件的线程是后台线程，会直接执行事件，如果当前线程是UI线程，事件会被加到一个队列中，由一个线程依次处理这些事件，如果某个事件处理时间太长，会阻塞后面的事件的派发或处理。

* Async：事件处理会在单独的线程中执行，主要用于在后台线程中执行耗时操作，每个事件会开启一个线程（有线程池），但最好限制线程的数目。

根据事件订阅都函数名称的不同，会使用不同的ThreadMode，比如果在后台线程加载了数据想在UI线程显示，订阅者只需把函数命名为onEventMainThread。
<!--more-->
#### 对相应的函数名，进一步解释一下：

**onEvent:**如果使用onEvent作为订阅函数，那么该事件在哪个线程发布出来的，onEvent就会在这个线程中运行，也就是说发布事件和接收事件线程在同一个线程。使用这个方法时，在onEvent方法中不能执行耗时操作，如果执行耗时操作容易导致事件分发延迟。

**onEventMainThread:**如果使用onEventMainThread作为订阅函数，那么不论事件是在哪个线程中发布出来的，onEventMainThread都会在UI线程中执行，接收事件就会在UI线程中运行，这个在Android中是非常有用的，因为在Android中只能在UI线程中跟新UI，所以在onEvnetMainThread方法中是不能执行耗时操作的。

**onEventBackground:**如果使用onEventBackgrond作为订阅函数，那么如果事件是在UI线程中发布出来的，那么onEventBackground就会在子线程中运行，如果事件本来就是子线程中发布出来的，那么onEventBackground函数直接在该子线程中执行。

**onEventAsync：**使用这个函数作为订阅函数，那么无论事件在哪个线程发布，都会创建新的子线程在执行onEventAsync。

### 基本用法

**引入EventBus:**

```
compile 'org.greenrobot:eventbus:3.0.0'
```

**定义事件:**

```
public class MessageEvent { /* Additional fields if needed */ }
```

**注册事件接收者：**

```
eventBus.register(this);
```

**发送事件:**

```
eventBus.post(event)
```

**接收消息并处理:**

```
public void onEvent(MessageEvent event) {}
```

**注销事件接收：**

```
eventBus.unregister(this);
```

最后，proguard 需要做一些额外处理:

```
 #EventBus
 -keepclassmembers class ** {
    public void onEvent*(**);
    void onEvent*(**);
 }
```

### 实战举例
#### 关于Activity之间的通信
第一步：自定义一个定义Event事件，用来封装信息
例子如下：
``` 
public class UserEvent {
    
    private String userName;

    public UserEvent() {
    }

    public UserEvent(String userName) {
        this.userName = userName;
    }

    public String getUserName() {
        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }
}

```
第二步：注册订阅者和定义处理方法
```
public class MainActivity extends Activity {
    private Button btn, fragment_btn;
    private TextView service_tv;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
		//注册订阅者
        EventBus.getDefault().register(this);
        btn = (Button) findViewById(R.id.btn);
        btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                // TODO Auto-generated method stub
                Intent intent = new Intent(MainActivity.this,
                        SecondActivity.class);
                startActivity(intent);
            }
        });
        fragment_btn = (Button) findViewById(R.id.fragment_btn);
        fragment_btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(MainActivity.this,
                        FragmenTestActivity.class);
                startActivity(intent);
            }
        });
        service_tv = (TextView) findViewById(R.id.service_tv);
        startService(new Intent(this, EventTestService.class));
    }
	
	//定义处理接收方法
    @Subscribe
    public void onEventMainThread(UserEvent event) {
        btn.setText(event.getUserName());
        service_tv.setText(event.getUserName());
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        EventBus.getDefault().unregister(this);
    }


}
```
第三步：发送事件
```
public class SecondActivity extends Activity {
    private Button btn;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_second);
        btn = (Button) findViewById(R.id.btn);
        btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                final UserEvent ue = new UserEvent();
                ue.setUserName("非著名程序员");
                EventBus.getDefault().post(ue);
                finish();
            }
        });
    }


}
```

fragment,service等之间的通信和activity之间的基本一样，就不过多的贴代码解释了，感兴趣的同学可以直接下载demo，里面有它们之间的通信。

**使用EventBus应该注意以下几点：**

1. 同一个onEvent函数不能被注册两次，所以不能在一个类中注册同时还在父类中注册。
2. 消息的接收是根据参数中的类名来决定执行哪一个接收处理方法的。即：订阅者的处理方法是根据订阅事件的类型来确定订阅函数的。
3. 每个事件可以有多个订阅者。
4. 当Post一个事件时，这个事件类的父类的事件也会被Post。
5. 所有事件处理方法必需是public void类型的，并且只有一个参数表示EventType。

在这里我把demo分享出去，demo里包含了Activity之间的通信，fragment之间的通信和service与activity之间的通信。
下载地址：[http://7xsgef.com1.z0.glb.clouddn.com/EventBusDemo2.zip](http://7xsgef.com1.z0.glb.clouddn.com/EventBusDemo2.zip "http://7xsgef.com1.z0.glb.clouddn.com/EventBusDemo2.zip")

