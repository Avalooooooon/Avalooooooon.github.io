---
title: AJAX笔记-XHR使用-跨域-同源-jsonp-cors
updated: 2022-11-04 16:24:23
date: 2022-11-04 16:24:23
tags: [JS]
categories: [前端,JS]
---

## 1. Ajax概述
### AJAX 简介
AJAX 全称为Asynchronous JavaScript And XML，即异步的JS和XML。特点是可以在网页不刷新的情况下向服务端发送HTTP请求并得到HTTP响应。使用HTTP协议。
通过AJAX 可以在浏览器中向服务器发送异步请求，最大优势：<span style="color:red"> **无刷新** </span>获取数据。
AJAX 不是新的编程语言，而是一种将现有的标准组合在一起使用的新<span style="color:red"> **方式** </span>。

### XML 简介
> 之前AJAX在进行数据交换时所使用的格式就是XML，服务端在向客户端返回结果时返回的就是一个XML格式的字符串；前端JS在接受到这个数据之后对这个内容进行解析，把数据提取出来并进行处理。
> 现在已经被JSON取代了。JSON比XML更简洁，而且在数据转换中可以借助JSON的一些API方法快速将字符串转化为对象，灵活度远胜XML。

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
  > 因为网页中的内容都是通过AJAX异步请求、动态创建的，爬虫爬不到。以购物网站为例，也就是源代码第一次请求时，响应体结果里是没有商品信息的。

### 区别：一般http请求 与 AJAX请求
1. AJAX请求是一种特别的 http请求
2. 对服务器端来说, 没有任何区别, 区别在 <span style='color:red'> **浏览器端** </span>。
3. 浏览器端发请求: 只有 ```XHR``` 或 ```fetch``` 发出的才是AJAX请求， 其它所有的都是非AJAX请求。
4. 浏览器端接收到响应
  1. 一般请求：浏览器一般会直接显示响应体数据，也就是我们常说的刷新/跳转页面
  2. ajax请求：浏览器不会对界面进行任何更新操作， 只是 <span style='color:red'> **调用监视的回调函数** </span> 并 <span style='color:red'> **传入响应相关数据** </span>。

## HTTP相关

即 "hypertext transport protocol" ，【超文本传输协议】。详细规定了浏览器和万维网服务器之间互相通信的规则。

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
1. ```GET```: 从服务器端读取数据（查）
2. ```POST```: 向服务器端添加新数据 （增）
3. ```PUT```: 更新服务器端已经数据 （改）
4. ```DELETE```: 删除服务器端数据 （删）

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

> 事实上，响应状态码 2开头的“2xx”都表示成功！不止200是。

### Chrome网络控制台查看通信报文
Network标签页会列出当前网页在加载过程中所有发送的请求。点进一条，会展开新标签页。
1. 标签```Headers```：
  1. ```Request headers```点开后是请求头，可以看出都是名字冒号空格的格式，如果没显示请求行，点击```view source```即可显示请求行（方法+url+协议版本）
  2. ```Query String Parameters```查询字符串参数，是对```url```（也就是请求行里的那个url）中的参数做解析，这一项在调试参数中很方便，可以方便的查看参数有没有成功发送。。
  3. ```Response headers```点开后是响应头，也是按照名字冒号空格的格式，点击```view source```即可显示原始的响应报文（只有响应行和响应头）。响应体在下面的和```Headers```同级的标签```Response```里。
  4. ```Form Data```：如果发送的是POST请求，点击```view source```显示的是原始的请求体内容。在我们点击提交按钮之后，浏览器会把原始的HTTP报文封装好，再发送给目标服务器的指定端口进行请求。
2. 标签```preview```：是对响应的预览（解析）
3. 标签```Response```：响应体。服务端返回的html内容。如果响应的结果是跳转则此处为空。

总之就是，看请求报文，就看标签```Headers```中的```Request headers```和```Query String Parameters```（有参数的GET请求）或```Form Data```（POST请求）；看响应报文就看标签```Headers```中的```Response headers```和标签```Response```

2.8 API 的分类
REST API: restful （Representational State Transfer (资源)表现层状态转化）
(1) 发送请求进行CRUD 哪个操作由请求方式来决定
(2) 同一个请求路径可以进行多个操作
(3) 请求方式会用到GET/POST/PUT/DELETE
非REST API: restless
(1) 请求方式不决定请求的CRUD 操作
(2) 一个请求路径只对应一个操作
(3) 一般只有GET/POST

## 原生AJAX的基本使用：XHR，new XMLHttpRequest()

### node环境与express框架
+ 安装node.js：[nodejs官网](http://nodejs.cn/)
+ 安装express（服务端框架）：[express官网](https://www.expressjs.com.cn/)
  1. 初始化环境（记得是在根目录，目录最外边启动终端）：```npm init --yes```
  2. 下载express包：```npm install express --save```
  3. 在文件```express基本使用```中编写js代码：
  ```javascript
  // 1. 引入express
  const express = require('express');

// 2. 创建应用对象
const app = express();

// 3. 创建路由规则
// request 是对请求报文的封装
// response 是对响应报文的封装
app.get('/', (request, response) => {
    //  设置响应
    response.send("Hello Express");
});

// 4. 监听端口，启动服务
app.listen(8000, () => {
    console.log("服务已经启动, 8000 端口监听中...");
})
```
  4. 运行js程序：```node express基本使用.js```
运行结果如下：
![express成功运行](express1.png)
打开网页```127.0.0.1:8000```，显示页面：
![express网页显示](express2.png)
打开NETWORK控制台，调试程序可以查看请求和响应。```Headers```标签页中的```Request headers```是我们刚才向8000端口发送的内容，```Response headers```是8000端口给我们的响应；```Response```标签页是响应体。这两块内容组合起来形成了完整的响应报文。
![express请求和响应1](express3.png)
![express请求和响应2](express4.png)

到此，这个HTTP服务就被启动起来了。接下来可以借助这个服务与前端的AJAX进行交互。

### AJAX请求发送前的准备：前端页面和服务端代码
创建两个文件，浏览器端使用的html文件和服务器端使用的js文件。需要实现的需求是，点击按钮返回响应信息。点击按钮后向服务端发送一个请求，把服务端返回的 <span style='color:red'> **响应体结果** </span>在紫色框呈现出来。
![页面](yemian.png)
![准备文件](zhunbeiwenjian.png)
html文件的基础代码如下：
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Ajax GET 请求</title>
  <style>
    #result {
      width: 200px;
      height: 100px;
      border: solid 1px #90b;
    }
  </style>
</head>
<body>
  <button>点击发送请求</button>
  <div id="result"></div>
</body>
</html>
```
server.js中的文件在上一部分的express代码基础上进行修改。把创建路由规则中的```app.get()```中的第一个参数改成```/server```，这样当客户端浏览器向服务器发送请求时，如果url的路径（也就是请求行的第二段内容）是```/server```的话，就会执行```app.get()```中的第二个参数也就是回调函数里面的代码，并且由```(request, response)```中的```response```来做出响应。整体代码如下：
```javascript
// 1. 引入express
const express = require('express');
// 2. 创建应用对象
const app = express();
// 3. 创建路由规则
app.get('/server', (request, response) => {
    // 设置响应头 设置允许跨域
    response.setHeader('Access-Control-Allow-Origin', '*');
    // 设置响应体
    response.send("Hello Ajax");
});
// 4. 监听服务
app.listen(8000, () => {
    console.log("服务已经启动, 8000 端口监听中...");
})
```
准备好这两个文件后，在这两个文件所在的文件夹下打开控制台，输入```node server.js```启动服务。

> 如果想查看127.0.0.1:8000，记得要在后面加```/server```，和代码一致！！！
> 即访问地址是```http://127.0.0.1:8000/server```。

### 接上节：完整的html和js代码
点击按钮后的html页面：
![页面显示响应体结果](yemian2.png)
控制台中的输出：
![控制台结果](yemian3.png)


html文件如下。在上一节的基础上，只展示```<body>```内的代码：
```html
<body>
  <button>点击发送请求</button>
  <div id="result"></div>
  <script>
    //获取button元素 getElementsByTagName返回数组的形式所以加0
    const btn = document.getElementsByTagName('button')[0];
    const result = document.getElementById('result');
    //绑定事件
    btn.onclick = function () {
      // 1. 创建对象 
      const xhr = new XMLHttpRequest();
      // 2. 初始化 设置请求方法和url 此处url前面的部分不可省略
      xhr.open('GET', 'http://127.0.0.1:8000/server')
      // 3. 发送
      xhr.send();
      // 4. 事件绑定 处理服务端返回的结果
      xhr.onreadystatechange = function () {
        // 判断 (服务端返回了所有的结果)
        if (xhr.readyState === 4) {
          //判断响应状态码 200  404  403 401 500
          if (xhr.status >= 200 && xhr.status < 300) {
            // 处理结果 行 头 空行 体
            // 响应
            console.log('状态码', xhr.status); 
            console.log('状态字符串', xhr.statusText); 
            console.log('所有响应头', xhr.getAllResponseHeaders()); 
            console.log('响应体', xhr.response); 
            //设置 result 的文本
            result.innerHTML = xhr.response;

            // 结束。可以看出并没有进行页面刷新就拿到了结果。
          } else {
          }
        }
      }
    }
  </script>
</body>
```
server.js文件同上一节。

### readystate

readyState 是 xhr 对象中的属性，表示状态 0 1 2 3 4。

0：未初始化。最开始的readyState状态就是0。

> XMLHttpRequest对象创建完成，还没有调用```open()```方法。

1：```open()```方法调用完毕。
> XMLHttpRequest对象初始化完成。已调用```send()```方法,正在发送请求。

2： ```send()```方法调用完毕。

>   请求已经发送——```send()```方法完成,已经收到全部响应内容。

3： 服务端返回了部分结果。
> 服务器返回了数据（但是还没有被解析，可能只一段http报文）——正在解析响应内容。

4： 服务端返回了所有结果。
> 数据解析完成——响应内容解析完成,可以在客户端调用了。
