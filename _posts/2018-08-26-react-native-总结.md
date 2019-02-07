---
layout: post
title: React-Native 总结
tags: [react-native]
---

总结一下ReactNative使用的经验, 毕竟已经开发了1年了.

# Dev FAQ:

### JavaScript相关

​	语音助手等React-Native是基于JavaScript ECMAScript 5 或 ECMAScript 6 以及React, 所以我们目前使用了Airbnb的代码规范

1. JS代码规范: https://github.com/yuche/javascript

2. React代码规范: https://github.com/airbnb/javascript/tree/master/react

   WebStorm的代码检测. WebStorm会用黄色波浪线等标识不符合规范的地方, 尽量修改这些不规范的问题.

 > ​	Tips: 测试JavaScript代码很方便, 打开Chrome或FireFox在控制台(Ctrl + Shift + i)中输入就可以测试. 如在控制台中输入: Date.now(); 可以获得当前的时间戳.

3.  空(Undefined)对象(空指针)的判断, 代码中最容易的崩溃异常:

 > Uncaught TypeError: Cannot read property '变量' of undefined

    判断方法:

	```
	if (a) { // a为null或undefined都为false, 其他为true, 为空字符串时也为true

	}
	```

### React相关
1. ##### 生命周期

   ​	React-Native的组件Component(相当于Android里面的View)都是基于React实现的, 所以也遵循React的生命周期. 生命周期在编码中非常重要. 组件的生成包含了三个部分: Mounting, Updating和Unmounting.

   ​	Mounting指的是组件第一次被加载到视图的DOM树中的过程.

   - [`constructor()`](https://reactjs.org/docs/react-component.html#constructor)

   - [`componentWillMount()`](https://reactjs.org/docs/react-component.html#unsafe_componentwillmount)

   - [`render()`](https://reactjs.org/docs/react-component.html#render)

   - [`componentDidMount()`](https://reactjs.org/docs/react-component.html#componentdidmount)

     Updating指组件的属性被更新的过程:

   - [`componentWillReceiveProps()`](https://reactjs.org/docs/react-component.html#unsafe_componentwillreceiveprops)

   - [`shouldComponentUpdate()`](https://reactjs.org/docs/react-component.html#shouldcomponentupdate)

   - [`componentWillUpdate()`](https://reactjs.org/docs/react-component.html#unsafe_componentwillupdate)

   - [`render()`](https://reactjs.org/docs/react-component.html#render)

   - [`componentDidUpdate()`](https://reactjs.org/docs/react-component.html#componentdidupdate)

   ​	官方说明: [https://reactjs.org/docs/react-component.html]( https://reactjs.org/docs/react-component.html)

   > ​	Tips: 在生命周期函数中调用的await等Promise, 同步方法(如和原生端交互, 网络请求等操作) 都不会阻止生命周期的继续进行.

2. ##### Component VS PureComponent
	React 15.3 引进了PureComponent类, 来优化组件更新以及DOM树比较的问题, 在使用时只要把原本继承Component改为PureComponent即可. PureComponent没有ShouldComponentUpdate()这个函数, 它只有在它的props或state改变时才会触发render()过程, React自动帮你完成了ShouldComponentUpdate的工作, 通过shallowEqual(浅比较)比较props和state来判断是否需要更新.

	[详细说明](http://www.wulv.site/2017-05-31/react-purecomponent.html)

	> ​	Tips: 一般的与业务逻辑无关的组件都可以使用PureComponent来代替Component.

3. ##### KeyExtractor
	你可以构建一个如下的"列表"(array)组件

	 ```
	 <li>{item0}</li>
	 <li>{item1}</li>
	 <li>{item2}</li>
	 ```
	 但你必须给每一项提供一个当前层次上唯一的key的属性, 因为不仅你在运行的时候它会报黄色的warning警告, 而且在组件更新时, 相同的key或没有key的组件可能会不显示.

	 ```
	 <li key='list0'>{item0}</li>
	 <li key='list1'>{item1}</li>
	 <li key='list2'>{item2}</li>
	 ```

4. ##### 重绘render问题
	使组件更新render的一般使用两种方法, 一个是setState()方法改变state, 还有一个是props的更新. 但注意不要混合使用, 即props更新后又去使用setState()再次更新, props更新就会触发render, 而不是仅仅是state的变化.

### React-Native组件的keng

- ##### FlatList

  FlatList是React-Native提供的优化的列表组件, 可以减少内存的使用以及提供一些列表操作的支持.

  [官方说明](https://facebook.github.io/react-native/docs/flatlist.html)

> ​	Tips: 虽然FlatList提供了减少内存使用的功能, 但也造成了一些使用上的问题:
>
> - FlatList中有一个windowSize的属性, 标识了能保存在DOM树中的列表项的长度, 超出部分会被执行Unmounting的生命周期, 被空白的View替换, 导致被从DOM树中移除, 所以在必要时需要将一些数据保存下来, 防止组件被重新加入到DOM树时重新走了Mounting的生命周期导致重复计算或重复操作.
> - FlatList若要指定滚动到某个地方, 需要把FlatList中的每一项的高度计算出来告诉FlatList, 才能准确的滚动. 具体见FlatList的getItemLayout方法.
> - FlatList是基于PureComponent的, 所以只有当[`extraData`](https://facebook.github.io/react-native/docs/flatlist.html#extradata)属性变化时才会促使里面的内容重绘.
> - FlatList继承了ScrollView以及VirtualizedList组件, 找不到想要的属性时, 可以到父组件的属性中看看.
> - FlatList提供了[`removeClippedSubviews`](https://facebook.github.io/react-native/docs/flatlist.html#removeclippedsubviews) 属性, 置为true时可以提高页面的渲染效率, 但会导致一些问题, 比如
> - FlatList在提供了[`initialScrollIndex`](https://facebook.github.io/react-native/docs/flatlist.html#initialscrollindex) 属性, 可以设置初次创建时的滚动位置, 但是这个只会调用一次, 即FlatList被render后, 改变这个属性就没有用了; 同时也由于优化机制的计算问题, 会导致在这个index之前的控件在第一次渲染时都会先以白色空白区域代替.

- ##### Text

  Text是React-Native提供的文本组件, 可以支持类似css中的很多文本样式, 同时也支持嵌套, 内部嵌套的Text会继承外部的字体等属性, 可以方便地实现同一行字符串中各个字符显示不同样式需求, 而且嵌套的Text在原生端会被整合成一个RCTTextView, 不会有View过多嵌套的问题.

> Tips: 虽然看起来是很完美的控件, 但是还有有问题:
>
> - 在一些能够自己设置(Fei)字(Zhu)体(liu), 字号的Android手机上, 如小米, Oppo等, 会导致有些字渲染不出来, 这时要在index.android.js 或index.ios.js等根入口处设置
>
>  [`Text.defaultProps.allowFontScaling = false`](https://github.com/facebook/react-native/issues/2519)
>
> - 嵌套字体虽然好用, 但是属性继承会出现问题, 所以可能需要将重复属性的设置给下一层Text.

### 平台相关 (Android & iOS)
- Image & ImageBackground


### GitHub项目
- [Facebook F8 App](https://github.com/fbsamples/f8app) 每年F8大会的App

