---
title: Vue.js你不知道的一些小技巧
date: 2019-02-16 15:50:33
tags:
- vue
- 技巧
categories:
- [前端]
- [vue]
---

## 给 props 属性设置多个类型
这个技巧在开发组件的时候用的较多，为了更大的容错性考虑，同时代码也更加人性化：

``` js
export default {
  props: {
    width: {
      type: [String, Number],
      default: '100px'
    }
    // 或者这样
    // width: [String, Number]
  }
}
```
<!-- more -->
比如一个 <my-button> 上暴露了一个 width 属性，我们既可以传 100px，也可以传 100 ：

``` html
<!-- my-button.vue -->
<template>
  <button :style="computedWidth">{{ computedWidth }}</button>
</template>

<script>
  export default {
    props: {
      width: [String, Number]
    },
    computed: {
      computedWidth () {
        let o = {}
        if (typeof this.width === 'string') o.width = this.width
        if (typeof this.width === 'number') o.width = this.width + 'px'
        return o
      }
    }
  }
</script>
```
使用：
``` html
<!-- 在其他组件中使用 -->
<template>
  <my-button width="100px"></my-button>
  <!-- or -->
  <my-button :width="100"></my-button>
</template>
```

## 禁止浏览器 Auto complete 行为
有时候我们输入账号密码登录后台系统，浏览器会弹出是否保存你的登录信息。我们一般会点击保存，因为下次再次登录的时候会自动填充增加了我们的用户体验，很好。

但有时，当我们开发某个某块（比如新增用户）时，点击新增用户按钮，显示弹框，不巧的是，在账号，密码输入框中浏览器帮我们填充上了。但这并不是我们想要的。所以，我的有效解决思路如下：

- 设置 `<el-input/>` 为只读模式
- 在 focus 事件中去掉只读模式

代码如下：
``` html
<el-input
  type="text"
  v-model="addData.loginName"
  readonly
  @focus="handleFocusEvent"
/>
```
``` js
...
methods: {
  handleFocusEvent(event) {
    event.target && event.target.removeAttribute('readonly')
  }
}
```

## 阻止 `<el-form>` 默认提交行为
有时候我们在用饿了么组件 `<el-form>` 在文本框中键入 enter 快捷键的时候会默认触发页面刷新。我们可以加入如下代码解决其默认行为：
``` html
<el-form @submit.native.prevent>
  ...
</el-form>
```

## data 初始化
因为 props 要比 data 先完成初始化，所以我们可以利用这一点给 data 初始化一些数据进去，看代码：
``` js
export default {
  data () {
    return {
      buttonSize: this.size
    }
  },
 props: {
   size: String
 }
}
```
除了以上，子组件的 data 函数也可以有参数，且该参数是当前实例对象。所有我们可以利用这一点做一些自己的判断。如，改写上面的代码：
``` js
export default {
  data (vm) {
    return {
      buttonSize: vm.size
    }
  },
 props: {
   size: String
 }
}
```

## template
我们在做 v-if 判断的时候，可以把判断条件放在 template 组件上，最终的渲染结果将不包含 `<template>` 元素。
``` html
<template>
  <div class="box">
    <template v-if="isVal">
      <h2>...</h2>
    </template>
    <template v-else>
      <h2>...</h2>
    </template>
  </div>
</template>
```
v-for 也同样适用。

## render 函数
下面是一段简单的 template 模板代码：
``` html
<template>
  <div class="box">
    <h2>title</h2>
    this is content
  </div>
</template>
```
我们用渲染函数来重写上面的代码：
``` js
export default {
  render (h) {
    let _c = h
    return _c('div', 
      { class: 'box'}, 
      [_c('h2', {}, 'title'), 'this is content'])
  }
}
```

## vuex 在页面刷新后状态丢失解决办法
刷新页面后，存在 vuex 的数据会丢失，给调试带来不便。把用户的登录信息放到 sessionStorage 中避免丢失。
``` js
const USER_INFO = "userInfo";

export default new Vuex.Store({
  state: {
    userInfo: JSON.parse(sessionStorage.getItem(USER_INFO))
  },
  mutations: {
    setUserInfo(state, userInfo){
      //存储到 sessionStorage 中以防刷新页面后状态丢失
      sessionStorage.setItem(USER_INFO, JSON.stringify(userInfo));
      state.userInfo = userInfo
    }
  }
}
```

## 修改页面 Title
首先在 router.js 里，每个路由加上 meta ，设置 title
``` js
routes: [
  {
    path: '/login',
    name: 'login',
    component: Login,
    meta:{
      title:'登录'
    }
  },
  {
    path: '/home',
    name: 'home',
    component: Home,
    children: [],
    meta:{
      title:'主页'
    }
  }
]
```
然后在 main.js 里通过前置路由动态修改 title
``` js
router.beforeEach((to, from, next) => {
  /* 路由发生变化修改页面title */
  if (to.meta.title) {
    document.title = to.meta.title;
  }
  next();
})
```


