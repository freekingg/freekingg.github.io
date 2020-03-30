---
title: mongoDB入门
date: 2020-04-30 17:24:34
tags:
- mongodb
- 数据库
categories:
- 数据库
---
##MongoDB介绍
- MongoDB是一个NoSQL的数据库
- MongoDB是一款文档型数据库
- 数据库指的就是一个存储数据的仓库
	数据库可以使我们完成对数据的持久化的操作
- MongoDB数据库中存储的数据的基本单位就是文档，
	MongoDB中存储的就是文档，所谓文档其实就是一个“JSON”
- MongoDB中的“JSON”我们称为BSON，比普通的JSON的功能要更加的强大
- MongoDB数据库使用的是JavaScript进行操作的，在MongoDB含有一个对ES标准实现的引擎，
	在MongoDB中所有ES中的语法中都可以使用
- MongoDB的基本的指令
	- 启动服务器
		mongod --dbpath 路径 --port 端口号
	- 启动客户端
		mongo

## 安装MongoDB
- 安装
- 配置环境变量
    - C:\Program Files\MongoDB\Server\3.2\bin
- 在c盘根目录
    - 创建一个文件夹 data
    - 在data中创建一个文件夹db
- 启动mongodb服务器
    - 打开cmd命令行窗口
    - 输入 `mongod` 启动mongodb服务器
        - [ 参数 ] `mongod --dbpath 数据库路径 --port 端口号`
    - 在打开一个cmd窗口
    - 输入 mongo 连接mongodb ，出现 >
    - 成功

- 数据库（database）
	- 数据库的服务器
		- 服务器用来保存数据
		- mongod 用来启动服务器
			
	- 数据库的客户端
		- 客户端用来操作服务器，对数据进行增删改查的操作
		- mongo 用来启动客户端
		
- 将MongoDB设置为系统服务，可以自动在后台启动，不需要每次都手动启动
	- 1.在c盘根目录创建data
		- 在data下创建db和log文件夹
	- 2 .创建配置文件
		在目录 C:\Program Files\MongoDB\Server\3.2 下添加一个配置文件
		mongod.cfg
	- 3 .以管理员的身份打开命令行窗口	
	
	- 4.执行如下的命令
		`sc.exe create MongoDB binPath= "\"C:\Program Files\MongoDB\Server\3.2\bin\mongod.exe\" --service --config=\"C:\Program Files\MongoDB\Server\3.2\mongod.cfg\"" DisplayName= "MongoDB" start= "auto"`
		
	`	sc.exe create MongoDB binPath= "\"mongod的bin目录\mongod.exe\" --service --config=\"mongo的安装目录\mongod.cfg\"" DisplayName= "MongoDB" start= "auto"`
		
	- 5.启动mongodb服务

	- 6.如果启动失败，证明上边的操作有误，
		在控制台输入 sc delete MongoDB 删除之前配置的服务
		然后从第一步再来一次

- 基本概念
	- 数据库（database）
	- 集合（collection）
	- 文档（document）
		- 在MongoDB中，数据库和集合都不需要手动创建，
			当我们创建文档时，如果文档所在的集合或数据库不存在会自动创建数据库和集合
## MongoDB的CRUD的操作
**基本指令**

- show dbs
show databases
    - 显示当前的所有数据库

- use 数据库名
    - 进入到指定的数据库中

- db
    - db表示的是当前所处的数据库
- show collections
    - 显示数据库中所有的集合

### 向数据库中插入文档
``` shell
db.collection.insert()
    - insert()可以向集合中插入一个或多个文档
        - 当我们向集合中插入文档时，如果没有给文档指定_id属性，则数据库会自动为文档添加_id
            该属性用来作为文档的唯一标识
        - _id我们可以自己指定，如果我们指定了数据库就不会在添加了，如果自己指定_id 也必须确保它的唯一性
    `例：`
    db.stus.insert([
     {name:"沙和尚",age:38,gender:"男"},
     {name:"白骨精",age:16,gender:"女"},
     {name:"蜘蛛精",age:14,gender:"女"}
    ]);
```
``` shell
db.collection.insertOne()
    - insert()向集合中插入一个文档
```
``` shell
db.collection.insertMany(
    - insert()向集合中插入多个文档)
```

### 查询数据库中的文档
``` shell
db.collection.find()
    - 可以根据指定条件从集合中查询所有符合条件的文档
	- 返回的是一个数组
	`例子:`
	db.stus.find({_id:"hello"});
    db.stus.find({age:16 , name:"白骨精"});
    db.stus.find({age:28});
    db.stus.find({}).count() 查询所有结果的数量
        
```
``` shell
db.collection.find()
	- 可以根据指定条件从集合中查询所有符合条件的文档
	- 返回的是一个数组
```
``` shell
db.collection.findOne()
	- 查询第一个符合条件的文档
	- 返回的是一个对象
```
``` shell
db.collection.find().count()
	- 查询符合条件的文档的数量
```
### 修改数据库中的文档
``` shell
db.collection.update(查询条件,新对象)
	- 可以修改、替换集合中的一个或多个文档
	- update()默认情况下会使用新对象来替换旧的对象
        - 如果需要修改指定的属性，而不是替换需要使用“修改操作符”来完成修改
            $set 可以用来修改文档中的指定属性
            $unset 可以用来删除文档的指定属性
        - update()默认只会修改一个
        `例子：`
        1、db.stus.update({name:"沙和尚"},{age:28});
        2、db.stus.update(
            {"_id" : ObjectId("59c219689410bc1dbecc0709")},
            {$set:{
                gender:"男",
                address:"流沙河"
            }}    
        )
        3、db.stus.update(
            {"_id" : ObjectId("59c219689410bc1dbecc0709")},
            {$unset:{
                address:1
            }}    
        )
```
``` shell
db.collection.updateOne()
	- 修改集合中的一个文档
```
``` shell
db.collection.updateMany()
	- 修改集合中的多个文档
```
``` shell
db.collection.replaceOne()
	- 替换集合中的一个文档
```
### 删除集合中的文档
``` shell
db.collection.remove()
	- 删除集合中的一个或多个文档（默认删除多个）
```
``` shell
db.collection.deleteOne()
	- 删除集合中的一个文档
```
``` shell
db.collection.deleteMany()
	- 删除集合中的多个文档
```
``` shell
清空一个集合
	- db.collection.remove({})
```
``` shell
删除一个集合
	- db.collection.drop()
```
``` shell
删除一个数据库
	- db.dropDatabase()
```
## 练习
``` shell
//1.进入my_test数据库
use my_test

//2.向数据库的user集合中插入一个文档  
db.users.insert({
    username:"sunwukong"
});

//3.查询user集合中的文档
db.users.find();

//4.向数据库的user集合中插入一个文档   
db.users.insert({
    username:"zhubajie"
});
   
//5.查询数据库user集合中的文档
db.users.find();

//6.统计数据库user集合中的文档数量
db.users.find().count();

//7.查询数据库user集合中username为sunwukong的文档
db.users.find({username:"sunwukong"});

//8.向数据库user集合中的username为sunwukong的文档，添加一个address属性，属性值为huaguoshan
db.users.update({username:"sunwukong"},{$set:{address:"huaguoshan"}});


//9.使用{username:"tangseng"} 替换 username 为 zhubajie的文档
db.users.replaceOne({username:"zhubajie"},{username:"tangseng"});    
    
//10.删除username为sunwukong的文档的address属性
db.users.update({username:"sunwukong"},{$unset:{address:1}});


//11.向username为sunwukong的文档中，添加一个hobby:{cities:["beijing","shanghai","shenzhen"] , movies:["sanguo","hero"]}
//MongoDB的文档的属性值也可以是一个文档，当一个文档的属性值是一个文档时，我们称这个文档叫做 内嵌文档
db.users.update({username:"sunwukong"},{$set:{hobby:{cities:["beijing","shanghai","shenzhen"] , movies:["sanguo","hero"]}}});
db.users.find();

//12.向username为tangseng的文档中，添加一个hobby:{movies:["A Chinese Odyssey","King of comedy"]}
db.users.update({username:"tangseng"},{$set:{hobby:{movies:["A Chinese Odyssey","King of comedy"]}}})

//13.查询喜欢电影hero的文档
//MongoDB支持直接通过内嵌文档的属性进行查询，如果要查询内嵌文档则可以通过.的形式来匹配
//如果要通过内嵌文档来对文档进行查询，此时属性名必须使用引号 
db.users.find({'hobby.movies':"hero"});

//14.向tangseng中添加一个新的电影Interstellar
//$push 用于向数组中添加一个新的元素
//$addToSet 向数组中添加一个新元素 ， 如果数组中已经存在了该元素，则不会添加
db.users.update({username:"tangseng"},{$push:{"hobby.movies":"Interstellar"}});
db.users.update({username:"tangseng"},{$addToSet:{"hobby.movies":"Interstellar"}});
db.users.find();

//15.删除喜欢beijing的用户
db.users.remove({"hobby.cities":"beijing"});

//16.删除user集合
db.users.remove({});
db.users.drop();

show dbs;

//17.向numbers中插入20000条数据 7.2s
for(var i=1 ; i<=20000 ; i++){
    db.numbers.insert({num:i});
}

db.numbers.find()

db.numbers.remove({});


//0.4s
var arr = [];

for(var i=1 ; i<=20000 ; i++){
    arr.push({num:i});
}

db.numbers.insert(arr);

//18.查询numbers中num为500的文档
db.numbers.find({num:500})

//19.查询numbers中num大于5000的文档
db.numbers.find({num:{$gt:500}});
db.numbers.find({num:{$eq:500}});

//20.查询numbers中num小于30的文档
db.numbers.find({num:{$lt:30}});

//21.查询numbers中num大于40小于50的文档
db.numbers.find({num:{$gt:40 , $lt:50}});

//22.查询numbers中num大于19996的文档
db.numbers.find({num:{$gt:19996}});

//23.查看numbers集合中的前10条数据
db.numbers.find({num:{$lte:10}});

//limit()设置显示数据的上限
db.numbers.find().limit(10);
//在开发时，我们绝对不会执行不带条件的查询
db.numbers.find();

//24.查看numbers集合中的第11条到20条数据
/*
    分页 每页显示10条
        1-10     0
        11-20    10
        21-30    20
        。。。
        
        skip((页码-1) * 每页显示的条数).limit(每页显示的条数);
        
    skip()用于跳过指定数量的数据    
    
    MongoDB会自动调整skip和limit的位置
*/
db.numbers.find().skip(10).limit(10);

//25.查看numbers集合中的第21条到30条数据
db.numbers.find().skip(20).limit(10);

db.numbers.find().limit(10).skip(10);

//26.将dept和emp集合导入到数据库中
db.dept.find()
db.emp.find()

//27.查询工资小于2000的员工
db.emp.find({sal:{$lt:2000}});

//28.查询工资在1000-2000之间的员工
db.emp.find({sal:{$lt:2000 , $gt:1000}});

//29.查询工资小于1000或大于2500的员工
db.emp.find({$or:[{sal:{$lt:1000}} , {sal:{$gt:2500}}]});

//30.查询财务部的所有员工
//(depno)
var depno = db.dept.findOne({dname:"财务部"}).deptno;
db.emp.find({depno:depno});

//31.查询销售部的所有员工
var depno = db.dept.findOne({dname:"销售部"}).deptno;
db.emp.find({depno:depno});

//32.查询所有mgr为7698的所有员工
db.emp.find({mgr:7698})

//33.为所有薪资低于1000的员工增加工资400元
db.emp.updateMany({sal:{$lte:1000}} , {$inc:{sal:400}});
db.emp.find()

```