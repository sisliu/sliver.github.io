---
layout:     post
title:      "js原型对象与原型链"
subtitle:   " \"每天积累一点\""
date:       2020-03-25 19:37:24
author:     "ZH"
header-img: "img/home.jpg"
catalog: true
tags:
    - JS
---

> "每天学点积累点，温故而知新"

## 前言
原型和原型链是JavaScript中不可避免需要碰到的知识点，在刚开始学习的时候，原谅我到现在才听说过原型对象和原型链，
惭愧惭愧，既然没听过，那肯定是需要好好学习的了

## 前置知识
1.构造函数
在js中，实际上并不存在所谓的"[构造函数](https://langtao.ltd/2020/03/24/hello-blog/)",只有对于函数的"构造调用"

让我们来看看构造/或者new函数的时候做了什么
- 创建一个全新的对象。
- 这个新对象的原型(Object.getProtopeOf(target))指向构造函数的prototype
- 该函数的this会绑定在新创建的对象上。
- 如果函数没有返回其他对象，那么new表达式中的函数会自动返回这个新对象
- 我们称这个新对象为构造函数的实例

2.普通对象和函数对象
在js中，一切皆对象，函数其实也是对象，只是功能比较强大而已Object和function其实就是js中典型的函数对象
实例:

```
var obj={};
var func=function(){};
console.log( obj.constructor ); //f Object() { }
console.log( func.constructor ); //f Function() { }

```
我们可以看到，普通对象的构造函数是一个Object，而函数对象构造函数为function

## 原型

1.什么是原型

按官方文档给出的定义，原型既是给其他对象共享的对象罢了
在这点要注意的是，函数对象有原型，而普通对象是没有prototype的
每声明一个函数，就会产生一个原型，并且这个原型的引用就保存在func.prototype上面

2.为什么要使用原型

JS通过new来生成对象，但每次生成的对象都不一样，如果需要在两个对象之间共享属性，那么就很麻烦，js在设计之初是没有类的概念，所以js使用函数的prototype来处理这部分需要被共享的属性，可以通过prototype来模拟类，简单来说就是共享属性

## 原型链

1.认识proto属性
先给出代码看看

```
var func = function (name) {
    this.name = name;
    this.get = function() {
        return this.name
    }     
}

func.prototype = {
    set: function(newName) {
        this.name = newName
    }
}

var afunc = new func("agam")
console.log(afunc.prototype) // undefined
console.log(afunc.get()) // agam
afunc.set("agamgn") 
console.log(afunc.get()) agamgn
console.log(afunc.__proto__) //{set:[Function:set]}
console.log(func.prototype) //{set: [Function:set]}
console.log(func.__proto__)

```
从上面得出结论：
- __proto_是对象实例和构造函数之间建立的链接，它的值是构造函数的prototype。也就是说，proto的值是他对应的原型对象，是某个函数的prototype
- 每一个对象，无论其是函数对象还好还是普通对象，都会有_proto_属性
注：这个属性现在已经不推荐使用了"[原因](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/proto)"
，应该使用应该使用：Object.getPrototypeOf(target)（读操作）、Object.setPrototypeOf(target)（写操作）、Object.create(target)（生成操作）代替

2.什么是原型链

看个例子

```
function func(){}
let newObj = new func() //构造调用func，返回一个新对象
const newObj__proto__ = Object.getPrototypeOf(newObj) //获取newObj原型对象
console.log(newObj__proto__ === func.prototype) //true验证newObj的原型指向func
const func__proto = Object.getPrototypeOf( func.prototype) // 获取func.prototype的原型
console.log(func__proto === Objecet.prototype)
```

如是以前的语法，从newObj查找func的原型，是这样的：

```
newObj.__proto__.__proto__ // 这种关系就是原型链

```
那么什么是原型链呢
 - 每个对象都拥有一个原型对象，普通对象的原型对象即它对应构造函数的prototype
 - 对象的原型也有可能是继承其他原型对象的,比如构造函数也有它的原型Object.prototype
 - 在访问对象属性以及方法时，在当前对象找不到，会访问该对象构造函数的原型，如果还是找不到那么就继续查找对象构造函数的原型的原型，依次
 查找下去，若在某一刻，找到了该属性那么立刻返回并停止对原型链的搜索，若找不到，则返回undefined。
 这种关系就是原型链

 3.判断一个对象是否在另一个对象的原型链上

 如果一个对象存在另一个对象的原型链上，我们称它们为继承关系
 方法有两种
 - instanceOf:用于测试构造函数的prototype属性是否出现在对象的原型链上任何位置
 - isPrototypeOf:测试一个对象是否存在于另一个对象的原型链上

 4.原型链的终点：Object.prototype

 Object.prototype是原型链的终点，所有对象都是从它继承了方法和属性
 
有了上面的基础，是时候看一下这个图了
![avatar](/img/demo.webp)

4.原型链的作用

1.属性查找 如果试图访问对象(实例instance)的某个属性,会首先在对象内部寻找该属性,直至找不到,然后才在该对象的原型(instance.prototype)里去找这个属性，以此类推

```
let stringtest = '测试'
let stringtestprototype = Object.getPrototypeOf(stringtest)
console.log(stringtestprototype === String.prototype) // true

```

当你访问test的某个属性时，是这样进行查找的：
- 浏览器首先查找stringtest 本身
- 接着查找它的原型对象：String.prototype
- 最后查找String.prototype的原型对象：Object.prototype
- 一旦在原型链上找到该属性，就会立即返回该属性，停止查找。
- 原型链上的原型都没有找到的话，返回undefiend

5.拒绝查找原型链
hasOwnProperty: 指示对象自身属性中是否具有指定的属性

```
let test ={ 'name': 'agamgn' }
test.hasOwnProperty('name');  // true
test.hasOwnProperty('toString'); // false test本身没查找到toString 
```

