---
title: js的面向对象详解
date: 2020-03-29 16:04:20
tags:
- 知识点
- javascript
- 面向对象
categories:
- 前端
---

## 对象的创建：
- 创建一个面向对象
``` javascript
var obj = new Object(); 
obj.name = 'haha';
obj.showName = function(){ 
  alert(obj.name);
}
obj.showName();
```
缺点：当我们想创建多个面向对象的时候，重复代码过多，需要封装，所以有了工厂方法。

<!-- more -->

- 工厂方式
``` javascript
function CreatePerson(name){ 
   var obj = new Object();   //原料
   obj.name = name;         //加工
   obj.showName = function(){
     alert(this.name);
 } 
   return obj;//出厂
}
var p1 = CreatePerson('haha');
p1.showName();
var p2 = CreatePerson('hehe');
p2.showName();
//其实就是简单的封装函数，整个过程像工厂的流水线，所以叫工厂方式
```
缺点：无法识别创建的对象的类型。因为全部都是Object，没有区分度，不像Date、Array等，因此出现了构造函数模式。

- 构造函数模式
``` javascript
function CreatePerson(name){ 
   this.name = name; 
   this.showName = function(){ 
     alert(this.name);
   } 
} 
var p1 =new CreatePerson('haha'); 
p1.showName();
var p2 = new CreatePerson('hehe'); 
p2.showName();
```
我们通过这二个方面来改变：

- 1 函数名首字母大写
这是为了区别于普通的函数，构造函数本身其实就是普通的函数，只是我们专门用它来实现了构造的功能，所以专门起了一个名字叫构造函数，任何函数都可以成为构造函数，这取决于你调用函数的方式，当使用了New的方式调用就成了构造函数。
- 2 New 关键字调用
调用函数的时候用了 New关键字，那么New到底做了什么？用不用New有什么区别？再来看下面的例子
``` javascript
function CreatePerson(name){
   this.name = name;
   this.showName = function(){
     alert(this.name); 
   }; 
    console.log(this);
} 
new CreatePerson('haha'); //CreatePerson 指向了当前构造函数
CreatePerson('haha');  //window 指向了window
```
我们会发现当用New去调用一个函数的时候，this的指向会不一样。其实New主要做了下面这些事，不过下面写的只是大概的行为，并不是内部源码。
``` javascript
function CreatePerson(name){ 
   var obj = {}; //声明一个空对象obj 
   obj._proto_= CreatePerson.prototype;
   //把这个对象的_proto_属性指向构造函数的原型对象,这样obj就可以调用CreatePerson原型对象下的所有方法 ，这里原型先知道结论，下面会讲。
    CreatePerson.apply(obj);   //用apply方法让this指向obj对象
    this.name = name;   //obj对象添加属性，方法
    this.showName = function(){ 
       alert(this.name);
      }; 
    return obj;//返回这个对象
}
```

函数构造模式存在的问题：
``` javascript
alert(p1.showName==p2.showName);//false
```
缺点：可见这两个对象并不是共用一个方法，每new一次，系统都会新创建一个内存，这两个对象各自有各自的地盘，但他们具有相同的功能，还不共用，肯定不是我们所希望的。所以就有了下一种方法，原型+构造模式

- 原型+构造模式
原型：每个函数都有一个prototype属性，它是一个对象，也称作原型对象，我们可以把方法和属性写在它上面（不过原型对象不仅仅有我们写的属性和方法，还有别的，下面会介绍），而通过这个函数创建出来的实例对象，都能共享这个原型对象下的方法和属性。所以我们只需要把想要共享的东西放在函数的prototype下，不想共享的东西通过构造函数来创建就可以了。
看个栗子（原型+构造）
``` javascript
function CreatePerson(name){ 
  this.name = name;
}
CreatePerson.prototype.showName = function(){ 
   alert(this.name);
}
var p1 =new CreatePerson('haha');
p1.showName();
var p2 = new CreatePerson('hehe');
p2.showName();
alert(p1.showName==p2.showName);//true
```
测试为true，可见showName()方法是共享的，也就是说他们共用一个内存，更进一步的说它们存在引用关系，也就是说你更改了p1的showName也会影响p2的showName。

_proto_属性:
同一个函数造出来的实例对象能共享这个函数的prototype下的方法和属性，但是它是如何做到的呢？这里要出场的就是_proto_属性.
每个实例化对象都有_proto_属性，它是一个指针，指向函数的prototype，也就是保存了它的地址。（JS中任何对象的值都是保存在堆内存中，我们声明的变量只是一个指针，保存了这个对象的实际地址，所以有了地址就能找到对象），
所以总得来说，每个实例化对象都有_proto_属性，保存了构造函数的原型对象的地址，通过这个属性就可以拥有原型对象下的所有属性和方法，_proto_属性实际就是实例化对象和原型对象之间的连接

原型链：
每个函数都可以成为构造函数，每个函数都有原型对象，每个原型对象也可以是一个实例化对象，比如，你创建了一个函数fun,它是构造函数function的实例化对象，而function的原型对象，又是Object的实例对象。所以fun有个_proto_属性可以访问到function的原型对象,function原型对象也是个实例对象，也有个_proto_属性,可以访问到Object的原型对象，所以通过_proto_属性，就形成了一条原型链。每个实例化对象都可以访问到链子上方的方法和属性，所以fun是可以访问Object原型对象下的方法和属性的。实际上所有对象都可以访问到Object的原型对象。

原型链的访问规则：先在自身的下面寻找，再去一级一级的往原型链上找。
如下：
``` javascript
function Aaa(){}
Aaa.prototype.num = 3;
var a1 = new Aaa();
a1.num =10;
alert(a1.num); //10
```
![image_1clisr95k1r2a857e9ii3oqc9.png-15.3kB][1]
[1]: http://static.zybuluo.com/wp0214/l00axuifrvo7bvpqfeiz8pfa/image_1clisr95k1r2a857e9ii3oqc9.png