---
title: 基础篇章：关于 React Native 之 ListView 组件的讲解
date: 2016-11-02 10:39:04
tags:
- React Native
- 基础组件
- ListView
categories: 
- 技术博客
- React Native
---
>我们讲完ScrollView组件，其实顺其自然的就应该讲解ListView,对于前段和移动端的开发人员应该非常熟悉这样的控件吧，具体是做什么的，我感觉不用我讲了吧。我们来看看它怎么使用吧。

大家好，我是ListView，我是React Native大家族中基础组件中，一个核心组件。我可以高效的展示垂直滚动的变化的数据列表，而且这个列表有一个特点就是结构和数据比较相似才可以哦。

我和ScrollView那家伙不太相同，我更适于长列表数据，且元素个数可以增删。和ScrollView不同的是，我并不立即渲染所有元素，而是优先渲染屏幕上可见的元素。怎么样？是不是感觉我更聪明？

我有两个必须的属性是dataSource和renderRow。dataSource是列表的数据源，而renderRow则逐个解析数据源中的数据，然后返回一个设定好格式的组件来渲染。举个例子：我最基本的使用方式就是创建一个ListView.DataSource数据源，然后给它传递一个普通的数据数组，再使用数据源来实例化一个ListView组件，并且定义它的renderRow回调函数，这个函数会接受数组中的每个数据作为参数，返回一个可渲染的组件（作为我的每一行）。

>记住：rowHasChanged函数也是我的必需属性。用于比较两行数据是否是同一个数据来判断某行数据是否变化了。

<!--more-->
## 官方简单例子
```js
class MyComponent extends Component {
  constructor() {
	super();
	const ds = new ListView.DataSource({rowHasChanged: (r1, r2) => r1 !== r2});
	this.state = {
	  dataSource: ds.cloneWithRows(['row 1', 'row 2']),
	};
  }

  render() {
	return (
	  <ListView
		dataSource={this.state.dataSource}
		renderRow={(rowData) => <Text>{rowData}</Text>}
	  />
	);
  }
}
```

## 高级属性
想我这么聪明的组件，我当然还支持一些高级的特性，比如：给每组数组加一个粘节标题，也就是类似于通讯录中其首字母会在滑动过程中吸附在屏幕上方，支持页眉和页脚，也就是可以在列表中添加头部和尾部。在到达列表尾部的时候调用回调函数(onEndReached)，还有在视野内可见的数据变化时调用回调函数(onChangeVisibleRows)，以及一些性能方面的优化。

在我母亲制定的官方介绍中，这么说：有一些性能优化使得我ListView可以滚动的更加平滑，尤其是在动态加载可能很大（或者概念上无限长的）数据集的时候：
 * 只更新变化的行 - 提供了rowHasChanged函数可以告诉ListView它是否需要重绘一行数据。
 *  限制频率的行渲染 - 默认情况下，每次消息循环只有一行会被渲染（可以用pageSize属性配置）。这把较大的工作分散成小的碎片，以降低因为渲染而导致丢帧的可能性。

## 基本属性
介绍完我的高级功能特性，再来看看我的基本属性吧，懂了这些，你可以玩我跟玩孩子似的，运用自如。

我前面说了，我这人比ScrollView那家伙聪明多了，所以它的属性，我都能用，这里关于和ScrollView相同的属性就不赘述了。看看我的与众不同，比它聪明在哪吧？
* dataSource 传入的数据源
* enableEmptySections bool  空内容的sections是否被渲染，默认是会渲染
* initialListSize number 指定在组件刚挂载的时候渲染多少行数据。用这个属性来确保首屏显示合适数量的数据，而不是花费太多帧逐步显示出来。
* onChangeVisibleRows function 当可见的行发生变化的时候回调该函数。visibleRows参数对所有可见的行为{selectionID:{rowId:true}}的形式，changedRow参数对已经改变可见的行为{selectionID:{rowID:true|false}}。该值true代表可见，false代表在视图之外不可见的行。
* onEndReached function 当所有的数据都已经渲染过，并且列表被滚动到距离最底部不足onEndReachedThreshold个像素的距离时调用。原生的滚动事件会被作为参数传递。译注：当第一次渲染时，如果数据不足一屏（比如初始值是空的），这个事件也会被触发。
* onEndReachedThreshold number 调用onEndReached之前的临界值，单位是像素。
* pageSize number  每一次事件的循环渲染的行数。
* removeClippedSubviews bool 用于提升大列表的滚动性能。需要给行容器添加样式overflow:'hidden'。（Android已默认添加此样式）。此属性默认开启。
* renderFooter function 方法  ()=>renderable ，在每次渲染过程中头和尾总会重新进行渲染。如果发现该重新绘制的性能开销比较大的时候，可以使用StaticContainer容器或者其他合适的组件。在每一次渲染过程中Footer(尾)该会一直在列表的底部，header(头)该会一直在列表的头部
* renderHeader function 与上同理
* renderRow function (rowData, sectionID, rowID, highlightRow) => renderable 从数据源(Data source)中接受一条数据，以及它和它所在section的ID。返回一个可渲染的组件来为这行数据进行渲染。默认情况下参数中的数据就是放进数据源中的数据本身，不过也可以提供一些转换器。如果某一行正在被高亮（通过调用highlightRow函数），ListView会得到相应的通知。当一行被高亮时，其两侧的分割线会被隐藏。行的高亮状态可以通过调用highlightRow(null)来重置。
* renderScrollComponent function 返回在列表行呈现的滚动组件的功能。默认为ScrollView。
* renderSectionHeader function (sectionData, sectionID) => renderable 如果提供了此函数，会为每个小节(section)渲染一个粘性的标题。粘性是指当它刚出现时，会处在对应小节的内容顶部；继续下滑当它到达屏幕顶端的时候，它会停留在屏幕顶端，一直到对应的位置被下一个小节的标题占据为止。
* renderSeparator function 如果提供了此属性，一个可渲染的组件会被渲染在每一行下面，除了小节标题的前面的最后一行。在其上方的小节ID和行ID，以及邻近的行是否被高亮会作为参数传递进来。
* scrollRenderAheadDistance number 当该行进入屏幕多少像素以内之后就开始渲染该行
* stickyHeaderIndices [number]  ios独有 一个子视图下标的数组，用于决定哪些成员会在滚动之后固定在屏幕顶端。

## 实例演示
### 效果图
来，看看我美不美，好不好用，我的真实面目如下：
![](https://raw.githubusercontent.com/loonggg/BlogImages/master/ReactNative/listview/4.gif)

### 代码
```js
import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  ListView,
  Image,
  TouchableHighlight,
  View
} from 'react-native';

class ListViewDemo extends Component {
  constructor(props) {
	super(props);
	const ds = new ListView.DataSource({rowHasChanged: (r1, r2) => r1 !== r2});
	this.state = {
	  dataSource: ds.cloneWithRows(this._genRows(-1))
	};
  }

  _genRows(flag){
		const dataBlob = [];
		for(let i = 0 ; i< 88 ; i ++ ){
		  if(i == flag){
			dataBlob.push("非著名程序员+我被打了"+i);
		  }else{
			 dataBlob.push("非著名程序员"+i);
		  }
		}
		return dataBlob;
	}

  render() {
	return (
		<ListView
		  dataSource={this.state.dataSource}
		  renderRow={this._renderRow.bind(this)}
		/>

	);
  }

  _renderRow (rowData,sectionID, rowID) {
	return (
		<TouchableHighlight onPress={() => {
		  this._pressRow(rowData,rowID);
		}}
		underlayColor='red'
		>
		  <View>
			<View style={styles.row}>
			  <Image style={{width:40,height:40}} source={require('./Thumbnails/head.jpg')}/>
			  <Text style={{flex:1,fontSize:20,marginLeft:20}}>
				{rowData}
			  </Text>
			</View>
		  </View>
		</TouchableHighlight>
	);
   }

  _pressRow(rowData,rowID){
		alert(rowData);
		this.setState({dataSource: this.state.dataSource.cloneWithRows(
		this._genRows(rowID)
	)});
	}
}

const styles = StyleSheet.create({
  container: {
	flex: 1,
	justifyContent: 'center',
	alignItems: 'center',
	backgroundColor: '#F5FCFF',
  },
  row: {
	flexDirection: 'row',
	justifyContent: 'center',
	alignItems:'center',
	padding: 10,
  },
});

AppRegistry.registerComponent('ListViewDemo', () => ListViewDemo);
```

ok，关于ListView组件的讲解大概就先讲到这里，更多的内容和实例，欢迎大家移步到官网，看文档，但是官网上大部分的例子用的是es5的语法。
官方文档地址：https://facebook.github.io/react-native/docs/listview.html













 










	


