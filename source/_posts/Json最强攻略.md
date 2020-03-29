---
title: Json最强攻略
date: 2020-03-30 16:25:19
tags:
- 知识点
- json
categories:
- 前端
---

## 一、 JSON是什么？
- JSON，全称“JavaScript Object Notation（JavaScript对象表示法）”，起源于JavaScript的对象和数组。JSON，说白了就是JavaScript用来处理数据的一种格式，这种格式非常简单易用。

- JSON，大部分都是用来处理JavaScript和web服务器端之间的数据交换，把后台web服务器的数据传递到前台，然后使用JavaScript进行处理，例如ajax等。

- JSON支持的语言非常多，包括JavaScript、C#、PHP、Java等等，这是由于JSON独立于语言的轻量级的数据交换格式，这个特点有点类似于SQL语言。

- JSON，就是一种数据传输格式而已，无它，非常容易掌握。但是大家不要误以为JSON最大的用处是在前端，其实JSON最大的用处是处理JavaScript和web服务器端之间数据交换。

<!-- more -->

## 二、JSON对象结构
>JSON结构共有2种：
（1）对象结构；
（2）数组结构；

### 1、对象结构
JSON，简单来说就是JavaScript中的对象或数组，所以这两种结构就是对象和数组。通过这两种结构就可以表示各种复杂的结构。

>对象结构是使用大括号“{}”括起来的，大括号内是由0个或多个用英文逗号分隔的“关键字:值”对（key:value）构成的。

`语法：`
``` javascript
var jsonObj =
{
    "键名1":值1,
    "键名2":值2,
    ……
    "键名n":值n
}
```
说明：
jsonObj指的是json对象。对象结构是以“{”开始，到“}”结束。其中“键名”和“值”之间用英文冒号构成对，两个“键名:值”之间用英文逗号分隔。

注意，这里的键名是字符串，但是值可以是数值、字符串、对象、数组或逻辑true和false。

`举例：`
``` javascript
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <title></title>
    <script type="text/javascript">
        var obj =
        {
            "name":"helicopter",
            "age":23,
            //JSON对象内部也有一个JSON对象
            hobby:
            {
                "first":"swimming",
                "second":"singing",
                "third":"dancing"
            }
        }
        //读取JSON数据
        document.write("名字是："+obj.name+"<br>");
        document.write("第一项爱好是："+obj.hobby.first);
    </script>
</head>
<body>
</body>
</html>
```
在浏览器预览效果如下：

![image_1c1ui3ugh1q3g1lr8118f1i121dd519.png-19.7kB][1]

  **1、从JSON中读数据**
 > 对于JSON对象结构，读取JSON非常简单，获取JSON中的数据共有2种方式。
 
``语法：``
``` javascript
	
jsonObj.key
jsonObj["key"]
```
`说明：`
jsonObj指的是JSON对象，key指的是键名。读取JSON数据使用的是“.”操作符，这个跟JavaScript对象读取属性值是差不多的。

实际例子请看上面举例。

**2、向JSON写数据**
>对于JSON对象结构，要往JSON中增加一条数据，也是使用“.”操作符。

`语法：`
``` javascript
jsonObj.key = 值;
jsonObj["key"] = 值;
```
`说明：`
jsonObj指的是JSON对象，key指的是键名。

`举例：`
``` javascript
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <title></title>
    <script type="text/javascript">
        var obj =
        {
            "name":"helicopter",
            "age":23,
            //JSON对象内部也有一个JSON对象
            hobby:
            {
                "first":"swimming",
                "second":"singing",
                "third":"dancing"
            }
        }
        obj.gender = "男";  //或者obj["gender"]="男";
        document.write("性别是："+obj.gender);
    </script>
</head>
<body>
</body>
</html>
```
在浏览器预览效果如下：

![在浏览器预览效果如下：][2]


 **3、修改JSON中的数据**
> 对于JSON对象结构，要修改JSON中的数据，也是使用“.”操作符。

`语法：`
``` javascript
jsonObj.key = 新值;
```
举例：
``` javascript
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <title></title>
    <script type="text/javascript">
        var obj =
        {
            "name":"helicopter",
            "age":23,
            "gender":"男",
            //JSON对象内部也有一个JSON对象
            hobby:
            {
                "first":"swimming",
                "second":"singing",
                "third":"dancing"
            }
        }
        obj.name = "小杰";  //或者obj["name"]="小杰";
        document.write("姓名是："+obj.name);
    </script>
</head>
<body>
</body>
</html>
```
在浏览器预览效果如下：
![image_1c1uid74elbi1s21nut1o8e1q6g23.png-15kB][3]


**4、删除JSON中的数据**
>对于JSON对象结构，我们使用delete关键字来删除JSON中的数据。

`语法：`
```delete  jsonObj.key;```

`说明：`
删除JSON中数据非常简单，只需要使用delete关键字即可。

`举例：`
``` javascript
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <title></title>
    <script type="text/javascript">
        var obj =
        {
            "name":"helicopter",
            "age":23,
            "gender":"男",
            //JSON对象内部也有一个JSON对象
            hobby:
            {
                "first":"swimming",
                "second":"singing",
                "third":"dancing"
            }
        }
        delete obj.age;
        if(obj.age==null)
        {
            console("JSON中的年龄项已经被删除！");
        }
    </script>
</head>
<body>
</body>
</html>
```

**5、遍历JSON对象**
>对于JSON对象结构，可以使用for…in…循环来遍历JSON对象中的数据。

`举例：`
``` javascript
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <title></title>
    <script type="text/javascript">
        var obj =
        {
            "name":"helicopter",
            "age":23,
            "gender":"男",
        }
        for(var c in obj)
        {
            if(c=="name")
            {
                document.write("姓名是："+obj[c]);
            }
        }
    </script>
</head>
<body>
</body>
</html>
```
在浏览器预览效果如下：
![image_1c1uimc401hkdk8a2qq14nn1ude2g.png-15.5kB][4]


### 2、数组结构
>JSON数组结构是用中括号“[]”括起来，中括号内部由0个或多个以英文逗号“,”分隔的值列表组成。

`语法：`
``` javascript
var arr =
[
    {
        "键名1":值1,
        "键名2":值2
    },
    {
        "键名3":值3,
        "键名4":值4
    },
    ……
]
```
`说明：`
arr指的是json数组。数组结构是以“[”开始，到“]”结束，这一点跟JSON对象不同。不过在JSON数组结构中，每一对“{}”相当于一个JSON对象，大家看看像不像？而且语法都非常类似。

注意，这里的键名是字符串，但是值可以是数值、字符串、对象、数组或逻辑true和false。

`举例：`
``` javascript
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <title></title>
    <script type="text/javascript">
        var arr =
        [
            {
                "name":"小明",
                "age":23,
                "gender":"男"
            },
            {
                "name":"小红",
                "age":21,
                "gender":"女"
            }
        ]
        //读取JSON数据
        document.write("第一个学生的名字是："+arr[0].name+"<br>");
        document.write("第二个学生的性别是："+arr[1].gender+"<br>");
    </script>
</head>
<body>
</body>
</html>
```
在浏览器预览效果如下：

![image_1c1uitq791nva1egmvbs1jqhnh2t.png-16.5kB][5]


JSON数组结构也是非常简单的，只需要通过数组下标来获取哪一个数组元素（一个“{}”的内容就类似一个数组元素），然后再配合使用“.”操作符就可以获取相应数组元素的内部数据。

对于获取、写入、修改、删除、遍历JSON数组结构中的数据，跟JSON对象结构的数据操作类似，大家对比琢磨一下基本就懂了，在此不再累赘讲解。

``` javascript
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <title></title>
    <script type="text/javascript">
        var arr =
        [
            {
                "name":"小明",
                "age":23,
                "gender":"男"
            },
            {
                "name":"小红",
                "age":21,
                "gender":"女"
            }
        ]
        delete arr[0];
        arr[1].name="小兰";
        document.write("第2个学生的姓名是："+arr[1].name);
    </script>
</head>
<body>
</body>
</html>
```

## 三、JSON对象和JSON字符串
**普通字符串、JSON字符串和JSON对象**
初学者经常有一个困惑，就是分不清普通字符串、JSON字符串和JSON对象。其实这3者是非常容易区分的。

1、普通字符串
    字符串嘛，大家都知道是使用单引号或双引号括起来的一串字符。
`举例：`
``` javascript
var str = "学习json教程";
```
2、JSON对象
JSON对象我们在“JSON对象结构”这一节已经详细为大家讲解了。JSON对象，指的是符合JSON格式要求的JavaScript对象。
`举例：`
``` javascript
var jsonObj = {"name":"helicopter","age":23,"gender":"男"};
```
3、JSON字符串
JSON字符串，指的是符合“JSON格式”的字符串。
JSON字符串要求两点：

（1）必须是字符串，也就是要用单引号或双引号括起来；

（2）必须符合“JSON”格式。
`举例：`
``` javascript
var jsonStr = '{"name": "helicopter", "age":23, "gender": "男"}';
```
JSON字符串说白了就是在JSON对象外面加一对单引号。

大家好好对比一下，就知道普通字符串、JSON对象和JSON字符串的区别了。

###JSON对象转换为JSON字符串
>在JSON中，可以使用JSON.stringify()方法将JSON对象转换为JSON字符串。

`语法：`
``` javascript
JSON.stringify(JSON对象名);
```
`举例：`
``` javascript
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <title></title>
    <script type="text/javascript">
        var obj =
        {
            "name":"helicopter",
            "age":23,
            //JSON对象内部也有一个JSON对象
            hobby:
            {
                "first":"swimming",
                "second":"singing",
                "third":"dancing"
            }
        }
        var jsonStr=JSON.stringify(obj);
        document.write(jsonStr);
    </script>
</head>
<body>
</body>
</html>
```
在浏览器预览效果如下：
![image_1c1ujb7d81fui9hhuk19sp89p3a.png-34.5kB][6]


  [1]: http://static.zybuluo.com/wp0214/28qkfqjnwfg8zsx8oq9k72x1/image_1c1ui3ugh1q3g1lr8118f1i121dd519.png
  [2]: http://static.zybuluo.com/wp0214/qef8saddse2xv8wbu98mefnu/image_1c1ui96k610351uq31ck91e3b1ts51m.png
  [3]: http://static.zybuluo.com/wp0214/kmgposdk24gulgwfq745g4mq/image_1c1uid74elbi1s21nut1o8e1q6g23.png
  [4]: http://static.zybuluo.com/wp0214/iy624bkzch2rlev5jrz60z6j/image_1c1uimc401hkdk8a2qq14nn1ude2g.png
  [5]: http://static.zybuluo.com/wp0214/v26g4p99j9m67jhbmaqh3xu6/image_1c1uitq791nva1egmvbs1jqhnh2t.png
  [6]: http://static.zybuluo.com/wp0214/8r04wc4v20xqb8p05yfnmzh4/image_1c1ujb7d81fui9hhuk19sp89p3a.png
  
### JSON字符串转换为JSON对象
 在JavaScript中，将JSON字符串转换为JSON对象非常有用。一般，在Web服务器后台向前台传输数据的过程中，往往都是用字符串形式来传输JSON数据。如果我们在前台想要获取JSON数据，就必须将JSON字符串转换为JSON对象才能操作。
>现在大多数浏览器（IE8及以上，Chrome和Firefox差不多全部）自带原生JSON对象，提供JSON.parse()方法来将JSON字符串转换为JSON对象。

`语法：`
``` javascript
JSON.parse(字符串名); 
```

`举例：`
``` javascript
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <title></title>
    <script type="text/javascript">
        var jsonStr = '{"name": "helicopter", "age":23, "gender": "男"}';
        var jsonObj=JSON.parse(jsonStr);
        document.write("姓名是："+jsonObj.name);
    </script>
</head>
<body>
</body>
</html>
```