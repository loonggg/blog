---
title: Android Material Design系列之Toolbar
date: 2016-06-28 12:39:04
tags:
- Material Design
- Toolbar
categories: 
- 技术博客
- Material Design
---
>今天我们主要讲Toolbar的使用，我们都知道Toolbar是在Android 5.0以后推出来的，之前都是ActionBar这个控件。我相信虽然谷歌在3.0以后推出了ActionBar，但是用的人肯定很少，因为个人认为它比较恶心。而且Android对ActionBar的界定很模糊,Toolbar和ActionBar在外观上并没有太大的区别，只是说Toolbar更加自由了，而不像ActionBar那样有太多系统定制的条条框框。

### Toolbar包含哪些元素呢
一个Toolbar包含哪些元素呢？通过我们看API文档可以知道，Toolbar包含如下这些元素：
![](http://7xsgef.com1.z0.glb.clouddn.com/%E6%9C%AA%E6%A0%87%E9%A2%98-1.png)
* 导航按钮
* 应用程序的标志logo
* 标题和子标题
* 一个和多个自定义的视图控件
* 操作菜单

<!--more-->
### 基本使用
#### 效果图
我们知道了一个Toolbar大概包含了5种元素，那我们就介绍一下它们的基本使用。先看做出来的效果图，如下：
![](http://7xsgef.com1.z0.glb.clouddn.com/Screenshot_2016-06-23-16-20-15-541_MaterialDesign.png)
Toolbar的效果图上，从左往右依次是：导航按钮，logo，标题和子标题，自定义控件和action menu操作菜单。

#### 布局代码
上面效果图实现的布局代码如下：
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              xmlns:app="http://schemas.android.com/apk/res-auto"
              android:layout_width="match_parent"
              android:layout_height="match_parent">

    <android.support.v7.widget.Toolbar
        android:id="@+id/toolbar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="@color/colorPrimaryDark"
        app:logo="@drawable/logo"
        app:navigationIcon="@drawable/menu_icon"
        app:subtitle="smart_android(子标题)"
        app:subtitleTextAppearance="@style/ToolbarTextAppearanceSubTitle"
        app:titleTextAppearance="@style/ToolbarTextAppearanceTitle"
        app:subtitleTextColor="@color/white"
        app:title="非著名程序员(标题)"
        app:titleTextColor="@color/white"
        >

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="自定义"
            android:textColor="@color/white"
            android:textSize="16sp"/>
    </android.support.v7.widget.Toolbar>

</LinearLayout>
```
布局代码中的app:logo，app:subtitle等我就不一一介绍了，相信大家都能看懂是什么意思。这些东西可以在布局文件中写，当然也可以在代码中实现，比如：
```java
toolbar.setNavigationIcon();
toolbar.setLogo();
toolbar.setTitle();
toolbar.setSubtitle();
……
```
#### 标题和子标题样式
对于标题和子标题的样式，我们也是可以修改的，比如：颜色，大小等。在style文件中如下：
```xml
<style name="ToolbarTextAppearanceTitle">
     <item name="android:textSize">16sp</item>
</style>
<style name="ToolbarTextAppearanceSubTitle">
     <item name="android:textSize">14sp</item>
</style>
```
#### 操作菜单
对于操作菜单（action menu），我们先在menu中配置item，如下：
```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:app="http://schemas.android.com/apk/res-auto"
    >
    <item
        android:id="@+id/share"
        android:icon="@drawable/share_icon"
        android:title="分享"
        app:showAsAction="ifRoom"/>

    <item
        android:id="@+id/setting"
        android:icon="@drawable/nav_icon_settings"
        android:orderInCategory="100"
        android:title="设置"
        app:showAsAction="never"/>

</menu>
```
然后我们通过这行代码加入到toolbar中：
```java
toolbar.inflateMenu(R.menu.toolbar_menu);
```
其实这样配置也是可以的，如下：
```java
@Override
public boolean onCreateOptionsMenu(Menu menu) {
     getMenuInflater().inflate(R.menu.toolbar_menu, menu);
     return true;
}
```
#### Activity中的代码和点击事件
```java
public class ToolbarActivity extends AppCompatActivity   {
    private Toolbar toolbar;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        supportRequestWindowFeature(Window.FEATURE_NO_TITLE);
        setContentView(R.layout.activity_toolbar);
        toolbar = (Toolbar) findViewById(R.id.toolbar);
        toolbar.inflateMenu(R.menu.toolbar_menu);
        //action menu操作菜单按钮的点击事件
        toolbar.setOnMenuItemClickListener(new Toolbar.OnMenuItemClickListener() {
            @Override
            public boolean onMenuItemClick(MenuItem item) {
                switch (item.getItemId()){
                    case R.id.share:
                        Toast.makeText(ToolbarActivity.this,"分享",Toast.LENGTH_SHORT).show();
                        break;
                    case R.id.setting:
                        Toast.makeText(ToolbarActivity.this,"设置",Toast.LENGTH_SHORT).show();
                        break;
                }
                return false;
            }
        });
        //导航按钮的点击事件
        toolbar.setNavigationOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                finish();
            }
        });
    }

}
```
#### 修改menu中三个点（更多）的样式
通过效果图我们可以看到更多三个点的颜色和我们的menu按钮的颜色不统一，特别丑，如果我们要想修改成白色该怎么做呢？
* 配置style
```xml
<style name="Theme.ToolBar.More" parent="Theme.AppCompat.Light.NoActionBar">
     <item name="actionOverflowButtonStyle">@style/ActionButton.Overflow.More</item>
</style>

<style name="ActionButton.Overflow.More" parent="android:style/Widget.Holo.Light.ActionButton.Overflow">
      <item name="android:src">@mipmap/more_icon</item>
</style>
```
* 在toolbar中设置主题
```xml
android:theme="@style/Theme.ToolBar.More"
```
* 效果图
![](http://7xsgef.com1.z0.glb.clouddn.com/Screenshot_2016-06-23-17-01-27-906_MaterialDesign.png)
颜色是不是统一了，好看了很多？

Toolbar以前刚开始用的时候有过很多坑，不过这一次我在给大家写博客的时候，重写的时候，发现以前的坑都没有了，看来谷歌是修复了，越弄越好了。关于Toolbar就讲这些吧。这个再说一遍，这个系列确实都是在讲Material Design控件的使用，都是一些基本的用法和遇到的一些坑，这些Material Design风格控件都会用了的，如果闲很基础，可以略过，Material Design出来很久了，确实时间很长了，写这个系列的目的是为了那些没用过Material Design的同学。所谓众口难调，还请大家担待点。

demo的github地址：https://github.com/loonggg/MaterialDesignDemo 去star吧，我会慢慢完善的。

