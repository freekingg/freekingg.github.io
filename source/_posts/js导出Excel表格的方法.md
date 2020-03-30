---
title: js导出Excel表格的方法
date: 2019-02-30 11:08:00
tags:
- 前端
- 技巧
- 下载
categories:
- [前端]
- [javascript]
---

js导出Excel的方法

- 利用html的table表格的格式书写想要的excel格式
- 获取table的内容并组装成一个xls格式的字符串
- 利用Blob对象生成一个xls格式的文件
- 利用a标签的download属性创建文件名，并下载到本地

<!-- more -->

**table表格内容**

先写一个正常的html表格

``` css
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <style media="screen">
    .tableA {
      border-collapse: collapse;
    }
    .tableA .title th{
      height: 50px;
      font-size: 24px;
      font-family: '微软雅黑';
      font-weight: 700;
    }
    .tableA tr th {
      border: 1px #000 solid;
      height: 40px;
      background: #efefef;
    }
    .tableA tr td {
      padding: 0 40px;
      border: 1px #000 solid;
      height: 40px;
      text-align: center;
    }
    .tableA .footer td {
      font-size: 20px;
      font-weight: 700;
    }
  </style>
</head>
<body>
  <table bordercolor="black" class="tableA">
    <tr class="title">
      <th colspan="4">学生信息</th>
    </tr>
    <tr>
      <th>名字</th>
      <th>性别</th>
      <th>年龄</th>
      <th>班级</th>
    </tr>
    <tr>
      <td>小明</td>
      <td>男</td>
      <td>19</td>
      <td>1班</td>
    </tr>
    <tr>
      <td>小黄</td>
      <td>男</td>
      <td>20</td>
      <td>2班</td>
    </tr>
    <tr>
      <td>老王</td>
      <td>男</td>
      <td>29</td>
      <td>3班</td>
    </tr>
    <tr class="footer">
      <td colspan="4">总人数：3人</td>
    </tr>
  </table>
</body>
</html>
```

**获取table的内容装成一个xls格式的字符串**
接下来就是获取table的html内容了，里面包括标签的class或id等。

``` js
var oHtml = document.getElementsByClassName('tableA')[0].outerHTML;

```

将table和style组成一个html，
``` js
var excelHtml = `
    <html>
      <head>
        <meta charset='utf-8' />
        <style>
          .tableA {
            border-collapse: collapse;
          }
          .tableA .title th{
            height: 50px;
            font-size: 24px;
            font-family: '微软雅黑';
            font-weight: 700;
          }
          .tableA tr th {
            border: 1px #000 solid;
            height: 40px;
            background: #efefef;
          }
          .tableA tr td {
            padding: 0 40px;
            border: 1px #000 solid;
            height: 40px;
			text-align: center;
          }
          .tableA .footer td {
            font-size: 20px;
            font-weight: 700;
          }
        </style>
      </head>
      <body>
        ${oHtml}
      </body>
    </html>
  `;
  ```

**生成xls文件并通过a标签下载到本地**
前面的准备工作就差不多了，接下来就是将字符串转成xls文件了，这里主要利用Blob对象和`URL.createObjectURL()` 方法

- Blob对象表示不可变的类似文件对象的原始数据。Blob表示不一定是JavaScript原生形式的数据。 File 接口基于Blob，继承了 blob的功能并将其扩展使其支持用户系统上的文件。

- URL.createObjectURL() 静态方法会创建一个 DOMString，其中包含一个表示参数中给出的对象的URL。这个 URL 的生命周期和创建它的窗口中的 document 绑定。这个新的URL 对象表示指定的 File 对象或 Blob 对象。

Blob 构造函数用法举例（生成一个json文件）：

``` js
var debug = {hello: "world"};
var blob = new Blob([JSON.stringify(debug, null, 2)],{type : 'application/json'});
```
同样道理利用第二个步骤的字符串生成Excel
``` js
var excelBlob = new Blob([excelHtml], {type: 'application/vnd.ms-excel'})
```

**最后一步就是通过a标签下载到本地了，下载前可以利用a标签的download属性命名**
``` js
// 创建一个a标签
var oA = document.createElement('a');

// 利用URL.createObjectURL()方法为a元素生成blob URL
oA.href = URL.createObjectURL(excelBlob);

// 给文件命名
oA.download = '学生名单.xls';

// 模拟点击
oA.click();
```

> ps:因为权限问题，生成的excel的格式只能为.xls而且每次打开都会弹窗询问。所以建议打开后另存一份excel

