---
layout:     post
title:      "js继承"
subtitle:   " \"每天积累一点之继承\""
date:       2020-03-26 21:17:24
author:     "ZH"
header-img: "img/home.jpg"
catalog: true
tags:
    - JS
---

## 前言
在了解js继承之前，也许你需要了解什么是[原型](https://langtao.ltd/2020/03/25/prototype/)，相信我，了解完什么是原型什么是原型链后，
你会更容易了解js继承

## 论述

也许你使用过Java，那么你肯定知道在Java有个关键字是class,在class内我们可以写公共属性通过extend来继承，
在es6中也引入了class，不过那只是个语法糖，真正js还是基于原型

当我们说到继承，js只有一种结构，那就是对象，每个实例对象都有一个私有属性为_proto_指向它的构造函数的原型对象。
该原型对象也有自己的原型对象(_proto)，层层向上直到一个对象的原型对象为null。根据定义，null没有结构，那就作为
这个原型链的最后一个环节。上述文字其实就是对于原型链的一个解释

几乎所有JS中的对象都是位于原型链顶端的Object实例

尽管这种原型继承通常被认为是 JavaScript 的弱点之一，但是原型继承模型本身实际上比经典模型更强大。例如，在原型模型的基础上构建经典模型相当简单

## 基于原型链的继承

### 继承属性
js对象是动态的属性"包"，js对象有一个指向原型对象的链，例如构造函数实例对象，当试图访问一个对象的属性，它不仅仅只是在该对象上搜寻，还会搜寻对象的原型，当然它一旦找到对象属性便会停下，一直查到原型末端，如果一直没找到则会返回null。

遵循ECMAScript标准，someObject.[[Prototype]]符合是用于指向someObject的原型，从es6开始，通过object.getPrototypeof()和Object.setPrototypeOf()来访问。这个等同于js的非标准但许多浏览器实现的属性_proto_

### 继承方法
JavaScript 并没有其他基于类的语言所定义的“方法”。在 JavaScript 里，任何函数都可以添加到对象上作为对象的属性。函数的继承与其他的属性继承没有差别，包括上面的“属性遮蔽”（这种情况相当于其他语言的方法重写）。

当继承的函数被调用时，this 指向的是当前继承的对象，而不是继承的函数所在的原型对象。

```
var o = {
  a: 2,
  m: function(){
    return this.a + 1;
  }
};

console.log(o.m()); // 3
// 当调用 o.m 时，'this' 指向了 o.

var p = Object.create(o);
// p是一个继承自 o 的对象

p.a = 4; // 创建 p 的自身属性 'a'
console.log(p.m()); // 5
// 调用 p.m 时，'this' 指向了 p
// 又因为 p 继承了 o 的 m 函数
// 所以，此时的 'this.a' 即 p.a，就是 p 的自身属性 'a' 
```

## 性能
在原型链上查找属性比较耗时，对性能有副作用，在性能要求苛刻的情况下很重要，如果你并不想在原型链上进行查找某个属性，你可以使用hasOwnProperty方法。它和object.key()起一样的作用
<font color = orange>注意:</font>检查属性是否为 undefined 是不能够检查其是否存在的。该属性可能已存在，但其值恰好被设置成了 undefined

## 错误实践
经常使用的一个错误实践是扩展 Object.prototype 或其他内置原型。

这种技术被称为猴子补丁并且会破坏封装。尽管一些流行的框架（如 Prototype.js）在使用该技术，但仍然没有足够好的理由使用附加的非标准方法来混入内置原型。

扩展内置原型的唯一理由是支持 JavaScript 引擎的新特性，如 Array.forEach。