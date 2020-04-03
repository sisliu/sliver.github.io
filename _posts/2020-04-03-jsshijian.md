---
layout:     post
title:      "js事件委托"
subtitle:   " \"js不得不说的故事\""
date:       2020-04-03 23:16:24
author:     "ZH"
header-img: "img/post-sample-image.jpg"
catalog: true
tags:
    - js
---
### 前言
这段时间忙着搭建自己的项目，博客就没时间去写了，那个项目搭建真的是一言难尽啊，
也侧面反映了当工具用多了真的会导致只会敲代码而不会去思考问题
### 正序
这章博客我来讲述一下js中的事件委托，其实事件委托在面试中已经实际项目中运用到的地方很多，只是惭愧的是，我在项目中还没实际运用过😁
#### js事件委托
根据JavaScript高级程序设计上讲：只指定一个事件处理程序，就可以管理某一类型的所有事件。原谅我JavaScript高级程序设计还没看过，等忙完这段时间，下点功夫把那本书给啃完吧
#### 为什么要用事件委托
在JavaScript中，添加到页面上的事件处理程序数量将直接关系到页面的整体运行性能，因为操作dom是很费资源的。所以前端性能优化主要思想之一就是减少dom操作。如果要用事件委托，就会将所有的操作放到js中，这样操作dom只需要一次，能大大减少dom的交互次数，提高性能    
每个函数都是一个对象，是对象就会占用内存。如果我们要对100个li进行操作，不用事件委托我们就需要100个内存空间，如果更多的话，那无法想象性能会有多差。如果用事件委托，那我们就可以只对它的父级这么一个对象进行操作，那我们只需要一个内存空间就行了。
#### 事件委托的原理
事件委托是利用事件的冒泡原理来实现的，何为事件冒泡呢？就是事件从最深的结点开始，然后逐步向上传播事件，举个例子：页面上有这么一个节点数，div>ul>li>a;给最里面的a加上一个点击事件，那么这个事件就会一层一层往外执行，执行顺序a>li>ul>div。有这么个机制，我们给最外面的div加点击事件，那么里面的ul,li,a做点击事件的时候，都会冒泡到最外层的div上，所以都会触发。
#### 事件委托怎么实现
我们先看段例子

```
<ul id="ul1">
    <li>111</li>
    <li>222</li>
    <li>333</li>
    <li>444</li>
</ul>
```
实现功能是点击li,弹出123

```
window.onload = function(){
    var oUl = document.getElementById("ul1");
    var aLi = oUl.getElementsByTagName('li');
    for(var i=0;i<aLi.length;i++){
        aLi[i].onclick = function(){
            alert(123);
        }
    }
}
```
我们可以看出这段代码性能很差，用事件委托来实现

```
window.onload = function(){
    var oUl = document.getElementById("ul1");
   oUl.onclick = function(){
        alert(123);
    }
}
```
这里用父级ul做事件处理，当li被点击时，由于冒泡原理，事件就会冒泡到ul上，因为ul上有点击事件，所以事件就会触发，当然，这里当点击ul的时候，也是会触发的，那么问题就来了，如果我想让事件代理的效果跟直接给节点的事件效果一样怎么办，比如说只有点击li才会触发，不怕，我们有绝招：
Event对象提供了一个属性叫target，可以返回事件的目标节点，我们成为事件源，也就是说，target就可以表示为当前的事件操作的dom，但是不是真正操作dom，当然，这个是有兼容性的，标准浏览器用ev.target，IE浏览器用event.srcElement，此时只是获取了当前节点的位置，并不知道是什么节点名称，这里我们用nodeName来获取具体是什么标签名，这个返回的是一个大写的，我们需要转成小写再做比较（习惯问题）：

```
window.onload = function(){
		    　　var oUl = document.getElementById("ul1");
		    　　oUl.onclick = function(ev){
		    	　　　　var ev = ev || window.event;
		   		　　　　var target = ev.target || ev.srcElement;
		        　　　　if(target.nodeName.toLowerCase() == 'li'){
		        　 　　　　　　	alert(123);
		　　　　　　　  alert(target.innerHTML);
				　　　　}
		    　　}
		}
``` 
通过这段代码，我们只需要执行一次dom操作，将极大的减少dom操作，性能优化可想而知     现在讲的都是document加载完成的现有dom节点下的操作，那么如果是新增的节点，新增的节点会有事件吗？

```
<input type="button" name="" id="btn" value="添加" />
    <ul id="ul1">
        <li>111</li>
        <li>222</li>
        <li>333</li>
        <li>444</li>
    </ul>
```
现在是移入li，li变红，移出li，li变白，这么一个效果，然后点击按钮，可以向ul中添加一个li子节点

```
window.onload = function(){
            var oBtn = document.getElementById("btn");
            var oUl = document.getElementById("ul1");
            var aLi = oUl.getElementsByTagName('li');
            var num = 4;
            
            //鼠标移入变红，移出变白
            for(var i=0; i<aLi.length;i++){
                aLi[i].onmouseover = function(){
                    this.style.background = 'red';
                };
                aLi[i].onmouseout = function(){
                    this.style.background = '#fff';
                }
            }
            //添加新节点
            oBtn.onclick = function(){
                num++;
                var oLi = document.createElement('li');
                oLi.innerHTML = 111*num;
                oUl.appendChild(oLi);
            };
        }
```
你会发现，新增加的li是没有事件的，通常的解决办法就是封装函数，然后在生成的时候调用
但你会发现性能会很差    
使用事件委托：

```
window.onload = function(){
            var oBtn = document.getElementById("btn");
            var oUl = document.getElementById("ul1");
            var aLi = oUl.getElementsByTagName('li');
            var num = 4;
            
            //事件委托，添加的子元素也有事件
            oUl.onmouseover = function(ev){
                var ev = ev || window.event;
                var target = ev.target || ev.srcElement;
                if(target.nodeName.toLowerCase() == 'li'){
                    target.style.background = "red";
                }
                
            };
            oUl.onmouseout = function(ev){
                var ev = ev || window.event;
                var target = ev.target || ev.srcElement;
                if(target.nodeName.toLowerCase() == 'li'){
                    target.style.background = "#fff";
                }
                
            };
            
            //添加新节点
            oBtn.onclick = function(){
                num++;
                var oLi = document.createElement('li');
                oLi.innerHTML = 111*num;
                oUl.appendChild(oLi);
            };
        }
```
通过事件委托，我们可以完美解决这个问题。我们可以发现，当用事件委托的时候，根本就不需要去遍历元素的子节点，只需要给父级元素添加事件就好了，其他的都是在js里面的执行，这样可以大大的减少dom操作，这才是事件委托的精髓所在    
### 总结
那什么样的事件可以用事件委托，什么样的事件不可以用呢？

适合用事件委托的事件：click，mousedown，mouseup，keydown，keyup，keypress。

值得注意的是，mouseover和mouseout虽然也有事件冒泡，但是处理它们的时候需要特别的注意，因为需要经常计算它们的位置，处理起来不太容易。

不适合的就有很多了，举个例子，mousemove，每次都要计算它的位置，非常不好把控，在不如说focus，blur之类的，本身就没用冒泡的特性，自然就不能用事件委托了。
