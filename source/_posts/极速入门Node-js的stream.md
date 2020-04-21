---
title: 极速入门Node.js的stream
date: 2020-04-21 11:07:30
tags:
- nodejs
- stream
categories:
- [nodejs]
---

## 01 stream 的基本概念和常用 API 概述

- 什么是 stream
- 为何要使用 stream
- stream 流转的过程
- stream 的常见使用场景

### 什么是 stream
> 在编写代码时，我们应该有一些方法将程序像连接水管一样连接起来 – 当我们需要获取一些数据时，可以去通过"拧"其他的部分来达到目的。这也应该是IO应有的方式。 – Doug McIlroy. October 11, 1964

英文叫 stream 中文叫“流”，都能很形象的表述出它的本质 —— 就是让数据流动起来。

我们用桶和水来做比喻还算比较恰当（其实计算机中的概念，都是数学概念，都是抽象的，都无法完全用现实事务做比喻），如下图。数据从原来的 source 流向 dest ，要像水一样，慢慢的一点一点的通过一个管道流过去。给鱼缸换水、偷汽油，都能用得上。

<!-- more -->

![](https://raw.githubusercontent.com/RocWangPeng/king-static/master/20200421111143.png)

stream 并不是 node.js 独有的概念，而是一个操作系统最基本的操作方式，只不过 node.js 有 API 支持这种操作方式。linux 命令的 | 就是 stream ，因此所有 server 端语言都应该实现 stream 的 API 。

### 为何要使用 stream

暂不管编程的原因，先分析一下上图中换水的例子。如果没有中间的管道，而是直接抱起 source 水桶往 dest 水桶中倒，那肯定得需要一个力量特别大的人（或者多个人）才能完成。而有了这个管道，小孩子都可以很轻松的完成换水，而且管道粗细都可以最终完成，只不过是时间长短的问题。即，有管道换水需要的力量消耗非常少，不用管道换水消耗力量很大，这个应该很好理解。

其实这里所说的“力量”，对应到计算机编程中就是硬件的性能，这包括 CPU 的运算能力，内存的存储能力，硬盘和网络的读写速度（硬盘存储能力暂不考虑）。将上面倒水的例子对应到一个计算机的场景中，例如在线看电影，source 就是服务器端的视频，dest 就是你自己的播放器（或者浏览器中的 flash 和 h5 video）。到这里大家应该一下子能明白，目前看电影的方式就是如同用管道换水一样，一点一点的从服务端将视频流动到本地播放器，一边流动一边播放，最后流动完了也播放完了。

那播放视频为何要使用这种方式？解决这个问题不妨考虑反证法，即不用管道和流动的方式，先从服务端加载完视频文件，然后再播放。这样导致最直观的问题就是，需要加载很长一段时间才能播放视频。其实这仅仅的表面现象，还有可能是视频加载过程中，因内存占用太多而导致系统卡顿或者崩溃。因为我们的网速、内存、CPU 运算速度都是有限的（而且还要多个程序共享使用），这个视频文件可能有几个 G 那么大。

再说一个更加直观的例子，先看下面的这段代码。语法上并没有什么问题，但是如果 data.txt 文件非常大，在响应大量用户的并发请求时，程序可能会消耗大量的内存，这样很可能会造成用户连接缓慢的问题。而且，如果并发请求过大，服务器内存开销也很大。
``` js
var http = require('http');
var fs = require('fs');
var path = require('path');

var server = http.createServer(function (req, res) {
    var fileName = path.resolve(__dirname, 'data.txt');
    fs.readFile(fileName, function (err, data) {
        res.end(data);
    });
});
server.listen(8000);
```

要解决这个问题很简单 —— 用 stream ，代码改造如下。即并不是把文件全部读取了再返回，而是一边读取一边返回，一点一点的把数据流动到客户端。

``` js
var http = require('http');
var fs = require('fs');
var path = require('path');

var server = http.createServer(function (req, res) {
    var fileName = path.resolve(__dirname, 'data.txt');
    var stream = fs.createReadStream(fileName);  // 这一行有改动
    stream.pipe(res); // 这一行有改动
});
server.listen(8000);
```
最后总结一下，之所以用 stream ，是因为一次性读取、操作大文件，内存和网络是“吃不消”的，因此要让数据流动起来，一点一点的进行操作。这其实也符合算法中一个很重要的思想 —— 分而治之。

### stream 流转的过程

从管道换水的例子可以看出，stream 包括 source，dest，还有中间的管道，下面将通过这三个方面来介绍 stream 的过程。其中比较关键的 API 有：

- `data` 事件，用来监听 stream 数据的输入
- `end` 事件，用来监听 stream 数据输入完成
- `fs.createReadStream` 方法，返回一个文件读取的 stream 对象
- `fs.createWriteStream` 方法，返回一个文件写入的 stream 对象
- `pipe` 方法，用来做数据流转

这些 API 下文都会有介绍和代码演示，能通过代码看懂语义即可，暂时不用深究 API 细节，后续章节会有详细介绍。

#### **source —— 从哪里来**

stream 常见的来源方式主要有三种：

- 从控制台输入
- http 请求中的 request
- 读取文件

**从控制台输入**
运行如下代码，然后从控制台输入任何内容，都会被 data 事件监听到，process.stdin 就是一个 stream 对象。注意，data 就是 stream 用来监听数据传入的一个自定义函数，后续会大量用到这个方法。
``` js
process.stdin.on('data', function (chunk) {
    console.log('stream by stdin', chunk.toString())
})
```

**http 请求中的 request**
http 请求中的 request 输入可以参考如下代码片段（不能直接运行，后面章节会详解）。即客户端发起了 http 请求，服务端可以通过这种方式（注意也用到了 data 事件监听）来监听到数据的传入。这种 http 请求一般是一个 post 请求，上传数据。注意，end 用来监听 stream 数据传输完毕，一般和 data 共用。
``` js
req.on('data', function (chunk) {
    // “一点一点”接收内容
    data += chunk.toString()
})
req.on('end', function () {
    // end 表示接收数据完成
})
```

**读取文件**
读取文件是用 stream 如以下代码。fs.createReadStream(...) 可以返回一个读取文件的 stream 对象，该对象可以监听 data 和 end 事件。
``` js
var fs = require('fs')
var readStream = fs.createReadStream('./file1.txt')  // 读取文件的 Stream 对象

var length = 0
readStream.on('data', function (chunk) {
    length += chunk.toString().length
})
readStream.on('end', function () {
    console.log(length)
})
```

#### **dest —— 到哪里去**
stream 常见输出方式主要有三种：
- 输出到控制台
- http 请求中的 response
- 写入文件

**输出到控制台**
上文讲解 source 时提到，process.stdin.on('data', ...) 可以监听控制台输入，而那仅仅是手动监听。如果让控制台输入这个 source 直接通过管道连接到控制台输入，即让数据从输入直接流向输出，使用如下代码。
``` js
process.stdin.pipe(process.stdout) // source.pipe(dest) 形式
```

**http 请求中的 response**
nodejs 处理 http 请求时会用到 req 和 res ，其实这两者都是 stream 对象。其中 req 是 source ，可以 req.on('data', ...) 使用（上文已经演示过），res 是 dest ，用法如下。下面这段代码在本节文章一开始就介绍了，到这里大家应该明白，这是用 stream 的方式读取文件然后直接返回 http 请求。
``` js
var stream = fs.createReadStream(fileName);
stream.pipe(res); // source.pipe(dest) 形式
```

**写入文件**
读取文件可以用 stream ，写入文件当然也可以用 stream ，如下代码。其中，fs.createWriteStream(...) 会返回一个写入文件的 stream 对象，即 dest 。这段代码，就是将一个文件中的内容，一点一点的流动到另外的文件中，完成复制功能。跟文章一开始管道换水的例子非常像。

``` js
var fs = require('fs')
var readStream = fs.createReadStream('./file1.txt')  // source
var writeStream = fs.createWriteStream('./file2.txt')  // dest
readStream.pipe(writeStream) // source.pipe(dest) 形式
```

### stream 的常见使用场景

stream 常见的应用场景是 http 请求和文件操作

总结来看，http 请求和文件操作都属于 IO ，即 stream 主要的应用场景就是处理 IO ，这就又回到了 stream 的本质 —— 由于一次性 IO 操作过大，硬件开销太多，影响软件运行效率，因此将 IO 分批分段操作，让数据一点一点的流动起来，直到操作完成。

## 02 在 http get 请求中使用 stream
stream 的应用场景都是 IO 操作，包括网络 IO 和文件 IO 。本节主要介绍 http 请求中的 get 请求如何应用 stream ，以及应用 stream 之后对性能的提升。

- node.js 实现 http 请求
- get 请求和 response
- response 和 stream
- 使用 stream 对性能的提升
- 应用场景

### node.js 实现 http 请求
这段代码就创建了一个服务，监听 8000 端口。有请求进来，就去读取 data.txt 这个文件，然后返回给客户端。
``` js
var http = require('http');
var fs = require('fs');
var path = require('path');

var server = http.createServer(function (req, res) {
    var fileName = path.resolve(__dirname, 'data.txt');
    fs.readFile(fileName, function (err, data) {
        res.end(data);
    });
});
server.listen(8000);
```

下面将这段代码真正运行起来。找一个目录，然后新建 test1.js 文件，然后把这段代码写入其中。然后再新建 data.txt 文件，随便写点什么文本内容，例如123456。然后打开命令行，定位到这个文件夹，运行 node test1.js ，打开浏览器访问 http://localhost:8000/ 即可看到效果。


### get 请求和 response
get 请求，通过 req.method 可获取请求方法，因此将代码做如下改造。

``` js
var http = require('http');
var fs = require('fs');
var path = require('path');

var server = http.createServer(function (req, res) {
    var method = req.method; // 获取请求方法
    if (method === 'GET') { // 暂只关注 get 请求
        var fileName = path.resolve(__dirname, 'data.txt');
        fs.readFile(fileName, function (err, data) {
            res.end(data);
        });
    }
    // 其他 method 暂时忽略
});
server.listen(8000);
```
将代码修改在 test1.js 中，重新运行 node 并刷新浏览器，依然可以看到效果。

http 常见的有 get 和 post 请求，get 请求本意是获取数据，即 response ，post 请求本意的上传数据，即 request 。本节只关注 get 请求，也就代表本节只关注 http 请求的 response 。

### response 和 stream

response 常用的 API 有 send end 等，如上面代码中的 res.end(data) ，但是 response 也是一个 stream 对象。大家再次回顾一开始的管道换水的图，以及 source.pipe(dest) 模型，response 就是一个 dest 。

![](https://raw.githubusercontent.com/RocWangPeng/king-static/master/20200421111143.png)

因此，之前的代码可以做出如下改动。

``` js
var http = require('http');
var fs = require('fs');
var path = require('path');

var server = http.createServer(function (req, res) {
    var method = req.method; // 获取请求方法
    if (method === 'GET') { // 暂只关注 get 请求
        var fileName = path.resolve(__dirname, 'data.txt');
        var stream = fs.createReadStream(fileName);
        stream.pipe(res); // 将 res 作为 stream 的 dest
    }
    // 其他 method 暂时忽略
});
server.listen(8000);
```

从之前的章节学习中我们知道 fs.createReadStream() 可以创建一个读取文件的 stream 对象，通过 pipe 可以将数据传递下去，这里就传递给了 response 。修改 test1.js 并创新运行 node ，刷新浏览器即可看到效果。


### 实际应用

在 nodejs 中如果要返回的数据是经过 IO 操作得来的，例如上面例子中读取文件内容，可以直接使用 stream.pipe(res); 这种方式，而不要再用 res.end(data); 了。

这种应用的实例应该比较多，主要有两种场景：

- 使用 node.js 作为服务代理，即客户端通过 node.js 服务作为跳板去请求其他服务，返回请求的内容
- 使用 node.js 做静态文件服务器，直接返回静态文件

## 03 在 http post 请求中使用stream

本节继续讲解常用的 post 请求，以及 stream 在其中的应用。get 请求关注 response ，而 post 请求更多关注 request 。

- node.js 处理 post 请求
- request 和 stream
- 使用 stream 对性能的提升
- 应用场景

### node.js 处理 post 请求

上一节讲解了 node.js 处理 http 请求，以及使用 req.method 可以获取请求的方法。根据这个基础，先用 node.js 实现一个最简单的 post 请求，代码如下

``` js
var http = require('http');
var fs = require('fs');
var path = require('path');

var server = http.createServer(function (req, res) {
    var method = req.method; // 获取请求方法
    if (method === 'POST') { // 暂只关注 post 请求
        res.end('OK');
    }
    // 其他请求方法暂不关心
});
server.listen(8000);
```

在 test1.js 的文件夹中，新建 test2.js 并将代码写入，然后运行 node test2.js ，即可监听 http://localhost:8000/ 的 post 请求。

不过 post 请求就不容易用浏览器直接验证，最简单的方式是借助于 postman 这个工具来发送 post 请求。大家可以直接搜索并安装 postman ，也可以去 chrome 商店搜索 postman 并安装，然后在 Chrome 中输入 chrome://apps/ 即可看到 postman 的入口。无论哪种安装手段，打开 postman ，如下图操作，即可看到请求返回了 OK 。

![](https://raw.githubusercontent.com/RocWangPeng/king-static/master/20200421115841.png)

但是，post 请求一般是要发送数据的，而且发送的数据量可能会很大，我们同样可以借助 postman 来模拟发送数据的操作。为了方便后面的演示效果，可以找一个（或者自己造一个）比较大的 json 文本，然后粘贴到 body 的文本框中，并且选择 JSON 格式。操作如下图。

![](https://raw.githubusercontent.com/RocWangPeng/king-static/master/20200421115914.png)

接下来看看 post 请求发送的数据将如何被 nodejs 接收。


### request 和 stream
web server 接收 http 请求肯定是通过 request ，而 request 接收数据的本质其实就是 stream 。再次联想一下课程一开始介绍的管道换水的图，以及 source.pipe(dest) 这个模型，其实 request 就是 source 。

![](https://raw.githubusercontent.com/RocWangPeng/king-static/master/20200421111143.png)

虽然看似是 request 接收数据，但是在 nodejs 服务本身而言，request 是产生数据的（即 source），它产生的数据就是它接收到客户端发送来的数据。

之前的章节介绍过，source 类型的 stream 对象都可以对其监听 data end 事件，分别触发数据接收和数据接收完成的通知。因此，代码可以被改造为以下样子：

``` js
var http = require('http');
var fs = require('fs');
var path = require('path');

var server = http.createServer(function (req, res) {
    var method = req.method; // 获取请求方法
    if (method === 'POST') { // 暂只关注 post 请求
        req.on('data', function (chunk) {
            // 接收到部分数据
            console.log('chunk', chunk.toString().length);
        });
        req.on('end', function () {
            // 接收数据完成
            console.log('end');
            res.end('OK');
        });
    }
    // 其他请求方法暂不关心
});
server.listen(8000);
```

以上代码中，通过 req.on('data', ...) 接收流转的数据，因为数据是一点一点“流”进来的，因此每次“流”进来的那一点，都可以通过 chunk 读取。最后，通过 req.on('end', ...) 来监听数据传输完成，此时请求可以结束了。

将代码重新修改到 test2.js ，然后重启 node ，重新运行 postman 中最后发送 json 的请求。如果你的 json 文件够大，你应该会看到控制台中打印了如下结果：

``` shell
chunk 63560
chunk 64556
chunk 64574
chunk 64596
chunk 64572
chunk 64612
chunk 64574
chunk 23086
end
```
从这个结果我们能很清楚的看到，post 请求发送的数据量很大，req.on('data', ...) 要分 8 次才能把数据接收完毕，每次接收到的数据比较小。这就很明显的看出 stream 的“一点一点”、“流” 的这个特点。

当然，如果 post 请求传递的数据本身就很小，那就没必要分多次接收，可能一次 req.on('data', ...) 就能把数据全部接收完成。


再举一个简单的例子，如果要把 request 请求的数据直接 response ，那么最快的方式就是 req.pipe(res) ，代码如下：

``` js
var http = require('http');
var fs = require('fs');
var path = require('path');

var server = http.createServer(function (req, res) {
    var method = req.method; // 获取请求方法
    if (method === 'POST') { // 暂只关注 post 请求
        req.pipe(res)  // 将 request 数据直接 response
    }
    // 其他请求方法暂不关心
});
server.listen(8000);
```

### 接收到 post 请求的数据，然后将其写入文件。

下面做一个简单的示例来验证在 post 请求中使用 stream 的性能提升。为了完成测试，要做一个简单的 demo ，即 nodejs 接收到 post 请求的数据，然后将其写入文件。

不适用 stream 的 demo 代码如下。即通过 req.on('data', ...) 获取数据，然后暂存下来，最后在 req.on('end', ...) 中将数据完成的写入到 post.txt 文件中。当然，你需要先创建 post.txt 文件。

``` js
var http = require('http');
var fs = require('fs');
var path = require('path');

var server = http.createServer(function (req, res) {
    var method = req.method; // 获取请求方法
    if (method === 'POST') { // 暂只关注 post 请求
        var fileName = path.resolve(__dirname, 'post.txt');
        var writeStream = fs.createWriteStream(fileName)
        req.pipe(writeStream)
        req.on('end', function () {
            // 接收数据完成
            res.end('OK');
        });
    }
    // 其他请求方法暂不关心
});
server.listen(8000);
```

### 实际应用

和 get 请求使用 stream 的场景类似，post 请求使用 stream 的场景，主要是用于将接收的数据直接进行 IO 操作，例如：

- 将接收的数据直接存储为文件
- 将接收的数据直接 post 给其他的 web server


## 04 node.js 文件操作中使用 stream

stream 主要解决 IO 操作的性能问题，之前讲解了 http 即网络 IO ，本节开始讲解文件 IO 即 node.js 的文件操作。

- node.js 如何读写文件
- 如何用 stream 读写文件
- 使用 stream 带来的性能提升
- 应用场景

### node.js 读写文件

node.js 提供了非常清晰易懂的读写文件的 API ，读取一个文件代码如下。通过 fs.readFile 读取文件，然后再回调函数中返回文件内容。

``` js
var fs = require('fs')
var path = require('path')

// 文件名
var fileName = path.resolve(__dirname, 'data.txt');

// 读取文件内容
fs.readFile(fileName, function (err, data) {
    if (err) {
        // 出错
        console.log(err.message)
        return
    }
    // 打印文件内容
    console.log(data.toString())
})
```
写入文件操作代码如下。使用 fs.writeFile 写入文件内容，然后在回调函数中返回操作状态。

``` js
var fs = require('fs')
var path = require('path')

// 文件名
var fileName = path.resolve(__dirname, 'data.txt');

// 写入文件
fs.writeFile(fileName, 'xxxxxx', function (err) {
    if (err) {
        // 出错
        console.log(err.message)
        return
    }
    // 没有报错，表示写入成功
    console.log('写入成功')
})
```

根据以上读写操作，可以简单做一个文件拷贝的程序，将 data.txt 中的内容拷贝到 data-bak.txt 中。代码如下。

``` js
var fs = require('fs')
var path = require('path')

// 读取文件
var fileName1 = path.resolve(__dirname, 'data.txt')
fs.readFile(fileName1, function (err, data) {
    if (err) {
        // 出错
        console.log(err.message)
        return
    }
    // 得到文件内容
    var dataStr = data.toString()

    // 写入文件
    var fileName2 = path.resolve(__dirname, 'data-bak.txt')
    fs.writeFile(fileName2, dataStr, function (err) {
        if (err) {
            // 出错
            console.log(err.message)
            return
        }
        console.log('拷贝成功')
    })
})
```

### 使用 stream 读写文件

用 stream 读写文件其实前面章节都有多处用到，这里再统一整理一下。

- 可以使用 `fs.createReadStream(fileName)` 来创建读取文件的 stream 对象
- 使用 `fs.createWriteStream(fileName)` 来创建写入文件的 stream 对象

回顾第一节讲的管道换水的图，还有 source.pipe(dest) 的模型图。读取文件的 stream 对象，对应的就是 source ，即数据的来源。写入文件的 steram 对象对应的就是 dest ，即数据的目的地。

![](https://raw.githubusercontent.com/RocWangPeng/king-static/master/20200421111143.png)

可以再分别回顾一下之前章节讲解 get 和 post 请求时的示例。

``` js
var fileName = path.resolve(__dirname, 'data.txt');
var stream = fs.createReadStream(fileName);
stream.pipe(res); // 将 res 作为 stream 的 dest
```

以上代码是 node.js 处理 get 请求时，读取了文件直接返回。这里 stream 就是 source ，res 就是 dest ，两者用 pipe 相连，表示数据流动的方向。

``` js
var fileName = path.resolve(__dirname, 'post.txt');
var writeStream = fs.createWriteStream(fileName)
req.pipe(writeStream)
```

以上代码是 node.js 处理 post 请求时，将传入的数据直接写入文件。这里 req 就是 source ，writeStream 就是 dest ，两者用 pipe 相连，表示数据流动的方向。

接下来用 stream 实现一个文件拷贝的功能，代码如下：

``` js
var fs = require('fs')
var path = require('path')

// 两个文件名
var fileName1 = path.resolve(__dirname, 'data.txt')
var fileName2 = path.resolve(__dirname, 'data-bak.txt')
// 读取文件的 stream 对象
var readStream = fs.createReadStream(fileName1)
// 写入文件的 stream 对象
var writeStream = fs.createWriteStream(fileName2)
// 执行拷贝，通过 pipe
readStream.pipe(writeStream)
// 数据读取完成，即拷贝完成
readStream.on('end', function () {
    console.log('拷贝完成')
})
```
### 使用 stream 来带的性能提升
上文分别用基本的文件操作 API 和 stream API 写了两个拷贝文件的程序，两者都能实现功能，但是性能上却有巨大的差异。这个程序并不是 web server ，因此我们不再用 ab 工具进行性能测试，而是看下两者对内存的占用情况，文件操作中内存往往是一个瓶颈。

先介绍一个监控 node.js 内存的轻量级小工具 —— `memeye` ，它能很方便很直观的监控 node.js 内存占用的情况。安装和使用也都非常简单。

找到之前 test2.js 的文件夹，命令行定位到这个文件夹，然后运行 `npm install memeye --save-dev` 等待安装完成。然后新建 test3.js ，在代码中写入：
``` js
var memeye = require('memeye')
memeye()
```

命令行运行 node test3.js ，然后用浏览器打开 http://localhost:23333/ 即可看到这个 node.js 进程的内存占用情况，如下图。其中我们最需要关心的就是页面中 Process Memory Usage 这部分的 heapUsed 内存大小，即 nodejs 的堆内存（可以简单理解为 JS 对象所占用的内存空间）。

![](https://raw.githubusercontent.com/RocWangPeng/king-static/master/20200421121240.png)

再继续完善一下 test3.js ，为了方便测试我们还需要做两个工作。第一，让拷贝操作延迟执行，程序运行之后还得切换到浏览器刷新统计界面。第二，连续执行 100 次拷贝操作，以便明显的看出差异。这样 test3.js 需要改进为：

``` js
var fs = require('fs')
var path = require('path')

// 开始监控内存
var memeye = require('memeye')
memeye()

// 将拷贝操作封装到一个函数中
function copy() {
    // 这里自行补充上文的拷贝代码
    // 测试一，使用 readFile 和 writeFile 编写的拷贝代码
    // 测试二，使用 stream 编写的拷贝代码
}

// 延迟 5s 执行拷贝
setTimeout(function () {
    // 连续执行 100 次拷贝
    var i
    for (i = 0; i < 100; i++) {
        copy()
    }
}, 5000)
```

以上代码中，把上文使用 readFile 和 writeFile 编写的拷贝代码粘贴到 copy 函数中，然后运行 node test3.js ，立刻切换到浏览器刷新页面，结果如下图。图中 heapUsed 从 5M 左右一下子飙升到了 60M 左右。

![](https://raw.githubusercontent.com/RocWangPeng/king-static/master/20200421121343.png)

再把上文用 stream 编写的拷贝代码粘贴到 copy 函数中，然后重新运行 node test3.js 并立刻切换到浏览器刷新页面，结果如下图。图中 heapUsed 从 5M 左右仅仅增长到了 6M 左右。

![](https://raw.githubusercontent.com/RocWangPeng/king-static/master/20200421121354.png)

对比两种情况，5M -> 60M VS 5M -> 6M 非常巨大的差异，而且文件体积越大、操作数量越多，这种差异就越明显。由此可见，使用 stream 操作文件对性能带来了非常大的提升，因此大家以后一旦遇到文件操作，应该第一时间想到 stream 并评估是否需要使用。

### 应用场景

所有执行文件操作的场景，都应该尝试使用 stream ，例如文件的读写、拷贝、压缩、解压、格式转换等。除非是体积很小的文件，而且读写次数很少，性能上被忽略。

近几年前端的打包构建工具都是用 node.js 编写的，而打包和构建其实就是频繁的文件操作，这其中会用到大量的 stream 。其中靠 stream 上位的最著名就是 gulp ，一个非常轻量级的前端构建工具。


## 05 逐行读取的最佳方案 readline

上一节讲解了用 stream 操作文件，会来带很大的性能提升。但是原生的 stream 却对“行”无能为力，它只是把文件当做一个数据流、简单粗暴的流动。很多文件格式都是分行的，例如 csv 文件、日志文件，以及其他一些自定义的文件格式。

node.js 提供了非常简单的按行读取的 API —— readline ，它本质上也是 stream ，只不过是以“行”作为数据流动的单位。本节将结合一个分析日志文件的案例，讲解 readline 的使用。

- 回顾 data end 自定义事件和 pipe 方法
- readline 的使用
- 应用场景：日志文件的分析

### 回顾 data end 和 pipe

通过学习以上的章节，我们知道 stream 对象有 data end 自定义事件和 pipe 方法。回顾一开始讲解的管道换水的图，即 source.pipe(dest) 这个模型。


![](https://raw.githubusercontent.com/RocWangPeng/king-static/master/20200421111143.png)

所有 source 类型的 stream 对象都可以监听它的 data end 自定义事件，例如 http 请求中的 request ，代码如下。

``` js
req.on('data', function (chunk) {
    console.log('chunk', chunk.toString().length);
});
req.on('end', function () {
    console.log('end');
    res.end('OK');
});
```
再例如读取文件的 stream 对象，代码如下。
``` js
var readStream = fs.createReadStream('./file1.txt')
var length = 0
readStream.on('data', function (chunk) {
    length += chunk.toString().length
})
readStream.on('end', function () {
    console.log(length)
})
```
所有 source 类型的 stream 对象都有 pipe 方法，可以传入一个 dest 类型的 stream 对象，例如文件拷贝，代码如下。

``` js
var readStream = fs.createReadStream(fileName1)
var writeStream = fs.createWriteStream(fileName2)
readStream.pipe(writeStream)
```

再例如 http 输入的内容直接输出，代码如下。

``` js
var method = req.method
if (method === 'POST') {
    req.pipe(res)  // 将 request 数据直接 response
}
```
以上都是 stream 的常用操作，而 readline 和 stream 类似，但是更加简单、易懂。

### readline 的使用

相比于 stream 的 data 和 end 自定义事件， readline 需要监听 line 和 close 两个自定义事件。readline 的基本使用示例如下：

以上代码，需要先根据文件名，创建读取文件的 stream 对象，然后传入并生成一个 readline 对象，然后通过 line 事件监听逐行读取，通过 close 事件监听读取完成。

如果 readline-data.txt 中的文件内容是

```
访问时间：[2018-10-23 13:56:48.407]，访问地址："http://www.example.com/oc/v/account/login.html"
访问时间：[2018-10-23 14:00:10.618]，访问地址："http://www.example.com/oc/v/account/user.html"
访问时间：[2018-10-23 14:00:34.200]，访问地址："http://www.example.com/oc/v/account/user.html"
```

那打印的内容将会是：
``` js
访问时间：[2018-10-23 13:56:48.407]，访问地址："http://www.example.com/oc/v/account/login.html"
----- this line read -----
访问时间：[2018-10-23 14:00:10.618]，访问地址："http://www.example.com/oc/v/account/user.html"
----- this line read -----
访问时间：[2018-10-23 14:00:34.200]，访问地址："http://www.example.com/oc/v/account/user.html"
----- this line read -----
readline end
```

### 应用场景

对于处理按行为单位的文件，如日志文件，使用 readline 是最佳选择。接下来会使用 readline 演示一个日志文件的分析，最终得出在 2018-10-23 14:00 这一分钟内，访问 user.html 的日志数量。日志格式和上文的示例一样，但是日志的内容我制造了 10w 行（文件体积 11.4M ），为了能更加真实的演示效果。

根据以上示例的代码改造一下即可完成这个需求，需要改造的就是 line 和 close 两个自定义事件，以及在上面再加一行 var num = 0 来记录最终的数量。需要改造的代码如下。

``` js
var num = 0

// 监听逐行读取的内容
rl.on('line', function (lineData) {
    if (lineData.indexOf('2018-10-30 14:00') >= 0 && lineData.indexOf('user.html') >= 0) {
        num++
    }
})
// 监听读取完成
rl.on('close', function () {
    console.log('num', num)
})
```

修改 test4.js 然后重新运行 node test4.js ，得到如下结果。即统计得到一共有 23040 条日志满足要求。
```
num 23040
```

使用上一节的方法看一下内存占用，发现统计这个 11.4M 的日志文件，堆内存占用从 5M 仅仅上升到了 8.78M ，内存占用非常少，如下图。如果是全部把文件读取再做分析，那内存就不会这么少了，具体可参考上一节的例子。

![](https://raw.githubusercontent.com/RocWangPeng/king-static/master/20200421122740.png)

借用这个简单的例子来演示 readline 的使用以及使用场景，其实日常工作中情况会更加复杂，不过再复杂的场景选择 readline 做逐行分析一定是不会错的。方向选对了很重要，选择大于努力。

最后，将这个示例所用的代码贴到下面，供大家参考。

``` js
var fs = require('fs')
var path = require('path')
var readline = require('readline') // 引用 readline

var memeye = require('memeye')
memeye()

function doReadLine() {
    // 文件名
    var fileName = path.resolve(__dirname, 'readline-data.txt')
    // 创建读取文件的 stream 对象
    var readStream = fs.createReadStream(fileName)

    // 创建 readline 对象
    var rl = readline.createInterface({
        // 输入，依赖于 stream 对象
        input: readStream
    })

    var num = 0

    // 监听逐行读取的内容
    rl.on('line', function (lineData) {
        if (lineData.indexOf('2018-10-30 14:00') >= 0 && lineData.indexOf('user.html') >= 0) {
            num++
        }
    })
    // 监听读取完成
    rl.on('close', function () {
        console.log('num', num)
    })
}

setTimeout(doReadLine, 5000);
```

### 总结

本节主要讲解了 readline 的使用和一个日志分析的示例，学完本节希望你能掌握：

- readline 本质也是 stream ，也是解决一次性 IO 操作的性能问题
- readline 是 node.js 按行读取文件的最佳选择
- readline 的使用，以及本节的分析日志的案例

到这里已经讲完了 stream 在网络 IO 和文件 IO 的基本使用，就像一开始介绍的管道换水的例子，一点一点的将数据流出来。那么，每次流动的这一点数据到底是什么（即之前代码示例中的 chunk 变量）？下一节讲解。

## 06 stream 和 buffer
以上章节已经介绍过了 stream 在网络 IO 和文件 IO 中的使用以及价值，但还有一个很重要的问题没有解决。本教程一开始就提到，stream 就是将数据一点一点的流动起来，那么每次流动的数据到底是什么？

``` js
var readStream = fs.createReadStream('./file1.txt')
readStream.on('data', function (chunk) {
    // ...
})
```

具体一点，就是以上代码中的 chunk 到底是什么？本节来讲解。

- 二进制介绍
- node.js 如何表示二进制
- stream 中“流动”的二进制数据
- 使用 Buffer 对性能的提升

### 二进制

首先得要普及一下二进制的知识，特别是对于前端的同学，相对于后端开发的同学，这些计算机基础知识都相对薄弱，更有必要了解一下。

半个世纪之前，现代计算机之父 冯.诺依曼 提出了“冯.诺依曼结构”，其中最核心的内容之一就是：计算式使用二进制形式进行存储了计算。这种设计一直沿用至今，而且在未来也会一直用下去，除非真的出现了传说中的量子计算以颠覆现代计算机结构。

计算机内存由若干个存储单元组成，每个存储单元只能存储 0 或者 1 （因为内存是硬件，计算机硬件本质上就是一个一个的电子元件，只能识别充电和放电的状态，充电代表 1 ，放电代表 0 ，可以先这么简单理解），即二进制单元（英文叫 bit）。但是这一个单元所能存储的信息太少，因此约定将 8 个二进制单元为一个基本存储单元，叫做字节（英文是 byte）。一个字节所能存储的最大整数就是 2^8 = 256 ，也正好是 16^2 ，因此也尝尝使用两位的 16 进制数代表一个字节。例如 CSS 中常见的颜色值 #CCCCCC 就是 6 位 16 进制数字，它占用 3 个字节的空间。

二进制是计算机最底层的数据格式，也是一种通用格式。计算机中的任何数据格式，字符串、数字、视频、音频、程序、网络包等，在最底层都是用二进制来进行存储。这些高级格式和二进制之间，都可通过固定的编码格式进行相互转换。例如，C 语言中 int32 类型的十进制整数（无符号的），就占用 32 bit 即 4 byte ，十进制的 3 对应的二进制就是 00000000 00000000 00000000 00000011 。字符串也同理，可以根据 ASCII 编码规则或者 unicode 编码规则（如 utf-8）等和二进制进行相互转换。

总之，计算机底层存储的数据都是二进制格式，各种高级类型都有对应的编码规则，和二进制进行相互转化。

### nodejs 表示二进制

Buffer 就是 nodejs 中二进制的表述形式。

``` js
var str = '学习 nodejs stream'
var buf = Buffer.from(str, 'utf-8') // 注意：node 版本 < 6.0 的使用 var buf = new Buffer(str, 'utf-8')
console.log(buf) // <Buffer e5 ad a6 e4 b9 a0 20 6e 6f 64 65 6a 73 20 73 74 72 65 61 6d>
console.log(buf.toString('utf-8'))  // 学习 nodejs stream
```

以上代码中，先通过 Buffer.from 将一段字符串转换为二进制形式，其中 utf-8 是一个编码规则。二进制打印出来之后是一个类似数组的对象（但它不是数组），每个元素都是两位的 16 进制数字，即代表一个 byte ，打印出来的 buf 一共有 20 byte 。即根据 utf-8 的编码规则，这段字符串需要 20 byte 进行存储。最后，再通过 utf-8 规则将二进制转换为字符串并打印出来。

### 流动的数据是二进制格式
先在之前的示例中分别打印一下 chunk instanceof Buffer 和 chunk ，看看结果。

``` js
var readStream = fs.createReadStream('./file1.txt')
readStream.on('data', function (chunk) {
    console.log(chunk instanceof Buffer)
    console.log(chunk)
})
```
打印结果如下图。

![](https://raw.githubusercontent.com/RocWangPeng/king-static/master/20200421123223.png)

可以看到 stream 中流动的数据就是 Buffer 类型，就是二进制。因此，在使用 stream chunk 的时候，需要将这些二进制数据转换为相应的格式。例如之前讲解 post 请求，从 request 中接收数据就是这样。再回归一下之前的代码，就能看明白了。

``` js
var dataStr = '';
req.on('data', function (chunk) {
    var chunkStr = chunk.toString()  // 这里，将二进制转换为字符串
    dataStr += chunkStr
});
```

stream 中为何要“流动”二进制格式的数据呢？回答这个问题得再次考虑一下 stream 的设计目的 —— 为了优化 IO 操作。无论是文件 IO 还是网络 IO ，其中包含的数据格式是未可知的，如字符串、音频、视频、网络包等。即便都是字符串，其编码规则也是未可知的，如 ASC 编码、utf-8 编码。再这么多未可知的情况下，只能是以不变应万变，直接用最通用的二进制格式，谁都能认识。

而且，用二进制格式进行流动和传输，是效率最高的。

PS：补充一点，按照上面的说法，那无论是用 stream 读取文件还是 fs.readFile 读取文件，读出来的都应该是二进制格式？—— 答案是正确的，验证代码如下（下文正好会用到这段代码）。

``` js
var fileName = path.resolve(__dirname, 'data.txt');
fs.readFile(fileName, function (err, data) {
    console.log(data instanceof Buffer)  // true
    console.log(data)  // <Buffer 7b 0a 20 20 22 72 65 71 75 69 72 65 ...>
});
```

## 07 stream 常用类型总结

再次回顾第一节中讲解的管道换水的图，source 通过一个管道流向了 dest ，如下图。

![](https://raw.githubusercontent.com/RocWangPeng/king-static/master/20200421111143.png)

通过之前的学习知道：这里的 source 可能是 http 请求中的 request ，也可能是读取文件的 stream 对象，也可能是 process.stdin ；这里的 dest 可能是 http 请求中的 response ，也可能是写入文件的 stream 对象，也可能是 process.stdout；这里的管道就是 pipe 函数。

先不管 pipe 函数。source 和 dest 完全就是两个不同的类型，一个是读取数据的，叫做 readable stream ，一个是写入数据的，叫做 writeable stream ，下文会详细介绍这两种类型。

除了这两种类型之外，还有一种类型叫做 duplex stream ，中文叫双工流，既有读取功能又有写入功能。例如下面的代码，readStream 读取的数据直接 pipe 到 zlib.createGzip() 这个 duplex stream 就是 gzip 压缩，然后再 pipe 到 writeStream 结束操作。

``` js
var fs = require('fs')
var zlib = require('zlib')
var readStream = fs.createReadStream('./file1.txt')
var writeStream = fs.createWriteStream('./file1.txt.gz')
readStream.pipe(zlib.createGzip())
          .pipe(writeStream)
```
其他类型（transform classic）不常用，暂时可不关注。

### readable stream
根据本节一开始的分析，http 请求中的 request 和读取文件的 stream 对象都是 readable stream ，即可以从中读取数据。它有两种常用的操作方式，第一种是直接将数据 pipe 到一个 writeable stream ，例如之前讲解 get 请求时的代码示例。

``` js
var fileName = path.resolve(__dirname, 'data.txt');
var stream = fs.createReadStream(fileName);
stream.pipe(res); // 直接将数据 `pipe` 到一个 writeable stream
```
第二种是通过监听 on end 自定义事件来获取数据再手动处理，例如之前讲解 post 请求时的代码示例。

``` js
var dataStr = '';
req.on('data', function (chunk) {
    // 接收到数据，先存储起来
    var chunkStr = chunk.toString()
    dataStr += chunkStr
});
req.on('end', function () {
    // 接收数据完成，将数据写入文件
    var fileName = path.resolve(__dirname, 'post.txt');
    fs.writeFile(fileName, dataStr)

    res.end('OK');
});
```

以上说的两个例子，都是已经封装好的 readable stream 。那么它的本来面目是怎样的？先看下面代码。
``` js
var Readable = require('stream').Readable;

// 构造一个 readable stream 并往里“灌入”数据
var rs = new Readable;
rs.push('学习 ');
rs.push('nodejs ');
rs.push('stream
');
rs.push(null);

// pipe 到一个 writable stream
rs.pipe(process.stdout);
```
以上代码可以看出，node.js 提供了 readable stream 的构造函数，可以 new 出一个新的 readable stream 对象。然后通过 push 函数往里“灌入”数据，当传入的是 null 的时候表示“灌入”完成，即可输入了。最后 pipe 到了一个 writeable stream 。

### writeable stream
根据本节一开始的分析，http 请求中的 response 和写入文件的 stream 对象都是 writable stream ，它可以作为参数传入 pipe 函数，以读取上游的数据。例如之前讲解文件操作时拷贝文件的代码示例。
``` js
var fileName = path.resolve(__dirname, 'post.txt');
var writeStream = fs.createWriteStream(fileName)
req.pipe(writeStream) // writeStream 作为参数传递到 pipe 函数中
```
以上代码中 writeStream 是已经封装好了的 writeable stream ，下面再看看它的本来面目。

``` js
var Writable = require('stream').Writable;

var ws = Writable();
ws._write = function (chunk, enc, next) {
    console.log(chunk.toString());  // 输出“流动”的数据
    next();  // 继续监听下一次输出
};

process.stdin.pipe(ws); // 作为参数传递到 pipe 函数中
```
根据以上代码得知，node.js 提供了 Writable 构造函数可以 new 一个新的 writable stream 。通过实现它的 _write 方法即可监听到每次“流动”的数据，运行 next() 可继续监听。

### 再谈 pipe

之前的章节中一直是用 source.pipe(dest) 这种模式来使用 pipe 的，其实 pipe 可以链式调用。例如上文演示的 duplex stream 示例代码 readStream.pipe(zlib.createGzip()).pipe(writeStream) ，还有之前讲解文件操作的那一节最后列举的 gulp 配置文件的示例代码（可翻到那一节再回顾一下）。

之前讲解 source.pipe(dest) 模式是为了方便理解和使用，现在我们更新一个更加严谨的 pipe 用法：

- 调用 pipe 的对象必须是 readable stream 或者 duplex stream ，即具有读取数据的功能。如 req.pipe(..)
- 传入 pipe 的参数必须是 writeable stream 或者 duplex stream ，即具有写入数据的功能。如 req.pipe(res)
- pipe 支持链式调用












