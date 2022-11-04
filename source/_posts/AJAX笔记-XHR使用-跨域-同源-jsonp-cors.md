---
title: AJAX笔记-XHR使用-跨域-同源-jsonp-cors
updated: 2022-11-04 16:24:23
date: 2022-11-04 16:24:23
tags: [JS]
categories: [前端,JS]
---

## 1. Ajax概述
### AJAX 简介
AJAX 全称为Asynchronous JavaScript And XML，即异步的JS和XML；不是新的编程语言，而是一种将现有的标准组合在一起使用的新方式。特点就是可以在网页不刷新的情况下向服务端发送HTTP请求并得到HTTP响应。使用HTTP协议。
通过AJAX 可以在浏览器中向服务器发送异步请求，最大优势：<span style="color:red"> **无刷新** </span>获取数据。
AJAX 不是新的编程语言，而是一种将现有的标准组合在一起使用的新方式

### XML 简介
XML，即可扩展标记语言(HTML叫做超文本标记语言)。被设计用来传输和存储数据。
和HTML 类似都是由标签组成的，不同的是HTML 中都是预定义标签，用于在网页当中呈现数据；而XML 中没有预定义标签，用来传输和存储数据。
XML全都是<span style="color:red"> 自定义标签 </span>，用来表示一些数据。

比如说有一个学生数据：name = “孙悟空” ; age = 18 ; gender = “男” ;
用XML 表示：
```XML
<student>
	<name>孙悟空</name>
	<age>18</age>
	<gender>男</gender>
</student>
```

> 之前AJAX在进行数据交换时所使用的格式就是XML，服务端在向客户端返回结果时返回的就是一个XML格式的字符串；前端JS在接受到这个数据之后对这个内容进行解析，把数据提取出来并进行处理。
> 现在已经被JSON取代了。JSON比XML更简洁，而且在数据转换中可以借助JSON的一些API方法快速将字符串转化为对象，灵活度远胜XML。

用JSON表示：
```JSON
{"name":"孙悟空","age":18,"gender":"男"}
```

### AJAX 的优缺点
+ 优点:
1. 可以无需刷新页面而与服务器端进行通信
2. 允许根据用户事件（鼠标/键盘/表单事件等）来更新部分页面内容

+ 缺点:
1. 没有浏览历史，不能回退
2. 存在跨域问题(同源)
  > 跨域：在一个服务（a.com）向另一个服务（b.com）发送AJAX请求，默认是不可以的。
3. SEO（搜索引擎优化）不友好
  > 因为网页中的内容都是通过AJAX异步请求、动态创建的，爬虫爬不到。以购物网站为例，也就是源代码第一次请求时，响应体结果里是没有商品信息的

## HTTP相关
即hypertext transport protocol，【超文本传输协议】。详细规定了浏览器和万维网服务器之间互相通信的规则。
[MDN文档](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Overview)

### HTTP请求交互的基本过程

1. 前台应用从浏览器端向服务器发送HTTP 请求(请求报文)
2. 后台服务器接收到请求后，调度服务器应用处理请求，向浏览器端返回HTTP响应(响应报文)
3. 浏览器端接收到响应，解析显示响应体/调用监视回调

### HTTP 请求报文：四部分
```
行       GET /s?ie=utf-8 HTTP/1.1
头       Host: www.baidu.com
         Cookie: BAIDUID=AD3B0FA706E; BIDUPSID=AD3B0FA70
         Content-Type: application/x-www-form-urlencoded
         User-Agent: chrome 83  [可选]
空行      
体       username=tom&pwd=123
         {"username": "tom", "pwd": 123}(有时是这种形式)
```
重点是格式与参数
1. 请求行：```请求类型method url路径 HTTP协议版本```
各个参数放在url的部分。
2. 多个请求头：格式都是 **名字 冒号 空格 值** 。
3. 空行：必须得有
4. 请求体：可以没有
GET请求体就是空的，如果是POST可以不为空。

### post 请求体参数格式
1. ```Content-Type: application/x-www-form-urlencoded;charset=utf-8```
用于键值对参数，参数的键值用=连接, 参数之间用&连接。
例如: ```name=%E5%B0%8F%E6%98%8E&age=12```
2. ```Content-Type: application/json;charset=utf-8```
用于 json 字符串参数。
例如: ```{"name": "%E5%B0%8F%E6%98%8E", "age": 12}```
3. ```Content-Type: multipart/form-data```
用于文件上传请求。

### 不同类型的请求及其作用
```GET```: 从服务器端读取数据（查）
```POST```: 向服务器端添加新数据 （增）
```PUT```: 更新服务器端已经数据 （改）
```DELETE```: 删除服务器端数据 （删）

### HTTP 响应报文：四部分
```
行       HTTP/1.1 200 OK
头       Content-Type: text/html;charset=utf-8
         Content-length: 2048
         Content-encoding: gzip
         ...
空行      
体       <html>
             <head>
             </head>
             <body> <h1>百度</h1> </body>
         </html>
```
1. 响应行：```HTTP协议版本 响应状态码 响应状态字符串```
响应状态字符串和响应状态码是对应的，不需要单独去设置。
2. 多个请求头：格式都是 **名字 冒号 空格 值** 。和请求报文请求头一样。
对响应体内容做一些相关描述，类型、长度、压缩方式等。
3. 空行：必须得有
4. 响应体：主要返回结果。
实例中把html内容放在响应报文中传输，浏览器收到后会把响应体结果提取出来并对内容进行解析，在页面渲染。

### 常见的响应状态码
200：OK 请求成功。一般用于GET 与POST 请求
201：Created 已创建。成功请求并创建了新的资源
401：Unauthorized 未授权/请求要求用户的身份认证
404：Not Found 服务器无法根据客户端的请求找到资源
500：Internal Server Error 服务器内部错误，无法完成请求

### Chrome网络控制台查看通信报文
Network标签页会列出当前网页在加载过程中所有发送的请求。点进一条，展开新标签页。
标签```headers```：点开可以看到它包含了请求头和响应头，默认不显示请求行，点击```view source```即可显示请求行；```Query String Parameters```查询字符串参数，是对```url```中的参数做解析，这一项在调试参数中很方便。```response```点开后是响应头信息，也是按照名字冒号空格的格式，点击```view source```即可显示原始的响应报文（只有响应行和响应头）。响应体在下面的和```headers```同级的标签```response```里。
标签```preview```：是对响应的预览（解析）
标签```response```：响应体。服务端返回的html内容。



2.8 API 的分类
REST API: restful （Representational State Transfer (资源)表现层状态转化）
(1) 发送请求进行CRUD 哪个操作由请求方式来决定
(2) 同一个请求路径可以进行多个操作
(3) 请求方式会用到GET/POST/PUT/DELETE
非REST API: restless
(1) 请求方式不决定请求的CRUD 操作
(2) 一个请求路径只对应一个操作
(3) 一般只有GET/POST
2.9 区别 一般http请求 与 ajax请求

ajax请求 是一种特别的 http请求
对服务器端来说, 没有任何区别, 区别在浏览器端
浏览器端发请求: 只有XHR 或fetch 发出的才是ajax 请求, 其它所有的都是非ajax 请求
浏览器端接收到响应
(1) 一般请求: 浏览器一般会直接显示响应体数据, 也就是我们常说的刷新/跳转页面
(2) ajax请求: 浏览器不会对界面进行任何更新操作, 只是调用监视的回调函数并传入响应相关数据
3. 原生AJAX 的基本使用 XHR






