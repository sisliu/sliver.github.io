---
layout:     post
title:      "js与jQuery对比"
subtitle:   " \"每天积累一点\""
date:       2020-03-26 18:10:24
author:     "ZH"
header-img: "img/jquery.jpg"
catalog: true
tags:
    - JS
    - Jquery
---

> "每天学点积累点，温故而知新"

## 前言
我们要学习一门技术，首先我们要知道这门技术诞生的原因，弥补了什么，以及它的效果
Jquery是为了旨在处理浏览器不兼容以及简化HTML DOM操作，事件处理以及动画和AJAX

## js与jQuery对比

1.通过id查找元素
- 在jQuery通过 var myelement = $("#id01")
- 在js中 var myelement = document.getElementById("id01")

2.通过标签查找元素
- 在jQuery通过 var myelement = $("p")
- 在js中通过 var myelement = document.getElementsByTagName("p")

3.通过类名查找标签
- var myelement = $(".className")
- var myelement = document.getElementsByClassName("className")
按类名查找元素在 Internet Explorer 8 和早期版本中不起作用。

4.通过 CSS 选择器查找 HTML 元素
比如查找class = "intro" 的所有的<p>元素的列表
- var myelement = $("p.intro")
- var myelement = document.querySelectorAll("p.intro")
querySelectorAll() 方法在 Internet Explorer 8 和早期版本中不起作用

5.设置文本内容
- myelement.text("内容")
- myelement.textContent = "内容"

6.获取文本内容
- var x = myelement.text()
- var x = myelement.textContent or myElement.innerText

7.设置HTML内容
- var myElement.html("<p>Hello World</p>")
- ar myElement.innerHTML = "<p>Hello World</p>"

8.获取 HTML 内容
- var content = myElement.html()
- var content = myElement.innerHTML

9.隐藏HTML元素
- myelement.hide()
- myelement.style.display = "none"

10.显示HTML元素
- myelement.show
- myelement.style.display = ""

11.样式化HTML元素
- myelement.css("font-size","35px")
- myelement.style.fontSize = "35px"

12.删除HTML元素
- $("#id").remove()
- element.parentNode.removeChild(element)

13.获取父元素
- var myParent = myElement.parent()
- var myParent = myElement.parentNode
