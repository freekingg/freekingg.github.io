---
title: 'react组件的6种通信方式'
date: 2020-4-04 10:36:20
tags:
- react
- 知识点
categories:
- [前端]
- [react]
---
问React组件有哪些通信方式？

- 1.props父子通信
- 2.回调函数，子父通信
- 3.变量提升，兄弟组建通信
- 4.Context,跨组建通信
- 5.node的events模块的单例通信
- 6.redux共享数据通信

<!-- more -->

## 1.props父子通信
``` js
function Children(props) {
      return (
        <div>
          <p>Children</p>
          <p>{props.text}</p>
        </div>
      )
    }

function Parent() {
  return (
    <div>
      <p>Parent</p>
      <Children text="这是爸爸传给你的东西"></Children>
    </div>
  )
}
    
export default Parent

```

## 2.回调函数，子父通信
``` js
function Children(props) {
  return (
    <div>
      <p>Children</p>
      <p>{props.text}</p>
      <button 
        onClick={() => { props.handleChange('改变了') }}>
        点击我改变爸爸传给我的东西
      </button>
    </div>
  )
}

function Parent() {
  let [text, setText] = useState('这是爸爸传给你的东西')
  function handleChange(val) {
    setText(val)
  }
  return (
    <div>
      <p>Parent</p>
      <Children handleChange={handleChange} text={text}></Children>
    </div>
  )
}
export default Parent

```

## 3.变量提升，兄弟组建通信
``` js
function Children(props) {
  return (
    <div>
      <p>Children</p>
      <button 
        onClick={() => { props.setText('我是Children发的信息') }}>
        给Children1发信息</button>
    </div>
  )
}
function Children1(props) {
  return (
    <div>
      <p>Children1</p>
      <p>{props.text}</p>
    </div>
  )
}

function App() {
  let [text, setText] = useState('')
  return (
    <>
      <div>APP</div>
      <Children setText={setText}></Children>
      <Children1 text={text}></Children1>
    </>
  )
}
export default App
```

## 4.Context,跨组建通信
``` js
const { Consumer, Provider } = React.createContext()

class Children extends React.Component {
  render() {
    return (
      <Consumer>
        {
          (value) => (
            <div>
              <p>Children1</p>
              <p>{value.text}</p>
            </div>
          )
        }
      </Consumer>
    )
  }
}

class Parent extends React.Component {
  render() {
    return (
      <Provider value={{ text: '我是context' }}>
        <div>
          <p>Parent</p>
          <Children1></Children1>
        </div>
      </Provider>
    )
  }
}

export default Parent
```

## 5.node的events模块的单例通信
``` js
function Children(props) {
  return (
    <div>
      <p>Children</p>
      <p>{props.text}</p>
      <button 
        onClick={() => { props.event.emit('foo') }}>点击我改变爸爸传给我的东西</button>
    </div>
  )
}

function Parent() {
  let [text, setText] = useState('这是爸爸传给你的东西')
  let event = new Events()
  event.on('foo', () => { setText('改变了') })
  return (
    <div>
      <p>Parent</p>
      <Children event={event} text={text}></Children>
    </div>
  )
}
export default Parent
```
注意
> 这种通信记住在顶部引入events模块，无需安装，node自身模块。

## 6.redux共享数据通信
store.js
``` js
import { createStore } from 'redux'

let defaultState = {
    text: '我是store'
}

let reducer = (state = defaultState, action) => {
    switch (action) {
        default: return state
    }
}

export default createStore(reducer)
```
child.js
``` js
import React from 'react'

import { connect } from 'react-redux'

class Child extends React.Component {
    render() {
        return (
            <div>Child<p>{this.props.text}</p></div>
        )
    }
}

let mapStataToProps = (state) => {
    return {
        text: state.text
    }
}

export default connect(mapStataToProps, null)(Child)
```
Parent.js
``` js
class Parent extends React.Component {
  render() {
    return (
      <Provider store={store}>
        <div>
          <p>Parent</p>
          <Child></Child>
        </div>
      </Provider>
    )
  }
}

export default Parent
```