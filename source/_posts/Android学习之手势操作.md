---
title: Android学习之手势操作
date: 2016-04-18 19:39:04
tags:
- 手势操作
categories: 
- 技术博客
---
在昨天我们讲了Android的OnTouch触摸事件，有时候触摸和手势是相互联系的，密不可分的关系，所以上节我们讲了触摸事件，今天我们自然而然的就需要讲手势操作了。触摸，手势操作已经很好的融入了我们的生活，那在Android开发中如何进行手势识别呢？下面我们就来讲讲。


## 什么是手势呢？

手势就是用户对手机屏幕进行连续触碰，比如：从屏幕左边滑到手机右边，从屏幕上面滑动到屏幕下面等等，手势可以是一笔，也可以是多笔，可以形成规则或不规则的图形。

手势是如何识别的呢？ Android的SDK提供了对手势的识别、自定义手势、存取手势、手势库中査询手势的相关类/接 口，提供了android.view.GestureDetector包用于检测用户的手势。就是通过这些先关的类和接口进行相关的操作的。今天我们主要讲的就是这些类和接口的作用与用法。
<!--more-->

## 与手势相关的类和接口

手势操作一般用到下面的三个类：

* android.view.GestureDetector
手势操作的识别类，通过他来使用下面的识别接口，该类在andmid.view.GestureDetector包中，可将该类视为手势检测器。

* android.view.GestureDetector.SimpleOnGestureListener
手势识别的接口类，使用他可以按需重载自己想要的方法，该类实现了 OnGestureListener接口，因此，通过该类可实现对手势的检测操作。

* android.view.GestureDetector.OnGestureListener;
手势识别的类，SimpleOnGestureListener接口的父类。使用他需要实现他所有的方法，该接口中定义了检测手势按下、按下未松开、轻击、滚动、 长按、拖动等方法。


## 方法说明


OnGestureListener有下面的几个方法：

按下（onDown）
在按下时调用。

抛掷 boolean OnGestureListener.onFling(MotionEvent el, MotionEvent e2, float velocityX, float velocityY)
作用：检测手势划动事件。
参数： el划动的起始坐标
  e2划动的结束坐标
  velocityX每秒横向划动的像素值。
  velocityY每秒纵向划动的像素值。

长按 OnGestureListener.onLongPress(MotiphEvent    e)
作用：检测长按事件。

滚动 boolean    onScroll(MotionEvent el, MotionEvent e2, float distanceX,  float distanceY);
作用：检测手势滚动事件。
参数：
el:滚动的起始坐标
e2:滚动的结束坐标
distanceX:滚动的横向巨离
distanceY:滚动的纵向距离

按住 OnGestureListener.onShowPress(MotionEvent    e)
手指按在触摸屏上，它的时间范围在按下起效，在长按之前。

抬起 onSingleTapUp(MotionEvent e)
作用：检测单击事件，手指离开触摸屏的那一刹那。

SimpleOnGestureListener比OnGestureListener多出来的方法：

双击（onDoubleTap）
双击的第二下Touch down时触发

双击事件（onDoubleTapEvent）
双击的第二下Touch down和up都会触发一次，可用e.getAction()区分。


## 检测手势的步骤

1. 创建手势监听器类继承SimpIeOnGestureListener或实现OnGestureListener
2. 创建gestureDetector对象
3. 在onTouch或onTouchEvent方法中，将Touch事件传递给gestureDetector对象的onTouchEvent方法进行手势判定。

