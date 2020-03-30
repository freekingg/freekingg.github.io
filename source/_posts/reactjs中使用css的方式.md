---
title: 'reactjs中使用css的方式'
date: 2020-03-29 23:06:52
tags:
- react
- 知识点
categories:
- [前端]
- [react]
---

由于 React的JSX语法，能在React中使用样式的方式有很多，本文主要介绍在React中经常使用CSS样式的五种方法：

- 1、行内样式
- 2、声明样式
- 3、引入样式
- 4、CSS Modules模块化
- 5、Styled-component
<!-- more -->
## 行内样式
行内样式是一种最基本的写法，也就是我们最开始学HTML时写的内联样式那样，在项目中可能会比较少用到
``` js
class App extends React.Component {
  // ...
  render() {
    return (
      <div style={{ background: '#eee', width: '200px', height: '200px'}}>
        <p style= {{color:'red', fontSize:'40px'}}>行内样式</p>
      </div>
    )
  }
}
```
## 声明样式
声明样式其实是行内样式的一种改进写法，在render函数外部创建style对象，然后传递给组件，让css与标签分离
``` js
class App extends React.Component {
//...
 const style1={    
      background:'#eee',
      width:'200px',
      height:'200px'
    }

  const style2={    
      color:'red',
      fontSize:'40px'
    }

  render() {
    return (
      <div style={style1}>
        <p style= {style2}>行内样式</p>
      </div>
    )
  }
}
```

## 引入样式
引入样式就是将CSS文件写下外部，在引入jsx使用
``` css
/* person.css */

.person{
    width: 60%;
    margin:16px auto;
    border: 1px solid #eee;
    box-shadow: 0 2px 3px #ccc;
    padding:16px;
    text-align: center;
}
```
``` js
// App.js
import React from 'react';
import './Person.css';
class App extends React.Component {
  //....  
  render() {

    return (
      <div className='person'>
        <p>person:Hello world</p>
      </div> 
    )
  }
}

export default App;
```
 **注意**
 > 因为CSS的规则都是全局的，任何一个组件的样式规则，都对整个页面有效，这可能会导致大量的冲突。

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

## Styled Components

随着组件化时代的来临，前端应用开始从组件的层面对 CSS 进行封装：也就是通过 JS 来声明、抽象样式从而提高组件的可维护性；在组件加载时动态的加载样式，动态生成类名从而避免全局污染。Styled Component以组件的形式来声明样式，让样式也成为组件从而分离逻辑组件与展示组件

Styled Component是react的一个第三方库，是CSS in JS 的优秀实践和代表，将CSS写在JS中，可以实现常规CSS所不好处理的逻辑复杂、函数方法、复用、避免干扰。样式书写将直接依附在JSX上面，HTML、CSS、JS三者再次内聚，同时也实现H5的语义化标签表现形式。

``` js
npm install --save styled-components
```

``` js
// 创建一个 Title 组件,它将渲染一个附加了样式的 <h1> 标签
const Title = styled.h1`
  font-size: 1.5em;
  text-align: center;
  color: palevioletred;
`;

// 创建一个 Wrapper 组件,它将渲染一个附加了样式的 <section> 标签
const Wrapper = styled.section`
  padding: 4em;
  background: papayawhip;
`;

// 就像使用常规 React 组件一样使用 Title 和 Wrapper 
render(
  <Wrapper>
    <Title>
      Hello World!
    </Title>
  </Wrapper>
);
```
