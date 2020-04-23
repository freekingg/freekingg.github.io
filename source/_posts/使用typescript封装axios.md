---
title: 使用typescript封装axios
date: 2020-04-22 12:25:51
tags:
- typescript
- axios
categories:
- [前端]
- [typescript]
---

我们在开发中广泛使用的axios调用接口，为了更好地调用，做一些全局的拦截，我们通常会对其进行一下封装，本小节我们就来看下如何使用TypeScript对它进行封装，使其同时能够有很好的类型支持。

首先我们需要安装axios
``` js
npm install axios
```
<!-- more -->
我们在src文件夹下创建utils文件夹，然后在utils文件夹创建*axios.ts文件，axios这个插件自带声明文件，所以我们不需要另外安装了。先来看下最基础的封装：

``` js
// 引入axios和定义在node_modules/axios/index.ts文件里的类型声明
import axios, { AxiosInstance, AxiosRequestConfig, AxiosPromise,AxiosResponse } from 'axios'; 

// 定义一个接口请求类，用于创建一个axios请求实例
class HttpRequest { 

    // 这个类接收一个字符串参数，是接口请求的基本路径
  constructor(public baseUrl: string) { 
    this.baseUrl = baseUrl;
  }

  public request(options: AxiosRequestConfig): AxiosPromise { // 我们实际调用接口的时候调用实例的这个方法，他返回一个AxiosPromise

  // 这里使用axios.create方法创建一个axios实例，他是一个函数，同时这个函数包含多个属性
    const instance: AxiosInstance = axios.create() 

// 合并基础路径和每个接口单独传入的配置，比如url、参数等
    options = this.mergeConfig(options) 

    // 调用interceptors方法使拦截器生效
    this.interceptors(instance, options.url) 

     // 最后返回AxiosPromise
    return instance(options)
  }

  // 定义这个函数用于添加全局请求和响应拦截逻辑
  private interceptors(instance: AxiosInstance, url?: string) {
    // 在这里添加请求和响应拦截
  }

  // 这个方法用于合并基础路径配置和接口单独配置
  private mergeConfig(options: AxiosRequestConfig): AxiosRequestConfig { 
    return Object.assign({ baseURL: this.baseUrl }, options);
  }
}
export default HttpRequest;
```

我们封装了这个类之后，里面的baseUrl一般本地开发环境的基础路径和线上生产环境的基础路径是不一样的，所以可以根据当前是开发环境还是生产环境做判断，应用不同的基础路径。这里我们要写在一个配置文件里，我们在src文件夹下创建一个config文件夹，然后在这个文件夹创建一个index.ts文件，在里面配置生产和开发环境接口基础路径：

``` js
// src/config/index.ts
export default {
    api: {
        devApiBaseUrl: '/test/api/xxx',
        proApiBaseUrl: '/api/xxx',
    },
};
```

然后我们在`axios.ts`文件里直接引入接口配置，然后判断当前环境：

``` js
// ... 其他代码省略
import config from '@/config'; 
const { api: { devApiBaseUrl, proApiBaseUrl } } = config;
const apiBaseUrl = process.env.NODE_ENV === 'production' ? proApiBaseUrl : devApiBaseUrl;
// ... 其他代码省略
```

接下来我们就可以将这个apiBaseUrl作为默认值传入HttpRequest的参数：

``` js
// ...
class HttpRequest { // 定义一个接口请求类，用于创建一个axios请求实例
  constructor(public baseUrl: string = apiBaseUrl) { // 这个类接收一个字符串参数，是接口请求的基本路径
    this.baseUrl = baseUrl;
  }
  // ...
// ...
```

接下来我们来完善拦截器，在类中interceptors方法内添加请求拦截器和响应拦截器，实现对所有接口请求的统一处理：

``` js
private interceptors(instance: AxiosInstance, url?: string) { // 定义这个函数用于添加全局请求和响应拦截逻辑
    // 在这里添加请求和响应拦截
    instance.interceptors.request.use((config: AxiosRequestConfig) => {
      // 接口请求的所有配置，都在这个config对象中，他的类型是AxiosRequestConfig，你可以看到他有哪些字段
      // 如果你要修改接口请求配置，需要修改 axios.defaults 上的字段值
      return config
    },
    (error) => {
      return Promise.reject(error)
    })
    instance.interceptors.response.use((res: AxiosResponse) => {
      const { data } = res // res的类型是AxiosResponse<any>，包含六个字段，其中data是服务端返回的数据
      const { code, msg } = data // 通常服务端会将响应状态码、提示信息、数据等放到返回的数据中
      if (code !== 0) { // 这里我们在服务端将正确返回的状态码标为0
        console.error(msg) // 如果不是0，则打印错误信息，我们后面讲到UI组件的时候，这里可以使用消息窗提示
      }
      return res // 返回数据
    },
    (error) => { // 这里是遇到报错的回调
      return Promise.reject(error)
    })
  }
```

请求时的拦截通过给`instance.interceptors.request.use`传入回调函数来处理，这个回调函数有一个参数，就是这次请求的配置对象。如果你需要修改请求的配置，需要修改`axios.defaults`上的字段，来修改全局配置。比如你要在`header`中添加字段`"Content-Type"`，需要这样添加：
``` js
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded'
```


到这里我们这个axios请求类就封装好了，上面我们讲了一般服务端会将状态码、提示信息和数据封装在一起，然后作为数据返回，所以我们调用所有api请求返回的数据格式都是一样的，那么我们就可以定义一个接口来指定返回的数据的结构，我们在src/utils/axios.ts文件中定义class HttpRequest前加一个接口定义，并且导出：
``` js
export interface ResponseData {
  code: number
  data?: any
  msg: string
}
```

你可以根据你服务端实际返回的情况，来定义这个接口ResponseData，我这里data字段设为可选的。

接下来我们来简单使用一下。在src文件夹下创建一个api文件夹，然后再这个文件夹创建一个index.ts文件，在这里创建请求实例：

``` js
// src/api/index.ts
import HttpRequest from '@/utils/axios'
export * from '@/utils/axios'
export default new HttpRequest()
```

我们把这个请求类引进来，然后默认导出一个这个类的实例。后面我们要进行接口请求的时候，要对接口进行分类，比如和用户相关的，和数据相关的等等。这里我们先添加一个登陆接口的请求，所以在api文件夹下创建一个user.ts文件，在这里创建一个登陆接口请求方法：

``` js
// src/api/user.ts
import axios, { ResponseData } from './index'
import { AxiosPromise } from 'axios'

interface LoginReqArguInterface {
  user_name: string;
  password: number|string
}
export const loginReq = (data: LoginReqArguInterface): AxiosPromise<ResponseData> => {
  return axios.request({
    url: '/api/user/login',
    data,
    method: 'POST'
  })
}
```

这里我们封装登录请求方法loginReq，指定他需要一个参数，参数类型要符合我们定义的`LoginReqArguInterface`接口指定的结构，这个方法返回一个类型为`AxiosPromise`的`Promise`，`AxiosPromise`这个接口是axios声明文件内置的类型，他可以传入一个泛型变量参数，用于指定api请求返回的结果中data字段的类型。

为什么我们要这样封装api的请求，而不是直接在需要调用接口的地方使用axios.get或者axios.request这些方法直接请求呢？这是因为我们一个api请求可能多个地方需要用到，这里的登录接口复用性很低，但是一些其他接口比如获取数据的接口，可能多个地方会用到，所以我们封装一下，可以统一管理，以后接口升级，就只需要改一个地方就可以了。

接下来我们调用一个这个请求试一下，我们在src/views/Home.vue文件里先调用一下，只是为了先感受一下调用请求的方式，登录接口我们会在下个小节放到登录页实现。

打开Home.vue文件后，我们首先要引入这个请求方法：

``` js
import { loginReq } from '@/api/user'
```

然后下面我们在Home这个组件类里添加mounted声明周期钩子函数，在这里调用这个api请求方法：

``` js
export default class Home extends Vue {
  public mounted() {
    loginReq({ user_name: 'Lison', password: 123 }).then((res) => {
      console.log(res.data.code)
    })
  }
}
```

## 完整代码
**utils/axios.ts**
```js
// axios.ts
import axios, { AxiosInstance, AxiosRequestConfig, AxiosPromise, AxiosResponse } from 'axios'
import config from '@/config'
const { api: { devApiBaseUrl, proApiBaseUrl } } = config
const apiBaseUrl = process.env.NODE_ENV === 'production' ? proApiBaseUrl : devApiBaseUrl

export interface ResponseData {
  code: number
  data?: any
  msg: string
}

class HttpRequest { // 定义一个接口请求类，用于创建一个axios请求实例
  constructor(public baseUrl: string = apiBaseUrl) { // 这个类接收一个字符串参数，是接口请求的基本路径
    this.baseUrl = baseUrl
  }
  public request(options: AxiosRequestConfig): AxiosPromise { // 我们实际调用接口的时候调用实例的这个方法，他返回一个AxiosPromise
    const instance: AxiosInstance = axios.create() // 这里使用axios.create方法创建一个axios实例，他是一个函数，同时这个函数包含多个属性，就像我们前面讲的计数器的例子
    options = this.mergeConfig(options) // 合并基础路径和每个接口单独传入的配置，比如url、参数等
    this.interceptors(instance, options.url) // 调用interceptors方法使拦截器生效
    return instance(options) // 最后返回AxiosPromise
  }
  private interceptors(instance: AxiosInstance, url?: string) { // 定义这个函数用于添加全局请求和响应拦截逻辑
    // 在这里添加请求和响应拦截
    instance.interceptors.request.use((config: AxiosRequestConfig) => {
      // 接口请求的所有配置，都在这个config对象中，他的类型是AxiosRequestConfig，你可以看到他有哪些字段
      // 如果你要修改接口请求配置，需要修改 axios.defaults 上的字段值
      return config
    },
    (error) => {
      return Promise.reject(error)
    })
    instance.interceptors.response.use((res: AxiosResponse) => {
      const { data } = res // res的类型是AxiosResponse<any>，包含六个字段，其中data是服务端返回的数据
      const { code, msg } = data // 通常服务端会将响应状态码、提示信息、数据等放到返回的数据中
      if (code !== 0) { // 这里我们在服务端将正确返回的状态码标为0
        console.error(msg) // 如果不是0，则打印错误信息，我们后面讲到UI组件的时候，这里可以使用消息窗提示
      }
      return res // 返回数据
    },
    (error) => { // 这里是遇到报错的回调
      return Promise.reject(error)
    })
  }
  private mergeConfig(options: AxiosRequestConfig): AxiosRequestConfig { // 这个方法用于合并基础路径配置和接口单独配置
    return Object.assign({ baseURL: this.baseUrl }, options)
  }
}
export default HttpRequest

```

**api/index.ts**
``` js
// api/index.ts

import HttpRequest from '@/utils/axios'
export * from '@/utils/axios'
export default new HttpRequest()
```

**api/user.ts**
``` js
import axios, { ResponseData } from './index'
import { AxiosPromise } from 'axios'

interface LoginReqArguInterface {
    user_name: string
    password: number|string
}
export const loginReq = (data: LoginReqArguInterface): AxiosPromise<ResponseData> => {
    return axios.request({
        url: '/api/user/login',
        data,
        method: 'POST'
    })
}

export const getInfoReq = (): AxiosPromise<ResponseData> => {
    return axios.request({
        url: '/api/user/get_info',
        method: 'GET'
    })
}
```