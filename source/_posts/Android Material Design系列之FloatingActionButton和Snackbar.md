---
title: Android Material Design系列之FloatingActionButton和Snackbar
date: 2016-06-23 12:39:04
tags:
- Material Design
- FloatingActionButton
- Snackbar
categories: 
- 技术博客
- Material Design
---
>今天主讲的Material Design系列的两个控件都不难，所以一起讲了，分别是FloatingActionButton和Snackbar。这个系列都是主讲的Material Design风格的控件，所以都是控件的一些基本使用，也会扩展讲一些与这个控件相关的东西和效果，如果都会了的同学，可以不看这个系列。当然看一下也没啥，再巩固一下知识点也挺好的。

### FloatingActionButton
FloatingActionButton从本质讲就是一个ImageView，从FloatingActionButton的继承来看，它首先继承了ImageButton，然后是ImageButton继承了ImageView。所以FloatingActionButton是重写ImageView的，所有FloatingActionButton拥有ImageView的一切属性。FloatingActionButton顾名思义就是一个浮动按钮。

#### FloatingActionButton属性介绍
由于FloatingActionButton本质上是ImageView，跟ImageView相关的就不介绍，这里重点介绍新加的几个属性。
* app:fabSize ：FloatingActionButton的大小，有两种赋值分别是 “mini” 和 “normal”，默认是“normal”.
* app:backgroundTint：FloatingActionButton默认正常显示时的背景颜色
* app:elevation ：FloatingActionButton阴影的深度，默认时的阴影
* app:rippleColor：FloatingActionButton点击时的背景颜色
* app:pressedTranslationZ：FloatingActionButton点击时阴影的深度

#### 例子效果图
![](http://7xsgef.com1.z0.glb.clouddn.com/fsdfsdfsdf45d45sd6fsd465fsd.gif)
<!--more-->
代码如下：
```xml
<android.support.design.widget.FloatingActionButton
        android:id="@+id/fab"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom|end"
        android:layout_margin="@dimen/fab_margin"
        android:src="@drawable/add"
        app:backgroundTint="@color/add_bg_color"
        app:elevation="6dp"
        app:rippleColor="@color/add_selected_color"
        app:pressedTranslationZ="25dp"
        app:fabSize="normal"/>
```
掌握了我给你们说的那几个属性，就基本掌握了FloatingActionButton的用法了。FloatingActionButton之所以出现这么久了，不太火，不太常用，估计跟他悬浮有关，容易挡住其他内容。那我们现在就研究改如何在滑动列表时隐藏和显示这个悬浮按钮FloatingActionButton。

#### FloatingActionButton显示与隐藏
那如何实现滑动列表时，下滑显示和上滑隐藏的效果呢？其实很简单，官方也提供了方法，但是得继承FloatingActionButton.Behavior进行重写。在这里我为了实现这个效果，给布局添加了一个RecyclerView，方法如下：
```java
/**
 * Created by loonggg on 2016/6/22.
 */
public class FloatingActionButtonScrollBehavior extends FloatingActionButton.Behavior {
    public FloatingActionButtonScrollBehavior(Context context, AttributeSet attrs) {
        super();
    }

    @Override
    public boolean onStartNestedScroll(final CoordinatorLayout coordinatorLayout, final
    FloatingActionButton child, final View directTargetChild, final View target, final int
            nestedScrollAxes) {
        // 确保是竖直判断的滚动
        return nestedScrollAxes == ViewCompat.SCROLL_AXIS_VERTICAL || super.onStartNestedScroll
                (coordinatorLayout, child, directTargetChild, target, nestedScrollAxes);
    }

    @Override
    public void onNestedScroll(final CoordinatorLayout coordinatorLayout, final
    FloatingActionButton child, final View target, final int dxConsumed, final int dyConsumed,
                               final int dxUnconsumed, final int dyUnconsumed) {
        super.onNestedScroll(coordinatorLayout, child, target, dxConsumed, dyConsumed,
                dxUnconsumed, dyUnconsumed);
        if (dyConsumed > 0 && child.getVisibility() == View.VISIBLE) {
            child.hide();
        } else if (dyConsumed < 0 && child.getVisibility() != View.VISIBLE) {
            child.show();
        }
    }
}
```
看动画效果，如下：
![](http://7xsgef.com1.z0.glb.clouddn.com/sss%E4%B8%89%E5%8D%81%E5%A4%9A%E5%B2%81%E7%9A%84.gif)

其实我感觉你们可以还会想说，点击一下这个悬浮按钮，动画弹出多个按钮这个效果怎么实现，这里我就不讲了，github上有太多跟这个相关的例子了。
可以参考这个例子：
https://github.com/Clans/FloatingActionButton

### Snackbar
Snackbar提供了一个介于Toast和AlertDialog之间轻量级控件，它可以很方便的提供消息的提示和动作反馈。
它的使用方式也是非常的简单，跟Toast差不多，代码如下：
```java
final Snackbar snackbar = Snackbar.make(view, "关注非著名程序员公众号了吗？", Snackbar
        .LENGTH_LONG);
snackbar.show();
snackbar.setAction("关注", new View.OnClickListener() {
    @Override
    public void onClick(View view) {
                        snackbar.dismiss();
    }
});
```
效果图如下：
![](http://7xsgef.com1.z0.glb.clouddn.com/Screenshot_2016-06-22-17-23-37-541_MaterialDesign.png)

#### Snackbar样式
如何修改Snackbar样式呢？其实也非常简单。代码如下：
```java
final Snackbar snackbar = Snackbar.make(rv, "你知道非著名程序员这个公众号吗？", Snackbar
                        .LENGTH_LONG);
// 设置动作按钮颜色
snackbar.setActionTextColor(getResources().getColor(R.color.add_bg_color));
// 获取 snackbar 视图
View snackbarView = snackbar.getView();

//设置修改snackbar文本颜色
int snackbarTextId = android.support.design.R.id.snackbar_text;
TextView tv = (TextView) snackbarView.findViewById(snackbarTextId);
tv.setTextColor(getResources().getColor(R.color.add_bg_color));
//设置snackbar背景色
snackbarView.setBackgroundColor(Color.GRAY);
snackbar.show();
snackbar.setAction("知道", new View.OnClickListener() {
    @Override
    public void onClick(View view) {
        snackbar.dismiss();
    }
});
```
效果图如下：
![](http://7xsgef.com1.z0.glb.clouddn.com/Screenshot_2016-06-22-18-19-20-260_MaterialDesign.png)

#### 添加icon和改变Snackbar的位置
修改了背景色，文字按钮颜色，是不是还不过瘾？看看我们如何在Snackbar上添加上一个icon图片。其实也非常简单，和修改样式的过程差不多。代码如下：
* 添加icon
```java
View snackbarView = snackbar.getView();
//设置icon
ImageView iconImage = new ImageView(MainActivity.this);
iconImage.setImageResource(R.mipmap.ic_launcher);
//icon插入布局
Snackbar.SnackbarLayout snackbarLayout = (Snackbar.SnackbarLayout) snackbarView;
Snackbar.SnackbarLayout.LayoutParams sl = new Snackbar.SnackbarLayout.LayoutParams(vl.WRAP_CONTENT, vl.WRAP_CONTENT);
//让icon的布局位于父布局垂直居中的位置
sl.gravity = Gravity.CENTER_VERTICAL;
iconImage.setLayoutParams(sl);
snackbarLayout.addView(iconImage, 0);
```

* 改变Snackbar的位置
其实改变Snackbar的位置和设置icon的位置布局大同小异，代码如下：
```java
// 获取 snackbar 视图
View snackbarView = snackbar.getView();
ViewGroup.LayoutParams vl = snackbarView.getLayoutParams();
CoordinatorLayout.LayoutParams cl = new CoordinatorLayout.LayoutParams(vl.width, vl.height);
cl.gravity = Gravity.CENTER_VERTICAL;
snackbarView.setLayoutParams(cl);
```

* 效果图如下：
![](http://7xsgef.com1.z0.glb.clouddn.com/Screenshot_2016-06-26-17-32-14-556_MaterialDesign.png)

到这里，关于FloatingActionButton和Snackbar基本就讲完了。非常简单，我相信大家都很容易理解。Material Design系列一发出去的时候，有人私下发消息要源码，前期我感觉没必要，以为都是控件的基本使用嘛，挡不住我心好啊，这个系列我都写在了一个demo里，我会慢慢完善，直到更新完。

demo的github地址：https://github.com/loonggg/MaterialDesignDemo 去star吧，我会慢慢完善的。

