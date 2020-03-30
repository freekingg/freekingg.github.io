---
title: axios-大众版封装
date: 2020-03-30 16:10:27
tags:
- axios
categories:
- [前端]
- [工具]
---

直接上代码吧，比较简单

``` js
import axios from 'axios';
import router from 'router/index';
import { TIMEOUT } from 'helpers/config';
import { getToken } from '../helpers/cache';
import qs from 'querystring';
import Message from 'components/dc-message/index';

// 配置一
const service = axios.create({
  baseURL: process.env.VUE_APP_URL,
  timeout: TIMEOUT,
  withCredentials: false
});

// 配置请求头
service.defaults.headers = {
  post: {
    'Content-Type': 'application/json;charset=utf-8'
  }
};

// 请求拦截
service.interceptors.request.use(
  config => {
    const token = getToken();

    if (token) {
      config.headers.Authorization = `Bearer ${token.access_token}`;
    }

    if (/^application\/x-www-form-urlencoded/.test(config.headers['Content-Type'])) {
      config.data = qs.stringify(config.data);
    }

    return config;
  },
  error => {
    return Promise.error(error.data);
  });

// 响应拦截
service.interceptors.response.use(
  res => {
    if (res.status === 200) {
      return Promise.resolve(res.data);
    } else {
      return Promise.reject(res.data);
    }
  },
  error => {
    console.log(error);
    if (error.response.data && error.response.data.status === 401) {
      router.replace({
        path: '/login',
        query: {
          redirect: router.currentRoute.fullPath
        }
      });
    } else {
      Message(`${error.response.status}: ${error.response.data.message ? error.response.data.message : error.response.data.error_description}`);
    }
    return Promise.reject(error.response);
  });

export default service;
```