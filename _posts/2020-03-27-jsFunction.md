---
layout:     post
title:      "js函数之call,applly,bind学习"
subtitle:   " \"每天积累一点之call,aplly,bind学习\""
date:       2020-03-27 10:47:24
author:     "ZH"
header-img: "img/home-bg-art.jpg"
catalog: true
tags:
    - JS
---

## 前言
其实这三者是三个很简单的函数,相信你在看了我文档后便会焕然大悟

## 论述
1.首先我们来举个例子

```
var name = "梅西" ,age = "33"
var obj = {
    name:  "C罗"
    ObjAge: this.age,
    myfun:function(){
        console.log(this.name + "年龄" + this.age)
    }
}
console.log(obj.ObjAge) // 33
obj.myfun() // C罗年龄Undefined
```
```
var show = "嗷呜"
function shows(){
    console.log(this.show) // 嗷呜
}
```
比较一下这两者区别，在例一中this指代的对象是obj，而在例二中this指代的对象是window  
那么如何改变this的指向，比如在例一中我想打印出梅西年龄33呢  
使用call apply bind三个函数来达到这目标，这三个目标总而言之就是用来重定义this对象的

1.三者不传参的情况对比
例子:  

```
var name = "小王" ,age = "17"
var obj = {
    name:  "小张"
    ObjAge: this.age
    myfun:function(){
        console.log(this.name + "年龄" + this.age)
    }
}

var db = {
    name:"小李"
    age:18
}

obj.myfun.call(db) // 小李年龄18
obj.myfun.apply(db) // 小李年龄18
obj.myfun.bind(db)() // 小李年龄18

```
通过这个例子我们可以得知，this对象从指向obj已经改变为指向了db  

2.对比三者传参情况下

```
var name = "小王" ,age = "17"
var obj = {
    name:  "小张"
    ObjAge: this.age
    myfun:function(a,b){
        console.log(this.name + "年龄" + this.age,"来自" + fm + "去往" + t)
    }
}

var db = {
    name:"小李"
    age:18
}

obj.myfun.call(db,'成都','上海') // 小李年龄18来自成都去往上海
obj.myfun.apply(db,['成都','上海']) // 小李年龄18来自成都去往上海
obj.myfun.bind(db,'成都','上海')() // 小李年龄18来自成都去往上海
obj.myfun.bind(db,['成都','上海'])()小李年龄18来自成都,上海去往undefined

```
其实差距很微妙:  
call,bind,apply 第一个参数都是this的指向对象,第二参数差别就来了。  
call 的参数是直接放进去的，第二第三第 n 个参数全都用逗号分隔，直接放到后面。  
apply 的所有参数都必须放在一个数组里面传进去 obj.myFun.apply(db,['成都', ..., 'string' ])。bind 除了返回是函数以外，它的参数和 call 一样  
当然，三者的参数不限定是 string 类型，允许是各种类型，包括函数 、 object 等等