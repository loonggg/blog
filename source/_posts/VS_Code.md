---
title: 环境配置：React Native智能开发工具，可代码提醒的IDE——VS Code
date: 2016-09-26 12:39:04
tags:
- React Native
- VS Code
categories: 
- 技术博客
- React Native
---
>VS Code是一个专门为React Native项目提供开发环境的扩展开发插件工具。可以调试代码，快速运行React Native的命令，并且可以智能提醒。非常的简便好用。

## 安装
首先要确保你已经安装了相关的React Native开发环境，并配置了开发环境。
安装VS Code方法非常简单，去github上下载插件，直接安装在电脑就可以了。
插件地址：https://github.com/Microsoft/vscode-react-native

它具有打开文件夹功能，定位到React Native项目的根目录直接使用文件夹打开功能就可以，这样就可以把整个项目目录放进去了。
效果如下：
![](https://raw.githubusercontent.com/loonggg/BlogImages/master/ReactNative/3.png)
<!--more-->

## 调试环境
### 安装调试环境
1. 点击VS Code左边菜单上的按钮![](https://github.com/Microsoft/vscode-react-native/raw/master/images/debug-view-icon.png)，然后点击configure左端最上面的设置按钮![](https://github.com/Microsoft/vscode-react-native/raw/master/images/configure-gear-icon.png)，选择 React Native 调试环境。
	如下图：
	![](https://github.com/Microsoft/vscode-react-native/raw/master/images/choose-debugger.png)
	
	>提示：在你的开发工具中，你可能没有找到选择 React Native 调试环   境。跟图上的样子不一样。没事，往下看，会告诉你解决办法的。

2. VS Code就生成了一个launch.json文件，我们项目中的一些默认配置就在上面，我们可以修改配置文件中的内容，比如：我们可以修改target属性来选择调试的模拟器。
	如下图：
	![](https://github.com/Microsoft/vscode-react-native/raw/master/images/launch-config.png)
	
### 开启调试对话
要启动调试会话，请从配置下拉列表中选择配置，然后点击开始按钮，齿轮形状的配置按钮（或按F5）。
![](https://github.com/Microsoft/vscode-react-native/raw/master/images/debug-targets.png)

更多关于使用VS Code调试的信息，可以查看整个指南：
地址：https://code.visualstudio.com/docs/editor/debugging

## 在命令面板上使用React Native命令
打开命令面板，选择React Native命令类型。
如下图：
![](https://github.com/Microsoft/vscode-react-native/raw/master/images/command-palette.png)
	>提示：在你的开发工具中，你可能没有找到图上的命令。没事，接着往下看，我会告诉解决办法的。

1. 运行android命令触发react-native run-android，启动安卓应用。
2.  运行ios命令触发react-native run-ios，在模拟器中可以运行ios应用。
3. 使用Packager命令，可以打开和关闭React-Packager。

## 提示中的解决办法
解决上面不显示和图中不一致的问题，其实是开发工具中没有安装React Native Tools的原因，我们可以在扩展里搜索React Native找到React Native Tools安装上这个插件就可以了。
如图：
![](https://raw.githubusercontent.com/loonggg/BlogImages/master/ReactNative/4.png)

## 使用智能提醒功能
智能提醒功能可以帮助我们找到React Native中相关的对象,方法以及参数。
如下图：
![](https://github.com/Microsoft/vscode-react-native/raw/master/images/intellisense.png)

### 启用智能提醒
React Native智能提醒功能依赖于VS Code工具支持JSX语法。为了打开该特性，当我们打开React Native项目的时候，会立即出现如下的提示。
![](https://github.com/Microsoft/vscode-react-native/raw/master/images/intellisense-prompt.png)
这是一个一次性的提示，使用JSX支持。我们需要重新启动VS Code使更改设置（智能提醒）生效。

>提示：最新版本本身就支持Salsa智能提醒。

我们可以验证是否已经启动Salsa智能提醒功能，可以检查最底部的状态栏Status Bar，如果这样显示，说明已经成功了。
![](https://camo.githubusercontent.com/25787e6ea812c1c074aad47c824cdf7c7a20a4b6/68747470733a2f2f636f64652e76697375616c73747564696f2e636f6d2f696d616765732f4a616e756172795f73616c73612d7374617475732e706e67)

关于VS Code的内容，我们大致先讲解这些吧。如果还有什么疑问或者不了解的，我们可以一起讨论。






