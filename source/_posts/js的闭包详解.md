---
title: js的闭包详解
date: 2020-03-29 16:09:20
tags:
- 知识点
- 闭包
- javascript
categories:
- 前端
---
## js变量作用域

js的变量作用域有两种，全局变量和局部变量

需要注意的是，函数内部可以直接读取全局变量。

``` javascript
var global = 666;

function func() {
    alert(global);
}

func(); //666
```
函数外部无法读取到函数内部的局部变量，因为函数在执行完之后，函数内部的环境就被销毁了。

<!-- more -->

``` javascript
function func() {
    var message = 888;
}

alert(message); // error
```
如果函数内部没有使用var，那么实际上就相当于把这个变量声明为全局变量了。

``` javascript
function func() {
	message = 999;
}

func();	//这里注意要运行一遍函数，`message`	才有定义

alert(message);

```
## 如何从外部读取到局部变量？
``` javascript
function f1() {
	var message = 999;
	function f2() {
		alert(message); //999
	}
}
```
上面的代码中，f2可以访问到f1的局部变量message，那么我们只要把f2作为f1的返回值return出去，那么就可以在外部访问到message了。
``` javascript
function f1() {
	
	var message = 999;

	function f2() {
		alert(message); //999
	}

	return f2;

}
```
## 简单理解闭包
上面的f2函数就是闭包。

闭包就是能够读取其他函数内部变量的函数，所以，闭包实际上是一个函数。
闭包可以理解为“定义在一个函数内部的函数”，本质上，闭包是将函数内部和外部连接起来的一座桥梁。
这里要注意一点，父函数内部定义的子函数，如果没有引用父函数作用域中的变量，那么这个子函数不是闭包，这点非常重要，也就是说，闭包是由函数和它所在的环境构成的，缺一不可

##  闭包的用途
- 读取函数内部的变量
- 让变量始终保存在内存中
``` javascript
function f1() {
	var n = 999;
	nAdd = function() { n += 1 }; //nAdd是一个全局函数
	function f2() {
		alert(n);
	}
	return f2;
}

var result = f1(); //result是一个函数，f1中return的字函数
result();//999
nAdd();//执行全局函数
result();//1000

```
- 闭包的应用比较典型是定义模块，我们将操作函数暴露给外部，而细节隐藏在模块内部：
``` javascript
function module() {
	var arr = [];
	function add(val) {
		if (typeof val == 'number') {
			arr.push(val);
		}
	}
	function get(index) {
		if (index < arr.length) {
			return arr[index]
		} else {
			return null;
		}
	}
	return {
		add: add,
		get: get
	}
}
var mod1 = module();
mod1.add(1);
mod1.add(2);
mod1.add('xxx');
console.log(mod1.get(2));
```


## 使用闭包的注意事项
闭包会让函数中的变量都被保存在内存中，内存消耗大，所以不能滥用闭包，可以在不使用该变量的时候将其delete。
闭包会在父函数外部改变父函数内部的值，如果把父函数当作object使用，把闭包当作公有方法，内部变量当作私有成员，那就要小心不要随意改变父函数内部变量的值。


## 经典闭包例子
``` javascript
<ul>
  <li>1</li>
  <li>2</li>
  <li>3</li>
  <li>4</li>
</ul>
```

``` javascript
var lis = document.querySelectorAll('li')
for (var i = 0; i < lis.length; i++) {
 lis[i].onclick = function () {
  console.log(s)
 }
}

// 本意是想每次点击，输入对应序号，但结果是每次输入都为3
```

-----------------------------------分割线-----------------------------------分割线--------------------------


首先我们来了解几个概念：

立即执行函数：形如 （function(){}）();的一类函数；

闭包：闭包是指有权访问另一函数作用域中的变量的函数。

作用域链：当代码执行的时候，会创建变量对象的一个作用域链....（具体百度）

``` javascript
var lis = document.querySelectorAll('li')
for (var i = 0; i < lis.length; i++) {

	lis[i].onclick = function () {
		console.log(s)
	}
}
```
首先，代码中的匿名函数没有变量 i，所以它必须向上查找，在全局环境中找到了 i。

当for循环运行后，全局变量中的 i 变成了3。此时当你点击文字的时候，会调用其绑定的函数，而该函数运行的时候，发现自己没有 i，就会取得全局环境中的 i。

所以，最后的结果是，不管你点击那段文字，最后结果都是3。



**那怎么办呢？你可以用立即执行函数，看代码：**

``` javascript
var lis = document.querySelectorAll('li')
for (var i = 0; i < lis.length; i++) {

	lis[i].onclick = function (s) {
		return function () {
			console.log(s)
		}
	}(i)
}
```
我们把参数 i 作为传给立即执行函数，这样，i 的值就传给了立即执行函数的局部变量 i 了。立即执行函数会直接执行，但是其活动不会销毁，因为里面有个匿名函数。执行后局部变量 i 与全局变量 i 联系就切断了，也就是执行的时候，传进去的变量 i 是多少，立即执行函数的局部变量 i  就是多少，并且该局部变量 i 仍然没有消失，因为匿名函数的存在。

这时候，return中的匿名函数的作用域链中会有两个变量 i。当点击文本的时候，它向上搜索 i 的时候，它找到立即执行函数的局部变量 i ，就停止向上查找了，因此最后的结果就不会是全局变量 i 的值3了。

 有一个方法可以检验你们有没有真的理解上面所说的，看上面的变体，代码：
``` javascript
　for(var i=0;i<dom.length;i++){
        dom[i].onclick=function(t){
            return function( ){ 
            console.log(t);//1
            console.log(i);//3
            };
        }(i);
    }
```
其实return中的匿名函数中的 t 就是立即执行函数的局部变量 i，而 i 就是 指全局变量 i，因为立即执行函数中没有变量i，只能继续向上搜索，然后就找到全局变量的 i 了。
