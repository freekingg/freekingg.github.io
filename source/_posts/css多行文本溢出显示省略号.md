---
title: css多行文本溢出显示省略号...
date: 2019-10-30 10:07:41
tags:
- css
- 技巧
categories:
- [前端]
- [css]
---

大家应该都知道用text-overflow:ellipsis属性来实现单行文本的溢出显示省略号(…)。当然部分浏览器还需要加宽度width属性。

``` css
overflow: hidden;
text-overflow: ellipsis;
white-space: nowrap;
```
<!-- more -->
但是这个属性并不支持多行文本溢出显示省略号，这里根据应用场景介绍几个方法来实现这样的效果。

WebKit浏览器或移动端的页面

在WebKit浏览器或移动端（绝大部分是WebKit内核的浏览器）的页面实现比较简单，可以直接使用WebKit的CSS扩展属性(WebKit是私有属性)-webkit-line-clamp 

`-webkit-line-clamp`用来限制在一个块元素显示的文本的行数。 为了实现该效果，它需要组合其他的WebKit属性

常见结合属性：
- `display: -webkit-box`; 必须结合的属性 ，将对象作为弹性伸缩盒子模型显示 。
- `-webkit-box-orient` 必须结合的属性 ，设置或检索伸缩盒对象的子元素的排列方式 。
- `text-overflow: ellipsis`;，可以用来多行文本的情况下，用省略号“…”隐藏超出范围的文本 
``` css
overflow : hidden;
text-overflow: ellipsis;
display: -webkit-box;
-webkit-line-clamp: 2;
-webkit-box-orient: vertical;
```