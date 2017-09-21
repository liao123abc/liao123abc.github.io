---
layout: post
title:  "React Native环境搭建!"
Description: "React Native预言"
modified: 2016-3-29
tags: Android
---



# React Native

### 安装好SDK和NDK

![](http://i.imgur.com/P1COXhP.png)

### 安装好SDK和NDK
![](http://i.imgur.com/LnZHLMn.png)

### 安装node.js
- https://nodejs.org/en/
  ![](http://i.imgur.com/f5ZbbXf.png)

### 安装nrm模块
- 为了方便切换npm源，需要先安装nrm模块
- cmd:     

    `npm install –g nrm`
    ​         
### 切换npm源
- 为了方便切换npm源，切换npm源到taobao—比较快

![](http://i.imgur.com/NPs3yBL.png)

### 安装RN cmd tools
- Install the React Native command line tools:
- Cmd:     
         `npm install –g react-native-cli`

### 建立project
- 1 Init：
      `react-native init HelloWorld`
- 2 run:
     `react-native run-android`
- 3 Start packager:
    `cd HelloWorld`
    `react-native start`


### 修改index.android.js

![](http://i.imgur.com/FUEwr9p.png)

![](http://i.imgur.com/FQxCYqu.png)

### 特性
- learn-once, write-anywhere

- OTA (over-the-air) 
    - bug fixes and minor updates to apps in the store without resubmitting--IOS

- Web编程
    - It feels like I'm developing for the web. But I'm writing a real native app, and you seriously can't tell the difference. 

- Fast
    - runs JavaScript on a separate thread, React Native takes your UI and sends the minimal amount of data to the main thread to render it with native components.

- how
    - A View is a UIView, for example. The best part is, you don't have to worry about updating your UI; you declaratively render your UI based on some state, and React uses a diffing algorithm to send the smallest amount of changes necessary over the bridge.
    - React 有一个观点很明白, 就是以往的编程太多过程式的写法, 他们想要变成声明式的,因为声明式的写法能讲底层逻辑交给可靠的代码, 那么开发者的门槛极大地降低

### 缺点
#### 在React Native中，写JS的工程师解决的是「将基本组件拼装成可用的React组件」的问题，写Native Code的工程师解决的是「提供核心组件，提供足够的扩展性、灵活性和性能」的问题。

- 1.learn-once, write-anywhere？nor run anywhere

- 2.系统仍然（不得不）依赖原生组件暴露出来的组件和方法

- 3.目前相当不完善
    - Native UI components
    - native Modules

- 4.由于最外层是React，初次学习成本高


### Only for view
![](http://i.imgur.com/4MAbfFE.png)


### Data flow
- solutions for data flow—flux
- React Native 的亮点是解决了在 Native 中使用声明式来开发 UI 的渲染效率问题，而不是软件架构和工程模型的问题
  ![](http://i.imgur.com/xYgjaMj.png)

### summary

#### 引用：[http://www.zhihu.com/question/27852694/answer/38829884](http://www.zhihu.com/question/27852694/answer/38829884 "作者：知乎|赵望野")

- React 作为一个 View Component 封装解决方案来讲，同 Polymer 以及 AngularJS 中 Directive 并没有本质区别，只是用不同的思路来封装 View 而已，用 React 也不一定非得用 Flux 模型，React 替换 Backbone.View 组件，用纯朴的 MVC 模型来描述也是 okay 的。但是当 component 很多且互相嵌套时，就需要有一个合理的模型来描述通信机制，优雅且高效，那就是 Flux 模型了。前年 React 刚发布，还没有提出 Flux 时，我们在终端产品中开始小范围尝试 React 就遇到了 component 之间通信麻烦或者不合理的问题，当时的解决方案是全局实例化了一个继承 Backbone Event 的对象作为 event hub，所有的 component 都在其上来 reg 和 trigger 事件。现在看来，就是简化版的 Flux 模型。

#### my opinion
- 这是一个大坑！至少目前来说是这样子！
