---
title: ECMAScript6 实用笔记
date: 2020-03-29 16:19:30
tags:
- javascript
- es6
categories:
- 前端
---

## 一、简介
 1、目前常用的版本是ECMAScript3.0，后来改了名字叫ECMAScript5
 >ECMAScript 6.0（以下简称 ES6）是 JavaScript 语言的下一代标准，已经在2015年6月正式发布了。它的目标，是使得 JavaScript 语言可以用来编写复杂的大型应用程序，成为企业级开发语言。
ECMAScript 和 JavaScript 的关系是，前者是后者的规格，后者是前者的一种实现

 2、ECMAScript6的特点：
>- ES6增添了许多必要的特性，例如模块和类，块级作用域，常量与变量

3、浏览器的支持程度

>- http://kangax.github.io/compat-table/es6/

4、可以通过Babel转码器把ES6写的代码转成ES5的，就不用担心运行环境是否支持
5、chrome下使用ES6为保证可以正常使用大部分语法，需要使用严格模式，即在js开始部分加上'use strict'
6、在firefox下使用ES6为保证可以正常使用大部分语法，需要知道测试版本，即在script标签的type属性中加上“application/javascript;version=1.7”属性值

<!-- more -->

## 二、let
> **let 声明变量的关键字**
1、在相同的作用域内，let不能重复声明一个变量（不同的作用域内除外）
2、let声明的变量不会被预解析
3、暂时性死区
``` javascript
'use strict'        //严格模式，即在js开始部分加上'use strict'
console.log(a);		//undefined let声明的变量不会被预解析
var a=12;
var a='kaivon';
console.log(a);

//console.log(b);		//报错	不能提前使用
let b=20;
//let b=30;			//报错 let不能重复声明一个变量（不同的作用域内除外）
console.log(b);

{
  let str='kaivon';
  console.log(str);
}
```

## 三、块级作用域
>块级作用域批是的是一对大括号就是一个作用域
块级作用域可以直接写一对大括号，以后就不用写自执行函数了
``` javascript
{
  let a=12;
  function fn(){
  let a=20;
   console.log(a);
 }
  fn();
}
```
## 四、const 常量
> **const 声明一个常量，一旦声明后就不能修改了**
1、如果声明后再去修改的话就会报错
2、只声明不赋值也会报错
3、只能先声明后使用，不会被提前解析
4、不能重复声明一个常量
**注意：const声明的对象中的属性是可以修改的**
``` javascript
'use strict'
			
var a=12;
a='kaivon';
console.log(a);

const str='kaivon';
//str=12;		//报错  声明后再去修改的话就会报错

console.log(str);

//const b;			//报错	只声明不赋值也会报错

//console.log(c);	//报错	只能先声明后使用，不会被提前解析
const c=20;
//const c='kaivon';	//报错	不能重复声明一个常量

//声明一个对象后，可以对它里面的属性进行修改
const obj={
	name:'kaivon'	
};

obj.name='陈学辉';
console.log(obj); //const声明的对象中的属性是可以修改的
```
## 五、数组解构赋值
**解构赋值:**
>按照一定的模式，从数组或者对象中把数据拿出来，对变量进行赋值

**数组解构赋值:**
>等号左边与右边必需都是数组，数组的解构赋值要一一对应。如果对应不上的话就是undefined
``` javascript
'use strict'
/*var a=1;  //传统声明变量
var b=2;*/

var [a,b,c]=[1,2,3];  //可以使用数组进行批量声明变量
console.log(a,b,c);		//1 2 3

let [x,,y,z]=[1,2,3];   //数组的解构赋值要一一对应。如果对应不上的话就是undefined
console.log(x,y,z);		//1 3 undefined 

 var [a,[b,c]]=[1,[2,3]];
 console.log(a,b,c);		//1 2 3  可以嵌套

 //可以用来调换两个值
 var n1=10;
 var n2=15;

 var [n1,n2]=[n2,n1];
 console.log(n1,n2);		//15 10


 //也可以用来取函数的返回值
 function fn(){
 	return ['red','green','blue'];
 }
 var [d,e,f]=fn();

 console.log(e);		//green
```
## 六、对象解构赋值
**解构赋值:**
>按照一定的模式，从数组或者对象中把数据拿出来，对变量进行赋值

**对象解构赋值:**
>等号左边与右边必需都是对象，名字要一一对应，顺序不需要对应，对应不上的值结果是undefined
``` javascript
'use strict'
			
var obj={
	name:'kaivon',
	QQ:356985332,
	language:['css','html','js'],
	work:function(){
		console.log('js');
	}
};

var {name,work,QQ,age}=obj; //等号左边与右边必需都是对象，名字要一一对应
console.log(name,work,QQ,age);//kaivon 356985332 js undefined


//可以用它来取一个对象的值
function fn(){
	return {
		c1:'red',
		c2:'green',
		c3:'blue'
	}
}

var {c1,c2,c3}=fn();

console.log(c2);		//green

```
## 七、字符串的扩展方法
>- includes(s) 字符串里面是否包含某个字符，参数是一个字符
- startsWidth(s) 字符串的开始位置的字符是否是参数的，参数是一个字符
- endsWidth(s) 字符串的结束位置的字符是否是参数的，参数是一个字符
**以上的几个方法都返回一个布尔值,包含有true,不包括为false**
- repeat(num) 复制字符串，参数为数字，表示复制的次数。参数必需是一个正数，其它的就会报错
``` javascript
'use strict'
var str='kaivon';   //声明个字符串
console.log(str.includes('i'));			//true 字符串里面包含 'i'
console.log(str.includes('b'));			//false 字符串里面不包含 'b'
console.log(str.startsWith('k'));		//true  开始位置包含'k'
console.log(str.endsWith('n'));			//true  结束位置包含'n'

console.log(str.repeat(3));				//kaivonkaivonkaivon 复制三次
//console.log(str.repeat(-1));				//报错 参数必需是一个正数，其它的就会报错
//console.log(str.repeat(Infinity));		//报错 参数必需是一个正数，其它的就会报错

```

## 八、模板字符串
**模板字符串：字符串及数据的拼接方式**
>1、字符串需要用一对反引号包起来，它可以定义多行字符串，只用一对反引号
 2、要拼进去的数据需要放在${}里面
 3、大括号里还可以进行运算
 4、大括号里还可以调用函数
 
``` javascript
var obj={
	title:‘心情‘,
	content:’今天很爽，吃了饭，睡了觉，还打了豆豆‘
}
var text=document.getElementById("text");
//text.innerHTML='<h1>'+obj.title+'</h1><p>'+obj.content+'</p>'; //常规的拼接方法

function fn(){
	return '那么问题来了，豆豆爽么？';
}

//要拼进去的数据需要放在${}里面
var str1=`<h1>${obj.title}</h1>  
			<p>${obj.content}</p>`;
			
//大括号里还可以进行运算
var str2=`<h1>${obj.title+'+1'}</h1>    
			<p>${obj.content}</p>`;

var str3=`<h1>${obj.title+'+1'}</h1>
			<p>${obj.content+fn()}</p>`;    //大括号里还可以调用函数
			
//text.innerHTML=str1;
//text.innerHTML=str2;
//text.innerHTML=str3;
```

## 九、Math对象的扩展方法
**Math.trunc(num): 去除小数部分，是直接把小数部分去掉**
> 1、对于非数值，先调用Number方法把它转成数字
2、对于空值和无法转成数字的值，结果是NaN
``` javascript
console.log(Math.trunc(12.74));			//12
console.log(Math.trunc(0.5));			//0
console.log(Math.trunc('36.01'));		//36
console.log(Math.trunc('kaivon'));		//NaN
```

**Math.sign(num): 判断一个数是正数还是负数还是0**
>1、参数为正数，返回1
2、参数为负数，返回-1
3、参数为0，返回0
4、参数为-0，返回-0
5、参数为其它值，返回NaN
```
console.log(Math.sign(5));				//1
console.log(Math.sign(-5));				//-1
console.log(Math.sign(0));				//0
console.log(Math.sign(-0));				//-0
console.log(Math.sign('kaivon'));		//NaN

```
## 十、数组的扩展方法
### **Array.from()**
> 把类数组转成真正的数组 任何有length属性的对象都可以用这个方法转真正数组
``` javascript
<ul>
	<li></li>
	<li></li>
	<li></li>
	<li></li>
	<li></li>
</ul>

'use strict'
var lis=document.querySelectorAll("li");
//console.log(lis);
var newLis=[].slice.call(lis); //利用以前的方法将类数组转换为数组

var newLis=Array.from(lis);     //Array.from() 把类数组转成真正的数组
console.log(newLis);			//[li, li, li, li, li]

var str='kaivon';
var newStr=Array.from(str);     //任何有length属性的对象都可以用这个方法转真正数组
console.log(newStr);			//["k", "a", "i", "v", "o", "n"]

//对象身上只要有length属性就可以调用Array.from()把对象转成数组,对象中的key必需是从0开始的数字才能转

var obj={
	0:'red',
	1:'green',
	2:'blue',
	3:'yellow',
	length:4
};
console.log(Array.from(obj)); // ['red','green','blue','yellow']		
```
### **Array.of()**
> 把一组数值转成真正的数组
``` javascript
console.log(new Array());		//[]
console.log(new Array(3));		//[, , ,]
console.log(new Array(1,2,3));	//[1, 2, 3]

console.log(Array.of(1));		//[1]
console.log(Array.of(1,2,3));	//[1, 2, 3]

```
### **filter**
``` javascript
//假定有一个对象数组(A),获取数组中指定类型的对象放到B数组中 
var porducts = [
  {name:"cucumber",type:"vegetable"},
  {name:"banana",type:"fruit"},
  {name:"celery",type:"vegetable"},
  {name:"orange",type:"fruit"}
];

// es5
var filteredProducts = [];
for(var i = 0; i < porducts.length; i++){
    if(porducts[i].type === "fruit"){
      filteredProducts.push(porducts[i]);
    }
}

// es6 filter
var filtered2 = porducts.filter(function(product){
  return product.type === "vegetable";
})

// * 假定有一个对象数组(A),过滤掉不满足以下条件的对象
// * 条件: 蔬菜 数量大于0,价格小于10
var products = [
  {name:"cucumber",type:"vegetable",quantity:0,price:1},
  {name:"banana",type:"fruit",quantity:10,price:16},
  {name:"celery",type:"vegetable",quantity:30,price:8},
  {name:"orange",type:"fruit",quantity:3,price:6}
];
products = products.filter(function(product){
    return product.type === "vegetable" 
    && product.quantity > 0 
    && product.price < 10
})

// * 假定有两个数组(A,B),根据A中id值,过滤掉B数组不符合的数据
 var comments = [
    {postId:4,content:"Angular4"},
    {postId:2,content:"Vue.js"},
    {postId:3,content:"Node.js"},
    {postId:4,content:"React.js"},
 ];

 function commentsForPost(post,comments){
    return comments.filter(function(comment){
      return comment.postId === post.id;
    })
 }

 console.log(commentsForPost(post,comments));

```

### **Array.includes**
> (数据,起始位置)	查找数组中有没有某个数据
``` javascript
var arr=['red','green','blue','yellow'];
console.log(arr.includes('red'));		//true
console.log(arr.includes('pink'));		//false
console.log(arr.includes('green',2));	//false

```
### **遍历相关**
> - for in		循环，能够直接读取键名
- for of		循环，能够直接读取键值
 * 				它不光可以遍历数组或者对象，只要有遍历接口的对象都可以用它
- keys()		存储了数组的所有键名
- values()		存储了数组的所有键值
- entries()		存储了数组的所有键值对
``` javascript
'use strict'
var color=['red','green','blue','yellow'];

//for in
for(var attr in color){
	console.log(attr);	//0 1 2 3 直接读取键名
}

//for of
for(var value of color){
	console.log(value);	//red green blue yellow 直接读取键值
}

//字符串也可以使用for of
var str='kaivon';
for(var value of str){
	console.log(value);		//k a i v o n 只要有遍历接口的对象都可以用它
}


//遍历keys
for(var key of color.keys()){
	console.log(key);		//0 1 2 3 存储了数组的所有键名
}

//遍历values
/*for(var value of color.values()){  存储了数组的所有键值
	console.log(value);		//red green blue yellow	提示一下，chrom还不支持
}*/

//遍历entries
for(let [key,value] of color.entries()){
	console.log(key,value);	//0 "red"	 1 "green"	2 "blue"		3 "yellow"  存储了数组的所有键值对
}
```
### map遍历
> 遍历数据或者对象，并映射新对象
``` javascript
//场景1
//假定有一个数值数组(A),将A数组中的值以双倍的形式放到B数组 
 var numbers = [1,2,3];
 var doubledNumbers = [];
// es5
for(var i = 0; i < numbers.length; i++){
  doubledNumbers.push(numbers[i] * 2);
}

// es6 map
var doubled = numbers.map(function(number){
    return number * 2;
})

//场景1
//假定有一个对象数组(A),将A数中对象某个属性的值存储到B数组中
var cars = [
  {model:"Buick",price:"CHEAP"},
  {model:"BMW",price:"expensive"}
];
var prices = cars.map(function(car){
    return car.price;
})

```
## 十一、函数参数默认值
``` javascript
'use strict'
function fn(a,b){
	b=b||'kaivon';  //以往设置默认参数方法
	console.log(a,b);
}
fn('hello');		//hello kaivon
fn('hello','moto');	//hello moto

//参数变量是默认声明的，不能用let或者const再次声明
function fn2(a=20,b=10){    //直接将默认参数写在函数括号中
	//console.log(a,b);		//20 10
	//let a=12;				//报错
	console.log(a+b);       //30
}
fn2();			//30
fn2(23,45);		//68

```
## 十二、箭头函数
>**语法：**
1、function用var、let、const来表示
2、参数要写在第一个等号后面

>- 1、如果没有参数，需要写一对空的小括号
* 2、只有一个参数，那就直接写，不用加括号
* 3、参数有多个，需要加一个小括号，参数用逗号隔开
``` javascript
'use strict'
/*function fn1(){
	console.log('kaivon');
}
fn1();*/ //常规写法

var fn1=()=>console.log('kaivon');  //es5写法 如果没有参数，需要写一对空的小括号
fn1();


let fn2=a=>console.log(a);  //只有一个参数，那就直接写，不用加括号
fn2('kaivon');			//kaivon

const fn3=(a,b)=>{      //参数有多个，需要加一个小括号，参数用逗号隔开
	let result=a+b;
	console.log(result);
}
fn3(1,2);		//3
			
```
## 十三、对象的简洁表示法
属性与方法都可以简洁表示 , 当属性与值的变量同名时。
``` javascript
'use strict'
/*function fn(){
	var x=10;
	var y=20;
	function sum(){
		return x+y;
	}
	
	return {x:x,y:y,sum:sum}
}
console.log(fn());*/        //常规获取函数内部变量及方法


function fn(){              //ES6获取函数内部变量及方法
	var x=10;
	var y=20;
	function sum(){
		return x+y;
	}
	
	return {
		x,
		y,
		sum(){
			return x+y;
		}
	}
}
console.log(fn());
console.log(fn().sum())			//30


const name = 'Jane';
const age = 20

// es6
const person = {
  name,
  age
}

// es5
var person = {
  name: name,
  age: age
};
```
## 十四、属性名表达式
**可以把表达式放在中括号里作为属性名**
``` javascript
'use strict'

let key='interest';

let obj={
	name:'kaivon',
	[key]:'打豆豆',
	['show'](){
		console.log(this.name);
	}
}
console.log(obj);

```
## 十五、Object.is()
**比较两个值是否相等，与===的结果一样，但是还是有一些区别**
>- 返回的结果是一个布尔值
 区别:
 *  1、+0与-0比较的结果为false
 *  2、NaN与NaN比较的结果为true
``` javascript
'use strict'
console.log(Object.is('kaivon','kaivon'));		//true

console.log(+0===-0);			//true
console.log(Object.is(+0,-0));	//false

console.log(NaN===NaN);			//false
console.log(Object.is(NaN,NaN));	//true
```
## 十六、Object.is()
**将一个对象身上的属性复制到另一个对象身上**
>- 至少需要两个参数
 * 	1、第一个参数为合并后的对象
 * 	2、从第二个参数开始往后就是所有要合并的对象
 * 	3、如果有同名的属性，后面会把前面覆盖了
 * 	4、如果有嵌套的对象，是覆盖并不是添加
 
 
``` javascript
'use strict'
var obj1={a:1};
var obj2={b:2};
var obj3={a:4,c:3};

Object.assign(obj1,obj2,obj3);
console.log(obj1); //{a:1,b:2,c:3}  //如果有同名的属性，后面会把前面覆盖了

//封装个函数
const merge=(target,...souce)=>{
	Object.assign(target,...souce);
	return target;
}
console.log(merge(obj1,obj2,obj3));		//Object {a: 4, b: 2, c: 3}

//如果有嵌套的对象，是覆盖并不是添加
var obj4={
	d:{
		e:5
	}
}
var obj5={
	d:{
		f:6
	}
}

console.log(merge({},obj4,obj5)); //{a: 4, b: 2, c: 3}
```
## 十七、Set数据结构
**Set 数据结构，类似数组。所有的数据都是唯一的，没有重复的值。它本身是一个构造函数**
> - size	数据的长度
- add()		添加一个数据
- deleate()	删除一个数据
- has()		查找某条数据，返回一个布尔值
- clear()	删除所有数据
``` javascript
'use strict'
var set=new Set([1,3,4,5,4,3,2]);
set.add(6);     //在数据结构中添加 6 
set.delete(1);  //在数据结构中删除 1
console.log(set.has(5));    //true 在数据结构中查找是否有 5
set.clear();    //删除所有数据内容
console.log(set,set.size);			//Set {1, 3, 4, 5, 2} 5
```

## 十八、 展开运算符
**在ES6中用...来表示展开运算符，它可以将数组方法或者对象进行展开。先来看一个例子它是如何使用的。**
``` javascript
const arr1 = [1, 2, 3];
const arr2 = [...arr1, 10, 20, 30];
// 这样，arr2 就变成了[1, 2, 3, 10, 20, 30];
```
当然，展开对象数据也是可以得到类似的结果
```
const obj1 = {
  a: 1,
  b: 2, 
  c: 3
}

const obj2 = {
  ...obj1,
  d: 4,
  e: 5,
  f: 6
}

// 结果类似于 const obj2 = Object.assign({}, obj1, {d: 4})