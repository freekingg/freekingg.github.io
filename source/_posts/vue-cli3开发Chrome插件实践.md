---
title: vue-cli3开发Chrome插件实践
date: 2020-04-31 16:59:10
tags:
- chrome
- 浏览器插件开发
categories:
- [浏览器插件开发]
- [前端]
---

之前找了不少如何开发谷歌插件的文章，结果发现都是些很基础的内容，并没有写到如何快速编译打包插件。我就在想为什么不能通过webpack来打包插件呢？如果通过webpack编译的话，就能使开发过程变得更舒服，使文件结构趋向模块化，并且打包的时候直接编译压缩代码。后来发现了vue-cli-plugin-chrome-ext插件，通过这个插件能很方便地用vue-cli3来开发谷歌插件，并能直接引用各种UI框架跟npm插件。

<!-- more -->
## 搭建环境
- 创建一个vue-cli3项目： vue create vue-extension，对话流程选择默认就行。
- 进入项目cd vue-extension
- 安装vue-cli-plugin-chrome-ext插件：vue add chrome-ext,根据安装对话选项设置好。
- 删除vue-cli3无用文件跟文件夹：src/main.js、src/components

## 运行项目
``` shell
npm run build-watch 
```
运行开发环境
对修改文件进行实时编译并自动在根目录下生成dist文件夹，然后在浏览器上加载dist文件夹完成插件安装。

``` shell
npm run build 
```
运行生产环境编译打包，将所有文件进行整合打包。

## 引入element UI
``` shell
npm install element-ui
```
考虑到插件打包后的文件大小，最后通过按需加载的方式来引入组件，按照element-ui官方的按需加载方法
``` shell
npm install babel-plugin-component -D
```
然后，将babel.config.js修改为：
``` js
module.exports = {
  presets: [
    '@vue/app'
  ],
  "plugins": [
    [
      "component",
      {
        "libraryName": "element-ui",
        "styleLibraryName": "theme-chalk"
      }
    ]
  ]
}
```
接下来修改popup相关文件引入所需组件， src/popup/index.js内容:
``` js
import Vue from "vue";
import AppComponent from "./App/App.vue";
 
Vue.component("app-component", AppComponent);
 
import {
  Card
} from 'element-ui';
 
Vue.use(Card);
 
new Vue({
  el: "#app",
  render: createElement => {
    return createElement(AppComponent);
  }
});
```
src/popup/App/App.vue 内容：
``` html
<template>
  <el-card class="box-card">
    <div
      slot="header"
      class="clearfix"
    >
      <span>卡片名称</span>
      <el-button
        style="float: right; padding: 3px 0"
        type="text"
      >操作按钮</el-button>
    </div>
    <div
      v-for="o in 4"
      :key="o"
      class="text item"
    >
      {{'列表内容 ' + o }}
    </div>
  </el-card>
</template>
 
<script>
export default {
  name: 'app',
}
</script>
 
<style>
.box-card {
  width: 300px;
}
</style>
```

**content.js使用element-ui组件**
content.js主要作用于浏览网页，对打开的网页进行插入、修改DOM，对其进行操作交互
> 根据评论的朋友提示，可以通过Chrome插件的chrome.extension.getURLAPI来引入字体文件，解决element-ui无法引入相对路径的字体文件问题。

首先我们创建src/content/index文件，在里面我们通过chrome.extension.getURLAPI来引入插件的字体文件，内容：

``` js
import {
    Message,
    MessageBox
} from 'element-ui';
 
// 通过Chrome插件的API加载字体文件
(function insertElementIcons() {
    let elementIcons = document.createElement('style')
    elementIcons.type = 'text/css';
    elementIcons.textContent = `
        @font-face {
            font-family: "element-icons";
            src: url('${ window.chrome.extension.getURL("fonts/element-icons.woff")}') format('woff'),
            url('${ window.chrome.extension.getURL("fonts/element-icons.ttf ")}') format('truetype'); /* chrome, firefox, opera, Safari, Android, iOS 4.2+*/
        }
    `
    document.head.appendChild(elementIcons);
})();
 
MessageBox.alert('这是一段内容', '标题名称', {
    confirmButtonText: '确定',
    callback: action => {
        Message({
            type: 'info',
            message: `action: ${ action }`
        });
    }
})
```

vue.config.js增加content.js文件的打包配置，因为content.js（background.js同样可以只生成js文件）只有js文件，不用像app模式那样打包生成相应的html文件。这里我们还要对字体打包配置处理下，因为默认打包后的文件名是带有hash值的，在这里我们要去除掉，完整内容如下：

``` js
const CopyWebpackPlugin = require("copy-webpack-plugin");
const path = require("path");
 
// Generate pages object
const pagesObj = {};
 
const chromeName = ["popup", "options"];
 
chromeName.forEach(name => {
  pagesObj[name] = {
    entry: `src/${name}/index.js`,
    template: "public/index.html",
    filename: `${name}.html`
  };
});
 
// 生成manifest文件
const manifest =
  process.env.NODE_ENV === "production" ? {
    from: path.resolve("src/manifest.production.json"),
    to: `${path.resolve("dist")}/manifest.json`
  } : {
    from: path.resolve("src/manifest.development.json"),
    to: `${path.resolve("dist")}/manifest.json`
  };
 
const plugins = [
  CopyWebpackPlugin([
    manifest
  ])
]
 
module.exports = {
  pages: pagesObj,
  // // 生产环境是否生成 sourceMap 文件
  productionSourceMap: false,
 
  configureWebpack: {
    entry: {
      'content': './src/content/index.js'
    },
    output: {
      filename: 'js/[name].js'
    },
    plugins: [CopyWebpackPlugin(plugins)]
  },
  css: {
    extract: {
      filename: 'css/[name].css'
      // chunkFilename: 'css/[name].css'
    }
  },
  chainWebpack: config => {
    // 处理字体文件名，去除hash值
    const fontsRule = config.module.rule('fonts')
 
    // 清除已有的所有 loader。
    // 如果你不这样做，接下来的 loader 会附加在该规则现有的 loader 之后。
    fontsRule.uses.clear()
    fontsRule.test(/\.(woff2?|eot|ttf|otf)(\?.*)?$/i)
      .use('url')
      .loader('url-loader')
      .options({
        limit: 1000,
        name: 'fonts/[name].[ext]'
      })
  }
};
```
最后在manifest.development.json加载content.js文件，以及将字体文件添加到网页可加载资源字段web_accessible_resources里去：
``` js

{
    "manifest_version": 2,
    "name": "vue-extension",
    "description": "a chrome extension with vue-cli3",
    "version": "0.0.1",
    "options_page": "options.html",
    "browser_action": {
        "default_popup": "popup.html"
    },
    "content_security_policy": "script-src 'self' 'unsafe-eval'; object-src 'self'",
    "content_scripts": [{
        "matches": [
            "*://*.baidu.com/*"
        ],
        "js": [
            "js/content.js"
        ],
        "run_at": "document_end"
    }],
    "web_accessible_resources": ["fonts/*"]
}
```

## 实时刷新插件
之前写的时候发现单纯地通过vue-cli3更新代码并不能使插件的background.js、content.js也跟着更新，因为代码已经加载到浏览器了，浏览器并不会监听这些文件的变化。也是通过评论的朋友推荐，通过crx-hotreload可以完美实现实时刷新功能，而不用重新手动加载。代码还简单易用，在这里我们直接将代码复制到src/utils/hot-reload.js文件：
``` js
// 代码来源：https://github.com/xpl/crx-hotreload/edit/master/hot-reload.js
const filesInDirectory = dir => new Promise(resolve =>
    dir.createReader().readEntries(entries =>
        Promise.all(entries.filter(e => e.name[0] !== '.').map(e =>
            e.isDirectory ?
            filesInDirectory(e) :
            new Promise(resolve => e.file(resolve))
        ))
        .then(files => [].concat(...files))
        .then(resolve)
    )
)
 
const timestampForFilesInDirectory = dir =>
    filesInDirectory(dir).then(files =>
        files.map(f => f.name + f.lastModifiedDate).join())
 
const reload = () => {
    window.chrome.tabs.query({
        active: true,
        currentWindow: true
    }, tabs => { // NB: see https://github.com/xpl/crx-hotreload/issues/5
        if (tabs[0]) {
            window.chrome.tabs.reload(tabs[0].id)
        }
        window.chrome.runtime.reload()
    })
}
 
const watchChanges = (dir, lastTimestamp) => {
    timestampForFilesInDirectory(dir).then(timestamp => {
        if (!lastTimestamp || (lastTimestamp === timestamp)) {
            setTimeout(() => watchChanges(dir, timestamp), 1000) // retry after 1s
        } else {
            reload()
        }
    })
}
 
window.chrome.management.getSelf(self => {
    if (self.installType === 'development') {
        window.chrome.runtime.getPackageDirectoryEntry(dir => watchChanges(dir))
    }
})
```

然后在vue.config.js对热刷新代码进行处理，如果是开发环境的话就将其复制到assets文件夹里面：
``` js
// vue.config.js
...
 
// 在这段下面添加
const plugins = [
  CopyWebpackPlugin([
    manifest
  ])
]
 
// 开发环境将热加载文件复制到dist文件夹
if (process.env.NODE_ENV !== 'production') {
  plugins.push(
    CopyWebpackPlugin([{
      from: path.resolve("src/utils/hot-reload.js"),
      to: path.resolve("dist")
    }])
  )
}

```

最后只要在开发环境manifest.development.json里配置一下，将hot-reload.js加载到background运行环境中即可：
``` js
"background": { "scripts": ["hot-reload.js"] }
```

搞定收工！

