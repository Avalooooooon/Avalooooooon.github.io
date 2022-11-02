---
title: axios笔记
updated: 2022-11-01 15:44:59
date: 2022-11-01 15:44:59
tags: [JS]
categories: [前端,JS]
---

[官方文档]( https://github.com/axios/axios)
## Production
axios是前端最流行的 ajax 请求库，react/vue 官方都推荐使用 axios 发 ajax 请求。在浏览器中可以利用axios向服务端发送ajax请求来获取数据，在node环境可以利用axios向远端服务发送HTTP请求。也就是它可以在两个环境中运行。它有以下几个特点：

1. 基于 xhr + promise 的异步 ajax 请求库
2. 浏览器端/node 端都可以使用
3. 支持请求/响应拦截器
4. 支持请求取消
5. 请求/响应数据转换
6. 批量发送多个请求

axios的原理如下图：
![axios原理图](yuanli.png)

## 准备工作：使用 json-server 搭建REST API
[在线文档]( https://github.com/typicode/json-server)

json-server 是一个用来快速搭建REST API 的工具包，它可以快速帮我们搭建一个http服务。
> 需要搭建http服务是因为后期用axios的时候需要向服务端发送请求，因此我们需要一个这样的角色来与axios结合做实践。


1. 下载：```npm install -g json-server```，这里使用了全局安装
2. 目标根目录下创建数据库 json 文件：```db.json```
  ```json
  {
    "posts": [
        {
            "id": 1,
            "title": "json-server",
            "author": "typicode"
        },
        {
            "id": 2,
            "title": "json-server2",
            "author": "typicode"
        }
    ],
    "comments": [
        {
            "id": 1,
            "body": "some comment",
            "postId": 1
        }
    ],
    "profile": {
        "name": "typicode"
    }
  }
```
这里的```posts```：文章；```comments```：评论，```profile```：作者信息。
3. 启动服务：```json-server --watch db.json```。注意启动服务时命令行定位到的目录一定是db.json所位于的文件夹。这个命令可以理解成，现在要启动json-server，它启动的数据位于的文件是db.json，注意这个文件的路径要写对。
4. 成功启动后出现如图提示：
![成功启动服务](jsonserver.png)
可以看到列出了三个url，可以访问。分别是```posts```、```comments```和```profile```这三个资源。
5. 除了获取之外，可以在官网看到关于REST API的其他操作，部分如下：
![其他操作](elseserver.png)
第一行的含义是获取所有文章，第二行```/posts/id```意为获取指定文章；第三行```POST 路径```就是新增文件，请求路径是```/posts```，请求类型是```POST```，然后把数据传到请求体里就可以新增内容；还可以更新/删除内容。使用浏览器分别访问 http://localhost:3000/posts 和 http://localhost:3000/posts/1 进行测试。

至此已经实现了json-server服务的构建。有了它，就可以用axios向它发送请求然后获取结果了。

## axios 基本使用
查看[官方文档]( https://github.com/axios/axios)。
在给出的几种下载方式里，如果是在项目中使用axios，推荐使用npm或yarn的下载方式。现在由于是在学习，所以采用最后两种CDN的方式进行引入。
新建测试文件，引入axios并测试：
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="https://cdn.jsdelivr.net/npm/axios@1.1.2/dist/axios.min.js"></script>
</head>
<body>
    <script> console.log(axios) </script>
</body>
</html>
```
> 又是cdn的网址（比如这里的https://cdn.jsdelivr.net/npm/axios@1.1.2/dist/axios.min.js ）可能会访问比较慢，为了提高访问速度，可以把这个资源换成国内服务器的。
> 访问https://www.bootcdn.cn 。在搜索框中输入axios，复制链接```<script src="https://cdn.bootcdn.net/ajax/libs/axios/1.1.3/axios.min.js"></script>```并在上面的html文件中进行替换。
> 两种链接的输出结果相同。

通过```axios()```函数发送AJAX请求。该函数接受一个对象作为参数，这个对象有几种属性：请求类型```method```、请求URL```url```等。

函数```axios()```的返回类型是一个promise，所以可以通过```then()```指定成功和失败时的回调。

### 使用axios访问测试
> 注意这部分需要开启前文中已经配置好的json-server服务器！

在一个网页放四个按钮，尝试为这四个按钮添加可以实现相关功能的点击事件：点击不同按钮发送不同的ajax请求，包括GET、POST、PUT、DELETE。给上一节实现的json- server发请求，，url是```http://localhost:3000```，这里的端口号是3000。
![axios测试](axiostest.png)

1. 引入axios文件：```<script src="https://cdn.bootcdn.net/ajax/libs/axios/1.1.3/axios.min.js"></script>```
2. GET，即需要实现查看时的请求：
  ```javascript
  // 查看id为2的文章详情
  function testGet() {
    // 发送AJAX请求
    axios({
        // 请求类型
        method: 'GET',
        // URL
        url: 'http://localhost:3000/posts/2'
    }).then(response => {
        console.log(response)
    })
  }
```
  > 使用不同的url会有不同的输出结果：
  > axios.get('http://localhost:3000/posts') // 返回一个数组，数组里有两个对象
  > axios.get('http://localhost:3000/posts/1') // 返回一个对象
  > axios.get('http://localhost:3000/posts?id=1') // 返回一个数组，数组里有一个对象

直接点击GET按钮，控制台中可以看到打印的结果```response```。
> 如果控制台中的日志一闪就没，可以从右上角的小齿轮进入偏好设置，勾选控制台->“在浏览时保留日志”
> 网络中的日志一闪就没，可以在上一步的偏好设置中勾选网络->“保留日志”，也可以进入网络选项卡直接勾选“保留日志”。

打开网络（Network）选项卡，清空后点击GET按钮，可以看到请求已经成功发送。
![GET请求发送](get1.png)
点击名称为2的http请求，在“标头”（Headers）中选择“请求标头”（Request Headers），点击“查看源代码”（view source)，查看请求报文，看到请求类型“GET”和请求路径“/posts/2”：
![GET请求报文](get2.png)
点击“响应”（Response），查看返回结果，可以看到返回的确实是id为2的文章信息：
![GET请求响应](get3.png)
3. POST，即需要实现添加时的请求类型，<span style = "color:red">POST除了必须的请求类型和URL外还需要有请求体</span>：
  ```javascript
// 添加一篇新的文章
function testPost() {
    // 发送AJAX请求
    axios({
        // 请求类型
        method: 'POST',
        // URL
        url: 'http://localhost:3000/posts',
        // 请求体
        data: {
            title: "没课，好得很",
            author: "张三"
        }
    }).then(response => {
        console.log(response)
    })
}
```
直接点击POST按钮，控制台中可以看到打印的结果```response```。
打开网络（Network）选项卡，清空后点击POST按钮，可以看到请求已经成功发送。
![POST请求发送](post1.png)
点击状态码为201的http请求，在“标头”（Headers）中选择“请求标头”（Request Headers），点击“查看源代码”（view source)，查看请求报文，看到请求类型“POST”和请求路径“/posts”：
![POST请求报文](post2.png)
点击“载荷”（Payload），点击“查看源代码”（view source)，查看请求体（Request Payload），可以看到请求体是一个JSON格式的字符串：
![POST请求响应](post3.png)
传递给服务器的就是这个JSON格式的字符串，JSON-Server服务接收到这个东西后会对它进行保存。查看文件```db.json```，可以看到这个id为3的文章就已经添加好了。
4. PUT，即需要实现更新时的请求类型，打算把刚添加的那篇文章的作者从张三改成李四，<span style = "color:red">PUT除了必须的请求类型和URL外还需要有请求体和id</span>，也就是url的末尾需要添加```/id```：
  ```javascript
// 更新数据
function testPut() {
    // 发送AJAX请求
    axios({
        // 请求类型
        method: 'PUT',
        // URL
        url: 'http://localhost:3000/posts/3',
        // 请求体
        data: {
            title: "没课，好得很",
            author: "李四"
        }
    }).then(response => {
        console.log(response)
    })
}
```
按照上面的步骤查看各内容；同时可以发现```db.json```也发生了变化，id3的作者已经成功变为李四。
5. DELETE，即需要实现删除时的请求类型，打算把之前添加的、在上一步中作者被改成了李四的那篇文章删掉，delete请求不需要请求体，直接把url一放就可以删除指定id的文章：
  ```javascript
// 删除数据
function testDelete() {
    // 发送AJAX请求
    axios({
        // 请求类型
        method: 'DELETE',
        // URL
        url: 'http://localhost:3000/posts/3',
    }).then(response => {
        console.log(response)
    })
}
```
按照上面的步骤查看各内容；同时可以发现```db.json```也发生了变化，id3已经成功删除。

完整的.html文件内的代码如下：
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="https://cdn.bootcdn.net/ajax/libs/axios/1.1.3/axios.min.js"></script>
</head>
<body>
    <div>
        <button onclick="testGet()">GET请求</button>
        <button onclick="testPost()">POST请求</button>
        <button onclick="testPut()">PUT请求</button>
        <button onclick="testDelete()">DELETE请求</button>
    </div>
    <script>
        function testGet() {
            axios({
                method: 'GET',
                url: 'http://localhost:3000/posts/2'
            }).then(response => {
                console.log(response)
            })
        }
        function testPost() {
            axios({
                method: 'POST',
                url: 'http://localhost:3000/posts',
                data: {
                    title: "没课，好得很",
                    author: "张三"
                }
            }).then(response => {
                console.log(response)
            })
        }
        function testPut() {
            axios({
                method: 'PUT',
                url: 'http://localhost:3000/posts/3',
                data: {
                    title: "没课，好得很",
                    author: "李四"
                }
            }).then(response => {
                console.log(response)
            })
        }
        function testDelete() {
            axios({
                method: 'DELETE',
                url: 'http://localhost:3000/posts/3',
            }).then(response => {
                console.log(response)
            })
        }
    </script>
</body>
</html>
```

### axios其他方式发送请求

## axios 常用语法
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