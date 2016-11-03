---
title: Android Material Design系列之CoordinatorLayout，CollapsingToolbarLayout等
date: 2016-07-09 12:39:04
tags:
- Material Design
- CoordinatorLayout
- CollapsingToolbarLayout
- Toolbar
- TabLayout
categories: 
- 技术博客
- Material Design
---
>这篇文章其实我一直在想，是写还是不写，因为关于讲 CoordinatorLayout，AppBarLayout，CollapsingToolbarLayout，Toolbar，TabLayout 等这些控件的使用，以前写过一篇，那就是《通过来模仿稀土掘金个人页面的布局来学习使用CoordinatorLayout 》，一直关注和看公众号的关注者应该看到过，上一篇的推送也特别讲了 CoordinatorLayout 的其他用法，看过的人都说还能这么用啊？哈哈……这次写这篇是对我上一篇《通过来模仿稀土掘金个人页面的布局来学习使用CoordinatorLayout 》遗留问题的加强和修改。

这次关于 CoordinatorLayout，AppBarLayout，CollapsingToolbarLayout，Toolbar，TabLayout 等这些控件的用法我就不讲了，不懂的或者不清楚的就去看《通过来模仿稀土掘金个人页面的布局来学习使用CoordinatorLayout 》这篇文章，因为这次我们主讲怎么把上次大家给我提问的问题进行修改和加强，优化等。
<!--more-->
### 问题
上次关于仿稀土掘金的文章发出后，我看到有人在微信后台跟我留言，也有人在github上给我提问题，大约问的最多的有三个问题。
* 原图是沉浸状态栏，你的这个模仿的效果图没有做到。
* 原图加载毛玻璃的效果比较快，你的这个打开app白屏好几秒。
* 原图fragment中有列表，你的没有，为什么我放上ListView之后，标题栏不能折叠等等

现在我们就从以上三个问题，进行修改和加强，优化，在讲之前，先看效果图对比，如果感觉好，你们再继续往下看。

#### 稀土掘金原图
![](https://raw.githubusercontent.com/loonggg/CoordinatorLayoutDemo/master/image/1.gif)

#### 之前的模仿效果图
![](https://raw.githubusercontent.com/loonggg/MaterialDesignDemo/master/image/%E4%B9%8B%E5%89%8D%E6%A8%A1%E4%BB%BF%E7%9A%84%E6%95%88%E6%9E%9C%E5%9B%BE.gif)

#### 增强，修改，优化后的效果图
![](https://raw.githubusercontent.com/loonggg/CoordinatorLayoutDemo/master/image/cld.gif)

### 解决方法

#### 第一个问题
大致的方法过程就是如下：
* 配置window的各项参数，使状态栏透明
```java
    /**
     * 使状态栏透明
     */
    @TargetApi(Build.VERSION_CODES.KITKAT)
    private static void transparentStatusBar(Activity activity) {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
            activity.getWindow().addFlags(WindowManager.LayoutParams.FLAG_DRAWS_SYSTEM_BAR_BACKGROUNDS);
            activity.getWindow().clearFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
            activity.getWindow().addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_NAVIGATION);
            activity.getWindow().setStatusBarColor(Color.TRANSPARENT);
        } else {
            activity.getWindow().addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
        }
    }
```

* 设置根布局的参数
```java
    /**
     * 设置根布局参数
     */
    private static void setRootView(Activity activity) {
        ViewGroup rootView = (ViewGroup) ((ViewGroup) activity.
        findViewById(android.R.id.content)).getChildAt(0);
        rootView.setFitsSystemWindows(true);
        rootView.setClipToPadding(true);
    }
```

* 添加半透明矩形条
```java
    /**
     * 添加半透明矩形条
     *
     * @param activity       需要设置的 activity
     * @param statusBarAlpha 透明值
     */
    private static void addTranslucentView(Activity activity, int statusBarAlpha) {
        ViewGroup contentView = (ViewGroup) activity.findViewById(android.R.id.content);
        // 移除半透明矩形,以免叠加
        if (contentView.getChildCount() > 1) {
            contentView.getChildAt(1).setBackgroundColor(Color.argb(statusBarAlpha, 0, 0, 0));
        } else {
            contentView.addView(createTranslucentStatusBarView(activity, statusBarAlpha));
        }
    }
```

大致的步骤就是这样，我这里用的是别人封装好的，github地址是：https://github.com/laobie/StatusBarUtil 大家可以去参考使用。

#### 第二个问题
以前的毛玻璃效果，是我从网上找的一个毛玻璃算法，然后搞出来的，确实很慢，然后我在网上找更快的加载的速度的算法，找来找去，网上都是那几种，最快的也得白屏四秒，最后终于搞定了一个快的，真的是脑子不灵活了，光想着毛玻璃了，不是glide也支持毛玻璃，支持模糊效果吗？一用它确实快。就是这么解决的。怎么使用的，我相信用过glide的都会吧，如果你没用过，就去下载源码吧，里面写的非常简单。

#### 第三个问题
CoordinatorLayout协同布局在一般只结合RecyclerView和NestedScrollView使用，所以你用ListView没有效果，如果你硬要用ListView那就比较麻烦了，倒不是做不到。你去分析源码可以知道RecyclerView和NestedScrollView它们都实现了一个接口NestedScrollingChild，所以我们可以自定义ListView，实现NestedScrollingChild接口就好。你也可以使用listView.setNestedScrollingEnabled(true)也行，也能做到，但是貌似这两种方法只能支持在android5.0以上的手机才能用，所以还是建议大家使用RecyclerView就没那么多事了。

这就是Material Design系列第六篇之《Android Material Design系列之CoordinatorLayout，CollapsingToolbarLayout等》。

demo的github地址：https://github.com/loonggg/MaterialDesignDemo 去star吧，我会慢慢完善的。

