---
title: 基础篇章：关于 React Native 之 Touchable 系列组件的讲解
date: 2016-10-31 10:39:04
tags:
- React Native
- 基础组件
- TouchableHighlight
- TouchableNativeFeedback
- TouchableOpacity
- TouchableWithoutFeedback
categories: 
- 技术博客
- React Native
---
>在上篇 ScrollView 的讲解的实例中，我们使用了TouchableOpacity的点击事件组件，可能很多人不是很理解，所以今天我们来讲讲触摸组件，这样有利于我们以后写实例。

## Touchable前传
Touchable系列组件，为什么是系列组件呢，去看官方文档我们知道，文档导航组件介绍中，有四个关于Touchable的组件，分别是：TouchableHighlight ，TouchableNativeFeedback，TouchableOpacity，TouchableWithoutFeedback。为什么要放到一起讲呢，因为这四个组件功能差不多，只不过是效果不太一样，所以放到一起讲很方便，而是从名字我们就可以看出触摸有效果和没效果之分，所以TouchableHighlight ，TouchableNativeFeedback，TouchableOpacity这三个带有触摸效果的组件是继承与TouchableWithoutFeedback的，它是触摸不带有反馈效果的。

## TouchableWithoutFeedback
除非你有一个非常的原因和理由，否则不要轻易使用这个组件。所有能够响应触摸事件的元素都应该带有一个反馈效果，这就是为什么web应用体验总是显得不如原生效果好的原因之一。

>友情提示：TouchableWithoutFeedback supports only one child If you wish to have several child components, wrap them in a View.（TouchableWithoutFeedback只支持一个子节点，如果你希望有多个子元素，请用view包裹住它们再使用）。

<!--more-->
### 属性
我们一起来看看，它有哪些属性方法呢？
* accessibilityComponentType 顾名思义：设置可访问的组件类型
* accessibilityTraits 设置可访问的特征
* accessible bool 当前组件是否可以访问
* delayLongPress number 设置延迟毫秒的时间，从onPressIn方法开始到onLongPress被调用之前
* delayPressIn 设置延迟时间，从用户触摸到delayPressIn被调用之间
* delayPressOut  number 设置延迟时间，从触摸事件释放到delayPressOut被调用这段时间
* disabled bool 如果为true，禁用次组件所有的交互
* hitSlop {top: number, left: number, bottom: number, right: number} 扩大了按钮的外延范围
* onLayout function 当布局加载或者改变时被调用
* onLongPress function 长按组件时调用该方法
* onPress function 当用户点击时被调用
* onPressIn function 当用户开始触摸组件时回调方法
* onPressOut function  同上相反，当用户完成触摸时调用
* pressRetentionOffset {top: number, left: number, bottom: number, right: number} 在当前视图不能滚动的前提下指定这个属性，可以决定当手指移开多远距离之后，会不再激活按钮。但如果手指再次移回范围内，按钮会被再次激活。只要视图不能滚动，你可以来回多次这样的操作。确保你传入一个常量来减少内存分配。

我说了该组件官网说了，不建议使用，因为没有反馈效果，所以常用的是其他三种，而且都是继承自它。我们主要讲解下面是三种效果。

## TouchableHighlight
TouchableHighlight，我们可以翻译一下什么意思？Touchable可触摸，Highlight高亮，所以这个触摸组件的效果是点击会出现高亮的反馈效果。

TouchableHighlight组件用于封装视图，使其可以正确响应用户的触摸操作。当我们按下的时候，封装的视图的不透明度会降低，同时会有一个底层的颜色透过并被用户看到，使得视图变暗或变亮。在底层实现上，实际会创建一个新的视图到视图层级中，如果使用的方法不正确，有时候会导致一些不希望出现的视觉效果出现。比如没有给视图的backgroundColor显式声明一个不透明的颜色。

>友情提示：TouchableHighlight只支持一个子节点，如果你希望有多个子元素，请用view包裹住它们再使用。

### TouchableHighlight属性
上面我们说了，TouchableHighlight继承于TouchableWithoutFeedback，所以TouchableWithoutFeedback得属性，我们都可以使用，这里就不重复介绍了。咱们介绍它自己有的。
* activeOpacity number 设置封装的视图在被触摸操作激活时用多少不透明度显示（通常在0到1之间）。
* onHideUnderlay function 当底层隐藏后立即调用
* onShowUnderlay function 同上面相反，显示时，立即调用
* style 风格样式的使用同View的一样，这里就省略了，不知道的去看View的style
*  underlayColor  当视图被触摸或者点击时，显示的颜色

### 效果展示
>我们还是拿前面那个Image和ScrollView中使用的例子，只不过我们现在给它加上按压效果。来看看吧，先看效果图，具体代码，我在最后放出来。

效果图如下：
![](https://raw.githubusercontent.com/loonggg/BlogImages/master/ReactNative/touchable/1.gif)

## TouchableOpacity
关于TouchableOpacity的例子，我们在上篇ScrollView中用到过了，现在讲讲概念。

TouchableOpacity组件用于封装视图，它使其可以正确响应触摸操作。当按下的时候，封装的视图的不透明度会降低，但是这个过程并不会真正改变视图层级，而且我们非常容易简单的添加到应用而且不会产生其他额外的一些错误。

### 属性和方法
同理，TouchableOpacity继承于TouchableWithoutFeedback，所以TouchableWithoutFeedback得属性，我们都可以使用，这里就不重复介绍了。咱们介绍它自己有的。
* activeOpacity number 设置按压效果时，视图的透明度
* setOpacityTo(value) 这是一个设置不透明度的方法

### 效果展示
![](https://raw.githubusercontent.com/loonggg/BlogImages/master/ReactNative/touchable/2.gif)

## TouchableNativeFeedback
注意：这个组件仅限于在Android平台使用，顾名思义：就是触摸事件带有本地原声反馈效果。在Android设备上，这个组件利用原生状态来渲染触摸的反馈。目前它只支持一个单独的View实例作为子节点。在底层实现上，实际会创建一个新的RCTView结点替换当前的子View，并附带一些额外的属性。而且原生触摸操作反馈的背景可以使用background属性来自定义。

### 属性
同理，TouchableNativeFeedback继承于TouchableWithoutFeedback，所以TouchableWithoutFeedback得属性，我们都可以使用，这里就不重复介绍了。咱们介绍它自己有的。
* background 决定在触摸反馈的时候显示什么类型的背景。它接受一个有着type属性和一些基于type属性的额外数据的对象。我们推荐使用以下的静态方法之一来创建这个对象：
	* TouchableNativeFeedback.SelectableBackground() - 会创建一个对象，表示安卓主题默认的对于被选中对象的背景。(?android:attr/selectableItemBackground)
	* TouchableNativeFeedback.SelectableBackgroundBorderless() - 会创建一个对象，表示安卓主题默认的对于被选中的无边框对象的背景。(?android:attr/selectableItemBackgroundBorderless)。只在Android API level 21+适用。
	* TouchableNativeFeedback.Ripple(color, borderless) - 会创建一个对象，当按钮被按下时产生一个涟漪状的背景，你可以通过color参数来指定颜色，如果参数borderless是true，那么涟漪还会渲染到视图的范围之外。（参见原生的actionbar buttons作为该效果的一个例子）。这个背景类型只在Android API level 21+适用。

### 效果展示
看看5.0以后的新特性，水波纹特性，如下：
![](https://raw.githubusercontent.com/loonggg/BlogImages/master/ReactNative/touchable/3.gif)
怎么样？很漂亮吧？

## 综合实例
代码如下：
```js
import React, { Component } from 'react';
import {
 AppRegistry,
  StyleSheet,
  Text,
  Image,
  ScrollView,
  TouchableOpacity,
  TouchableHighlight,
  TouchableNativeFeedback,
  View
} from 'react-native';

class TouchableDemo extends Component {
  render() {
	return (
	  <View style={styles.container}>
		 <View style={styles.title_view}>
		 <Text style={styles.title_text}>
			   空间动态
		 </Text>
		</View>
		<ScrollView  ref={(scrollView) => { _scrollView = scrollView; }}>
		<View style={styles.three_image_view}>
		 <View style={styles.vertical_view}>
			  <Image source={require('./img/igs.png')} style={{alignSelf:'center',width:45,height:45}} />
			  <Text style={styles.top_text}>
				好友动态
			  </Text>
		 </View>
		  <View style={styles.vertical_view}>
			  <Image source={require('./img/eqc.png')} style={{alignSelf:'center',width:45,height:45}}/>
			  <Text style={styles.top_text}>
				附近
			  </Text>
		 </View>
		  <View style={styles.vertical_view}>
			  <Image source={require('./img/iei.png')} style={{alignSelf:'center',width:45,height:45}}/>
			  <Text style={styles.top_text} >
				兴趣部落
			  </Text>
		 </View>
		</View>
		<View style={{height:30,backgroundColor:'#f9f9fb'}}/>
		<TouchableHighlight 
		  underlayColor="blue"
		  activeOpacity={0.5}
		  onPress={()=>{
		  console.log('我被点击了');
		  }}
		>
			<View style={styles.rectangle_view}>
			  <View style={{flexDirection:'row',alignItems: 'center'}}>
				  <Image source={require('./img/nsa.png')} style={{alignSelf:'center',width:30,height:30}}/>
				  <Text style={styles.rectangle_text} >
					羽毛球
				  </Text>
			  </View>
			  <Image source={require('./img/ppe.png')} style={{alignSelf:'center',width:20,height:20}}/>
			</View>
		 </TouchableHighlight>
		 <TouchableNativeFeedback 
			background={TouchableNativeFeedback.SelectableBackground()}
			onPress={()=>{
			console.log('我被点击了');
			}}
		 >
			<View style={styles.rectangle_view}>
			  <View style={{flexDirection:'row',alignItems: 'center'}}>
				  <Image source={require('./img/nsb.png')} style={{alignSelf:'center',width:30,height:30}}/>
				  <Text style={styles.rectangle_text} >
					火车票
				  </Text>
			  </View>
			  <Image source={require('./img/ppe.png')} style={{alignSelf:'center',width:20,height:20}}/>
			</View>
		 </TouchableNativeFeedback>
		 <View style={styles.rectangle_view}>
		  <View style={{flexDirection:'row',alignItems: 'center'}}>
			  <Image source={require('./img/nrz.png')} style={{alignSelf:'center',width:30,height:30}}/>
			  <Text style={styles.rectangle_text} >
				视频
			  </Text>
		  </View>
		  <Image source={require('./img/ppe.png')} style={{alignSelf:'center',width:20,height:20}}/>
		 </View>
		 <View style={styles.rectangle_view}>
		  <View style={{flexDirection:'row',alignItems: 'center'}}>
			  <Image source={require('./img/nsa.png')} style={{alignSelf:'center',width:30,height:30}}/>
			  <Text style={styles.rectangle_text} >
				羽毛球
			  </Text>
		  </View>
		  <Image source={require('./img/ppe.png')} style={{alignSelf:'center',width:20,height:20}}/>
		 </View>
		 <View style={styles.rectangle_view}>
		  <View style={{flexDirection:'row',alignItems: 'center'}}>
			  <Image source={require('./img/nsb.png')} style={{alignSelf:'center',width:30,height:30}}/>
			  <Text style={styles.rectangle_text} >
				火车票
			  </Text>
		  </View>
		  <Image source={require('./img/ppe.png')} style={{alignSelf:'center',width:20,height:20}}/>
		 </View>
		 <View style={styles.rectangle_view}>
		  <View style={{flexDirection:'row',alignItems: 'center'}}>
			  <Image source={require('./img/nrz.png')} style={{alignSelf:'center',width:30,height:30}}/>
			  <Text style={styles.rectangle_text} >
				视频
			  </Text>
		  </View>
		  <Image source={require('./img/ppe.png')} style={{alignSelf:'center',width:20,height:20}}/>
		 </View>
		 <View style={styles.rectangle_view}>
		  <View style={{flexDirection:'row',alignItems: 'center'}}>
			  <Image source={require('./img/nsa.png')} style={{alignSelf:'center',width:30,height:30}}/>
			  <Text style={styles.rectangle_text} >
				羽毛球
			  </Text>
		  </View>
		  <Image source={require('./img/ppe.png')} style={{alignSelf:'center',width:20,height:20}}/>
		 </View>
		 <View style={styles.rectangle_view}>
		  <View style={{flexDirection:'row',alignItems: 'center'}}>
			  <Image source={require('./img/nsb.png')} style={{alignSelf:'center',width:30,height:30}}/>
			  <Text style={styles.rectangle_text} >
				火车票
			  </Text>
		  </View>
		  <Image source={require('./img/ppe.png')} style={{alignSelf:'center',width:20,height:20}}/>
		 </View>
		 <View style={styles.rectangle_view}>
		  <View style={{flexDirection:'row',alignItems: 'center'}}>
			  <Image source={require('./img/nrz.png')} style={{alignSelf:'center',width:30,height:30}}/>
			  <Text style={styles.rectangle_text} >
				视频
			  </Text>
		  </View>
		  <Image source={require('./img/ppe.png')} style={{alignSelf:'center',width:20,height:20}}/>
		 </View>
		 <View style={styles.rectangle_view}>
		  <View style={{flexDirection:'row',alignItems: 'center'}}>
			  <Image source={require('./img/nsa.png')} style={{alignSelf:'center',width:30,height:30}}/>
			  <Text style={styles.rectangle_text} >
				羽毛球
			  </Text>
		  </View>
		  <Image source={require('./img/ppe.png')} style={{alignSelf:'center',width:20,height:20}}/>
		 </View>
		 <View style={styles.rectangle_view}>
		  <View style={{flexDirection:'row',alignItems: 'center'}}>
			  <Image source={require('./img/nsb.png')} style={{alignSelf:'center',width:30,height:30}}/>
			  <Text style={styles.rectangle_text} >
				火车票
			  </Text>
		  </View>
		  <Image source={require('./img/ppe.png')} style={{alignSelf:'center',width:20,height:20}}/>
		 </View>
		 <View style={styles.rectangle_view}>
		  <View style={{flexDirection:'row',alignItems: 'center'}}>
			  <Image source={require('./img/nrz.png')} style={{alignSelf:'center',width:30,height:30}}/>
			  <Text style={styles.rectangle_text} >
				视频
			  </Text>
		  </View>
		  <Image source={require('./img/ppe.png')} style={{alignSelf:'center',width:20,height:20}}/>
		 </View>
		 <View style={styles.rectangle_view}>
		  <View style={{flexDirection:'row',alignItems: 'center'}}>
			  <Image source={require('./img/nsa.png')} style={{alignSelf:'center',width:30,height:30}}/>
			  <Text style={styles.rectangle_text} >
				羽毛球
			  </Text>
		  </View>
		  <Image source={require('./img/ppe.png')} style={{alignSelf:'center',width:20,height:20}}/>
		 </View>
		 <View style={styles.rectangle_view}>
		  <View style={{flexDirection:'row',alignItems: 'center'}}>
			  <Image source={require('./img/nsb.png')} style={{alignSelf:'center',width:30,height:30}}/>
			  <Text style={styles.rectangle_text} >
				火车票
			  </Text>
		  </View>
		  <Image source={require('./img/ppe.png')} style={{alignSelf:'center',width:20,height:20}}/>
		 </View>
		 <View style={styles.rectangle_view}>
		  <View style={{flexDirection:'row',alignItems: 'center'}}>
			  <Image source={require('./img/nrz.png')} style={{alignSelf:'center',width:30,height:30}}/>
			  <Text style={styles.rectangle_text} >
				视频
			  </Text>
		  </View>
		  <Image source={require('./img/ppe.png')} style={{alignSelf:'center',width:20,height:20}}/>
		 </View>
		 <View style={styles.rectangle_view}>
		  <View style={{flexDirection:'row',alignItems: 'center'}}>
			  <Image source={require('./img/nsa.png')} style={{alignSelf:'center',width:30,height:30}}/>
			  <Text style={styles.rectangle_text} >
				羽毛球
			  </Text>
		  </View>
		  <Image source={require('./img/ppe.png')} style={{alignSelf:'center',width:20,height:20}}/>
		 </View>
		 <View style={styles.rectangle_view}>
		  <View style={{flexDirection:'row',alignItems: 'center'}}>
			  <Image source={require('./img/nsb.png')} style={{alignSelf:'center',width:30,height:30}}/>
			  <Text style={styles.rectangle_text} >
				火车票
			  </Text>
		  </View>
		  <Image source={require('./img/ppe.png')} style={{alignSelf:'center',width:20,height:20}}/>
		 </View>
		 <View style={styles.rectangle_view}>
		  <View style={{flexDirection:'row',alignItems: 'center'}}>
			  <Image source={require('./img/nrz.png')} style={{alignSelf:'center',width:30,height:30}}/>
			  <Text style={styles.rectangle_text} >
				视频
			  </Text>
		  </View>
		  <Image source={require('./img/ppe.png')} style={{alignSelf:'center',width:20,height:20}}/>
		 </View>
		 </ScrollView>
		 <TouchableOpacity
		  activeOpacity={0.2}
		  style={styles.button}
		  onPress={() => { _scrollView.scrollTo({y: 0}); }}>
		  <Text>让我滚回去(注意看按压时透明度的改变)</Text>
		</TouchableOpacity>
	  </View>
	);
  }
}

const styles = StyleSheet.create({
  container: {
	flex: 1,
	backgroundColor: 'white',
  },
   title_view:{
	flexDirection:'row',
	height:50,
	justifyContent: 'center',
	alignItems: 'center',
	backgroundColor:'#27b5ee',
  },
   title_text:{
	color:'white',
	fontSize:20,
	textAlign:'center'
  },
  three_image_view:{
	paddingTop: 15,
	flexDirection:'row',
	justifyContent: 'space-around',
	alignItems: 'center',
	backgroundColor:'white',
  },
  vertical_view:{
	justifyContent: 'center',
	alignItems: 'center',
	backgroundColor:'white',
	paddingBottom:15,
  },
   top_text:{
	marginTop:5,
	color:'black',
	fontSize:16,
	textAlign:'center'
  },
  rectangle_view:{
	paddingTop:8,
	paddingBottom:8,
	paddingLeft:15,
	paddingRight:15,
	flexDirection:'row',
	justifyContent: 'space-between',
	alignItems: 'center',
	backgroundColor:'white',  
	borderBottomColor:'#dedfe0',
	borderBottomWidth:1,
  },
  rectangle_text:{
	color:'black',
	fontSize:16,
	textAlign:'center',
	paddingLeft:8,
  },
  button: {
	margin: 7,
	padding: 5,
	alignItems: 'center',
	backgroundColor: '#eaeaea',
	borderRadius: 3,
  },
});

AppRegistry.registerComponent('TouchableDemo', () => TouchableDemo);

```

关于触摸按压的组件，我们就讲到这里了，东西确实很简单，喜欢看英文的，还是建议看官网，这些内容其实都是我从官网学的，然后根据学的，自己写了一个例子罢了，没有什么难的，希望大家多动手实践吧！














 










	


