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

### 对XHR的理解
使用XMLHttpRequest (XHR)对象可以与服务器交互，也就是发送ajax请求；前端可以获取到数据，而无需让整个的页面刷新。这使得Web 页面可以只更新页面的局部，而不影响用户的操作。
https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest
<span style="color:red"> **XMLHttpRequest** </span>，AJAX 的所有操作都是通过该对象进行的。

### 核心对象使用步骤
1. 创建XMLHttpRequest 对象：```var xhr = new XMLHttpRequest()```
2. 在这个对象上使用 open 方法创建一个 HTTP 请求，并设置请求信息。open 方法所需要的参数是请求的方法、请求的地址、是否异步和用户的认证信息。
```javascript
// 请求方式
xhr.open(method, url);
```
3. 在发起请求前，可以为这个对象添加一些信息和监听函数。比如说可以通过 setRequestHeader 方法来为请求添加头信息。还可以为这个对象添加一个状态监听函数。一个 XMLHttpRequest 对象一共有 5 个状态，当它的状态变化时会触发onreadystatechange 事件，可以通过设置监听函数，来处理请求成功后的结果。当对象的 readyState 变为 4 的时候，代表服务器返回的数据接收完成，这个时候可以通过判断请求的状态，如果状态是 2xx 或者 304 的话则代表返回正常。这个时候就可以通过 response 中的数据来对页面进行更新了。
```javascript
//可以设置请求头，一般不设置
xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
```
4. 发送请求。当对象的属性和监听函数设置完成后，最后调用 sent 方法来向服务器发起请求，可以传入参数作为发送的数据体。```xhr.send(body) //get请求不传 body 参数，只有post请求使用```
5. 接收响应（事件绑定，处理服务端返回的结果）
```javascript
//xhr.responseXML 接收 xml格式 的响应数据
//xhr.responseText 接收 文本格式 的响应数据
xhr.onreadystatechange = function (){
	// readyState 是 xhr对象中的属性, 表示状态 0 1 2 3 4
	if(xhr.readyState == 4 && xhr.status == 200){
		var text = xhr.responseText;
		console.log(text);
	}
}
```

整体过程如下：
```javascript
const SERVER_URL = "/server";
let xhr = new XMLHttpRequest();
// 创建 Http 请求
xhr.open("GET", url, true);
// 设置状态监听函数
xhr.onreadystatechange = function() {
  if (this.readyState !== 4) return;
  // 当请求成功时
  if (this.status === 200) {
    handle(this.response);
  } else {
    console.error(this.statusText);
  }
};
// 设置请求失败时的监听函数
xhr.onerror = function() {
  console.error(this.statusText);
};
// 设置请求头信息
xhr.responseType = "json";
xhr.setRequestHeader("Accept", "application/json");
// 发送 Http 请求
xhr.send(null);
```
使用Promise封装AJAX：
```javascript
// promise 封装实现：
function getJSON(url) {
  // 创建一个 promise 对象
  let promise = new Promise(function(resolve, reject) {
    let xhr = new XMLHttpRequest();
    // 新建一个 http 请求
    xhr.open("GET", url, true);
    // 设置状态的监听函数
    xhr.onreadystatechange = function() {
      if (this.readyState !== 4) return;
      // 当请求成功或失败时，改变 promise 的状态
      if (this.status === 200) {
        resolve(this.response);
      } else {
        reject(new Error(this.statusText));
      }
    };
    // 设置错误监听函数
    xhr.onerror = function() {
      reject(new Error(this.statusText));
    };
    // 设置响应的数据类型
    xhr.responseType = "json";
    // 设置请求头信息
    xhr.setRequestHeader("Accept", "application/json");
    // 发送 http 请求
    xhr.send(null);
  });
  return promise;
}
```
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

### 案例——GET——AJAX请求发送前的准备：前端页面和服务端代码
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

### 接上节：GET 请求设置请求参数
在url后面直接缀参数即可，用问号分割然后加参数名和值；多个参数用```&```分割。
```xhr.open('GET', 'http://127.0.0.1:8000/server?a=100&b=200&c=300')```
Network选项卡中的结果如下。
![Network结果](getparams.png)
在Headers选项卡->Query String Parameters也可以进行参数确认。

### 案例——POST——AJAX发送POST请求
鼠标放到div中，发post请求，将响应体放在div中呈现。即红色框中显示文字"Hello Ajax POST"。

post.html:
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Ajax POST 请求</title>
  <style>
    #result {
      width: 200px;
      height: 100px;
      border: solid 1px #903;
    }
  </style>
</head>
<body>
  <div id="result"></div>
  <script>
    // 获取元素对象
    const result = document.getElementById('result');
    // 绑定事件
    result.addEventListener("mouseover", function(){
      // 1. 创建对象
      const xhr = new XMLHttpRequest();
      // 2. 初始化 设置类型（请求方式）与url
      xhr.open('POST', 'http://127.0.0.1:8000/server');
      // 3. 发送   设置请求参数（请求体）
      xhr.send('a=100&b=200&c=300');
      // 4. 事件绑定
      xhr.onreadystatechange = function(){
        // 判断
        if(xhr.readyState === 4){
          if(xhr.status >=200 && xhr.status < 300){
            // 处理服务端返回的结果
            result.innerHTML = xhr.response;
          }
        }
      }
    });
  </script>
</body>
</html>
```
如果直接用GET案例中的server.js，会发现报错。原因是因为服务端没有与POST请求匹配的路由规则，只有一个GET的规则。需要添加POST路由规则：
```javascript
app.post('/server', (request, response) => {
  // 设置响应头, 设置允许跨域
  response.setHeader('Access-Control-Allow-Origin', '*');
  // 设置响应体
  response.send("Hello Ajax POST");
});
```
注意JS代码修改后要重新启动服务才能生效。

### 接上节：POST 请求设置请求体参数
> POST不能把参数写在url里！

请求体是在```.send()```中设置的，参数的形式可以以url的形式多个参数用```&```分割：
```xhr.send('a=100&b=200&c=300')```

POST请求体在Headers选项卡->Request Payload进行参数确认。

> 新版谷歌浏览器把requst payload变成独立标签放在headers右边。

请求体中的格式是很灵活的，除了上面的形式，还可以写```xhr.send('a:100&b:200&c:300')```；```xhr.send('112233')```。但需要注意的是，无论请求体中写的是什么，服务端(server.js)一定要有与之对应的处理方式。

### 接上节：POST请求设置请求头信息
前文中已经给出了请求行的方法/路径/参数的设置方法和请求体的设置方法。还剩一个设置请求头信息。一般会把身份校验的信息放在头信息里然后传给服务器，服务器对参数做提取并对用户身份进行校验。

POST请求设置请求头信息，只需要在```send()```方法后调用一个方法```setRequesHeader('头的名字','头的值')```即可。
```javascript
// 设置请求体内容的类型
xhr.setRequesHeader('Content-Type','application/x-www-from-urlencoded');
```
其中，```Content-Type```是用来设置请求体内容的类型的；```application/x-www-from-urlencoded```是一个固定写法，是用来设置请求体中参数字符串的类型的。

还可以设置响应头允许自定义请求头：
```javascript
// 自定义头信息
xhr.setRequesHeader('name', 'Bob');
```
但这需要在server.js文件中的post路由规则改成all，并添加如下语句：
```javascript
response.setHeader('Access-Control-Allow-Header','*');
```
修改后的当前路由规则如下：
```javascript
// 可以接收任意类型的请求
app.all('/server', (request, response) => {
    // 设置响应头 设置允许跨域
    response.setHeader('Access-Control-Allow-Origin', '*');
    // 响应头
    response.setHeader('Access-Control-Allow-Header','*');
    // 设置响应体
    response.send("Hello Ajax POST");
});
```

### 案例——json数据请求
```javascript
app.all('/json-server', (request, response) => {
  // 设置响应头, 设置允许跨域
  response.setHeader('Access-Control-Allow-Origin', '*');
  // 设置响应头, 设置允许自定义头信息
  response.setHeader('Access-Control-Allow-Headers', '*');
  // 响应一个数据
  const data = {
    name: 'atguigu'
  };
  // 对 对象 进行 字符串 转换
  let str = JSON.stringify(data)
  // 设置响应体 
  response.send(str);
});
```
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>JSON</title>
  <style>
    #result {
      width: 200px;
      height: 100px;
      border: solid 1px #89b;
    }
  </style>
</head>
<body>
  <div id="result"></div>
  <script>
    const result = document.getElementById('result');
    // 绑定键盘按下事件
    window.onkeydown = function(){
      // 发送请求
      const xhr = new XMLHttpRequest();
      // *2*.(自动转换) 设置响应体数据的类型(自动转换)
      xhr.responseType = 'json';
      // 初始化
      xhr.open('GET', 'http://127.0.0.1:8000/json-server');
      // 发送
      xhr.send();
      // 事件绑定
      xhr.onreadystatechange = function(){
        if(xhr.readyState === 4){
          if(xhr.status >= 200 && xhr.status < 300){
            console.log(xhr.response);
            // 1. 手动对数据转化 (字符串再转换成json)
            // let data = JSON.parse(xhr.response); //转换成json
            // result.innerHTML = data.name;
            // *2*. (自动转换)自动转换(自动转换)
            result.innerHTML = xhr.response.name; //已经自动变成json
          }
        }
      }
    }
  </script>
</body>
</html>
```
### 请求超时与网络异常
```javascript
// 超时设置 （2秒）
xhr.timeout = 2000;
// 超时回调
xhr.ontimeout = function(){
	alert('网络超时，请稍后重试')
}
// 网络异常回调
xhr.onerror = function(){
	alert('网络异常，请稍后重试')
}
```
### 取消请求
```javascript
// 手动取消
xhr.abort()
```
### 请求重复发送问题

### 解决 IE 缓存问题
问题：在一些浏览器中(IE),由于缓存机制的存在，ajax 只会发送的第一次请求，剩余多次请求不会在发送给浏览器而是直接加载缓存中的数据。
解决方式：浏览器的缓存是根据url 地址来记录的，所以我们只需要修改url 地址即可避免缓存问题。
```javascript
xhr.open("get","/testAJAX?t="+Date.now());
```

## API总结
+ ```XMLHttpRequest()```：创建 XHR 对象的构造函数
+ ```status```：响应状态码值，如 200、404
+ ```statusText```：响应状态文本，如 ’ok‘、‘not found’
+ ```readyState```：标识请求状态的只读属性 0-1-2-3-4
+ ```onreadystatechange```：绑定 readyState 改变的监听
+ ```responseType```：指定响应数据类型，如果是 ‘json’，得到响应后自动解析响应
+ ```response```：响应体数据，类型取决于 responseType 的指定
+ ```timeout```：指定请求超时时间，默认为 0 代表没有限制
+ ```ontimeout```：绑定超时的监听
+ ```onerror```：绑定请求网络错误的监听
+ ```open()```：初始化一个请求，参数为：(method, url[, async])
+ ```send(data)```：发送请求
+ ```abort()```：中断请求 （发出到返回之间）
+ ```getResponseHeader(name)```：获取指定名称的响应头值
+ ```getAllResponseHeaders()```：获取所有响应头组成的字符串
+ ```setRequestHeader(name, value)```：设置请求头

### API 的分类
1. REST API: restful （Representational State Transfer (资源)表现层状态转化）
(1) 发送请求进行CRUD 哪个操作由请求方式来决定
(2) 同一个请求路径可以进行多个操作
(3) 请求方式会用到GET/POST/PUT/DELETE
2. 非REST API: restless
(1) 请求方式不决定请求的CRUD 操作
(2) 一个请求路径只对应一个操作
(3) 一般只有GET/POST

## 跨域问题
### 同源策略
同源策略(Same-Origin Policy)最早由Netscape 公司提出，是浏览器的一种安全策略
同源： 协议、域名、端口号必须完全相同
跨域： 违背同源策略就是跨域
### JSONP
1) JSONP 是什么
JSONP(JSON with Padding)，是一个非官方的跨域解决方案，纯粹凭借程序员的聪明
才智开发出来，只支持get 请求。
2) JSONP 怎么工作的？
在网页有一些标签天生具有跨域能力，比如：img link iframe script。
JSONP 就是利用script 标签的跨域能力来发送请求的。
3) JSONP 的使用
1.动态的创建一个script 标签：```var script = document.createElement("script")```
2.设置script 的src，设置回调函数
```javascript
script.src = "http://localhost:3000/testAJAX?callback=abc";
function abc(data) {
	alert(data.name);
};
```
3.将script 添加到body 中：```document.body.appendChild(script)```
4.服务器中路由的处理
```javascript
router.get("/testAJAX" , function (req , res) {
	console.log("收到请求");
	var callback = req.query.callback;
	var obj = {
		name:"孙悟空",
		age:18
	}
	res.send(callback+"("+JSON.stringify(obj)+")");
});
```
###  CORS
https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS

1) CORS 是什么？
CORS（Cross-Origin Resource Sharing），跨域资源共享。CORS 是官方的跨域解决方
案，它的特点是不需要在客户端做任何特殊的操作，完全在服务器中进行处理，支持
get 和post 请求。跨域资源共享标准新增了一组HTTP 首部字段，允许服务器声明哪些
源站通过浏览器有权限访问哪些资源
2) CORS 怎么工作的？
CORS 是通过设置一个响应头来告诉浏览器，该请求允许跨域，浏览器收到该响应
以后就会对响应放行。
3) CORS 的使用
主要是服务器端的设置：
```javascript
router.get("/testAJAX" , function (req , res) {
	//通过res 来设置响应头，来允许跨域请求
	//res.set("Access-Control-Allow-Origin","http://127.0.0.1:3000");
	res.set("Access-Control-Allow-Origin","*");
	res.send("testAJAX 返回的响应");
});
```