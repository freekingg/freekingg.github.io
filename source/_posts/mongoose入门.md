---
title: mongoose入门
date: 2020-03-30 17:27:37
tags:
- mongodb
- mongoose
- 数据库
categories:
- 数据库
---

## **简介**

今天我们将学习Mongoose，什么是Mongoose呢，它于MongoDB又是什么关系呢，它可以用来做什么呢，介绍Mongoose之前，我们先简单了解一下MongoDB。

　**Mongoose是在node.js异步环境下对mongodb进行便捷操作的对象模型工具。本文将详细介绍如何使用Mongoose来操作MongoDB**

　　MongoDB是一个开源的NoSQL数据库，相比MySQL那样的关系型数据库，它更显得轻巧、灵活，非常适合在数据规模很大、事务性不强的场合下使用。同时它也是一个对象数据库，没有表、行等概念，也没有固定的模式和结构，所有的数据以文档的形式存储(文档，就是一个关联数组式的对象，它的内部由属性组成，一个属性对应的值可能是一个数、字符串、日期、数组，甚至是一个嵌套的文档。)，数据格式就是JSON。

介绍了MongoDB，我们下面就要认识Mongoose了。

Mongoose是什么？

　　Mongoose是MongoDB的一个对象模型工具，是基于node-mongodb-native开发的MongoDB nodejs驱动，可以在异步的环境下执行。同时它也是针对MongoDB操作的一个对象模型库，封装了MongoDB对文档的的一些增删改查等常用方法，让NodeJS操作Mongodb数据库变得更加灵活简单。

Mongoose能做什么？

　　Mongoose，因为封装了对MongoDB对文档操作的常用处理方法，让NodeJS操作Mongodb数据库变得easy、easy、So easy!

　　学习了上面的介绍，相信你已经对Mongoose有了初步的认识和了解，千里之行，始于足下，奔跑吧，少年！

## **安装引用**

前面我们已经认识了Mongoose，也了解了MongoDB，回顾一下：MongoDB是一个对象数据库，是用来存储数据的；Mongoose是封装了MongoDB操作的一个对象模型库,是用来操作这些数据的。

好，下面我们就来进行操作数据的第一步吧。

1. **安装mongoose：**
``` shell
npm install mongoose
```

2. **引用mongoose：**
``` shell
var mongoose = require("mongoose");
```
3. **使用"mongoose"连接数据库：**

使用connect()方法连接到MongoDB数据库
connect()最简单的使用方式，就是只要传入url参数即可，如下所示。连接到本地localhost的db1服务器
``` shell
mongoose.connect('mongodb://localhost/db1');
```

如果还需要传递用户名、密码，则可以使用如下方式
``` shell
mongoose.connect('mongodb://username:password@host:port/database?options...');
```
  connect()方法还接受一个选项对象options，该对象将传递给底层驱动程序。这里所包含的所有选项优先于连接字符串中传递的选项
``` shell
  mongoose.connect(uri, options);
```
可用选项如下所示
``` shell
 db            -数据库设置
 server        -服务器设置
 replset       -副本集设置
 user          -用户名
 pass          -密码
 auth          -鉴权选项
 mongos        -连接多个数据库
 promiseLibrary
```
 **栗子 :**
``` shell
 var options = {
  db: { native_parser: true },
  server: { poolSize: 5 },
  replset: { rs_name: 'myReplicaSetName' },
  user: 'myUserName',
  pass: 'myPassword'
}
mongoose.connect(uri, options);
```
如果要连接多个数据库，只需要设置多个url以,隔开，同时设置mongos为true
``` shell
mongoose.connect('urlA,urlB,...', {
   mongos : true 
})
```

connect()函数还接受一个回调参数
``` shell
mongoose.connect(uri, options, function(error) {

});
```

执行下列代码后，控制台输出“连接成功”
``` shell
var mongoose = require('mongoose');
mongoose.connect("mongodb://localhost/test", function(err) {
    if(err){
        console.log('连接失败');
    }else{
        console.log('连接成功');
    }
});
```

 **栗子 :**
 **如果开启鉴权控制，以用户名"u1"，密码"123456"登录'db1'数据库。执行代码后，控制台输出“连接成功”**
``` shell
 var mongoose = require('mongoose');
mongoose.connect("mongodb://u1:123456@localhost/db1", function(err) {
    if(err){
        console.log('连接失败');
    }else{
        console.log('连接成功');
    }
});
```

4. **执行下面代码检查默认数据库test，是否可以正常连接成功?**
``` shell
var mongoose = require("mongoose");
var db = mongoose.connect("mongodb://127.0.0.1:27017/test");
db.connection.on("error", function (error) {
    console.log("数据库连接失败：" + error);
});
db.connection.on("open", function () {
    console.log("------数据库连接成功！------");
});
```

**栗子：**

- 1、使用require()方法引入mongodb数据库；然后使用MongoClient对象的connect()方法连接mongodb；最后通过node来对mongodb进行异步的增删改查
- 2、在mongodb数据库中建立db1数据库，然后通过以下代码，建立col集合，并插入{"a":1}文档
``` shell
var mongodb = require('mongodb');
mongodb.MongoClient.connect("mongodb://localhost/db1",function(err,db){
    if(!err){
        db.collection("col").insert({"a":1},function(err,result){
            if(!err){
                console.log(result);
            }
        })
    }
})
```
  最后返回结果如下
``` shell
{
    result: {
        ok: 1,
        n: 1
    },
    ops: [{
        a: 1,
        _id: 597077 dc271d092728caa362
    }],
    insertedCount: 1,
    insertedIds: [597077 dc271d092728caa362]
}
```

## **概念理解**

Mongoose是NodeJS的驱动，不能作为其他语言的驱动。Mongoose有两个特点

- 1、通过关系型数据库的思想来设计非关系型数据库

- 2、基于mongodb驱动，简化操作

![!\[\](images/screenshot_1516091179384.png)][1]

 Mongooose中，有三个比较重要的概念，分别是Schema、Model、Entity。
 它们的关系是：
> Schema生成Model
Model创造Document
Model和Document都可对数据库操作造成影响，但Model比Document更具操作性

Schema用于定义数据库的结构。类似创建表时的数据定义(不仅仅可以定义文档的结构和属性，还可以定义文档的实例方法、静态模型方法、复合索引等)，每个Schema会映射到mongodb中的一个collection，Schema不具备操作数据库的能力

Model是由Schema编译而成的构造器，具有抽象属性和行为，可以对数据库进行增删查改。Model的每一个实例（instance）就是一个文档document

Document是由Model创建的实体，它的操作也会影响数据库
  
  


  [1]: http://static.zybuluo.com/wp0214/jktjwns8ieb1iwtwrkj4jnvh/image_1c3vgq5iem5i1u6p18gi1l1h1gi9.png

## **了解集合collection**

通过上节课程的学习我们已经打下了基础，本节课程就开始对MongoDB数据库进行具体操作。首先，我们再次简单介绍一下MongoDB数据库。

　　MongoDB —— 是一个对象数据库，没有表、行等概念，也没有固定的模式和结构，所有的数据以Document(以下简称文档)的形式存储(Document，就是一个关联数组式的对象，它的内部由属性组成，一个属性对应的值可能是一个数、字符串、日期、数组，甚至是一个嵌套的文档。)，后面我们会学习如何创建文档并插入内容。

　　**在MongoDB中，多个Document可以组成Collection(以下简称集合)，多个集合又可以组成数据库。**

　　我们想要操作MongoDB数据，那就得先要具备上面所说的包含数据的`文档`，文档又是什么意思呢，请看如下介绍。

　　**文档** —— 是MongoDB的核心概念，是键值对的一个有序集，在JavaScript里文档被表示成对象。同时它也是MongoDB中数据的基本单元，非常类似于关系型数据库管理系统中的行，但更具表现力。

　　**集合** —— 由一组文档组成，如果将MongoDB中的一个文档比喻成关系型数据库中的一行，那么一个集合就相当于一张表。

　　如果我们要通过Mongoose去创建一个“集合”并对其进行增删改查，该怎么实现呢，到这里我们就要先了解Schema(数据属性模型)、Model、Entity了。
  
  好，下面就开始去深入了解它们吧！

## **模型骨架Schema**

**Schema** —— 一种以文件形式存储的数据库模型骨架，无法直接通往数据库端，也就是说它不具备对数据库的操作能力，仅仅只是数据库模型在程序片段中的一种表现，可以说是数据属性模型(传统意义的表结构)，又或着是“集合”的模型骨架。

**Schema主要用于定义MongoDB中集合Collection里文档document的结构**

定义Schema非常简单，指定字段名和类型即可，支持的类型包括以下8种
``` shell
String      字符串
Number      数字    
Date        日期
Buffer      二进制
Boolean     布尔值
Mixed       混合类型
ObjectId    对象ID    
Array       数组
```

**通过mongoose.Schema来调用Schema，然后使用new方法来创建schema对象**
``` shell
var mongoose = require('mongoose');

var Schema = mongoose.Schema;

var mySchema = new Schema({
  title:  String,
  author: String,
  body:   String,
  comments: [{ body: String, date: Date }],
  date: { type: Date, default: Date.now },	//类型为日期，默认为当前时间
  hidden: Boolean,
  meta: {
    votes: Number,
    favs:  Number
  }
});
```
**创建Schema对象时，声明字段类型有两种方法，一种是首字母大写的字段类型，另一种是引号包含的小写字段类型**
``` shell
var mySchema = new Schema({title:String, author:String});
//或者 
var mySchema = new Schema({title:'string', author:'string'});
```

如果需要在Schema定义后添加其他字段，可以使用add()方法
``` shell
var MySchema = new Schema();
MySchema.add({ name: 'string', color: 'string', price: 'number' });
```


## **模型Model**

**模型Model是根据Schema编译出的构造器，或者称为类，通过Model可以实例化出文档对象document**

文档document的创建和检索都需要通过模型Model来处理

如何通过Schema来创建Model呢，如下示例：

``` js
mongoose.model('test1','TestSchema')
```
**test1：数据库中的集合名称,当我们对其添加数据时如果test1已经存在，则会保存到其目录下，如果未存在，则会创建test1集合，然后在保存数据。**

拥有了Model，我们也就拥有了操作数据库的金钥匙，就可使用Model来进行增删改查的具体操作，




如果你想对某个集合有所作为，那就交给Model模型来处理吧，
创建一个Model模型，我们需要指定：
1、集合名称
2、集合的Schema结构对象

满足这两个条件，我们就会拥有一个操作数据库的金钥匙。

**栗子**

``` js
var TestSchema = new mongoose.Schema({ 
    num:Number, 
    name: String, 
    size: String
});
var MyModel = mongoose.model('MyModel', TestSchema);
```
>　Mongoose会将集合名称设置为模型名称的小写版。如果名称的最后一个字符是字母，则会变成复数；如果名称的最后一个字符是数字，则不变；如果模型名称为"MyModel"，则集合名称为"mymodels"；如果模型名称为"Model1"，则集合名称为"model1"

## **实例Entity**

通过对原型Model使用new方法，实例化出文档document对象

**document** —— 由Model创建的实体，使用save方法保存数据，Model和document都有能影响数据库的操作，但Model比document更具操作性。

使用Model创建实例Entity，如下示例：

``` js
var TestEntity = new TestModel({
       name : "Lenka",
       age  : 36,
       email: "lenka@qq.com"
});
console.log(TestEntity.name); // Lenka
console.log(TestEntity.age); // 36
```

**通过对原型Model使用new方法，实例化出文档document对象**
``` js
var mongoose = require('mongoose');
mongoose.connect("mongodb://u1:123456@localhost/db1", function(err) {
    if(err){
        console.log('连接失败');
    }else{
        console.log('连接成功');
        var schema = new mongoose.Schema({ //定义骨架
            num:Number, 
            name: String, 
            size: String
        });
        
        var MyModel = mongoose.model('MyModel', schema);//应用模型
        
        var doc1 = new MyModel({ size: 'small' });	//使用new方法，实例化出文档document对象
        
        console.log(doc1.size);	//'small'
        
    }
});
```

**文档保存**

　通过 以上 new MyModel()创建的文档doc1，必须通过save()方法，才能将创建的文档保存到数据库的集合中，集合名称为模型名称的小写复数版

回调函数是可选项，第一个参数为err，第二个参数为保存的文档对象

``` js
save(function (err, doc) { })
```
``` js
var mongoose = require('mongoose');

mongoose.connect("mongodb://u1:123456@localhost/db1", function(err) {
    if(!err){
        var schema = new mongoose.Schema({
            num:Number, 
            name: String,
            size: String
        });
        var MyModel = mongoose.model('MyModel', schema);
        var doc1 = new MyModel({ size: 'small' });
        doc1.save(function (err,doc) {
        //{ __v: 0, size: 'small', _id: 5970daba61162662b45a24a1 }
          console.log(doc);
        })
    }
});
```
由下所示，db1数据库中的集合名称为mymodels，里面有一个{size:"small"}的文档
![image_1c3vhncrl1mtoh4o131h1dfd8379.png-4.7kB][1]


  [1]: http://static.zybuluo.com/wp0214/uf73zx0l65fp739dk20dl1cj/image_1c3vhncrl1mtoh4o131h1dfd8379.png


## **文档新增**

**文档新增常用方法:**

- 一种是使用文档的`save()`方法，
- 另一种是使用模型model的`create()`方法，
- 一种是模型model的`insertMany()`方法	

**save()**

``` js
Entity.save([options], [options.safe], [options.validateBeforeSave], [fn])
```
**栗子**
 新建{age:10,name:'save'}文档，并保存
``` js
var mongoose = require('mongoose');

mongoose.connect("mongodb://u1:123456@localhost/db1", function(err) {
    if(!err){
    
        var schema = new mongoose.Schema({ 
            age:Number, 
            name: String
        });        
        
        var temp = mongoose.model('temp', schema);
        //使用链式写法    
        new temp({age:10,name:'save'}).save(function(err,doc){
            //[ { _id: 59720bc0d2b1125cbcd60b3f, age: 10, name: 'save', __v: 0 } ]
            console.log(doc);        
        });         
    }           
});
```

**create()**
使用save()方法，需要先实例化为文档，再使用save()方法保存文档。
而create()方法，则直接在模型Model上操作，并且可以同时新增多个文档
``` js
Model.create(doc(s), [callback])
```
**栗子**
``` js
var mongoose = require('mongoose');
mongoose.connect("mongodb://u1:123456@localhost/db1", function(err) {
    if(!err){
        var schema = new mongoose.Schema({
            age:Number, 
            name: String
        });        
        var temp = mongoose.model('temp', schema);   
        temp.create(
            {name:"xiaowang"},
            {name:"xiaoli"},
            function(err,doc1,doc2){
                //{ __v: 0, name: 'xiaowang', _id: 59720d83ad8a953f5cd04664 }
                console.log(doc1); 
                //{ __v: 0, name: 'xiaoli', _id: 59720d83ad8a953f5cd04665 }
                console.log(doc2); 
        });       
    }           
});
```

**insertMany()**
``` js 
Model.insertMany(doc(s), [options], [callback])
```

**栗子**
``` js
var mongoose = require('mongoose');
mongoose.connect("mongodb://u1:123456@localhost/db1", function(err) {
    if(!err){
        var schema = new mongoose.Schema({ 
            age:Number, 
            name: String
        });        
        var temp = mongoose.model('temp', schema);   
        temp.insertMany([
            {name:"a"},
            {name:"b"}
      	  ],
        function(err,docs){
            //[ { __v: 0, name: 'a', _id: 59720ea1bbf5792af824b30c },
            //{ __v: 0, name: 'b', _id: 59720ea1bbf5792af824b30d } ]
            console.log(docs); 
        });       
    }           
});

```
## **文档查询**
查询就是返回一个集合中的文档的子集，Mongoose 模型提供了find、findOne、和findById方法用于文档查询。

使用Mongoose来查找文档很容易，有以下几种方法可供选择

- find()
- findById()
- findOne()

**find()**
```
Model.find(conditions, [projection], [options], [callback])
```
   第一个参数表示查询条件，第二个参数用于控制返回的字段，第三个参数用于配置查询参数，第四个参数是回调函数，回调函数的形式为function(err,docs){}
   
**栗子**
现在，使用find()方法找出所有数据
```
var mongoose = require('mongoose');
mongoose.connect("mongodb://u1:123456@localhost/db1", function(err) {
    if(!err){
        var schema = new mongoose.Schema({ age:Number, name: String});        
        var temp = mongoose.model('temp', schema);
        temp.find({},function(err,docs){
            //若没有向find传递参数，默认的是显示所有文档
        	 console.log(docs);
            //[ { _id: 5971f93be6f98ec60e3dc86c, name: 'huochai', age: 27 },
            //{ _id: 5971f93be6f98ec60e3dc86d, name: 'wang', age: 18 },
            //{ _id: 5971f93be6f98ec60e3dc86e, name: 'huo', age: 30 },
            //{ _id: 5971f93be6f98ec60e3dc86f, name: 'li', age: 12 } ]
          
        })
    }
});
```
找出年龄大于18的数据
```
temp.find({age:{$gte:18}},function(err,docs){
     console.log(docs);
})
```
找出年龄小于18的数据
```
temp.find({age:{$lt:18}},function(err,docs){
     console.log(docs);
})
```
查询所有nage大于18小于60的数据
```
temp.find({"age":{"$gt":18,"$lt":60}},function(error,docs){
   //查询所有nage大于18小于60的数据
});
```
找出年龄大于18且名字里存在'huo'的数据
```
temp.find({name:/huo/,age:{$gte:18}},function(err,docs){
    console.log(docs);
})
```
找出名字里存在'a'的数据，且只输出'name'字段
*注意id字段默认输出*
```
temp.find({name:/a/},'name',function(err,docs){
    //[ { _id: 5971f93be6f98ec60e3dc86c, name: 'huochai' },
    //{ _id: 5971f93be6f98ec60e3dc86d, name: 'wang' } ]
    console.log(docs);
})
```
如果确实不需要_id字段输出，可以进行如下设置
```
temp.find({name:/a/},{name:1,_id:0},function(err,docs){
    //[ { name: 'huochai' }, { name: 'wang' } ]
    console.log(docs);
})
```
找出跳过前两条数据的其他所有数据
*如果使用第三个参数，前两个参数如果没有值，需要设置为null*
```
temp.find(null,null,{skip:2},function(err,docs){
    //[ { _id: 5971f93be6f98ec60e3dc86e, name: 'huo', age: 30 },
    //{ _id: 5971f93be6f98ec60e3dc86f, name: 'li', age: 12 } ]
    console.log(docs);
})
```

**findById()**
``` js
Model.findById(id, [projection], [options], [callback])
```
显示第0个元素的所有字段

``` js
var aIDArr = [];
temp.find(function(err,docs){
    docs.forEach(function(item,index,arr){
        aIDArr.push(item._id);
    })
    temp.findById(aIDArr[0],function(err,doc){
        //{ _id: 5971f93be6f98ec60e3dc86c, name: 'huochai', age: 27 }
        console.log(doc);
    })            
})
```
以上代码的另一种写法如下
``` js
var aIDArr = [];
temp.find(function(err,docs){
    docs.forEach(function(item,index,arr){
        aIDArr.push(item._id);
    })
    temp.findById(aIDArr[0]).exec(function(err,doc){
        //{ _id: 5971f93be6f98ec60e3dc86c, name: 'huochai', age: 27 }
        console.log(doc);
    })            
})
```
只输出name字段
``` js
temp.findById(aIDArr[0],{name:1,_id:0},function(err,doc){
    //{  name: 'huochai'}
    console.log(doc);
})  
```
或者写成下面这种形式
``` js
temp.findById(aIDArr[0],{name:1,_id:0}).exec(function(err,doc){
    //{  name: 'huochai'}
    console.log(doc);
}) 
```

输出最少的字段
``` js
temp.findById(aIDArr[0],{lean:true},function(err,doc){
    //{ _id: 5971f93be6f98ec60e3dc86c }
    console.log(doc);
})   
temp.findById(aIDArr[0],{lean:true}).exec(function(err,doc){
    //{ _id: 5971f93be6f98ec60e3dc86c }
    console.log(doc);
}) 
```
**findOne()**
该方法返回查找到的所有实例的第一个

``` js
Model.findOne([conditions], [projection], [options], [callback])
```

找出age>20的文档中的第一个文档

``` js
temp.findOne({age:{$gt : 20}},function(err,doc){
    //{ _id: 5971f93be6f98ec60e3dc86c, name: 'huochai', age: 27 }
    console.log(doc);
})   
//或者
temp.findOne({age:{$gt : 20}}).exec(function(err,doc){
    //{ _id: 5971f93be6f98ec60e3dc86c, name: 'huochai', age: 27 }
    console.log(doc);
})  
```
找出age>20的文档中的第一个文档，且只输出name字段
``` js
temp.findOne({age:{$gt : 20}},{name:1,_id:0},function(err,doc){
    //{ name: 'huochai' }
    console.log(doc);
})   
或者
temp.findOne({age:{$gt : 20}},{name:1,_id:0}).exec(function(err,doc){
    //{ name: 'huochai' }
    console.log(doc);
})  
```
**文档查询常用的查询条件**
``` js
$or　　　　或关系
$nor　　　 或关系取反
$gt　　　　大于
$gte　　　 大于等于
$lt　　　　小于
$lte　　　 小于等于
$ne　　　　不等于
$in　　　　在多个值范围内
$nin　　　 不在多个值范围内
$all　　　 匹配数组中多个值
$regex　　 正则，用于模糊查询
$size　　　匹配数组大小
$maxDistance　范围查询，距离（基于LBS）
$mod　　　　取模运算
$near　　　 邻域查询，查询附近的位置（基于LBS）
$exists　　 字段是否存在
$elemMatch　匹配内数组内的元素
$within　　　范围查询（基于LBS）
$box　　　　 范围查询，矩形范围（基于LBS）
$center　　　范围醒询，圆形范围（基于LBS）
$centerSphere　范围查询，球形范围（基于LBS）
$slice　　　　查询字段集合中的元素（比如从第几个之后，第N到第M个元素
```
###**$where**

如果要进行更复杂的查询，需要使用`$where`操作符，`$where`操作符功能强大而且灵活，它可以使用任意的JavaScript作为查询的一部分，包含JavaScript表达式的字符串或者JavaScript函数

目前数据：
![image_1c3vjh8s51cvu95vv051ci21hjq9.png-8.6kB][1]


  [1]: http://static.zybuluo.com/wp0214/kzi2kv6v80cuil1h6mzvnjya/image_1c3vjh8s51cvu95vv051ci21hjq9.png
  
**使用字符串**
``` js
/*查询当条数据的x值等于当条数据y值的文档*/
temp.find({$where:"this.x == this.y"},function(err,docs){
    //[ { _id: 5972ed35e6f98ec60e3dc887,name: 'wang',age: 18,x: 1,y: 1 },
    //{ _id: 5972ed35e6f98ec60e3dc889, name: 'li', age: 20, x: 2, y: 2 } ]
    console.log(docs);
}) 

//或者

temp.find({$where:"obj.x == obj.y"},function(err,docs){
    //[ { _id: 5972ed35e6f98ec60e3dc887,name: 'wang',age: 18,x: 1,y: 1 },
    //{ _id: 5972ed35e6f98ec60e3dc889, name: 'li', age: 20, x: 2, y: 2 } ]
    console.log(docs);
}) 
```
**使用函数**
``` js
temp.find({$where:function(){
        return obj.x !== obj.y;
    }},function(err,docs){
    //[ { _id: 5972ed35e6f98ec60e3dc886,name: 'huochai',age: 27,x: 1,y: 2 },
    //{ _id: 5972ed35e6f98ec60e3dc888, name: 'huo', age: 30, x: 2, y: 1 } ]
    console.log(docs);
})

//或者

temp.find({$where:function(){
        return this.x !== this.y;
    }},function(err,docs){
    //[ { _id: 5972ed35e6f98ec60e3dc886,name: 'huochai',age: 27,x: 1,y: 2 },
    //{ _id: 5972ed35e6f98ec60e3dc888, name: 'huo', age: 30, x: 2, y: 1 } ]
    console.log(docs);
})

```

## 文档修改


文档更新可以使用以下几种方法

- update()
- updateMany()
- find() + save()
- updateOne()
- findOne() + save()
- findByIdAndUpdate()
- fingOneAndUpdate()

### **update()**
``` js
Model.update(conditions, doc, [options], [callback])
```
>第一个参数conditions为查询条件
第二个参数doc为需要修改的数据
第三个参数options为控制选项
第四个参数是回调函数

options有如下选项
``` js
    safe (boolean)： 默认为true。安全模式。
　　upsert (boolean)： 默认为false。如果不存在则创建新记录。
　　multi (boolean)： 默认为false。是否更新多个查询记录。
　　runValidators： 如果值为true，执行Validation验证。
　　setDefaultsOnInsert： 如果upsert选项为true，在新建时插入文档定义的默认值。
　　strict (boolean)： 以strict模式进行更新。
　　overwrite (boolean)： 默认为false。禁用update-only模式，允许覆盖记录。
```
数据库temps中现有数据如下:
![image_1c3vkaq1ui8k2k015orkjt1ia79.png-7.2kB][1]


现在使用update()方法查询age大于20的数据，并将其年龄更改为40岁
``` js
var mongoose = require('mongoose');
mongoose.connect("mongodb://u1:123456@localhost/db1", function(err) {
    if(!err){
        var schema = new mongoose.Schema({ age:Number, name: String});        
        var temp = mongoose.model('temp', schema);   
        temp.update({age:{$gte:20}},{age:40},function(err,raw){
            //{ n: 1, nModified: 1, ok: 1 }
            console.log(raw);
        })
    }           
});
```
经过以上操作，数据库结果如下。只有第一个数据更改为40岁。而第三个数据没有发生变化
![image_1c3vkdd629khvjlnn11cv5inem.png-7.2kB][2]


如果要同时更新多个记录，需要设置options里的multi为true。下面将名字中有'a'字符的年龄设置为10岁
``` js
var mongoose = require('mongoose');
mongoose.connect("mongodb://u1:123456@localhost/db1", function(err) {
    if(!err){
        var schema = new mongoose.Schema({ age:Number, name: String});        
        var temp = mongoose.model('temp', schema);   
        temp.update({name:/a/},{age: 10},{multi:true},function(err,raw){
            //{ n: 2, nModified: 2, ok: 1 }
            console.log(raw);
        })
    }           
});
```
结果
![image_1c3vkrfahu171gp9u8pmms16i713.png-7.4kB][3]


如果设置的查找条件，数据库里的数据并不满足，默认什么事都不发生
``` js
temp.update({age:100},{name: "hundred"},function(err,raw){
    //{ n: 0, nModified: 0, ok: 1 }
    console.log(raw);
})
```
如果设置options里的upsert参数为true，若没有符合查询条件的文档，mongo将会综合第一第二个参数向集合插入一个新的文档
``` js
temp.update({age:100},{name: "hundred"},{upsert:true},function(err,raw){
    //{ n: 1, nModified: 0,upserted: [ { index: 0, _id: 5972c202d46b621fca7fc8c7 } ], ok: 1 }
    console.log(raw);
})
```
![image_1c3vl875k1kpo138s1ikh7ce1flu1g.png-9.6kB][4]
``` js
temp.update({name:/aa/},{age: 0},{upsert:true},function(err,raw){
    //{ n: 1, nModified: 0,upserted: [ { index: 0, _id: 5972c288d46b621fca7fdd8f } ], ok: 1 }
    console.log(raw);
})
```
![image_1c3vl8f3c90r2np1lncamqg6d1t.png-11.1kB][5]


 **update()方法中的回调函数不能省略，否则数据不会被更新。如果回调函数里并没有什么有用的信息，则可以使用exec()简化代码**
``` js
temp.update({name:/aa/},{age: 0},{upsert:true}).exec();
```
### **updateMany()**
updateMany()与update()方法唯一的区别就是默认更新多个文档，即使设置{multi:false}也无法只更新第一个文档
``` js
Model.updateMany(conditions, doc, [options], [callback])
```
将数据库中名字中带有'huo'的数据，年龄变为50岁
``` js
temp.updateMany({name:/huo/},{age:50},function(err,raw){
    //{ n: 2, nModified: 2, ok: 1 }
    console.log(raw);
});
```
![image_1c3vlcq8crf714451mufup2s8k2a.png-7.2kB][6]


### **find() + save()**
如果需要更新的操作比较复杂，可以使用find()+save()方法来处理

找到年龄小于20岁的数据，名字后面添加'30'字符
``` js
temp.find({age:{$lt:20}},function(err,docs){
    //[ { _id: 5971f93be6f98ec60e3dc86d, name: 'wang', age: 10 },
    //{ _id: 5971f93be6f98ec60e3dc86f, name: 'li', age: 12 }]
    console.log(docs);
    docs.forEach(function(item,index,arr){
        item.name += '30';
        item.save();
    })
    //[ { _id: 5971f93be6f98ec60e3dc86d, name: 'wang30', age: 10 },
    // { _id: 5971f93be6f98ec60e3dc86f, name: 'li30', age: 12 }]
    console.log(docs);
});
```
### **updateOne()**
updateOne()方法只能更新找到的第一条数据，即使设置{multi:true}也无法同时更新多个文档

将数据库中名字中带有'huo'的数据，年龄变为60岁
``` js
temp.updateOne({name:/huo/},{age:60},function(err,raw){
    //{ n: 1, nModified: 1, ok: 1 }
    console.log(raw);
});
```
![image_1c3vloi85170s10ndml1jl5fj92n.png-7.2kB][7]


  [1]: http://static.zybuluo.com/wp0214/yiae4nuyebgafljdmqwd5aud/image_1c3vkaq1ui8k2k015orkjt1ia79.png
  [2]: http://static.zybuluo.com/wp0214/qyfdsm8pkaf13ljhoqvdjpo7/image_1c3vkdd629khvjlnn11cv5inem.png
  [3]: http://static.zybuluo.com/wp0214/hbqtyngmqo2qsyx1lr7n4muk/image_1c3vkrfahu171gp9u8pmms16i713.png
  [4]: http://static.zybuluo.com/wp0214/s6ieypnrxz0zlsk1f77u85l2/image_1c3vl875k1kpo138s1ikh7ce1flu1g.png
  [5]: http://static.zybuluo.com/wp0214/52zgf0pg756qjuntehu1azb7/image_1c3vl8f3c90r2np1lncamqg6d1t.png
  [6]: http://static.zybuluo.com/wp0214/ce3gpjsnaw3ujir5xnphygfa/image_1c3vlcq8crf714451mufup2s8k2a.png
  [7]: http://static.zybuluo.com/wp0214/ly52z99mrfkjsdgunf7a4ydm/image_1c3vloi85170s10ndml1jl5fj92n.png

### **findOne() + save()**
如果需要更新的操作比较复杂，可以使用findOne()+save()方法来处理

找到名字为'huochai'的数据，年龄加100岁
``` js
temp.findOne({name:'huochai'},function(err,doc){
    //{ _id: 5971f93be6f98ec60e3dc86c, name: 'huochai', age: 10 }
    console.log(doc);
    doc.age += 100;
    doc.save();
    //{ _id: 5971f93be6f98ec60e3dc86c, name: 'huochai', age: 110 }
    console.log(doc);
})
```

## 文档删除


常用的文档删除方法

- remove()
- findOneAndRemove()
- findByIdAndRemove()

### **remove()**
remove有两种形式，一种是文档的remove()方法，一种是Model的remove()方法

下面介绍Model的remove()方法，该方法的第一个参数conditions为查询条件，第二个参数回调函数的形式如下function(err){}
```
model.remove(conditions, [callback])
```
temp中数据：
![image_1c3vm1q1v104j1uetd63gk71l7j9.png-7.3kB][1]


删除数据库中名称包括'30'的数据
```
temp.remove({name:/30/},function(err){})
```
结果：
![image_1c3vm32b1p0iqbo10s6h4obbvm.png-4.1kB][2]


**[注意]remove()方法中的回调函数不能省略，否则数据不会被删除。当然，可以使用exec()方法来简写代码**
```
temp.remove({name:/30/}).exec()
```
下面介绍文档的remove()方法，该方法的参数回调函数的形式如下function(err,doc){}
```
document.remove([callback])
```
删除数据库中名称包含'huo'的数据

[注意]文档的remove()方法的回调函数参数可以省略
```
temp.find({name:/huo/},function(err,doc){
    doc.forEach(function(item,index,arr){
        item.remove(function(err,doc){
            //{ _id: 5971f93be6f98ec60e3dc86c, name: 'huochai', age: 30 }
            //{ _id: 5971f93be6f98ec60e3dc86e, name: 'huo', age: 60 }
            console.log(doc);
        })
    })
})  
```

### **findOneAndRemove()**
model的remove()会删除符合条件的所有数据，如果只删除符合条件的第一条数据，则可以使用model的findOneAndRemove()方法
```
Model.findOneAndRemove(conditions, [options], [callback])
```
集合temps现有数据如下
![image_1c3vmkjud1fj415l43a7hvk19ju13.png-7.3kB][3]


现在删除第一个年龄小于20的数据
```
temp.findOneAndRemove({age:{$lt:20}},function(err,doc){
    //{ _id: 5972d3f3e6f98ec60e3dc873, name: 'wang', age: 18 }
    console.log(doc);
})
```
![image_1c3vmlltq1dfl17771hqg1tfn1dr51g.png-5.5kB][4]


与model的remove()方法相同，回调函数不能省略，否则数据不会被删除。当然，可以使用exec()方法来简写代码
```
temp.findOneAndRemove({age:{$lt:20}}).exec()
```
![image_1c3vmo3ff1quqoei1d8uq1r79e1t.png-7.5kB][5]


删除第0个元素
```
var aIDArr = [];
temp.find(function(err,docs){
    docs.forEach(function(item,index,arr){
        aIDArr.push(item._id);
    })
    temp.findByIdAndRemove(aIDArr[0],function(err,doc){
        //{ _id: 5972d754e6f98ec60e3dc882, name: 'huochai', age: 27 }
        console.log(doc);
    })            
})
```
![image_1c3vmpbqi6cnm9j14r2e561ouv2a.png-5.6kB][6]


  [1]: http://static.zybuluo.com/wp0214/clfej1gexkm3297leavihdac/image_1c3vm1q1v104j1uetd63gk71l7j9.png
  [2]: http://static.zybuluo.com/wp0214/ghjcd917bnm946y596949umv/image_1c3vm32b1p0iqbo10s6h4obbvm.png
  [3]: http://static.zybuluo.com/wp0214/fqc12wmxatb3q9qatthaorih/image_1c3vmkjud1fj415l43a7hvk19ju13.png
  [4]: http://static.zybuluo.com/wp0214/wb7qvbrpik9mj32d4qyrano0/image_1c3vmlltq1dfl17771hqg1tfn1dr51g.png
  [5]: http://static.zybuluo.com/wp0214/urjp1jifc4xfsax54q1369w2/image_1c3vmo3ff1quqoei1d8uq1r79e1t.png
  [6]: http://static.zybuluo.com/wp0214/0d33lh1k8apddbuw6r908cww/image_1c3vmpbqi6cnm9j14r2e561ouv2a.png

## 前后钩子pre()和post()


前后钩子即pre()和post()方法，又称为中间件，是在执行某些操作时可以执行的函数。
中间件在schema上指定，类似于静态方法或实例方法等

可以在数据库执行下列操作时，设置前后钩子
```
init
validate
save
remove
count
find
findOne
findOneAndRemove
findOneAndUpdate
insertMany
update

```
### **pre()**

以find()方法为例，在执行find()方法之前，执行pre()方法
```
var schema = new mongoose.Schema({ 
    age:Number,
    name: String,
    x:Number,
    y:Number
    });  
    
schema.pre('find',function(next){
    console.log('我是pre方法1');
    next();
});
schema.pre('find',function(next){
    console.log('我是pre方法2');
    next();
});  
var temp = mongoose.model('temp', schema);
temp.find(function(err,docs){
    console.log(docs[0]);
})    

/*
我是pre方法1
我是pre方法2
{ _id: 5972ed35e6f98ec60e3dc886,name: 'huochai',age: 27,x: 1,y: 2 }
*/
```

### **post()**
post()方法并不是在执行某些操作后再去执行的方法，而在执行某些操作前最后执行的方法，post()方法里不可以使用next()
```
var schema = new mongoose.Schema({
    age:Number, 
    name: String,
    x:Number,
    y:Number
    });  
    
schema.post('find',function(docs){
    console.log('我是post方法1');
});
schema.post('find',function(docs){
    console.log('我是post方法2');
});
var temp = mongoose.model('temp', schema);
temp.find(function(err,docs){
    console.log(docs[0]);
}) 

/*
我是post方法1
我是post方法2
{ _id: 5972ed35e6f98ec60e3dc886,name: 'huochai',age: 27,x: 1,y: 2 }
 */   
```
## 文档查询后处理

常用的查询后处理的方法如下所示：

```
sort     排序
skip     跳过
limit    限制
select   显示字段
exect    执行
count    计数
distinct 去重
```

当前数据
```
var schema = new mongoose.Schema({ 
    age:Number, 
    name: String,
    x:Number,
    y:Number
    });  
    
var temp = mongoose.model('temp', schema);

temp.find(function(err,docs){
    //[ { _id: 5972ed35e6f98ec60e3dc886,name: 'huochai',age: 27,x: 1,y: 2 },
    //{ _id: 5972ed35e6f98ec60e3dc887,name: 'wang',age: 18,x: 1,y: 1 },
    //{ _id: 5972ed35e6f98ec60e3dc888, name: 'huo', age: 30, x: 2, y: 1 },
    //{ _id: 5972ed35e6f98ec60e3dc889, name: 'li', age: 20, x: 2, y: 2 } ]
    console.log(docs);
}) 
```

### **sort()**
sort函数可以将查询结果数据进行排序操作，该函数的参数是一个或多个键/值对，键代表要排序的键名，值代表排序的方向，1是升序，-1是降序或者值的正负号。

按age从小到大排序
```
temp.find().sort("age").exec(function(err,docs){
    //[ { _id: 5972ed35e6f98ec60e3dc887,name: 'wang',age: 18,x: 1,y: 1 },
    //{ _id: 5972ed35e6f98ec60e3dc889, name: 'li', age: 20, x: 2, y: 2 },
    //{ _id: 5972ed35e6f98ec60e3dc886,name: 'huochai',age: 27,x: 1,y: 2 },
    //{ _id: 5972ed35e6f98ec60e3dc888, name: 'huo', age: 30, x: 2, y: 1 } ]
    console.log(docs);
});
```
按x从小到大，age从大到小排列
```
temp.find().sort("x -age").exec(function(err,docs){
    //[ { _id: 5972ed35e6f98ec60e3dc886,name: 'huochai',age: 27,x: 1,y: 2 },
    //{  _id: 5972ed35e6f98ec60e3dc887,name: 'wang',age: 18,x: 1,y: 1 },
    //{ _id: 5972ed35e6f98ec60e3dc888, name: 'huo', age: 30, x: 2, y: 1 },
    //{ _id: 5972ed35e6f98ec60e3dc889, name: 'li', age: 20, x: 2, y: 2 } ]
    console.log(docs);
}); 
```

结果排序：find(Conditions,fields,options,callback);
```
temp.find({},null,{sort:{age:-1}},function(err,docs){
  //查询所有数据，并按照age降序顺序返回数据docs
});
```

### **skip()**
skip函数的功能是略过指定数量的匹配结果，返回余下的查询结果

跳过1个，显示其他
```
temp.find().skip(1).exec(function(err,docs){
    //[ { _id: 5972ed35e6f98ec60e3dc887,name: 'wang',age: 18,x: 1,y: 1 },
    //{ _id: 5972ed35e6f98ec60e3dc888, name: 'huo', age: 30, x: 2, y: 1 },
    //{ _id: 5972ed35e6f98ec60e3dc889, name: 'li', age: 20, x: 2, y: 2 } ]
    console.log(docs);
}); 
```
### **limit()**
在查询操作中，有时数据量会很大，这时我们就需要对返回结果的数量进行限制，那么我们就可以使用limit函数，通过它来限制结果数量

显示2个
```
temp.find().limit(2).exec(function(err,docs){
    //[ { _id: 5972ed35e6f98ec60e3dc886,name: 'huochai',age: 27,x: 1,y: 2 },
    //{ _id: 5972ed35e6f98ec60e3dc887,name: 'wang',age: 18,x: 1,y: 1 } ]
    console.log(docs);
}); 
```
### **select()**

对返回的结果进行选择显示

显示name、age字段，不显示_id字段
```
temp.find().select("name age -_id").exec(function(err,docs){
    //[ { name: 'huochai', age: 27 },{ name: 'wang', age: 18 },{ name: 'huo', age: 30 },{ name: 'li', age: 20 } ]
    console.log(docs);
}); 
```
或者
```
temp.find().select({name:1, age:1, _id:0}).exec(function(err,docs){
    //[ { name: 'huochai', age: 27 },{ name: 'wang', age: 18 },{ name: 'huo', age: 30 },{ name: 'li', age: 20 } ]
    console.log(docs);
}); 
```
### **综合栗子**
下面将以上方法结合起来使用，
跳过第1个后，只显示2个数据，按照age由大到小排序，且不显示_id字段
```
temp.find().skip(1).limit(2).sort("-age").select("-_id").exec(function(err,docs){
    //[ { name: 'huochai', age: 27, x: 1, y: 2 },
    //{ name: 'li', age: 20, x: 2, y: 2 } ]
    console.log(docs);
}); 
```

### **count()**
显示集合temps中的文档数量

```
temp.find().count(function(err,count){
    console.log(count);//4
});
```
### **distinct()**
返回集合temps中的x的值

```
temp.find().distinct('x',function(err,distinct){
    console.log(distinct);//[ 1, 2 ]
}); 
```

## 文档验证
为什么需要文档验证呢？
以一个例子作为说明，schema进行如下定义
``` javascript
var schema = new mongoose.Schema({ 
    age:Number,
    name: String,
    x:Number,
    y:Number
});  

```
如果不进行文档验证，保存文档时，就可以不按照Schema设置的字段进行设置，分为以下几种情况

**1、缺少字段的文档可以保存成功**
```
var temp = mongoose.model('temp', schema);
new temp({age:10}).save(function(err,doc){
    //{ __v: 0, age: 10, _id: 597304442b70086a1ce3cf05 }
    console.log(doc);
}); 
```
**2、包含未设置的字段的文档也可以保存成功，未设置的字段不被保存**
```
new temp({age:100,abc:"abc"}).save(function(err,doc){
    //{ __v: 0, age: 100, _id: 5973046a2bb57565b474f48b }
    console.log(doc);
}); 
```
**3、包含字段类型与设置不同的字段的文档也可以保存成功，不同字段类型的字段被保存为设置的字段类型**
```
new temp({age:true,name:10}).save(function(err,doc){
    //{ __v: 0, age: 1, name: '10', _id: 597304f7a926033060255366 }
    console.log(doc);
}); 
```
而通过文档验证，就可以避免以下几种情况发生

文档验证在SchemaType中定义，格式如下
```
{name: {type:String, validator:value}}
```
常用验证包括以下几种
```
required: 数据必须填写
default: 默认值
validate: 自定义匹配
min: 最小值(只适用于数字)
max: 最大值(只适用于数字)
match: 正则匹配(只适用于字符串)
enum:  枚举匹配(只适用于字符串)
```
### **required**
必填项

将age设置为必填字段，如果没有age字段，文档将不被保存，且出现错误提示
```
var schema = new mongoose.Schema({ 
    age:{
        type:Number,
        required:true
        },
        name: String,
        x:Number,
        y:Number
    });  
var temp = mongoose.model('temp', schema);
new temp({name:"abc"}).save(function(err,doc){
    //Path `age` is required.
    console.log(err.errors['age'].message);
}); 
```

### **default**
默认值

设置age字段的默认值为18，如果不设置age字段，则会取默认值
```
var schema = new mongoose.Schema({ 
    age:{type:Number,default:18},
    name:String,
    x:Number,
    y:Number
});  
var temp = mongoose.model('temp', schema);
new temp({name:'a'}).save(function(err,doc){
    //{ __v: 0, name: 'a', _id: 59730d2e7a751d81582210c1, age: 18 }
    console.log(doc);
}); 
```

### **min | max**

将age的取值范围设置为[0,10]。如果age取值为20，文档将不被保存，且出现错误提示
```
var schema = new mongoose.Schema({ 
        age:{type:Number,min:0,max:10},
        name: String,
        x:Number,
        y:Number
    });  
var temp = mongoose.model('temp', schema);
new temp({age:20}).save(function(err,doc){
    //Path `age` (20) is more than maximum allowed value (10).
    console.log(err.errors['age'].message);
}); 
```

### **match**

将name的match设置为必须存在'a'字符。如果name不存在'a'，文档将不被保存，且出现错误提示
```
var schema = new mongoose.Schema({ age:Number, name:{type:String,match:/a/},x:Number,y:Number});  
var temp = mongoose.model('temp', schema);
new temp({name:'bbb'}).save(function(err,doc){
    //Path `name` is invalid (bbb).
    console.log(err.errors['name'].message);
}); 
```

### **validate**

validate实际上是一个函数，函数的参数代表当前字段，返回true表示通过验证，返回false表示未通过验证。利用validate可以自定义任何条件。比如，定义名字name的长度必须在4个字符以上
```
ar validateLength = function(arg){
    if(arg.length > 4){
        return true;
    }
    return false;
};
var schema = new mongoose.Schema({ name:{type:String,validate:validateLength}, age:Number,x:Number,y:Number});  
var temp = mongoose.model('temp', schema);
new temp({name:'abc'}).save(function(err,doc){
    //Validator failed for path `name` with value `abc`
    console.log(err.errors['name'].message);
}); 
```
