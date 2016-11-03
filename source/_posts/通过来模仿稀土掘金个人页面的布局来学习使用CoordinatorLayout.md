---
title: 通过来模仿稀土掘金个人页面的布局来学习使用CoordinatorLayout
date: 2016-05-16 12:37:23
categories: 
- 技术博客
- Material Design
tags:
- CoordinatorLayout
- AppBarLayout
- CollapsingToolbarLayout
- Toolbar
- Toolbar
- Material Design
---
>特别喜欢稀土掘金个人界面的样子，那我们就来看看如何实现这个效果吧，要想实现这个效果，肯定需要的是Material Design风格，那就需要学会使用以下控件：CoordinatorLayout，AppBarLayout，CollapsingToolbarLayout，Toolbar，Toolbar等，如果你做出这个效果来，那这些控件你就基本掌握了。

### 效果对比图
介绍之前，我们先来看看效果对比图：

#### 稀土掘金app原图
![原图](https://raw.githubusercontent.com/loonggg/CoordinatorLayoutDemo/master/image/1.gif)

#### 模仿的效果图
![模仿效果图](https://raw.githubusercontent.com/loonggg/CoordinatorLayoutDemo/master/image/cld.gif)
<!--more-->
### CoordinatorLayout的介绍

CoordinatorLayout作为“super-powered FrameLayout”基本实现两个功能：
1. 作为顶层布局
2. 调度协调子布局

CoordinatorLayout使用新的思路通过协调调度子布局的形式实现触摸影响布局的形式产生动画效果。CoordinatorLayout通过设置子View的 Behaviors来调度子View。系统（Support V7）提供了AppBarLayout.Behavior, AppBarLayout.ScrollingViewBehavior, FloatingActionButton.Behavior, SwipeDismissBehavior<V extends View> 等。

使用CoordinatorLayout需要在Gradle加入Support Design Library：
```
compile 'com.android.support:design:22.2.1'
```

具体的怎么协调子控件的，建议大家看下面的参考文章，写的非常好，看完你就基本明白了：
[https://segmentfault.com/a/1190000005024216?utm_source=Weibo&amp;utm_medium=shareLink&amp;utm_campaign=socialShare&amp;from=singlemessage&amp;isappinstalled=0](https://segmentfault.com/a/1190000005024216?utm_source=Weibo&amp;utm_medium=shareLink&amp;utm_campaign=socialShare&amp;from=singlemessage&amp;isappinstalled=0 "Android 优化交互 —CoordinatorLayout 与 Behavior")

### AppBarLayout的介绍

AppBarLayout 是一个竖直排列的线性布局，它实现了很多Material Design风格app bar的设计概念，换句话说就是滚动手势。

在AppBarLayout里面的View,通过app:layout_scrollFlags属性来控制,滚动时候的表现。其中有4种Flag的类型。
* **scroll**: this flag should be set for all views that want to scroll off the screen - for views that do not use this flag, they’ll remain pinned to the top of the screen
* **enterAlways**: this flag ensures that any downward scroll will cause this view to become visible, enabling the ‘quick return’ pattern
* **enterAlwaysCollapsed**: When your view has declared a minHeight and you use this flag, your View will only enter at its minimum height (i.e., ‘collapsed’), only re-expanding to its full height when the scrolling view has reached it’s top.
* **exitUntilCollapsed**: this flag causes the view to scroll off until it is ‘collapsed’ (its minHeight) before exiting

我们的例子中用的是 scroll 和 exitUntilCollapsed。

翻译的比较烂，英文好的，看上面的英文解释。
* **Scroll**: 表示向下滚动时,这个View会被滚出屏幕范围直到隐藏.

* **enterAlways**: 表示向上滚动时,这个View会随着滚动手势出现,直到恢复原来的位置.

* **enterAlwaysCollapsed**: 顾名思义，这个flag定义的是何时进入（已经消失之后何时再次显示）。假设你定义了一个最小高度（minHeight）同时enterAlways也定义了，那么view将在到达这个最小高度的时候开始显示，并且从这个时候开始慢慢展开，当滚动到顶部的时候展开完。

* **exitUntilCollapsed**: 同样顾名思义，这个flag时定义何时退出，当你定义了一个minHeight，这个view将在滚动到达这个最小高度的时候消失。

所以我们就在AppBarLayout里面的CollapsingToolbarLayout进行了如下设置：
```xml
<android.support.design.widget.CollapsingToolbarLayout
            android:id="@+id/collapsing_toolbar_layout"
            android:layout_width="match_parent"
            android:layout_height="280dp"
            android:fitsSystemWindows="true"
            app:collapsedTitleTextAppearance="@style/ToolBarTitleText"
            app:contentScrim="#46a8ba"
            app:expandedTitleMarginEnd="48dp"
            app:expandedTitleMarginStart="48dp"
            app:expandedTitleTextAppearance="@style/transparentText"
            app:layout_scrollFlags="scroll|exitUntilCollapsed">
</android.support.design.widget.CollapsingToolbarLayout>
```
**记住：我们刚才上面也说了AppBarLayout是一个竖直方向的线性布局，如果里面包含多个子View时，要想有折叠动画效果，必须把带有scroll flag的view放在前面，这样收回的view才能让正常退出，而固定的view继续留在顶部。**

### CollapsingToolbarLayout的介绍

CollapsingToolbarLayout作用是提供了一个可以折叠的Toolbar，它继承至FrameLayout，给它设置layout_scrollFlags，它可以控制包含在CollapsingToolbarLayout中的控件在响应layout_behavior事件时作出相应的scrollFlags滚动事件(移除屏幕或固定在屏幕顶端)。它是设计用于直接AppBarLayout的子视图。

CollapsingToolbarLayout的子View中可以设置这两个属性
1. ayout_collapseMode (折叠模式) - 有两个值:

* pin -  设置为这个模式时，当CollapsingToolbarLayout完全收缩后，Toolbar还可以保留在屏幕上。
* parallax - 设置为这个模式时，在内容滚动时，CollapsingToolbarLayout中的View（比如ImageView)也可以同时滚动，实现视差滚动效果，通常和layout_collapseParallaxMultiplier(设置视差因子)搭配使用。

2. layout_collapseParallaxMultiplier(视差因子) - 设置视差滚动因子，值为：0~1。 

### 关于CollapsingToolbarLayout几个属性的介绍

* **app:collapsedTitleTextAppearance** 这是在收缩时Title文字特点外形的设置
* **app:expandedTitleTextAppearance** 同理这是在展开时Title文字特点外形的设置
* **app:contentScrim 这是toolbar** 标题工具栏停留在顶部时候背景的设置
* **app:expandedTitleMarginStart** 设置扩张时候(还没有收缩时)title向左填充的距离
* **app:expandedTitleMarginEnd** 这个同理是收缩结束时向左填空的距离

其他的就不一一介绍了，具体的去查看API文档即可获知。

我在做这里的时候遇到一个问题，那就是CollapsingToolbarLayout里的Title的问题，一般默认是显示的，即使你不写，它也有会一个默认值一直显示在那里，等折叠收缩完的时候，停留在标题工具栏上。怎么消除这个默认值呢？怎么知道收缩完成了，再把这个值设置出来呢？这里我对AppBarLayout设置了一个监听，它有一个监听方法：**addOnOffsetChangedListener**监听折叠收缩的位移。如下：
```java
app_bar_layout.addOnOffsetChangedListener(new AppBarLayout.OnOffsetChangedListener() {
            @Override
            public void onOffsetChanged(AppBarLayout appBarLayout, int verticalOffset) {
                if (verticalOffset <= -head_layout.getHeight() / 2) {
                    mCollapsingToolbarLayout.setTitle("涩郎");
                } else {
                    mCollapsingToolbarLayout.setTitle(" ");
                }
            }
        });
```

### Toolbar的介绍

Toolbar 是在 Android 5.0 开始推出的一个 Material Design 风格的导航控件 ，Google 非常推荐大家使用 Toolbar 来作为Android客户端的导航栏，以此来取代之前的 Actionbar 。与 Actionbar 相比，Toolbar 明显要灵活的多。它不像 Actionbar 一样，一定要固定在Activity的顶部，而是可以放到界面的任意位置。除此之外，在设计 Toolbar 的时候，Google也留给了开发者很多可定制修改的余地，这些可定制修改的属性在API文档中都有详细介绍，如：
* 设置导航栏图标；
* 设置App的logo；
* 支持设置标题和子标题；
* 支持添加一个或多个的自定义控件；
* 支持Action Menu；

Toolbar的具体使用方法，我在这里就不过多的赘述了，学习的点太多了，简单介绍完了，我给大家推荐两篇参考学习使用的文章就行了，写的很详细和完整，之前我们公众号也推送过D_clock写的文章。
学习参考文章：

* [http://jcodecraeer.com/a/anzhuokaifa/androidkaifa/2014/1118/2006.html](http://jcodecraeer.com/a/anzhuokaifa/androidkaifa/2014/1118/2006.html "android：ToolBar详解（手把手教程）")

* [http://www.jianshu.com/p/79604c3ddcae#rd](http://www.jianshu.com/p/79604c3ddcae#rd "Android开发：最详细的 Toolbar 开发实践总结")

### TabLayout的介绍

毫无疑问，TabLayout也是Material Design设计风格，当然也是5.0以后出来的。TabLayout提供一个水平布局来显示选项卡。TabLayout一般都是配合ViewPager一起来使用。TabLayout设置Tab标签有两种方法如下：
#### 第一种
```java
TabLayout tabLayout = ...;
tabLayout.addTab(tabLayout.newTab().setText("Tab 1"));
tabLayout.addTab(tabLayout.newTab().setText("Tab 2"));
tabLayout.addTab(tabLayout.newTab().setText("Tab 3"));
```
#### 第二种
```xml
 <android.support.design.widget.TabLayout
         android:layout_height="wrap_content"
         android:layout_width="match_parent">

     <android.support.design.widget.TabItem
             android:text="@string/tab_text"/>

     <android.support.design.widget.TabItem
             android:icon="@drawable/ic_android"/>

 </android.support.design.widget.TabLayout>

```

#### TabLayout的坑
使用TabLayout有个坑，这个坑如果一般用户不知道，解决起来比较麻烦，当然看到这篇文章的人有福了，因为你找到解决方法。
如果设计的需求不要求选项卡在切换时附带有图标的切换效果，仅仅文字的颜色发生变化以响应用户的点击事件，那么TabLayout和ViewPager建立联系可以用官方提供的方法，它可以做到交互双向联动，也就是点击tab，viewpager就会去变动，滑动viewpager，tab也会自动变。相互建立联系的方法如下：
```java
setupWithViewPager(ViewPager viewPager)
```
如果选项卡里带有图标或者仅仅只有图标时就麻烦了，那个选项卡会变得什么都没有了。解决方法其实很简单就是不使用上面的方法，而且这样用：
```java
viewPager.addOnPageChangeListener(new TabLayoutOnPageChangeListener(tabLayout));  
tabLayout.setOnTabSelectedListener(new ViewPagerOnTabSelectedListener(viewPager));
```
其实这个**setupWithViewPager(ViewPager viewPager)**方法，跟进到源码里，你可以看到，就是调用了上面的两种方式。

### 全部代码

#### 布局代码
到这里基本就讲完了，全部的布局代码如下：
```xml
<android.support.design.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fitsSystemWindows="true"
    tools:context=".MainActivity">

    <android.support.design.widget.AppBarLayout
        android:id="@+id/app_bar_layout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:fitsSystemWindows="true">

        <android.support.design.widget.CollapsingToolbarLayout
            android:id="@+id/collapsing_toolbar_layout"
            android:layout_width="match_parent"
            android:layout_height="280dp"
            android:fitsSystemWindows="true"
            app:collapsedTitleTextAppearance="@style/ToolBarTitleText"
            app:contentScrim="#46a8ba"
            app:expandedTitleMarginEnd="48dp"
            app:expandedTitleMarginStart="48dp"
            app:expandedTitleTextAppearance="@style/transparentText"
            app:layout_scrollFlags="scroll|exitUntilCollapsed">


            <LinearLayout
                android:id="@+id/head_layout"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:orientation="vertical"
                app:layout_collapseMode="pin"
                app:layout_collapseParallaxMultiplier="0.7">

                <RelativeLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:layout_marginTop="50dp"
                    android:padding="20dp">

                    <LinearLayout
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_alignParentLeft="true"
                        android:layout_centerVertical="true"
                        android:orientation="vertical">

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="涩郎"
                            android:textColor="#ffffff"
                            android:textSize="16sp"
                            android:textStyle="bold" />

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:layout_marginTop="3dp"
                            android:text="Android开发工程师 @ 非著名程序员"
                            android:textColor="#ffffff"
                            android:textSize="13sp"
                            android:textStyle="bold" />
                    </LinearLayout>

                    <ImageView
                        android:layout_width="50dp"
                        android:layout_height="50dp"
                        android:layout_alignParentRight="true"
                        android:layout_centerVertical="true"
                        android:scaleType="centerCrop"
                        android:src="@mipmap/bg" />
                </RelativeLayout>

                <TextView
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:layout_marginLeft="20dp"
                    android:layout_marginRight="40dp"
                    android:ellipsize="end"
                    android:maxLines="2"
                    android:text="关注我（微博@涩郎，微信公众号：非著名程序员），我与你闲扯技术大话，笑谈科技人生。以幽默诙谐的态度，面对乏味无聊的技术，用扯淡的方式，分享技术的内涵。谈的是技术，更是我们的人生。"
                    android:textColor="#ffffff"
                    android:textSize="12sp" />

                <RelativeLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:layout_marginTop="10dp"
                    android:padding="20dp">

                    <LinearLayout
                        android:id="@+id/one"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_alignParentLeft="true"
                        android:layout_centerVertical="true"
                        android:orientation="vertical">

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="分享"
                            android:textColor="#ffffff"
                            android:textSize="12sp"
                            android:textStyle="bold" />

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="50篇"
                            android:textColor="#ffffff"
                            android:textSize="12sp"
                            android:textStyle="bold" />
                    </LinearLayout>

                    <LinearLayout
                        android:id="@+id/two"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_centerVertical="true"
                        android:layout_marginLeft="10dp"
                        android:layout_toRightOf="@id/one"
                        android:orientation="vertical">

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="获得收藏"
                            android:textColor="#ffffff"
                            android:textSize="12sp"
                            android:textStyle="bold" />

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="3000次"
                            android:textColor="#ffffff"
                            android:textSize="12sp"
                            android:textStyle="bold" />
                    </LinearLayout>

                    <LinearLayout
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_centerVertical="true"
                        android:layout_marginLeft="10dp"
                        android:layout_toRightOf="@id/two"
                        android:orientation="vertical">

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="被阅读"
                            android:textColor="#ffffff"
                            android:textSize="12sp"
                            android:textStyle="bold" />

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="60000次"
                            android:textColor="#ffffff"
                            android:textSize="12sp"
                            android:textStyle="bold" />
                    </LinearLayout>

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_alignParentRight="true"
                        android:layout_centerVertical="true"
                        android:background="@drawable/setting_bg"
                        android:paddingBottom="3dp"
                        android:paddingLeft="6dp"
                        android:paddingRight="6dp"
                        android:paddingTop="3dp"
                        android:text="设置"
                        android:textColor="#ffffff"
                        android:textSize="12sp" />
                </RelativeLayout>
            </LinearLayout>

            <android.support.v7.widget.Toolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="?attr/actionBarSize"
                app:layout_collapseMode="pin"
                app:popupTheme="@style/ThemeOverlay.AppCompat.Light"
                app:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar" />

        </android.support.design.widget.CollapsingToolbarLayout>

        <android.support.design.widget.TabLayout
            android:id="@+id/toolbar_tab"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:layout_gravity="bottom"
            android:background="#ffffff"
            android:fillViewport="false"
            app:layout_scrollFlags="scroll"
            app:tabIndicatorColor="#0835f8"
            app:tabIndicatorHeight="2.0dp"
            app:tabSelectedTextColor="#0835f8"
            app:tabTextColor="#ced0d3">

            <android.support.design.widget.TabItem
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:icon="@drawable/tab_selector" />

            <android.support.design.widget.TabItem
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:text="分享" />

            <android.support.design.widget.TabItem
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:text="收藏" />

            <android.support.design.widget.TabItem
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:text="关注" />

            <android.support.design.widget.TabItem
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:text="关注者" />


        </android.support.design.widget.TabLayout>

    </android.support.design.widget.AppBarLayout>

    <android.support.v4.widget.NestedScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:fillViewport="true"
        android:scrollbars="none"
        app:layout_behavior="@string/appbar_scrolling_view_behavior">

        <android.support.v4.view.ViewPager
            android:id="@+id/main_vp_container"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:layout_behavior="@string/appbar_scrolling_view_behavior" />

    </android.support.v4.widget.NestedScrollView>
</android.support.design.widget.CoordinatorLayout>
```

#### Activity中的代码
```java
package com.loonggg.coordinatorlayoutdemo;

import android.annotation.SuppressLint;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.graphics.drawable.BitmapDrawable;
import android.support.design.widget.AppBarLayout;
import android.support.design.widget.CollapsingToolbarLayout;
import android.support.design.widget.TabLayout;
import android.support.v4.view.ViewPager;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.support.v7.widget.Toolbar;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.widget.LinearLayout;
import android.widget.Toast;


public class MainActivity extends AppCompatActivity {
    private LinearLayout head_layout;
    private TabLayout toolbar_tab;
    private ViewPager main_vp_container;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        AppBarLayout app_bar_layout = (AppBarLayout) findViewById(R.id.app_bar_layout);
        Toolbar mToolbar = (Toolbar) findViewById(R.id.toolbar);
        setSupportActionBar(mToolbar);
        getSupportActionBar().setDisplayHomeAsUpEnabled(true);
        mToolbar.setNavigationOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                onBackPressed();
            }
        });
        head_layout = (LinearLayout) findViewById(R.id.head_layout);
        Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.mipmap.bg);
        head_layout.setBackgroundDrawable(new BitmapDrawable(BlurUtil.fastblur(this, bitmap, 180)));
        //使用CollapsingToolbarLayout必须把title设置到CollapsingToolbarLayout上，设置到Toolbar上则不会显示
        final CollapsingToolbarLayout mCollapsingToolbarLayout = (CollapsingToolbarLayout) findViewById(R.id.collapsing_toolbar_layout);
        mCollapsingToolbarLayout.setContentScrim(new BitmapDrawable(BlurUtil.fastblur(this, bitmap, 180)));
        app_bar_layout.addOnOffsetChangedListener(new AppBarLayout.OnOffsetChangedListener() {
            @Override
            public void onOffsetChanged(AppBarLayout appBarLayout, int verticalOffset) {
                if (verticalOffset <= -head_layout.getHeight() / 2) {
                    mCollapsingToolbarLayout.setTitle("涩郎");
                } else {
                    mCollapsingToolbarLayout.setTitle(" ");
                }
            }
        });
        Toolbar.OnMenuItemClickListener onMenuItemClick = new Toolbar.OnMenuItemClickListener() {
            @Override
            public boolean onMenuItemClick(MenuItem menuItem) {
                String msg = "";
                switch (menuItem.getItemId()) {
                    case R.id.webview:
                        msg += "博客跳转";
                        break;
                    case R.id.weibo:
                        msg += "微博跳转";
                        break;
                    case R.id.action_settings:
                        msg += "设置";
                        break;
                }

                if (!msg.equals("")) {
                    Toast.makeText(MainActivity.this, msg, Toast.LENGTH_SHORT).show();
                }
                return true;
            }
        };

        mToolbar.setOnMenuItemClickListener(onMenuItemClick);
        toolbar_tab = (TabLayout) findViewById(R.id.toolbar_tab);
        main_vp_container = (ViewPager) findViewById(R.id.main_vp_container);

        ViewPagerAdapter vpAdapter = new ViewPagerAdapter(getSupportFragmentManager(), this);
        main_vp_container.setAdapter(vpAdapter);
        main_vp_container.addOnPageChangeListener(new TabLayout.TabLayoutOnPageChangeListener(toolbar_tab));
        toolbar_tab.setOnTabSelectedListener(new TabLayout.ViewPagerOnTabSelectedListener(main_vp_container));
        //tablayout和viewpager建立联系为什么不用下面这个方法呢？自己去研究一下，可能收获更多
        //toolbar_tab.setupWithViewPager(main_vp_container);

    }

    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.menu_main, menu);
        return true;
    }

}
```

源码下载地址：[https://github.com/loonggg/CoordinatorLayoutDemo](https://github.com/loonggg/CoordinatorLayoutDemo)
记得star一下哦。


