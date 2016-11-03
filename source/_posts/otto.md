---
title: 浅析Otto框架，并与EventBus对比
date: 2016-01-22 12:39:04
tags:
- otto
categories: 
- 技术博客
---
>前两天在公众号里发了一篇有关EventBus的文章《玩转EventBus,详解其使用》，有读者和开发者反馈说没有OTTO好用。确实是，各有优缺点吧，那今天就有必要再讲一下Otto事件框架。

OTTO是Square推出的基于Guava项目的Android支持库，otto是一个事件总线，用于应用程序的不同组件之间进行有效的通信。OTTO是基于Observer的设计模式。它有发布者，订阅者这两个主要对象。OTTO的最佳实践就是通过反射牺牲了微小的性能，同时极大的降低了程序的耦合度。

Otto 官网： http://square.github.io/otto/

## Why和应用场景
#### 1. Why
Otto框架的主要功能是帮助我们来降低多个组件通信之间的耦合度的（解耦）。

#### 2. 应用场景
比如：由界面 A 跳转到界面 B ，然后点击 B 中的 button, 现在要更新 界面 A 的视图。再比如：界面有一个 界面 A,A 里面的有个 Fragment, 点击 Fragment 中的一个 button,跳转到界面 B, 点击界面 B的 button 要更新界面 A 的 Fragment 的视图，等等。

我们可以看出上面举例的两种场景，以前可以用startActivityForResult 和 interface 的方式实现的话,会比较麻烦,并且产生了很多的状态判断和逻辑判断,并且可能产生很多不必要的 bug, 代码量也比较大和繁琐,使用 otto 就可以能容易的避免这些问题。
<!--more-->
## 基本用法
#### 引入Otto
```
dependencies {
  compile 'com.squareup:otto:1.3.8'
}
```

#### 定义事件:
```
public class MessageEvent { /* Additional fields if needed */ }
```

#### 订阅和取消订阅
```
bus.register(this);
```

```
bus.unregister(this);
```

#### 发布:
```
bus.post(new MessageEvent());
```

#### 注解
@Subscribe:这个在调用了register后有效,表示订阅了一个事件,并且方法的用 public 修饰的.方法名可以随意取,重点是参数,它是根据你的参数进行判断

@Produce注解告诉Bus该函数是一个事件产生者，产生的事件类型为该函数的返回值。

#### 最后，proguard 需要做一些额外处理，防止混淆:
```
-keepattributes *Annotation*
-keepclassmembers class ** {
    @com.squareup.otto.Subscribe public *;
    @com.squareup.otto.Produce public *;
}
```

## 实际例子
### 首先实现一个Bus的单例
```java
package com.loonggg.ottodemo;

import com.squareup.otto.Bus;

public final class BusProvider {
    private static final Bus BUS = new Bus();

    public static Bus getInstance() {
        return BUS;
    }

    private BusProvider() {
    }
}

```

### 其次是自定义一个定义Event事件，用来封装信息
```java
package com.loonggg.ottodemo;

public class MessageEvent {
    public String msg;

    public MessageEvent(String msg) {
        this.msg = msg;
    }
}

```
### 再次是MainActivity，订阅事件
```java
public class MainActivity extends Activity {
    private Button btn;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_second);
        BusProvider.getInstance().register(this);
        btn = (Button) findViewById(R.id.btn_two);
        btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                startActivity(new Intent(SecondActivity.this, ThreeActivity.class));
            }
        });

    }
	//这个注解一定要有,表示订阅了MessageEvent,并且方法的用 public 修饰的.方法名可以随意取,重点是参数,它是根据你的参数进行判断来自于哪个发送的事件
    @Subscribe
    public void showEvent(MessageEvent event) {
        btn.setText(event.msg);
    }


    @Override
    protected void onDestroy() {
        super.onDestroy();
        BusProvider.getInstance().unregister(this);
    }
}
```

### 最后事发送订阅事件
```java
public class ThreeActivity extends Activity {
    private Button btn;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_three);
        btn = (Button) findViewById(R.id.btn);
        btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                BusProvider.getInstance().post(produceMessageEvent());
				//或者这样用也行
				//BusProvider.getInstance().post(new MessageEvent("非著名程序员"));
                finish();
            }
        });

    }

    @Produce
    public MessageEvent produceMessageEvent() {
        return new MessageEvent("非著名程序员");
    }

//    @Override
//    protected void onResume() {
//        super.onResume();
//        BusProvider.getInstance().register(this);
//    }
//
//    @Override
//    protected void onPause() {
//        super.onPause();
//        BusProvider.getInstance().unregister(this);
//    }
}
```

## 总结
通过例子我们可以发现，其实事件发布者不用@Produce注解和注册事件也可以发布消息。但是你要Subscribe订阅事件就一定要register这个类了,否则是接受不到事件的。

## 与EventBus的对比

从事件订阅的处理差别来看：

1、eventbus是采用反射的方式对整个注册的类的所有方法进行扫描来完成注册；

2、otto采用了注解的方式完成注册；

3、共同的地方缓存所有注册并有可用性的检测。同时可以移除注册;

4、注册的共同点都是采用method方法进行一个集成。

在otto更多使用场景应该就是在主线程中，因为它内部没有异步线程的场景。（也许是它自身的定位不一样，它就是为了解决UI的通信机制。所以出发点就是轻量级）在代码中主要体现这一特色的地方就是在接口ThreadEnforcer以及内部的实现域ANY和MAIN。在MAIN内部有一个是否是主线程的检查，而ANY不做任何检查的事情。 

EventBus在3.0以前，还需要根据四种线程模式分别对应固定接收方法，而OTTO则可以通过注解的方法自定义方法，比较方便，但是EventBus在3.0也实现了通过注解自定义方法了。而otto介绍上不管是订阅者还是发送者都需要注册事件，但是我发现现在发送者不用注册也可以发送了。

每个框架都有自己的特点，我们开发者必须明白每个框架的出发点才能更好的使用，没有哪个框架好不好的问题，只要开发者自己使用哪个舒服，哪个就是最好的。适合自己的才是最好的。

>最后我想说，可能EventBus和Otto很早以前就有了，现在RxJava就能实现这样的功能，但是对于不了解Rx技术的人来说，这些还是非常有用的，Rx技术虽好，虽然很新，如果没有搞懂的情况下，贸然使用估计会给你带来很大的困难。最好在有一个比较懂Rx技术的人的前提下，开始使用，提高自己。

