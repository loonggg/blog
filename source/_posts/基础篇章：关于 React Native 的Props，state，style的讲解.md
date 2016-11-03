---
title: 基础篇章：关于 React Native 的props，state，style的讲解
date: 2016-10-01 12:39:04
tags:
- React Native
- 基础组件
categories: 
- 技术博客
- React Native
---
>React Native看起来很像React，其实React Native就是根据React发展而来的，只不过其基础组件是原生组件而非web组件。所以在体验交互上更加接近原生操作，所以体验比web效果好很多。加上可以跨平台，体验又接近原生，所以自15年以来比较火。

我们要想理解React Native应用的基本结构，我们首先需要先了解一些基本的React的概念，比如JSX语法、组件、state状态以及props属性。所以这篇我们重点讲讲Props，state和style样式。今天讲解的内容，都是根据React Native官方文档上的内容来的。
官方文档地址：
Props ：
[https://facebook.github.io/react-native/docs/props.html]
state：
[https://facebook.github.io/react-native/docs/state.html]
style：
[https://facebook.github.io/react-native/docs/style.html]
<!--more-->

## props
### props(属性) 概念
大多数组件在创建的时候就可以用各种参数来进行定制。用于定制的这些参数就称为props（属性）。所谓props，就是属性传递，而且是单向传递的。属性多的时候，可以传递一个对象，这是es6中的语法。

#### 例子1:
官网给的第一个例子是用现成的一个Image基础组件来解释这个概念的，例子如下：
```js
import React, { Component } from 'react';
import { AppRegistry, Image } from 'react-native';

class Bananas extends Component {
  render() {
	let pic = {
	  uri: 'https://upload.wikimedia.org/wikipedia/commons/d/de/Bananavarieties.jpg'
	};
	return (
	  <Image source={pic} style={{width: 193, height: 110}}/>
	);
  }
}

AppRegistry.registerComponent('Bananas', () => Bananas);
```
当我们创建一个image图片的时候，我们可以使用名为source的props属性去控制这个image显示什么图片。

注意{pic}外围有一层括号，我们需要用括号来把pic这个变量嵌入到JSX语句中。我们可以把任意合法的JavaScript表达式通过括号嵌入到JSX语句中。

为了更好的说明props的用法和概念，我把上面的例子又修改了一下，我的这个例子只是为了更好的说明props属性的用法，不建议大家这么使用，毕竟image是现成的基础组件。
#### 修改后的例子：
```js
import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  Image,
  View
} from 'react-native';

class Bananas extends Component {
  render() {
	return (
	  <Image source={this.props.image} style={{width: 120, height: 80}}/>
	);
  }
}

class FirstProject extends Component {
  render() {
	let pic = {
	  uri: 'https://upload.wikimedia.org/wikipedia/commons/d/de/Bananavarieties.jpg'
	};
	return (
	  <View style={{padding: 10}}>
		<Bananas image ={pic}/>
	  </View>
	);
  }
}

AppRegistry.registerComponent('FirstProject', () => FirstProject);

```
这里我自定义了一个名为Bananas的组件，定义了一个名为image的属性props，注意image是小些的， 大些的Image是官方图片基础组件。在自定义的Bananas组件中的Image组件中，引用了我们定义的image的属性props。这样一对比，可能大家就更能清楚的理解了props的用法了。说白了就是定制参数，然后传值。

#### 例子2
官方给的第二个例子，就非常清楚了，是这样的。
```js
import React, { Component } from 'react';
import { AppRegistry, Text, View } from 'react-native';

class Greeting extends Component {
  render() {
	return (
	  <Text>Hello {this.props.name}!</Text>
	);
  }
}

class LotsOfGreetings extends Component {
  render() {
	return (
	  <View style={{alignItems: 'center'}}>
		<Greeting name='Rexxar' />
		<Greeting name='Jaina' />
		<Greeting name='Valeera' />
	  </View>
	);
  }
}

AppRegistry.registerComponent('LotsOfGreetings', () => LotsOfGreetings);
```
意思就是：自定义了一个名为Greeting的组件，然后，属性名为name，传不同的name值，在Text显示不同的名字。

## state
React靠一个state来维护状态，当state发生变化则更新DOM。控制一个组件，一般有两种数据类型，一种是props，一种是state。props是在父组件中设置，一旦指定，它的生命周期是不可以改变的。对于组件中数据的变化，我们是通过state来控制的。

一般情况下，我们初始化state状态，是在constructor构造函数中，然后如果需要改变时，我们可以调用setState方法。官方给的例子时一个闪烁的文字的例子，看看官网的例子是如何制作文字闪烁效果的。

### 例子
```js
import React, { Component } from 'react';
import { AppRegistry, Text, View } from 'react-native';

class Blink extends Component {
  constructor(props) {
	super(props);
	this.state = { showText: true };

	// 每1000毫秒对showText状态做一次取反操作
	setInterval(() => {
	  this.setState({ showText: !this.state.showText });
	}, 1000);
  }

  render() {
	// 根据当前showText的值决定是否显示text内容
	let display = this.state.showText ? this.props.text : ' ';
	return (
	  <Text>{display}</Text>
	);
  }
}

class BlinkApp extends Component {
  render() {
	return (
	  <View>
		<Blink text='I love to blink' />
		<Blink text='Yes blinking is so great' />
		<Blink text='Why did they ever take this out of HTML' />
		<Blink text='Look at me look at me look at me' />
	  </View>
	);
  }
}

AppRegistry.registerComponent('BlinkApp', () => BlinkApp);
```
首先，它是自定义了一个Blink的组件，在构造函数中初始化了state，然后写了一个定时器，每个1秒改变一次状态，然后setState,然后在渲染render()方法中，判断状态的变化，如果是true，显示文字，false显示空。这样一闪一闪的效果就出来了。

然后我们在BlinkApp中使用Blink组件，并传入我们需要的文字内容即可。

效果图如下：
![](https://raw.githubusercontent.com/loonggg/BlogImages/master/ReactNative/6.gif)
其实在实际开发中，我们不需要设置定时器来改变状态，一般情况下，我们都是在获取到服务器的数据时或者用户输入时，更新状态去显示最新的数据。这是我们就是通过setState来做到的。

## style
在React Native中我们不需要使用什么特殊的语言或者语法去定义样式，我们还是使用JavaScript来写样式。所有的核心组件都接受名为style的属性。唯一的不同就是属性样式的命名使用了驼峰命名法，例如将background-color改为backgroundColor。

### 例子：
```js
import React, { Component } from 'react';
import { AppRegistry, StyleSheet, Text, View } from 'react-native';

class FirstProject extends Component {
  render() {
	return (
	  <View>
		<Text style={styles.red}>just red</Text>
		<Text style={styles.bigblue}>just bigblue</Text>
		<Text style={[styles.bigblue, styles.red]}>bigblue, then red</Text>
		<Text style={[styles.red, styles.bigblue]}>red, then bigblue</Text>
		<Text style={{color:'red' , fontSize:30}}>
			非著名
	  <Text style={{color:'blue'}}>
		  程序员
	  </Text>
		</Text>
	  </View>
	);
  }
}

const styles = StyleSheet.create({
  bigblue: {
	color: 'blue',
	fontWeight: 'bold',
	fontSize: 30,
  },
  red: {
	color: 'red',
  },
});

AppRegistry.registerComponent('FirstProject', () => FirstProject);
```
在js中，最简单的样式用法是style属性可以是一个普通的JavaScript对象。但是这里我们可以传入一个数组的样式，在数组中位置后面的样式覆盖前面的样式，后面的优先级比较高。所以我们可以这样使用去继承样式。

随着组件的复杂性，我们建议使用StyleSheet.create来集中定义组件的样式，就像上面的用法一样，当然也支持单独的使用，就像上面例子中的最后一个的用法，上面文字的展示中，第三个，第四个使用了数组样式的方法，后面的样式覆盖了前面的样式，最后一个使用了内嵌的方式，做成了前半部分显示红色，后半部分显示蓝色的效果。

效果图如下：
![](https://raw.githubusercontent.com/loonggg/BlogImages/master/ReactNative/5.png)



