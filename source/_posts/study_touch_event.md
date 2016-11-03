---
title: Android学习之Touch事件的处理
date: 2016-04-18 12:37:23
categories: 技术博客
tags: Touch事件
---
>这是一篇老文章，移到我的新博客中，没看过的，可以看看。

在移动开发过程当中，我们经常会遇到手势处理和事件触摸的情况，如果不了解整个事件的处理机制，对于开发的同学和码农是非常痛苦的，但是事件触摸的处理确实是一个非常复杂的过程，细讲起来，估计我都能讲迷糊，这里呢，我只做一些简单的和常用事件的讲解，希望能够帮助大家。


## 概念，什么是事件的派发


事件的派发简单来讲就是Activity将事件派发给容器和控件，容器或控件将事件进一步派发给其子容器和子控件，直到事件最终派发到事件发生的焦点控件上。


## 常用的事件触摸方法


我想对于这些常用的事件触摸方法大家都应该有一定的了解，或者熟悉，那就是onTouchEvent()和dispatchTouchEvent()，OnInterceptTouchEvent()，看到方法后，相信你很快就会说，对我用过，但是具体的意思没有搞清楚，看别人这么说的，别人这么用的，我就跟着这么用的，用之前，对于这些方法我们一定要搞清其作用，这样才能在做起来有的放矢。现在我们就来介绍一下这三种方法的大概用法和作用。
<!--more-->
#### Boolean onTouchEvent()

**作用**：处理本次触摸事件。

**解释**：如果某个控件或容器消费了touch事件，则与该touch事件相关的后续touch事件者会派发给该控件或容器进行处理。

**返回值**：该方法的返回值机理与键盘响应事件的相同，同样是当已经完整地处理了该事件且不希望其他回调方法再次处理时返回true，否则返回false。


**一般情况下以下三种情况的事件全部由onTouchEvent方法处理，只是三种情况中的动作值不同。**


**屏幕被按下**：当屏幕被按下时，会自动调用该方法来处理事件，此时MotionEvent.getAction()的值为MotionEvent.ACTION_DOWN，如果在应用程序中需要处理屏幕被按下的事件，只需重新该回调方法，然后在方法中进行动作的判断即可。


**屏幕被抬起**：当触控笔离开屏幕时触发的事件，该事件同样需要onTouchEvent方法来捕捉，然后在方法中进行动作判断。当MotionEvent.getAction()的值为MotionEvent.ACTION_UP时，表示是屏幕被抬起的事件。


**在屏幕中拖动**：该方法还负责处理触控笔在屏幕上滑动的事件，同样是调用MotionEvent.getAction()方法来判断动作值是否为MotionEvent.ACTION_MOVE再进行处理。


#### Boolean OnInterceptTouchEvent()

**作用**：是否拦截触摸事件，true表示拦截，faIse表示不拦截。

**解释**：onInterceptTouchEvent这个事件是从父控件开始往子控件传的，直到有拦截或者到没有这个事件的view，然后就往回从子到父控件，（类似于预处理，当然也可以不处理）并改变事件的传递方向，也就是决定是否允许Touch事件继续向下（子控件）传递，一但返回True（代表事件在当前的viewGroup中会被处理），则向下传递之路被截断（所有子控件将没有机会参与Touch事件），同时把事件传递给当前的控件的onTouchEvent()处理；返回false，则把事件交给子控件的onInterceptTouchEvent()。


#### void dispatchTouchEvent()

**作用**:将触摸事件向下层的ViewGroup或View派发。

**解释**：当TouchEvent发生时，首先Activity将TouchEvent传递给最顶层的View， TouchEvent最先到达最顶层 view 的 dispatchTouchEvent ，然后由  dispatchTouchEvent 方法进行分发，如果dispatchTouchEvent返回true ，则交给这个view的onTouchEvent处理，如果dispatchTouchEvent返回 false ，则交给这个 view 的 interceptTouchEvent 方法来决定是否要拦截这个事件，如果 interceptTouchEvent 返回 true ，也就是拦截掉了，则交给它的 onTouchEvent 来处理，如果 interceptTouchEvent 返回 false ，那么就传递给子 view ，由子 view 的 dispatchTouchEvent 再来开始这个事件的分发。如果事件传递到某一层的子 view 的 onTouchEvent 上了，这个方法返回了 false ，那么这个事件会从这个 view 往上传递，都是 onTouchEvent 来接收。而如果传递到最上面的 onTouchEvent 也返回 false 的话，这个事件就会“消失”，而且接收不到下一次事件。


## 我们常用的ACTION常量
```
public static final int ACTION_DOWN=0
作用：表示按下的状态。

public static final int ACTION_ UP=1
作用：表示抬起的状态。

public static final int ACTION_MOVE=2
作用：表示移动的状态。

```
## 特别注意

```
ViewGroup里的onInterceptTouchEvent默认值是false这样才能把事件传给View里的onTouchEvent.

ViewGroup里的onTouchEvent默认值是false。

View里的onTouchEvent返回默认值是true.这样才能执行多次touch事件

```


