---
title: 浅谈开发中的MVVM模式及与MVP和MVC的区别
date: 2016-3-16 12:39:04
tags:
- MVVM
- MVP
- MVC
categories: 
- 技术博客
---
![](http://7xsgef.com1.z0.glb.clouddn.com/704225-016c6b3ecb16e9dc.png)
我记得前段时间分享了一篇文章[《 **浅谈Andorid开发中的MVP模式**》](http://mp.weixin.qq.com/s?__biz=MjM5NDkxMTgyNw==&mid=404577331&idx=1&sn=e92bf72c490d728470378e4ee1953a80&scene=21#wechat_redirect)，反响不错，为了进一步介绍MVVM模式，还提前分享了实现Android中MVVM模式的一个关键技术的文章[《**Android 数据绑定框架DataBinding，堪称解决界面逻辑的黑科技**》](http://mp.weixin.qq.com/s?__biz=MjM5NDkxMTgyNw==&mid=405212572&idx=1&sn=e2a84186dccc7661e0644feb38d74f8c&scene=21#wechat_redirect) 。如果没有看过这两篇文章的，建议先看看这两篇文章，再看下面的分享。

## 什么是MVVM？
****
MVVM是Model-View-ViewModel的简写。微软的WPF带来了新的技术体验，如Silverlight、音频、视频、3D、动画……，这导致了软件UI层更加细节化、可定制化。同时，在技术层面，WPF也带来了 诸如Binding、Dependency Property、Routed Events、Command、DataTemplate、ControlTemplate等新特性。MVVM（Model-View-ViewModel）框架的由来便是MVP（Model-View-Presenter）模式与WPF结合的应用方式时发展演变过来的一种新型架构框架。它立足于原有MVP框架并且把WPF的新特性糅合进去，以应对客户日益复杂的需求变化。
<!--more-->

## MVC和MVP，MVVM之间的关系
****
**MVC和MVP的关系**
我们都知道MVP是从经典的模式MVC演变而来，它们的基本思想有相通的地方：Controller/Presenter负责逻辑的处理，Model提供数 据，View负责显示。作为一种新的模式，MVP与MVC有着一个重大的区别：在MVP中View并不直接使用Model，它们之间的通信是通过 Presenter (MVC中的Controller)来进行的，所有的交互都发生在Presenter内部，而在MVC中View会直接从Model中读取数据而不是通过 Controller。

**MVVM和MVP的关系**
而 MVVM 模式将 Presenter 改名为 ViewModel，基本上与 MVP 模式完全一致。 唯一的区别是，它采用双向绑定（data-binding）：View的变动，自动反映在 ViewModel，反之亦然。这样开发者就不用处理接收事件和View更新的工作，框架已经帮你做好了。

## 视图化解释关系
****
**MVC架构：**
View：对应于布局文件
Model：业务逻辑和实体模型
Controllor：对应于Activity

![](http://7xsgef.com1.z0.glb.clouddn.com/704225-2cc532b4feb7bb99.png)
View可以与Model直接交互。

Controller是基于行为的，并且可以被多个View共享。

可以负责决定显示哪个View。

**MVP架构：**
View： 对应于Activity，负责View的绘制以及与用户交互Model： 依然是业务逻辑和实体模型Presenter： 负责完成View于Model间的交互

![](http://7xsgef.com1.z0.glb.clouddn.com/704225-54b5b07bbb6fe04d.png)
View不直接与Model交互，而是通过与Presenter交互来与Model间接交互。

Presenter与View的交互是通过接口来进行的。

通常View与Presenter是一对一的，但复杂的View可能绑定多个Presenter来处理逻辑。

**MVVM架构：**
Model：代表你的基本业务逻辑
View：显示内容
ViewModel：将前面两者联系在一起的对象

![](http://7xsgef.com1.z0.glb.clouddn.com/704225-b25cc91dab740fd6.png)
一个ViewModel接口提供了两个东西：动作和数据。动作改变Model的下层（click listener，监听文字改变的listener等等），而数据则是Model的内容。

去年的I/O大会上谷歌介绍了一个非常好用的新框架DataBinding，该框架可以让你将view和一个对象的对field绑定。当field更新的时候，framework将收到通知，同时view也会自动更新。其语法和使用方式和 JSP 中的 EL 表达式非常类似。

在MVVM中，ViewModel在改变内容之后通知binding framework内容发生了改变。然后framework自动更新和那些内容绑定的view。这两个组件只是通过ViewModel松耦合在一起。这种设计模式之所以好用和方便，除了明显智能化了的View之外，还方便了测试。因为ViewModel不在依赖于View了，你可以在没有View的情况下也能测试ViewModel。在合适的依赖注入的帮助下，测试就会变得非常简单。

## MVVM的优点
****
**1. 低耦合**。视图（View）可以独立于Model变化和修改，一个ViewModel可以绑定到不同的"View"上，当View变化的时候Model可以不变，当Model变化的时候View也可以不变。

**2. 可重用性**。你可以把一些视图逻辑放在一个ViewModel里面，让很多view重用这段视图逻辑。

**3. 独立开发**。开发人员可以专注于业务逻辑和数据的开发（ViewModel），设计人员可以专注于页面设计。

**4. 可测试**。界面素来是比较难于测试的，而现在测试可以针对ViewModel来写。

具体怎么好用，怎么使用，建议参考我写的[《**Android 数据绑定框架DataBinding，堪称解决界面逻辑的黑科技**》](http://mp.weixin.qq.com/s?__biz=MjM5NDkxMTgyNw==&mid=405212572&idx=1&sn=e2a84186dccc7661e0644feb38d74f8c&scene=21#wechat_redirect)这篇文章。

参考文章：
[《 **浅谈Andorid开发中的MVP模式**》](http://mp.weixin.qq.com/s?__biz=MjM5NDkxMTgyNw==&mid=404577331&idx=1&sn=e92bf72c490d728470378e4ee1953a80&scene=21#wechat_redirect)
[《**Android 数据绑定框架DataBinding，堪称解决界面逻辑的黑科技**》](http://mp.weixin.qq.com/s?__biz=MjM5NDkxMTgyNw==&mid=405212572&idx=1&sn=e2a84186dccc7661e0644feb38d74f8c&scene=21#wechat_redirect)
> **特别声明，未经允许禁止任何形式的转载。**

