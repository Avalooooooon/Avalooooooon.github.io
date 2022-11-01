---
title: axios笔记
updated: 2022-11-01 15:44:59
date: 2022-11-01 15:44:59
tags: [JS]
categories: [前端,JS]
---

![官方文档]( https://github.com/axios/axios)
## Production
axios是前端最流行的 ajax 请求库，react/vue 官方都推荐使用 axios 发 ajax 请求。它有以下几个特点：

1. 基于 xhr + promise 的异步 ajax 请求库
2. 浏览器端 / node 端都可以使用
3. 支持请求／响应拦截器
4. 支持请求取消
5. 请求 / 响应数据转换
6. 批量发送多个请求

axios的原理如下图：
[axios原理图](yuanli.png)

### axios 常用语法
1. ```axios(config)```: 通用/最本质的发任意类型请求的方式
2. ```axios(url[, config])```: 可以只指定 url 发 get 请求
3. ```axios.request(config)```: 等同于 axios(config)
4. ```axios.get(url[, config])```: 发 get 请求
5. ```axios.delete(url[, config])```: 发 delete 请求
6. ```axios.post(url[, data, config])```: 发 post 请求
7. ```axios.put(url[, data, config])```: 发 put 请求
8. ```axios.defaults.xxx```: 请求的默认全局配置
9. ```axios.interceptors.request.use()```: 添加请求拦截器
10. ```axios.interceptors.response.use()```: 添加响应拦截器
11. ```axios.create([config])```: 创建一个新的 axios(它没有下面的功能)
12. ```axios.Cancel()```: 用于创建取消请求的错误对象
13. ```axios.CancelToken()```: 用于创建取消请求的 token 对象
14. ```axios.isCancel()```: 是否是一个取消请求的错误
15. ```axios.all(promises)```: 用于批量执行多个异步请求
16. ```axios.spread()```: 用来指定接收所有成功数据的回调函数的方法