---
title: html转图片小技巧
date: 2017-02-14 14:42:59
tags:
- 知识点
- javascript
- canvas
categories:
- 前端
---

``` javascript
var canvas = document.createElement('canvas');
var wth = 500,
	hgt = 360;
canvas.width = wth;
canvas.height = hgt;
document.body.appendChild(canvas);

var ctx = canvas.getContext('2d');
var data = "<svg xmlns='http://www.w3.org/2000/svg' width='200' height='200'>" +
             "<foreignObject width='100%' height='100%'>" +
               "<div xmlns='http://www.w3.org/1999/xhtml' style='font-size:40px'>" +
                  "<em>I</em> like <span style='color:white; text-shadow:0 0 2px blue;'>cheese</span>" +
               "</div>" +
             "</foreignObject>" +
           "</svg>";
ctx.fillStyle = '#ddd';
ctx.fillRect(0, 0, wth, hgt);
var img = new Image();
var svg = new Blob([data], {type: "image/svg+xml;charset=utf-8"});
var url = window.URL.createObjectURL(svg);
img.crossOrigin = 'anonymous';
img.src = url;
img.onload = function() {
    window.URL.revokeObjectURL(url);
    ctx.drawImage(img, 0, 0);
    window.open(canvas.toDataURL('image/jpeg', 0.75));
}
```
核心代码是while循环部分，实际上就是一个递归调用，你也可以写成一个函数，用递归的方法来调用，同时用到冒泡的原理，从里往外冒泡，知道currentTarget为止，当当前的target是li的时候，就可以执行对应的事件了，然后终止循环