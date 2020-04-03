---
layout:     post
title:      "WebPack 管理依赖"
subtitle:   " \"WebPack学习第一章\""
date:       2020-04-01 12:06:24
author:     "ZH"
header-img: "img/post-bg-kuaidi.jpg"
catalog: true
tags:
    - WebPack
---
### 前言
这是自己搭建项目第二天，在我打算用store保存登录数据的时候出现了问题，也不是说出问题了吧，只是以自己的思路来的话，结构会很冗长。想依靠大神模板进行目录构建。但前提也得看得懂他的代码才行，不然就失去了学习的意义

### 大神的代码

```
// https://webpack.js.org/guides/dependency-management/#requirecontext
const modulesFiles = require.context('./modules', true, /\.js$/)

// you do not need `import app from './modules/app'`
// it will auto require all vuex module from modules file
const modules = modulesFiles.keys().reduce((modules, modulePath) => {
  // set './app.js' => 'app'
  const moduleName = modulePath.replace(/^\.\/(.*)\.\w+$/, '$1')
  const value = modulesFiles(modulePath)
  modules[moduleName] = value.default
  return modules
}, {})
```
为什么会出现这段代码的前提要求是对[vuex]()进行一个modules拆分。我会专门分出一篇博客来讲述vuex，在此不展开论述。    
对于每个状态，写在一个文件类会造成代码冗长且难于维护，所以可以拆分针对每个state写在一个文件内，而index就只维护这些文件就行。然而问题来了，我们需要在index将这些文件一个一个引进来，这是件很繁琐的事，就像我现在改的bug让人繁琐郁闷。    
在此前提下，前端自动化工具便出现了，通过webpack自带的api，require.context可以获取特定的上下文，主要是为了实现自动化导入模块，在前端工程中，如果遇到从一个文件夹引入很多模块的情况，可以使用这个api，它会遍历文件夹中的指定文件，然后自动导入，这样就不需要每次调用import导入模块

### 分析require.context
该函数接受三个参数
1. directory{String}-读取文件路径
2. useSubdirectories{Boolean}-是否遍历文件的子目录
3. reqExp{RegExp}-匹配文件的正则

```
require.context('./modules', true, /\.js$/)
```
这行代码的意思是遍历modules文件下所有的js文件，该函数返回一个函数，该函数有三个属性
- resolve {Function} -接受一个参数request,request为test文件夹下面匹配文件的相对路径,返回这个匹配文件相对于整个工程的相对路径，对于resolve方法可以看到它是一个函数接受req参数,经过实践我发现这个req参数的值是keys方法返回的数组的元素
- keys {Function} -返回匹配成功模块的名字组成的数组
- id {String} -执行环境的id,返回的是一个字符串,主要用在module.hot.accept,应该是热加载?

```
const modulesFiles = require.context('./modules', true, /\.js$/)
modulesFiles(key).default
```
modulesFiles作为一个函数,也接受一个req参数,这个和resolve方法的req参数是一样的,即匹配的文件名的相对路径,而files函数返回的是一个模块,这个模块才是真正我们需要的
### 关于reduce方法
reduce属于js数组对象里的方法，在这我就不把这个方法放到js去总结了，在这进行一个总结
其实reduce总结起来就是对数组每一个元素执行一个你提供的reduce函数(升序执行)，将其结果汇总为单个返回值
reduce函数接收四个参数
1. accumulator(累加器)
2. current value(当前值)
3. current index(当前索引)
4. source array(src)    
下面我们来看个例子    
var arr = [3,9,4,3,6,0,9]    
求数组之和 

```
var sum = arr.reduce(function (prev, cur) {
    return prev + cur;
},0);
```
由于传入了initialValue函数初始值，所以这时候acc即Prev为零，且cur值为第一项，如果不传入初始值，那么prev值为数组第一项，且cur值为第二项。相加之后返回值作为下一轮回调的prev值，然后再继续与下一个数组项相加，以此类推，直至完成所有数组项的和并返回    
数组去重

```
var newArr = arr.reduce(function (prev, cur) {
    prev.indexOf(cur) === -1 && prev.push(cur);
    return prev;
},[]);

```
这很容易理解，我们先将prev初始化为一个空数组，通过表达式我们可以看到，如果cur值在prev不出现即通过indexOf为-1则push进去

### 讲解大神代码
知道了require.context以及js reduce用法我们再来看开始的代码就容易多了

```
// https://webpack.js.org/guides/dependency-management/#requirecontext
const modulesFiles = require.context('./modules', true, /\.js$/)

// you do not need `import app from './modules/app'`
// it will auto require all vuex module from modules file
const modules = modulesFiles.keys().reduce((modules, modulePath) => {
  // set './app.js' => 'app'
  const moduleName = modulePath.replace(/^\.\/(.*)\.\w+$/, '$1')
  const value = modulesFiles(modulePath)
  modules[moduleName] = value.default
  return modules
}, {})
```
首先通过require.context我们得到一个包含resolve，key，id的函数对象，然后通过获取keys
我们可以得到文件路径地址，通过reduce以及初始化为{}，每次对keys执行reduce函数，在函数里，通过
正则表达式获取文件名称，在上面我们提过，modulesFiles也是个函数接收一个res参数，即匹配的文件名的相对路径，
它返回的是一个模块，在这里用value接收，而获取default即是我们需要获取的内容。    
最后返回的既是一个类似下面这种格式

```
modules: {
    a: moduleA,
    b: moduleB
  }
```
### 后记
大神之所以是大神，既他的代码哪怕简单几行，也需要你花一上午去查询资料去总结学习