---
title: 环境配置：React Native 开发环境配置 For Android
date: 2016-09-25 12:39:04
tags:
- React Native
- RN环境配置
categories: 
- 技术博客
- React Native
---
>React Native 是FaceBook开源的一个项目，FaceBook希望可以用写 Web App 的方式去写 Native App。它可以让我们用JS和React来开发应用，使用React Native可以通吃Android 和 IOS ,以及Web,仅仅需要一份业务逻辑代码就可以来创建我们的应用。
 
## 起源
React Native 是由 React 衍生出来的，而 React 起源于 Facebook 的内部项目，因为FaceBook对市场上所有 JavaScript MVC 框架，都不太满意，就决定自己写一套，用来架设自己的 Instagram 的网站。等做出来以后，发现这套东西很不错，而且好用，就在2013年5月开源了。

而衍生的 React Native 项目，希望用写 Web App 的方式去写 Native App。这样同一组人只需要写一次 UI ，就能同时运行在服务器、浏览器和手机上。FaceBook最早发布的关于React Native是针对IOS的，而React Native for Android是在去年也就是2015年9月15日开源发布的。现阶段React Native的体验虽然已经很不错了，只能说很接近原生应用，但是还是不能达到Native App的体验。所以它很火，将来的事，我们将来说，但是目前已经非常火了，多门技术，多条路。我们也应该学学了。

## 环境搭建
>因为我用的是macbook，所以这次环境搭建是根据mac电脑来说的，不过大家放心，针对windows电脑的环境搭建，我会分享给大家一篇文章或者视频的，不会忘了你们的。

<!--more-->
### Homebrew安装
Homebrew是一款自由及开放源代码的软件包管理系统，用以简化Mac OS X系统上的软件安装过程，Homebrew以Ruby语言写成，针对于Mac OS X操作系统自带Ruby的版本。默认安装在/usr/local，由一个核心git版本库构成，以使用户能更新Homebrew。是 OS X 不可或缺的套件管理器。
安装之前，你可以先检查一下电脑上是否已经安装了Homebrew，检查方式如下：
在终端执行下列命令：
```
brew -v
```
如果已经安装了，就像下图一样，显示版本号。
![](https://github.com/loonggg/BlogImages/blob/master/ReactNative/1.png?raw=true)
如果没有安装，那就可以用下面这种方式，进行安装，在终端上直接输入下面的命令即可：
```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
### Node.js的安装
>Node.js®是一个基于Chrome V8 引擎的 JavaScript 运行时。 Node.js 使用高效、轻量级的事件驱动、非阻塞 I/O 模型。Node.js 之生态系统是目前最大的开源包管理系统。我们都知道React Native是需要使用js开发的，所以Node.js是必不可少的安装。

安装Node.js也很简单，如下：
```
nvm install node && nvm alias default node
```
可能你发现了，在Terminal终端上使用的命令是nvm，如果你没有安装nvm是会提示command not found的，所以我们还得先安装nvm。
nvm 是 Node.js 的版本管理器，可以轻松安装各个版本的 Node.js 版本。安装方式如下：
我们可以通过Homebrew安装：
```
brew install nvm
```
或者通过这里的方式安装，地址：https://github.com/creationix/nvm#installation


安装完了nvm，我们这里最好配置一下环境变量到.bash_profile文件，因为如果不配置的话，容易出现这个错误**nvm command not found**。
配置方式如下：
```
export NVM_DIR="/Users/loonggg/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"  # This loads nvm
```
检查是否安装好了node和nvm我们可以通过如下方式：
```
bogon:~ loonggg$ node -v
v5.10.1
bogon:~ loonggg$ npm -v
3.8.3
```
### 安装watchman 和 flow
* Watchman 是 facebook 的一个开源项目，它开源用来监视文件并且记录文件的改动情况，当文件变更它可以触发一些操作,例如执行一些命令等等。
* flow是一个Javascript静态类型检查器。Flow为Javascript添加了静态类型检查，以提高开发效率和代码质量。

安装方式如下：
```
brew install watchman
brew install flow
```

### 安装React Native
我们使用npm进行安装，如下：
```
npm install -g react-native-cli
```
安装完React Native之后，要想运行或者初始化一个项目，然后运行到模拟器或者真机，我们需要搭建一个Android或者ios开发环境，我这里只介绍android，相信想学习React Native的同学，电脑上都基本上有了Android的开发环境。但是可能会有坑，有一个大坑就是得配置SDK的环境变量：ANDROID_HOME。
#### SDK环境变量的配置
1. 启动Terminal终端工具
2. 输入cd ~/ 进入当前用户的home目录
3. 创建：
```
touch .bash_profile
```
4. 打开并编辑：
```
open .bash_profile
```
5. 在文件中写入以下内容：export PATH=${PATH}:/Users/loonggg/Application/android-sdk-mac_x86/tools:/Users/loonggg/Application/android-sdk-mac_x86/platform-tools 
友情提示：上述路径，请换成自己电脑上的SDK所在路径
6. 执行如下命令：
```
source .bash_profile 
```
7. 验证：输入adb回车。如果未显示command not found，说明此命令有效，环境便亮设置完成。

### 创建我们的第一个React Native应用
初始化项目
```
react-native init AwesomeProject
```
上面的AwesomeProject这个项目名字，你可以自己随意定义，自己命名，没有限制。
### 运行项目
* 切换到AwesomeProject的主目录
* 运行项目命令
```
react-native run-android
```
* 我们使用编辑器打开和修改index.android.js文件，调出模拟器菜单键，选择重新载入 js 即可看到变化。
启动后，模拟器效果图：

![](https://github.com/loonggg/BlogImages/blob/master/ReactNative/2.png?raw=true)

到这里我们就把环境配置讲完了，关于windows上的安装，我会给大家发篇资料或者视频的。

