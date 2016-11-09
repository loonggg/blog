---
title: 基础篇章：关于 React Native 之 RefreshControl 组件的讲解
date: 2016-11-02 10:39:04
tags:
- React Native
- 基础组件
- ListView
categories: 
- 技术博客
- React Native
---
>我们已经讲完了 ScrollView 和 ListView ，自然而然我们就应该讲的组件就是下拉刷新的喽，因为它们几个是兄弟，常常一块出现，就跟打麻将似的，四缺一不能打，那它们三就是斗地主，三缺一不能玩。今天讲的这个组件就是它们的兄弟：RefreshControl 。

## 介绍
我的母亲官网是这么介绍我的，说：我是大家在使用我的兄弟ScrollView或ListView添加拉刷新功能用的，我们几个好兄弟常常在一起玩。当我的兄弟ScrollView中 scrollY:0时,触发一个onRefresh事件，我就开始工作，下拉刷新起来。

>注意：refreshing 是一个你们可以控制我的属性，这就是为什么你们在使用onRefresh方法时，必须设置为 true，否则，我会立刻停止刷新，不跟你们玩了。

## 我的特性
一如既往的，想要跟我玩，而且要玩的好，必须先得了解我的性格和特点才行，知己知彼，才能运用自如嘛。我这人，比较自信，所以不怕把我的弱点和特点透漏给敌人。

* onRefresh function 当视图开始刷新的时候回调
* refreshing bool 视图是否应该在刷新时显示刷新的指示器。
* colors [color] android专有 指定刷新指示器的颜色，至少设置一种颜色，最多可设置四种颜色，相当于android中的refreshLayout
* enabled bool android 是否启用下拉刷新功能
* progressBackgroundColor color android 刷新指示器的背景色
* progressViewOffset number android 进度视图离顶部的偏移量
* size enum(RefreshLayoutConsts.SIZE.DEFAULT, RefreshLayoutConsts.SIZE.LARGE)  android 刷新指示器的大小
* tintColor color ios 刷新指示器的颜色
* title string ios 刷新指示器下面展示的文字
* titleColor color ios title的颜色

<!--more-->
## 如何和我玩
来，看看我的直观魅力美化效果图：
![](https://github.com/loonggg/BlogImages/blob/master/ReactNative/listview/refreshcontrol.gif?raw=true)

### 逻辑代码实现
```js
import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  ListView,
  Image,
  TouchableHighlight,
  RefreshControl,
  View
} from 'react-native';

class RefreshControlDemo extends Component {
  constructor(props) {
	super(props);
	const ds = new ListView.DataSource({rowHasChanged: (r1, r2) => r1 !== r2});
	this.state = {
	  isRefreshing: false,
	  dataSource: ds.cloneWithRows(this._genRows(-1)),
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
		  refreshControl={
		  <RefreshControl
			refreshing={this.state.isRefreshing}
			onRefresh={this._onRefresh.bind(this)}
			tintColor="#ff0000"
			colors={['#ff0000', '#00ff00', '#0000ff']}
			progressBackgroundColor="#ffff00"
			enabled={true}  
		  />
		}
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
		this._genRows(rowID))});
	}

	_refreshRows(){
		const dataBlob = [];
		for(let i = 0 ; i< 8 ; i ++ ){
			dataBlob.push("我爱非著名程序员");
		}
		return dataBlob;
	}


	_onRefresh(){
	  this.setState({isRefreshing: true});
	  setTimeout(() => {
	  this.setState({
		isRefreshing: false,
		dataSource: this.state.dataSource.cloneWithRows(
		this._refreshRows())
	  });
	}, 5000);
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

AppRegistry.registerComponent('RefreshControlDemo', () => RefreshControlDemo);
```

>今天我们讲的这个demo例子，我用的是ListView和RefreshControl搭配使用，因为官网的例子是ScrollView和RefreshControl的搭配使用。所以关于ScrollView和RefreshControl怎么使用，大家请移步官网，看例子，我这里就不重复介绍了。官方例子地址：[https://facebook.github.io/react-native/docs/refreshcontrol.html]











 










	






