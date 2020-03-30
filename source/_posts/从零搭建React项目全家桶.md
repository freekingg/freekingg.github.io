---
title: 从零搭建React项目全家桶
date: 2020-03-30 13:15:11
tags:
- react
- redux
- react-router
categories:
- [react]
- [前端]
---

React是近几年来前端项目开发非常火的一个框架，其背景是Facebook团队的技术支持，市场占有率也很高。很多初学者纠结一开始是学react还是vue。个人觉得，有时间的话，最好两个都掌握一下。从学习难度上来说，react要比vue稍难一些。万事开头难，但是掌握了react对于大幅提高前端技能还是非常有帮助的。本文一步步详细梳理了从创建react、精简项目、集成插件、初步优化等过程。对于react开发者来说，能够节省很多探索的时间。下面请跟着我来一步步操作。

<!-- more -->

## 先睹为快
正式开始前，先看下通过本次分享，能掌握什么？

- 从零创建React项目
- 支持Sass/Scss/Less/Stylus
- 路由使用：react-router-dom
- 组件的创建与引用
- React Developer Tools浏览器插件
- redux、react-redux使用
- redux-thunk
- store的创建与使用
- Redux DevTools安装与使用
- immutable.js使用
- Mock.js使用
- 解决本地跨域反向代理
- 其他常用工具汇总
- 超值附赠：集成Ant Design

即使你是新手，跟着操作一遍以后，也可以快速上手React项目啦！

> ※注：本文代码区域每行开头的“+”表示新增，“-”表示删除，“M”表示修改；代码中的“...”表示省略。

## 1 创建React-APP

为了加速npm下载速度，先把npm设置为淘宝镜像地址。

``` js
npm config set registry http://registry.npm.taobao.org/
```
通过官方的create-react-app，找个喜欢的目录，执行：
``` js
npx create-react-app react-app
```
命令最后的react-app是项目的名称，可以自行更改。


稍等片刻即可完成安装。安装完成后，可以使用npm或者yarn启动项目。

进入项目目录，并启动项目：

``` shell
cd react-app
yarn start  （或者使用npm start）
```
启动后，可以通过`http://localhost:3000/`地址访问项目：

## 2 精简项目
### 2.1 删除文件
接下来，删除一般项目中用不到的文件，最简化项目。
``` js
    ├─ /node_modules
    ├─ package.json
    ├─ /public
    |  ├─ favicon.ico
    |  ├─ index.html
-   |  ├─ logo192.png
-   |  ├─ logo512.png
-   |  ├─ mainfest.json
-   |  └─ robots.txt
    ├─ README.md
    ├─ /src
-   |  ├─ App.css
    |  ├─ App.js
-   |  ├─ App.test.js      (jTest自动化测试)
-   |  ├─ index.css
-   |  ├─ index.js
-   |  ├─ logo.svg
-   |  ├─ serviceWorker.js
-   |  └─ setuoTests.js    (PWA)
    └─ yarn.lock
```
以上文件删除后，页面会报错。这是因为相应的文件引用已不存在。需要继续修改代码。

### 2.2 简化代码
现在目录结构如下，清爽许多：
``` js
    ├─ /node_modules
    ├─ package.json
    ├─ /public
    |  ├─ favicon.ico
    |  └─ index.html
    ├─ README.md
    ├─ /src
    |  ├─ App.js
    |  └─ index.js
    └─ yarn.lock
```
> 记的删除文件中引用的代码
### 2.3 使用Fragment去掉组件外层标签

react要求每个组件HTML的最外层必须是由一个标签包裹，且不能存在并列的标签。例如，在src/App.js中，如果是这样就会报错：
``` js
 // 以下代码将会报错，最外层不能存在并列的标签。
 function App() {
  return (
    <div className="App">
      <h1>This is React App.</h1>
    </div>
    <div className="App-other">
      <h1>This is React App-other.</h1>
    </div>
  )
}
```
如果确实需要这样的HTML，并且不想再添加一个父级标签，可以使用Fragment作为最外层。代码修改如下：
``` js
M   import React, { Fragment } from 'react'

    function App() {
        return (
+           <Fragment>
                <div className="App">
                    <h1>This is React App.</h1>
                </div>
                <div className="App-other">
                    <h1>This is React App-ohter.</h1>
                </div>
+           </Fragment>
        )
    }

    export default App
```
以上仅为了说明Fragment的使用效果。在某些组件嵌套的使用场景下，非常适合使用Fragment。例如父组件是`<table>`，而子组件可以用`<Fragment>`包裹多个`<tr>`。

## 3 项目目录结构
项目目录结构可根据项目实际灵活制定。这里分享下我常用的结构，仅供参考。
``` js
    ├─ /node_modules
    ├─ package.json
    ├─ /public
    |  ├─ favicon.ico        <-- 网页图标
    |  └─ index.html         <-- HTML页模板
    ├─ README.md
    ├─ /src
    |  ├─ /common            <-- 全局公用目录
    |  |  ├─ /fonts          <-- 字体文件目录
    |  |  ├─ /images         <-- 图片文件目录
    |  |  ├─ /js             <-- 公用js文件目录
    |  |  └─ /style          <-- 公用样式文件目录
    |  |  |  ├─ frame.css    <-- 全部公用样式（import其他css）
    |  |  |  ├─ reset.css    <-- 清零样式
    |  |  |  └─ global.css   <-- 全局公用样式
    |  ├─ /components        <-- 公共模块组件目录
    |  |  ├─ /header         <-- 头部导航模块
    |  |  |  ├─ index.js     <-- header主文件
    |  |  |  └─ header.css   <-- header样式文件
    |  |  └─ ...             <-- 其他模块
    |  ├─ /pages             <-- 页面组件目录
    |  |  ├─ /home           <-- home页目录
    |  |  |  ├─ index.js     <-- home主文件
    |  |  |  └─ home.css     <-- home样式文件
    |  |  ├─ /login          <-- login页目录
    |  |  |  ├─ index.js     <-- login主文件
    |  |  |  └─ login.css    <-- login样式文件
    |  |  └─ ...             <-- 其他页面
    |  ├─ App.js             <-- 项目主模块
    |  └─ index.js           <-- 项目入口文件
    └─ yarn.lock
```
### 3.1 引入全局公用样式
在frame.css里引入其他公用样式： src/common/style/frame.css
``` css
@import './reset.css';
@import './global.css';
```
然后在src/index.js里引入frame.css
```
    import React from 'react'
    import ReactDOM from 'react-dom'
    import App from './App'
+   import './common/style/frame.css'

    ReactDOM.render(<App />, document.getElementById('root'))
```
### 3.2 支持Sass/Less/Stylus
工欲善其事必先利其器，这么高大上的react怎能好意思用最原始的css搭配呢？create-react-app默认情况下未暴露配置文件。要更灵活配置项目，需要将配置文件暴露出来。

执行以下命令，暴露配置文件：
> ※注意：暴露配置的文件的操作不可逆！
``` shell
npm run eject
```
如果之前没有提及git的话，可能会报以下错误：
```
Remove untracked files, stash or commit any changes, and try again
```
需要先在项目根目录下执行：
``` shell
git add .
git commit -m "初始化项目(备注)"
```
稍等片刻，eject成功，目录变化如下：

``` js
+   ├─ /config
+   |  ├─ /jest
+   |  ├─ env.js
+   |  ├─ module.js
+   |  ├─ paths.js
+   |  ├─ pnpTs.js
+   |  ├─ webpack.config.js   <-- webpack配置文件
+   |  └─ webpackDevServer.config.js
    ├─ /node_modules
    ├─ package.json
    ├─ /public
    |  ├─ favicon.ico
    |  └─ index.html
    ├─ README.md
+   ├─ /scripts
+   |  ├─ build.js
+   |  ├─ start.js
+   |  └─ test.js
    ├─ /src
    |  ├─ /common         <-- 全局公用目录
    |  ├─ /components     <-- 公共模块组件目录
    |  ├─ /pages          <-- 页面组件目录
    |  ├─ App.js          <-- 项目主模块
    |  └─ index.js        <-- 项目入口文件
    └─ yarn.lock
```
### 3.2.1 支持Sass/Scss
eject后，虽然package.json以及webpack.config.js里有了sass相关代码，但是要正确使用Sass/Scss，还要再安装node-sass。

执行以下命令：
``` shell
npm install node-sass -D
  or
yarn add node-sass -D
```
安装完成后，项目已支持Sass/Scss，可以将原css文件后缀名修改为sacc/scss，别忘了把src/index.js中引入的frame.css后缀名修改为sacc/scss。

### 3.2.2 支持Less
支持Less稍微多一点步骤，首先安装less和less-loader：
执行以下命令：
``` shell
npm install less-loader -D
  or
yarn add less-loader -D
```
然后修改config/webpack.config.js：
``` js
    // style files regexes
    const cssRegex = /\.css$/;
    const cssModuleRegex = /\.module\.css$/;
    const sassRegex = /\.(scss|sass)$/;
    const sassModuleRegex = /\.module\.(scss|sass)$/;
+   const lessRegex = /\.less$/;
+   const lessModuleRegex = /\.module\.less$/;
    ...(略)
    // Opt-in support for SASS (using .scss or .sass extensions).
    // By default we support SASS Modules with the
    // extensions .module.scss or .module.sass
    {
        test: sassRegex,
        exclude: sassModuleRegex,
        use: getStyleLoaders(
            {
                importLoaders: 2,
                sourceMap: isEnvProduction && shouldUseSourceMap,
            },
            'sass-loader'
        ),
        // Don't consider CSS imports dead code even if the
        // containing package claims to have no side effects.
        // Remove this when webpack adds a warning or an error for this.
        // See https://github.com/webpack/webpack/issues/6571
        sideEffects: true,
    },
    // Adds support for CSS Modules, but using SASS
    // using the extension .module.scss or .module.sass
    {
        test: sassModuleRegex,
        use: getStyleLoaders(
            {
                importLoaders: 2,
                sourceMap: isEnvProduction && shouldUseSourceMap,
                modules: {
                    getLocalIdent: getCSSModuleLocalIdent,
                  },
            },
            'sass-loader'
        ),
    },
    // 以下这里仿照上面sass的代码，配置下less。
+   {
+       test: lessRegex,
+           exclude: lessModuleRegex,
+           use: getStyleLoaders(
+               {
+                   importLoaders: 2,
+                   sourceMap: isEnvProduction && shouldUseSourceMap,
+               },
+               'less-loader'
+            ),
+           sideEffects: true,
+   },
+   {
+       test: lessModuleRegex,
+       use: getStyleLoaders(
+           {
+               importLoaders: 2,
+               sourceMap: isEnvProduction && shouldUseSourceMap,
+               modules: {
+                   getLocalIdent: getCSSModuleLocalIdent,
+               },
+           },
+           'less-loader'
+       ),
+   },

```
修改后需要执行yarn start重启项目。

然后将原css文件的后缀名修改为less，src/index.js中引入的frame.less，页面已正常解析less。

### 3.2.3 支持Stylus
支持Stylus跟Less完全一样，首先安装stylus和stylus-loader：

执行以下命令：
``` shell
npm install stylus-loader -D
  or
yarn add stylus-loader -D
```
安装完成后，按照上一小节介绍的支持less的方法，修改config/webpack.config.js。完成后重启项目，引入stylus文件可以正常解析了。

我个人习惯使用Stylus，因此后续的讲解中使用Stylus。同时，把src/common/下的style目录也更名为stylus。

``` js
    ├─ /config
    ├─ /node_modules
    ├─ package.json
    ├─ /public
    ├─ README.md
    ├─ /scripts
    ├─ /src
    |  ├─ /common       <-- 全局公用目录    
    |  |  ├─ /fonts       
    |  |  ├─ /images 
    |  |  ├─ /js 
M   |  |  └─ /stylus
M   |  |  |  ├─ frame.styl
M   |  |  |  ├─ reset.styl
M   |  |  |  └─ global.styl  
    |  ├─ /components   <-- 公共模块组件目录
    |  ├─ /pages        <-- 页面组件目录
    |  ├─ App.js        <-- 项目主模块
    |  └─ index.js      <-- 项目入口文件
    └─ yarn.lock
```
最基本的配置搞定了，接下来要开始引入页面（pages）了。页面的切换需要使用路由（Router），请继续阅读下面的章节。

## 4 路由
### 4.1 页面构建
首先，构建home和login页面。

src/pages/home/index.js代码：
``` js
import React, { Component } from 'react'
import './home.styl'

class Home extends Component {
    render() {
        return (
            <div className="P-home">
                <h1>Home page</h1>
            </div>
        )
    }
}

export default Home
```
src/pages/home/home.styl代码
``` css
.P-home
    h1
        padding: 20px 0
        font-size: 30px
        color: #fff
        background: #67C23A
        text-align: center
```
src/pages/login/index.js代码：
``` js
import React, { Component } from 'react'
import './login.styl'

class Login extends Component {
    render() {
        return (
            <div className="P-login">
                <h1>Login page</h1>
            </div>
        )
    }
}

export default Login
```

接下来，我们使用react-router-dom实现路由。

### 4.2 使用react-router-dom
执行安装命令：
``` shell
npm install react-router-dom --save
  or
yarn add react-router-dom

```
修改src/App.js，代码如下：

``` js
import React, { Fragment } from 'react'
import Login from './pages/login'
import Home from './pages/home'
import { HashRouter, Route, Switch, Redirect } from 'react-router-dom'

function App() {
  return (
    <Fragment>
      <HashRouter>
        <Switch>
          <Route path="/login" component={Login} />
          <Route path="/home" component={Home} />
          <Route exact path="/" component={Home} />
          <Redirect to={"/home"} />
        </Switch>
      </HashRouter>
    </Fragment>
  )
}

export default App
```
App.js引入了Home和Login两个页面级组件。然后使用react-router-dom分别设置了路径。

import的机制是默认寻找index.js，所以每个组件的主文件名设为index.js，在引用的时候就可以省略文件名。

这里说明一下`<Route>`的属性：
- path表示路径，这个很好理解。
- component表示绑定的组件。
- exact表示是否精确匹配。

如果没有设置exact，那么：
- localhost:3000/会显示Home页，
- localhost:3000/abc也会显示Home页。
- 因为匹配到了前面的"/"，路由就会成功。

最后的`<Redirect>`表示以上都没有匹配成功的会，默认跳转的路由。

### 4.3 路由跳转
接下来，简单介绍下如果在页面之间进行路由跳转。

在Login页面添加一个用于跳转至Home页的按钮，代码修改如下：
``` js
    import React, { Component } from 'react'
    import './login.styl'

    class Login extends Component {
        render() {
            return (
               <div className="P-login">
                    <h1>Login page</h1>
+                   <button onClick={this.gotoHome.bind(this)}>跳转Home页</button>
                </div>
            )
        }

+       gotoHome() {
+           this.props.history.push('/home')
+       }
    }

    export default Login
```
注意button的onClick里要bind(this)，否则，在gotoHome里的this是undefined。

当然，也可以这么写：
``` js
<button onClick={() => {this.gotoHome()}}>跳转Home页</button>
```
最终目的都是要让gotoHome中的this指向正确的作用域。

## 5 组件引入
这章节内容也很容易，接触过vue的同学应该也很清楚，为了教程的完整性，还是简单说一下。下面来简单实现一个公用的头部组件。

### 5.1 创建header组件
目录结构如下：
``` shell
    |  ├─ /components   <-- 公共模块组件目录
+   |  |  ├─ /header    <-- 公用header组件
+   |  |  |  ├─ index.js 
+   |  |  |  └─ header.styl 
```
src/components/header/index.js代码：
``` js
import React, { Component } from 'react'
import './header.styl'

class Header extends Component {
    render() {
        return (
            <div className="M-header">
                Header
            </div>
        )
    }
}

export default Header
```
src/components/header/header.styl代码：
``` css
.M-header
    height: 40px
    line-height: 40px
    font-size: 36px
    color: #fff
    background: #409EFF
```
### 5.2 引入Header组件
然后，在Home和Login页面里引入Header组件。

以Home页面为例，修改src/pages/home/index.js：
``` js
    import React, { Component } from 'react'
+   import Header from '../../components/header'
    import './home.styl'

    class Home extends Component {
        render() {
            return (
                <div className="P-home">
+                   <Header />
                    <h1>Home page</h1>
                </div>
            )
        }
    }

    export default Home
```
### 5.3 组件传参
使用过vue的同学都知道，vue组件有data和props。对应react的是state和props。

react向子组件传参使用以下方式：
``` js
<Header 
    param1="abc"
    param2="c"
    func1={()=>{console.log('func1')}}
/>
```
在Header组件内部，直接使用this.props就可以接收。例如：this.props.param1。

## 6 React Developer Tools浏览器插件
为了更方便调试react项目，建议安装chrome插件。

先科学上网，在chrome网上应用店里搜索“React Developer Tools”并安装。

安装完成后，打开chrome调试工具，可以清晰的看到react项目代码结构。

## 7 Redux及相关插件
做过vue开发的同学都知道vuex，react对应的工具就是Redux，当然还有一些附属工具，比如react-redux、redux-thunk、immutable。
### 7.1 安装redux
执行：
``` shell
npm install  redux --save
or
yarn add redux
```
仅安装redux也是可以使用的，但是比较麻烦。redux里更新store里的数据，需要手动订阅(subscribe)更新。可以借助另一个插件（react-redux）提高开发效率。

### 7.2 安装react-redux
执行：
``` shell
npm install  react-redux --save
or
yarn add react-redux
```

react-redux允许通过connect方法，将store中的数据映射到组件的props，省去了store订阅。原state中读取store的属性改用props读取。

### 7.3 安装redux-thunk
执行：
``` shell
npm install  redux-thunk --save
or
yarn add redux-thunk
```

redux-thunk允许在actionCreators里返回函函数。这样可以把业务逻辑（例如接口请求）集中写在actionCreator.js，方便复用的同时，可以使组件的主文件更简洁。

### 7.4 安装浏览器Redux插件
为了更方便跟踪redux状态，建议安装chrome插件。

先科学上网，在chrome网上应用店里搜索“Redux DevTools”并安装。

安装完成后还不能直接使用，需要在项目代码中进行配置。接下来进行说明。

### 7.5 创建store
安装以上各种插件后，可以store用来管理状态数据了。

如果项目比较简单，只有一两个页面，可以只创建一个总store管理整体项目。目录结构参考如下
``` shell
    ├─ /src   
+   |  ├─ /store
+   |  |  ├─ actionCreators.js
+   |  |  ├─ contants.js       <-- 定义方法的常量
+   |  |  ├─ index.js
+   |  |  └─ reducer.js
```
以下是各文件的代码示例：

src/store/actionCreators.js:
```
import * as constans from './constants'

export const getData = (data) => ({
  type: constans.SET_DATA,
  data
})
```
src/store/contants.js:
``` js
export const SET_DATA = 'SET_DATA'
```
src/store/index.js:
``` js
import { createStore, applyMiddleware, compose } from 'redux'
import reducer from './reducer'
import thunk from 'redux-thunk'

// 这里让项目支持浏览器插件Redux DevTools
const composeEnhancers = typeof window === 'object' &&
  window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ?
  window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}) : compose

const enhancer = composeEnhancers(
  applyMiddleware(thunk)
);

const store = createStore(
  reducer,
  enhancer
)

export default store
```
> 以上是store的核心代码，支持了Redux DevTools。同时，利用redux的集成中间件（applyMiddleware）功能将redux-thunk集成进来，最终创建了store。

src/store/reducer.js:
``` js
import * as constants from './constants'

// 初始默认的state
const defaultState = {
    myData: null
}

export default (state = defaultState, action) => {
    // 由于state是引用型，不能直接修改，否则是监测不到state发生变化的。因此需要先复制一份进行修改，然后再返回新的state。
    let newState = Object.assign({}, state)
    switch(action.type) {
        case constants.SET_DATA:
            newState.myData = action.data
            return newState
        default:
            return state
    }
}
```
以上代码，我们在store设置了一个myData。如何更好地解决state修改问题


### 7.6 复杂项目store分解
应对更多页面的项目，如果数据都集中放在一个store里，其维护成本非常高。接下来分享下如何将store分解到各个组件中。

一般来说，每个组件有自己的store，再由src下的store作为总集，集成每个组件的store。

以header和login两个组件为例，分别创建组件自己的store。

header的store目录结构如下：

``` shell
    |  |  ├─ /store
    |  |  |  ├─ /modules
+   |  |  |  |  ├─ /header
+   |  |  |  |  |  ├─ actionCreators.js
+   |  |  |  |  |  ├─ contants.js      
+   |  |  |  |  |  ├─ index.js
+   |  |  |  |  |  └─ reducer.js
```
module下store下的index.js代码如下：
``` js
import reducer from './reducer'
import * as actionCreators from './actionCreators'
import * as constants from './constants'

export { reducer, actionCreators, constants}
```
其实就是把组件store下的其他文件集中起来作为统一输出口。

modules下store下的contants.js代码如下：
``` js
const ZONE = 'components/header/'

export const SET_DATA = ZONE + 'SET_DATA'
```
ZONE是用来避免与其他组件的contants重名。

同样的方式，在login下进行创建store（不再赘述）

然后修改项目src下的总store，目录结构变动如下：

src/store/index.js重写如下：
``` js
import { combineReducers } from 'redux'

import { reducer as loginReducer } from './modules/login'
import { reducer as headerReducer } from './modules/header'

const reducer = combineReducers({
    login: loginReducer,
    header: headerReducer
})

export default reducer
```
以上代码的作用就是把login和header的store引入，然后通过combineReducers合并在一起，并分别加上唯一的对象key值。

这样的好处非常明显：

- 避免各组件的store数据互相污染
- 组件独立维护自己的store，减少维护成本

非常建议使用这种方式维护store。

### 7.7 对接react-redux与store

为了方便每个组件都能使用store，而不用一遍一遍的引用store。下面来对接react-redux与store。

修改src/index.js:

```  js
    import React from 'react'
    import ReactDOM from 'react-dom'
    import App from './App'
+   import { Provider } from 'react-redux'
+   import store from './store'
    import './common/style/frame.styl'

+   const Apps = (
+       <Provider store={store}>
+           <App />
+       </Provider>
+   )

M   ReactDOM.render(Apps, document.getElementById('root'))

```
以上代码就是用react-redux提供的Provider，把store传给了整个App。

在需要使用store的组件中，要使用react-redux提供的connect方法对组件进行包装。

以login为例，修改src/pages/login/index.js:
``` js
    import React, { Component } from 'react'
    import Header from '../../components/header'
+   import { connect } from 'react-redux'
+   import * as actionCreators from './store/actionCreators'
    import './login.styl'

    class Login extends Component {
        render() {
            return (
                <div className="P-login">
                    <Header />
                    <h1>Login page</h1>
+                   <p>login: myData = {this.props.myData}</p>
+                   <button onClick={()=> {this.props.getData('123456')}}>更改login的myData</button>
                    <button onClick={this.gotoHome.bind(this)}>跳转Home页</button>
                </div>
            )
        }

        gotoHome() {
            this.props.history.push('/home')
        }
    }

+   // 把store中的数据映射到组件的props
+   const mapStateToProps = (state) => ({
+       myData: state.getIn(['login', 'myData']),
+   })

+   // 把store的Dispatch映射到组件的props
+   const mapDispatchToProps = (dispatch) => ({
+       getData(data) {
+           const action = actionCreators.getData(data)
+           dispatch(action)
+       }
+   })

M   export default connect(mapStateToProps, mapDispatchToProps)(Login)
```
最大的变化就是代码最后一行，被connect方法包装了。

然后把store里的state和dispatch都映射到了组件的props。这样可以直接通过props进行访问了，store中数据的变化会直接改变props从而触发组件的视图更新。

## 8 Mock.js安装与使用
在开发过程中，为了方便前端独自调试接口，经常使用Mock.js拦截Ajax请求，并返回预置好的数据。本小节介绍下如何在react项目中使用Mock.js。

执行安装：
``` shell
npm install mockjs -D
or
yarn add mockjs -D
```

在src下新建mock.js，代码如下：
``` js
import Mock from 'mockjs'

const domain = '/api/'

// 模拟getData接口
Mock.mock(domain + 'getData', function () {
    let result = {
      code: 200,
      message: 'OK',
      data: 'test'
    }
    return result
})
```

然后在src/index.js中引入mock.js:
``` js
    import React from 'react'
    import ReactDOM from 'react-dom'
    import App from './App'
    import { Provider } from 'react-redux'
    import store from './store'
+   import './mock'
    import './common/style/frame.styl'

    ...（略）
```
如此简单。这样，在项目中请求/api/getData的时候，就会被Mock.js拦截，并返回mock.js中写好的数据

## 9 解决本地开发跨域问题
在react开发环境中，默认启动的是3000端口，而后端API服务可能在本机的80端口，这样在ajax请求的时候会出现跨域问题。可以借助http-proxy-middleware工具实现反向代理。
执行安装：
``` shell
npm install http-proxy-middleware -D
or
yarn add http-proxy-middleware -D
```
在src下创建setupProxy.js，代码如下：
``` js
const proxy = require('http-proxy-middleware');
module.exports = function (app) {
    app.use(
        '^/api',
        proxy({
            target: 'http://localhost:5000',
            changeOrigin: true
        })
    )
}
```
> 注意： 你无需在任何位置导入此文件。 它在启动开发服务器时会自动注册。

> 注意： 此文件仅支持 Node 的 JavaScript 语法。 请务必仅使用支持的语言特性（即不支持 Flow ，ES Modules 等）

## CSS Modules
CSS Modules 的做法就是通过配置将.css文件进行编译，编译后在每个用到css的组件中的css类名都是独一无二的，从而实现CSS的局部作用域。

create-react-app自从2.0.版本就已经开始支持CSS Modules了

### 局部样式
``` js
命名规则: xxx.module.css     

引入方式 import xxx from 'xxx.module.css'

用法：<div className={xxx.styleName}>
```
### 全局样式
```  js
命名规则: xxx.css   

引入方式 import ‘xxx.css’

用法：<div className='styleName'>
``` 
### 案例：
`person.module.css`
``` css

.person{
    width: 60%;
    margin:16px auto;
    border: 1px solid #eee;
    box-shadow: 0 2px 3px #ccc;
    padding:16px;
    text-align: center;
}
```
`person.js`
``` js
import React, { Component } from 'react';

//局部样式
import styles from './Person.module.css';

//全局样式
import '../App.css'
class App extends Component {
  
  render() {

    return (
      <div className={styles.person}>
        <p className='fz'>person:Hello world</p>
      </div> 
    )
  }
}

export default App;
```