---
title: 什么是Context
date: 2016-04-16 12:39:04
tags:
- context
categories: 
- 技术博客
---
今天的面试题，也就是我们常见面试题系列的第二题，我们来讲一讲android中的context。我相信大家android开发者一定对于这个context非常熟悉，肯定都有使用过，肯定没有没使用过的。但是这个context是什么呢？有多少人能理解呢？我们上一个题讲的是listview，这个也是非常常见的，今天的context也是非常常见的，所以面试题其实离我们很近的，一定都是常用的才会问题，很少有公司面试一些非常偏僻的，不用的东西，在开发中没用面试什么啊？对吧，好了，有点扯多了，我们从茄子地里回来，接着讲这个context。

## 什么是context？
这个在我们开发中最熟悉而又陌生的朋友到底是什么呢？可能大家会不约而同的说，是场景，是上下文，但是到底有多少人理解这个原理呢？我简单概括一下，应该有以下几点：
①、它描述的是一个应用程序环境的信息，即上下文。
②、该类是一个抽象(abstract class)类，Android提供了该抽象类的具体实现类。
③、通过它我们可以获取应用程序的资源和类，也包括一些应用级别操作，例如：启动一个Activity，发送广播，接受Intent信息等。

说了这三点好像你还有点模糊，那我就总结来说。
<!--more-->
总结就是：Context是一个抽象基类，我们通过它访问当前包的资源（getResources、getAssets）和启动其他组件（Activity、Service、Broadcast）以及得到各种服务（getSystemService），当然，通过Context能得到的不仅仅只有上述这些内容。对Context的理解可以来说：Context提供了一个应用的运行环境，在Context的大环境里，应用才可以访问资源，才能完成和其他组件、服务的交互，Context定义了一套基本的功能接口，我们可以理解为一套规范，而Activity和Service是实现这套规范的子类，这么说也许并不准确，因为这套规范实际是被ContextImpl类统一实现的，Activity和Service只是继承并有选择性地重写了某些规范的实现。

## 我们来看一下context的相关类的继承关系
![](http://7xsgef.com1.z0.glb.clouddn.com/640.png)
通过图上我们可以看出：Activity类 、Service类 、Application类本质上都是Context子类，知道为什么老是在这些里面用到context了吧？

## Application、Activity和Service作为Context的区别
* 相同点：它们都间接继承了Context。

* 不同点：首先看它们的继承关系，通过对比可以清晰地发现，Service和Application的类继承关系比较像，而Activity还多了一层继承ContextThemeWrapper，这是因为Activity有主题的概念，而Service是没有界面的服务，Application更是一个抽象的东西，它也是通过Activity类呈现的。Context的真正实现都在ContextImpl中，也就是说Context的大部分方法调用都会转到ContextImpl中，而三者的创建均在ActivityThread中完成，Activity启动的核心过程是在ActivityThread中完成的，这里要说明的是，Application和Service的创建也是在ActivityThread中完成的。

## 一个应用程序中有多少个context？
一个应用程序中到底有多少个context？这个问题就是我们面试中最常见的，也是经常被问到的，看完以上三点的分析？你知道有多少个了吗？

**答案显而易见：总Context实例个数 = Service个数 + Activity个数 + 1（Application对应的Context实例）**

说到这里我相信大家应该有所理解了吧？实在没理解的抽空去查一下源码分析一下，深刻的理解一下。了解了我上边所说的那些内容之后，你在面试中遇到相关context的问题，基本上没问题了。


