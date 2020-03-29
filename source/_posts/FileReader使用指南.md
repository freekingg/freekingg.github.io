---
title: FileReader使用指南
date: 2016-06-30 14:42:59
tags:
- html
- javascript
- 知识点
categories:
- 前端
---
## FileReader
FileReader用来把文件读入内存，并且读取文件中的数据。其接口提供了一个异步API，使用该API可以在浏览器主线程中异步访问文件系统，读取文件中的数据。到目前文职，只有FF3.6+和Chrome6.0+实现了FileReader接口。

**接口方法:**

|方法|	参数|	描述|
|---|---|---|
|readAsText|	file|	将文件读取为文本
|readAsDataURL|	file|	将文件读取为DataURL
|readAsBinaryString|	file|	将文件读取为二进制编码
|readAsArrayBuffer|	file|	将文件读取为ArrayBuffer
|abort|	none|	终止读取操作

<!-- more -->

**接口事件**

|事件|	描述|
|--|--|
|onabort|	中断|
|onerror|	出错|
|onloadstart|	开始|
|onprogress|	正在读取|
|onload|	成功读取|
|onloadend|	读取完成，无论成功失败|

**完整示例**
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>FileReader</title>
</head>
<body>
    
    <input type="file" multiple="multiple">
    <button type="button">获取</button>

    <script>
    // 具体脚本
    </script>
</body>
</html>

```
具体脚本
``` javascript
var $ = document.querySelectorAll.bind(document),
    log = console.log.bind(console);

$('button')[0].addEventListener('click', handle);

function handle() {
    var files = $('input')[0].files,
        len = files.length,
        file;
    if(len > 0) { // 如果有选中图片
        for(var i=0; i<len; i++) {
            file = files[i];
            if(/image\/[jpeg,gif,jpg,png]/.test(file.type)) { // 如果是图片
                log(file);
                // readAsText(file);
                // readAsDataURL(file);
                // readAsBinaryString(file);
                readAsArrayBuffer(file);
                // createObjectURL(file);
            }
        }
    } else {
        log('请选择图片!');
    }
}

// 将文件以文本形式读入页面
function readAsText(file) {
    var fileReader = new FileReader();  
    fileReader.readAsText(file);
    fileReader.onload = function(e) {
        var p = document.createElement('p');
        p.textContent = e.currentTarget.result;
        $('body')[0].appendChild(p);
    }
}

// 将文件以Data URL形式读入页面
function readAsDataURL(file) {
    var fileReader = new FileReader();  
    fileReader.readAsDataURL(file);
    log(fileReader)
    fileReader.onload = function(e) {
        var img = document.createElement('img');
        img.src = e.currentTarget.result;
        img.onload = function() {
            $('body')[0].appendChild(img);
        }
    }
}

// 将文件以二进制形式读入页面
function readAsBinaryString(file) {
    var fileReader = new FileReader();  
    fileReader.readAsBinaryString(file);
    fileReader.onload = function(e) {
        var p = document.createElement('p');
        p.textContent = e.currentTarget.result;
        $('body')[0].appendChild(p);
    }    
}

// 将文件以Array Buffer形式读入页面
function readAsArrayBuffer(file) {
    var fileReader = new FileReader();  
    fileReader.readAsArrayBuffer(file);
    fileReader.onload = function(e) {
        log(e.currentTarget.result);
    } 
}

// 创建对象URL
function createObjectURL(file) {
    var img = document.createElement('img');
    img.onload = function() {
        window.URL.revokeObjectURL(img.src); // revokeObjectURL() 静态方法用来释放一个之前通过调用 URL.createObjectURL() 创建的已经存在的 URL 对象
    }
    img.src = window.URL.createObjectURL(file);
    $('body')[0].appendChild(img);
}
```
