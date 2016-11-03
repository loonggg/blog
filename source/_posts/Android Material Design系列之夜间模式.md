---
title: Android Material Design系列之夜间模式
date: 2016-07-28 12:39:04
tags:
- Material Design
- 夜间模式
categories: 
- 技术博客
- Material Design
---
>今天我们讲讲夜间模式的实现，这篇文章的名字叫：《Android Material Design系列之夜间模式》。在Android 5.0 之后，实现夜间模式并非很难了，支持的5.0库提供了非常简单的实现方式。不信，你就往下看。

首先说，这种方式有它的局限性，只能是两种模式，夜间和白天的黑白两种模式，由于其局限性，所以实现就非常简单。讲之前，咱先看看效果图吧。

### 效果图
![image](https://raw.githubusercontent.com/loonggg/MaterialDesignDemo/master/image/night.gif)
<!--more-->
实现真的是非常简单，就如下几步，来一起来看看。
### 实现依赖
```xml
compile 'com.android.support:appcompat-v7:23.4.0'
```

### 配置对应的主题模式
上篇文章我们补充了主题模式的讲解，对于一些主题样式进行的相应的说明，我也说过，到这里我们就有可能用到。
#### 白天模式
对于白天模式的主题样式和颜色，我们就是按正常的来做就可以了。就是我们平常设置主题和颜色的地方设置就行。不同的是我们的主题style样式需要继承的是DayNight主题。
如下：
```xml
<!-- Base application theme. -->
<style name="AppTheme" parent="AppTheme.NoActionBar">
  <!-- Customize your theme here. -->
  <item name="colorPrimary">@color/colorPrimary</item>
  <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
  <item name="colorAccent">@color/colorAccent</item>
  <item name="android:textColorPrimary">@color/text_color_primary</item>
</style>

<style name="AppTheme.NoActionBar" parent="Theme.AppCompat.DayNight">
  <item name="windowActionBar">false</item>
  <item name="windowNoTitle">true</item>
</style>
``` 
重点是这句话：
```xml
parent="Theme.AppCompat.DayNight"
```
#### 夜间模式
对于夜间模式的颜色和主题配置，我们需要建立一个res下建立一个values-night文件夹，里面放着夜间主题样式的color等资源。
colors.xml配置如下：
```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="colorPrimary">#0a0a0a</color>
    <color name="colorPrimaryDark">#000000</color>
    <color name="colorAccent">#fc0404</color>
    <color name="add_bg_color">#FF2ECC71</color>
    <color name="add_selected_color">#51C332</color>
    <color name="white">#ffffff</color>
    <color name="text_color">#7f7f7f</color>
    <color name="navigation_bar_color">#0a0a0a</color>
    <color name="color_control_normal">#f305be</color>
    <color name="text_color_primary">#00ffff</color>
    <color name="navigationview_bg_color">#000000</color>
</resources>
```
需要注意和说明的是：这里的name的属性的名字和我们正常情况下的，也就是白天模式下的名字必须一样，只不过是值不一样罢了，颜色值白天和夜间的值你们自己配置就可以了。

### 切换主题
这一步就是改切换主题的调用了，如下：
```java
isNight = sp.getBoolean("night", false);
if (isNight) {
    AppCompatDelegate.setDefaultNightMode(AppCompatDelegate.MODE_NIGHT_NO);
    sp.edit().putBoolean("night", false).commit();
} else {
    AppCompatDelegate.setDefaultNightMode(AppCompatDelegate.MODE_NIGHT_YES);
    sp.edit().putBoolean("night", true).commit();
}
recreate();
```
我这里对模式进行了保存，先判断现在处于什么模式，然后点击的时候，再根据现在的模式切换到另一种模式。
重点是这两行代码：
```java
//这是设置成非夜间模式
AppCompatDelegate.setDefaultNightMode(AppCompatDelegate.MODE_NIGHT_NO);
//这是设置成夜间模式
AppCompatDelegate.setDefaultNightMode(AppCompatDelegate.MODE_NIGHT_YES);
```

#### 补充说明
AppCompatDelegate.setDefaultNightMode(int mode);
这里的mode有四个可选值，分别是：
* MODE_NIGHT_NO： 使用亮色(light)主题，不使用夜间模式
* MODE_NIGHT_YES：使用暗色(dark)主题，使用夜间模式
* MODE_NIGHT_AUTO：根据当前时间自动切换 亮色(light)/暗色(dark)主题
* MODE_NIGHT_FOLLOW_SYSTEM(默认选项)：设置为跟随系统，通常为MODE_NIGHT_NO

到这里关于夜间模式的切换就讲完了，是不是非常简单？对，就是这么简单，如果你还不知道这种方式，那就赶紧去试试吧，最近MD系列的阅读量真的是很低，看来大家都不太喜欢，如果这篇文章的阅读量低于1500，就暂时先停一段MD系列的更新，换别的文章更新，给大家换换脑子，写这个系列我也有点累了，MD系列后面应该讲解转场动画和按压特效了。

demo的github地址：https://github.com/loonggg/MaterialDesignDemo 去star吧，我会慢慢完善的。

