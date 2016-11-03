---
title: 基础篇章：React Native之 ScrollView 的讲解
date: 2016-10-05 10:39:04
tags:
- React Native
- 基础组件
- ScrollView
categories: 
- 技术博客
- React Native
---
>大家好，我是ScrollView，相信做过移动或者前端开发的人肯定都很熟悉我，对，我就是那个可以滚动的容器，滚有点难听，我是可以滑动的容器，我滑动起来，摩擦摩擦，似魔鬼的步伐。我不仅可以上下滚动，就是垂直，还可以左右滚动，这叫有水平。我厉不厉害？我这个人，为人心胸宽广，可以包容很多东西，我这叫宰相肚子里能撑船，什么组件，什么视图都可以放进来，主要是本人太饿了，啥都喜欢吃。这就是我的自我介绍。

关于我，我还想说，我这个人身上笑点比较低，随便一触摸就想笑，当然，你们摸我，让我干什么事，我还是很听话的，唯独有一点就是你们别想让我滚，让我滚是有条件的，条件就是必须让我吃饱，吃撑了才可以，这样我才有力气滚动（视图高度一定才可以滚动）。要么设置我的身高是固定的，当然我想长高，所以不建议这么做，要么就是设置我上级的高度，当然要这样做，不要忘了设置flex:1,要不然一样没用。
<!--more-->
## 我的性格
来看看我有哪些性格特点，只有知道了我的性格特点，才更能容易了解我，针对我，容易控制我啊，如果你不了解我，就想让我帮你干活？做梦去吧。
* contentContainerStyle 这个样式会应用到一个内层的内容容器上，所有的子视图都会包裹在内容容器内。
* horizontal 如果设为true，意思是我吃的东西都是左右，在水平方向上排列的，貌似不太容易消化（玩笑），默认false，当然是垂直方向了。
* keyboardDismissMode enum('none', 'interactive', 'on-drag')  当我滚动的时候，是否隐藏键盘
	* none（默认值），拖拽时不隐藏软键盘。
	* on-drag 当拖拽开始的时候隐藏软键盘。
	* interactive 软键盘伴随拖拽操作同步地消失，并且如果往上滑动会恢复键盘。安卓设备上不支持这个选项，会表现的和none一样。
* keyboardShouldPersistTaps 当此属性为false的时候，在软键盘激活之后，点击焦点文本输入框以外的地方，键盘就会隐藏。如果为true，滚动视图不会响应点击操作，并且键盘不会自动消失。默认值为false。
* onContentSizeChange function 该函数方法会在ScrollView内部可滚动内容的视图发生变化时调用。
* onScroll function 在滚动的过程中，每帧最多调用一次此回调函数。调用的频率可以用scrollEventThrottle属性来控制。
* pagingEnabled 如果为true，滚动视图的滚动视图大小的倍数滚动时停止。这可用于水平分页。默认值false。
* refreshControl 告诉RefreshControl组件，为我供下拉刷新功能。
* removeClippedSubviews  （实验属性） 当为true的时候。在ScrollView视图之外的视图(该视图的overflow属性值必须要为hidden)会从被暂时移除，该设置可以提高滚动的性能。
* scrollEnabled 为false时，内容视图不可以滚动，默认值true。
* showsHorizontalScrollIndicator  当为true时，显示水平滚动条
* showsVerticalScrollIndicator 与上面正好相反。

## 我的穿衣打扮
来，一起来看看，我有哪些外在的服饰和化妆品，可以使用更佳美观和漂亮，修饰我的内在和外在。
* backfaceVisibility (['visible', 'hidden']) 显示还是隐藏
* backgroundColor color 背景色
* borderBottomColor color 底部边框颜色
* borderBottomLeftRadius number 左下角圆角大小
* borderBottomRightRadius 自己翻译（与上同理）
* borderBottomWidth 底部边框宽度
* borderColor color 边框颜色
* borderLeftColor color
* borderLeftWidth number 同理
* borderRadius number 四周圆角大小
* borderRightColor color
* borderRightWidth number
* borderStyle （[’solid', 'dotted', 'dashed']) 边框的样式，是实现，还是点，还是虚线
* borderTopColor color
* borderTopLeftRadius number
* borderTopRightRadius number
* borderTopWidth number
* borderWidth number 边框宽度
* opacity number 设置透明度
* overflow (['visible', 'hidden'])
* androidelevation number android5.0以上有的，立体阴影效果
* 我只介绍了以上常用的的风格，还有几个和android相关，还有很多与ios相关的属性，我就不再介绍了，自己去官方文档查查吧。地址：[https://facebook.github.io/react-native/docs/scrollview.html]

## 我的秀丽身材
闻其声不见其人，光知道我，没见过我岂不是很out？像我这么美的人，你们不用我，是不是有点那个啥？哈哈……
### 魔鬼样子
![](https://raw.githubusercontent.com/loonggg/BlogImages/master/ReactNative/7.gif)

### 逻辑实现
```js
import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  View,
  Image,
  ScrollView,
  TouchableOpacity,
} from 'react-native';

class ImageDemo extends Component {
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
		  style={styles.button}
		  onPress={() => { _scrollView.scrollTo({y: 0}); }}>
		  <Text>让我滚回去</Text>
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

AppRegistry.registerComponent('ImageDemo', () => ImageDemo);
```

ok,到这里ScrollView就讲完了，由于非常简单，大家赶紧去练练手吧！不懂的可以在下面留言，由于我也是第一次学，欢迎大家提出不足，一起交流学习。

