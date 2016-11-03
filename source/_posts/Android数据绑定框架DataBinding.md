---
title: Android数据绑定框架DataBinding，堪称解决界面逻辑的黑科技
date: 2016-02-22 12:39:04
tags:
- DataBinding
categories: 
- 技术博客
---
>去年谷歌 I/O大会上介绍了一个非常厉害的新框架DataBinding， 数据绑定框架给我们带来了很大的方便，以前我们可能需要在每个Activity里写很多的findViewById，不仅麻烦，还增加了代码的耦合性，如果我们使用DataBinding，就可以抛弃那么多的findViewById，省时省力。说到这里，其实网上也有很多快速的注解框架，但是注解框架与DataBinding想比还是不好用，而且官网文档说DataBinding还能提高解析XML的速度，其实DataBinding的好用，不仅仅体现在可以省去使用很多啰嗦findViewById，还有很多。往下看你就会明白的。

在介绍DataBinding之前，肯定要先学会搭建使用它的环境。鉴于是去年发布的，现在大家AS的版本估计都在1.5以上了，就在1.5的版本上搭建和介绍吧，1.5之前的大家可以在网上搜索，因为我看网上大多数是介绍的1.3的使用方法，还没有很多对于1.5之后的环境搭建方法。

#### 环境搭建

Android 的 Gradle 插件版本不低于 1.5.0-alpha1：
classpath 'com.android.tools.build:gradle:1.5.0'
然后修改对应模块（Module）的 build.grade：
```xml
android {
    ....
    dataBinding {
        enabled = true
    }
}
```
注意：Android stuido 的版本一定要大于1.3，而且Android Studio目前对binding对象没有自动代码提示，只会在编译时进行检查。

就是这么简单，但是1.3及以前的版本，对于环境的搭建，可能就会麻烦一点（没事1.3的环境搭建方法，网上多得是）。
<!-- more-->
#### 基础展示

我们在具体的讲解之前，先用一个简单的小例子来学习一下基础并展现一下DataBinding的巨大魅力，估计你会被其简单的特性所吸引哦。

首先我们先建立一个java bean，就是一个非常简单的用户类吧。
```java
package loonggg.net.databinding.bean;

/**
 * Created by loongggdroid on 2016/3/14.
 */
public class User {
    private String name;
    private String age;

    public User(String name, String age) {
        this.name = name;
        this.age = age;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(String age) {
        this.age = age;
    }

    public String getName() {
        return this.name;
    }

    public String getAge() {
        return this.age;
    }
}
```
其次，来看看使用了DataBinding之后的布局文件是什么样子的呢？主要的变化是在layout布局文件之中。
```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">

    <data>
        <variable
            name="user"
            type="loonggg.net.databinding.bean.User" />
    </data>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{user.name}" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{user.age}" />
    </LinearLayout>
</layout>
```
看到这里，可能有的人就开始有点迷糊了，没有给控件定义id，而是用了@{ }的方法，到底是怎么回事？先不忙，一会再给你解释，我们再来看看怎么把值传进去的，在Activity中是如何使用的。
```java
package loonggg.net.databinding;

import android.app.Activity;
import android.databinding.DataBindingUtil;
import android.os.Bundle;

import loonggg.net.databinding.bean.User;
import loonggg.net.databinding.databinding.ActivityMainBinding;

public class MainActivity extends Activity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        ActivityMainBinding binding = DataBindingUtil.setContentView(this, R.layout.activity_main);
        User user = new User("loonggg", "23");
        binding.setUser(user);
    }

}
```
看到Activity是不是感觉很简洁，很清爽，没有了控件的初始化findViewById，然后再去setText(), 就仅仅只添加了两行代码。
运行结果，不用说，显而易见，肯定会显示loonggg和23，效果图如下：
<img src="http://loonggg.com/wp-content/uploads/2016/03/704225-af159ca4b4064cdf-300x67.png" alt="704225-af159ca4b4064cdf" width="300" height="67" class="alignnone size-medium wp-image-30" />

#### 基础解释

**布局生命**
例子我们介绍完了，对于例子中出现的一些新东西，有必要解释一下，java bean那里就不解释了，大家肯定都懂，我们就从布局文件讲起。相比以前使用的xml，根节点由具体的某个layout（比如LinearLayout ）变成了layout，里面包括了data节点和传统的视图。这里的data节点就像是连接 View 和 Modle 的桥梁。在这个data节点中声明一个variable变量，那值就可以轻松传到布局文件中来了。
```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">

<!--type中声明的就是我们的用户实体类User，一定要写全，带着包名，我们给这个实体类命名为user-->
    <data>
        <variable
            name="user"
            type="loonggg.net.databinding.bean.User" />
    </data> 
    <LinearLayout>
     ……
    </LinearLayout>
</layout>
```
变量名为user
变量类型为"loonggg.net.databinding.bean.User "

type中声明的就是我们的用户实体类User，一定要写全，带着包名，我们给这个实体类命名为user，TextView中的@{user.name}就是把这个user中的名字展示出来，age同样如此。

**绑定Variable**
虽然在布局文件中对应上了，但是值是怎么传进去的呢？这就是我们要将的Activity中的那两行代码了，它把实体类和布局文件进行了绑定。修改MainActivity中的onCreate，用 DatabindingUtil.setContentView() 来替换掉 setContentView()，然后创建一个 user 对象，通过 binding.setUser(user) 与 variable 进行绑定。
```java
@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        ActivityMainBinding binding = DataBindingUtil.setContentView(this, R.layout.activity_main);
        User user = new User("loonggg", "23");
        binding.setUser(user);
    }
```
#### 高级用法

**import用法**
其实data节点也是支持import用法的，比如：
```xml
<data>
     <import type="loonggg.net.databinding.bean.User "/>
     <variablename="user"type="User" />
</data>
```
这里需要注意的是：import 并不能像java 一样可以 import xx.xxx.*，必须具体到写清楚每个要导入的类名。

**类名相同**
到这里你可能会问如果import了两个相同名称的类咋办？别怕，人家想的很周到，可以起个别名或者昵称吗！例如：
```xml
···
<data> 
  <import type="xxx.User" alias="MyUser"> 
  <import type="xxx.xx.User"> 

   <variable 
            name="user" 
            type="User" /> 
    <variable 
            name="myUser" 
            type="MyUser"/> 
</data> 
<TextView xxx:@{myUser.getName()}> 
<TextView xxx:@{user.getName()}>
···
```
**变量定义的高级用法**
在上面，我们学会了如何去在xml中定义一些简单的变量。我们没有定义像List、Map等这样的集合变量。那这种集合变量该如何定义呢？其实定义的方式和我们上面的基本一致，区别就在于我们还需要为它定义key的变量，例如：
```xml
<layout xmlns:android="http://schemas.android.com/apk/res/android"> 
        <data> 
            <import type="android.graphics.Bitmap" /> 
            <import type="java.util.ArrayList" /> 
            <import type="java.util.HashMap" />  
          <!-- 集合的定义 -->
            <variable 
                name="list" 
                type="ArrayList<String>" /> 
            <variable 
                name="map" 
                type="HashMap<String, String>" /> 
            <variable 
                name="array" 
                type="String[]" /> 
          <!-- 为集合定义对应的索引 -->
            <variable 
                name="listKey" 
                type="int" /> 
            <variable 
                name="mapKey" 
                type="String" /> 
            <variable 
                name="arrayKey" 
                type="int" /> 

            <!-- 字符串，布尔值和int的用法-->
            <variable 
                name="str" 
                type="String"/> 
            <variable 
                name="error" 
                type="boolean"/> 
            <variable 
                name="num" 
                type="int" /> 

        </data> 

        <LinearLayout 
            android:orientation="vertical" 
            android:layout_width="match_parent" 
            android:layout_height="wrap_content"> 

            <TextView 
                android:layout_width="wrap_content" 
                android:layout_height="wrap_content" 
                android:text="@{list[listKey]}"/> 

            <TextView 
                android:layout_width="wrap_content" 
                android:layout_height="wrap_content" 
                android:text="@{map[`name`]}"/> 
            <TextView 
                android:layout_width="wrap_content" 
                android:layout_height="wrap_content" 
                android:text="@{array[0]}"/> 

            <TextView 
                android:layout_width="wrap_content" 
                android:layout_height="wrap_content" 
                android:text="@{str}"/> 
            <TextView 
                android:layout_width="wrap_content" 
                android:layout_height="wrap_content" 
                android:text="@{String.valueOf(num)}"/> 
        </LinearLayout> 
    </layout>
```
**事件的绑定**
可以直接在 xml 导入android.view.View.OnClickListener，并制定其点击事件。
```xml
<variable
    name="clickListener"
    type="android.view.View.OnClickListener" />
...
  android:onClick="@{clickListener}"
...
```
```java
holder.binding.setClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
               //do something
        });
```
**表达式**
其实在xml文件中还是支持表达式的，比如说如下：
```xml
<TextView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text='@{error ? "error" : "ok"}'/>
```
这是一个布尔值的使用。
下面我们就来看看表达式支持的一下语法和不支持的语法
支持的表达式：
Mathematical + - / * %
String concatenation +
Logical && ||
Binary & | ^
Unary + - ! ~
Shift >> >>> <<
Comparison == > < >= <=
instanceof
Grouping ()
Literals - character, String, numeric, null
Cast
Method calls
Field access
Array access []
Ternary operator ?:

不支持的表达式：
this
super
new
Explicit generic invocation

**带id的控件的赋值方式**
对于xml文件中控件的赋值，其实也是可以在java文件中，用java来实现的。我就以最上面那个简单的小例子来说，假如给一个TextView设置的id如下：
```xml
<TextView
            android:id="@+id/name"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
           />
```
在Activity中这样赋值：
```java
@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        ActivityMainBinding binding = DataBindingUtil.setContentView(this, R.layout.activity_main);
        binding.name.setText("非著名程序员");
    }
```
看到这里基础的大部分就讲完了，其实还有很多更高级的用法，比如在ListView中怎么用等等，大家可以去自己查阅资料学习，这样认识的可以更深刻。希望这篇文章对大家能够有所帮助。DataBinding大家可以从现在开始尝试的使用起来。

