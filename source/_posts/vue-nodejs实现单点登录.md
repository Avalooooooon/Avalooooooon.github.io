---
title: vue+nodejs实现单点登录
tags:
  - node
categories:
  - 前端
  - Vue
updated: 2023-03-07 15:37:45
date: 2023-03-07 15:37:45
---



参考博客：https://blog.csdn.net/qq_45453266/article/details/117066170

## 同源和跨域

同源策略：两个页面拥有相同的协议（protocol）、域名/主机（host）、端口号（port）

> 同源策略是 **浏览器 ** 的安全功能。

## token

token的意思是“令牌”，是 **服务端** 生成的一串字符串，作为客户端进行请求的一个标识。
当用户第一次登录后，服务器生成一个token并将此token返回给客户端，**以后客户端只需带上这个token前来请求数据即可，无需再次带上用户名和密码。**

HTTP是一种没有状态的协议，它不知道是谁访问了我们的应用。这一次客户端使用用户名还有密码通过了身份验证，下次这个客户端再发送请求时候，就还得再验证一下。
通用的解决方法是：当用户请求登录的时候，如果没有问题，在服务端生成一条记录，在这个记录里可以说明登录的用户是谁，然后 **把这条记录的id发送给客户端，客户端收到以后把这个id存储在cookie里**，下次该用户再次向服务端发送请求的时候，可以带上这个cookie，这样服务端会验证一下cookie里的信息，看能不能在服务端这里找到对应的记录，如果可以，说明用户已经通过了身份验证，就把用户请求的数据返回给客户端。

<span style='color:red'>以上所描述的过程就是利用session，那个id值就是sessionid。</span>我们需要在服务端存储为用户生成的session，这些session会存储在内存，磁盘，或者数据库。

### 基于token机制的身份认证

使用token机制的身份验证方法，在服务器端不需要存储用户的登录记录。基本流程如下：

1. 第一次登录的时候，前端调后端的登陆接口，发送用户名和密码
2. 后端收到请求，验证用户名和密码，验证成功，就给前端返回一个token
3. 前端拿到token，将token存储到localStorage和vuex中，并跳转路由页面
4. 前端每次跳转路由，就判断 localStroage 中有无 token ，没有就跳转到登录页面，有则跳转到对应路由页面
5. 每次调后端接口，都要在请求头中加token
6. 后端判断请求头中有无token，有token，就拿到token并验证token，验证成功就返回数据，验证失败（例如：token过期）就返回401，请求头中没有token也返回401
7. 如果前端拿到状态码为401，就清除token信息并跳转到登录页面


## 单点登录(SSO)

单点登录(SingleSignOn，SSO)是一种允许用户通过单次登录跨多个系统访问不同应用程序的方法，就是用户可以通过一次性鉴别登录访问各种应用程序（例如登录淘宝后天猫也会变成登录状态，尽管它们域名不同）。这种方式减少了由登录产生的时间消耗，辅助了用户管理，是目前比较流行的。

当用户在身份认证服务器上登录一次以后，即可获得访问单点登录系统中其他关联系统和应用软件的权限，同时这种实现是不需要管理员对用户的登录状态或其他信息进行修改的，这意味着在多个应用系统中，用户只需一次登录就可以访问所有相互信任的应用系统。

## 情况一：同域SSO

不需要设置独立的SSO服务器，因为业务后台服务器本身就足以承担SSO的职能。

## 情况二：同父域SSO

> 同父域：如`www.app1.aaa.com`和`www.app2.aaa.com`就是同父域。这两个服务器都是在`.aaa.com`的父域名下。
> 默认情况下，这两个服务器下页面之间的cookie是互相访问不到的。

和同域SSO不同在于，服务器在返回cookie的时候，可以通过设置cookie的domain属性为它们的父域名，使得两个服务器下页面之间的cookie可以相互访问到。

下例涉及到的页面url分别为`www.app1.a.com/createCookie`和`www.app2.a.com/getCookie`：

```javascript
// /createCookie页显示文本create cookie ok
router.get('/createCookie', async (ctx, next) => {
  ctx.cookies.set('username', '123', {
    maxAge: 60 * 60 * 1000,
    httpOnly: false,
    path: '/',
    domain:'.a.com' //设置domain为共通的父域名
  });
  ctx.body = "create cookie ok"
})

// /getCookie页显示文本123，即成功拿到cookie
router.get('/getCookie', async (ctx, next) => {
  let username=ctx.cookies.get('username')
  if (username){
    ctx.body=username
  }else{
    ctx.body='no cookie'
  }
})
```

## 情况三：跨域SSO

> 跨域：`www.a.com`和`www.b.com`。这时候就怎么设置情况二里的domain都没用。
> 当两个产品不同域时，cookie无法共享，所以需要设置专门的SSO服务器。

需要设法 <span style='color:red'>**将身份凭证(token)写入到所有域的cookie中。**</span>

## 跨域SSO的实现过程

用户第一次访问的是`www.a.com`首页，需要跳转到`www.c.com:3000`登录页，登录成功后跳转回`www.a.com`首页。
再次访问`www.a.com`首页，无需登录直接跳转；访问`www.b.com`首页，也无需登录直接跳转。

![sso过程](sso.png)

1. 用户第一次访问系统`www.a.com/index`，并发送请求。但由于是第一次访问，此时用户发送的请求里不携带cookie（key=token）或tk，请求被拦截，重定向到sso登录页面【路径/login】：`www.c.com:3000/view/login?originURL=www.a.com/index`。
2. tk=${uuid}。在`www.c.com:3000`校验用户名密码，<span style='color:red'>生成session（key=token），写入到浏览器cookie</span>（key="token",value=token,domain=`www.c.com`）。
3. 在`www.c.com:3000`：带上tk重定向回原来的url`www.a.com/index？tk=${uuid}`
4. 回到`www.a.com`，此时没有token但是有tk，当前client带上tk去sso发送tk请求校验
5. 请求发到`www.c.com:3000/validate`【路径/validate】。校验到tk存在，向client返回userInfo。
6. 回到client登录后的`www.a.com`，此时可以拿到token。<span style='color:red'>client生成session（key=token,value=${tk}）写入到浏览器token</span>（key="token",value=token,domain=`www.a.com`）
7. 跳转到访问页面。
8. 如果再次访问client登录后的`www.a.com`，会携带cookie，从session中取出tk，再次重复第4步向sso发送请求校验。

此后，如果访问的是`www.b.com`：
9. 发送请求，没有cookie（key=token）或tk，请求被拦截，重定向到sso登录页面：`www.c.com:3000/view/login?originURL=www.b.com/index`。(注意这里的domain变了)
10. 登录页面如果有cookie（key=token）,向sso服务器查询用户状态，此时会带上之前第二步生成的cookie。sso服务器根据cookie取到tk，重复第三步及其之后流程。


## 跨域写cookie

### 利用< script />标签跨域写cookie(jsonp)

在`http://www.a.com/index.js`中直接向`https://www.c.com:3000/sso`发送网络请求，是无法跨域写入cookie的。

```html
<script>
  $.ajax({
    url: 'https://www.c.com:3000/sso?key=username&value=123',
    method: 'get',
  })
</script>
```

但是可以通过`< script />`标签发起跨域请求，写入cookie。

```html
<script src="https://www.c.com:3000/sso?key=username&value=123"></script>
```

这样通过`< script />`标签就实现了往`www.a.com`中写入了domain为`www.c.com`的跨域cookie。

### url参数实现跨域信息传递

访问`http://www.c.com:3000/createToken?from=http://www.a.com/createCookie`。

`www.c.com`上生成token后将url重写，带上token，重定向到`www.a.com`:

```javascript
router.get('/createToken', async (ctx, next) => {
  let { from } = ctx.request.query
  let token = "123";
  ctx.response.redirect(`${from}?token=${token}`)
})
```

`www.a.com`上从url上获取token，存入cookie:

```javascript
router.get('/createCookie', async (ctx, next) => {
  let { token } = ctx.request.query
  ctx.cookies.set('token', token, {
    maxAge: 60 * 60 * 1000, //有效时间，单位毫秒
    httpOnly: false,
    path: '/',
  });
  ctx.body = 'set cookie ok'
})
```

这样就实现了跨域信息的传递.与上面的方式不同，这种方法只是单纯的http请求，适用于所有浏览器，但是缺点也很明显，每次只能分享给一个服务器。

## 跨域读cookie

### 利用< script />标签跨域读cookie(jsonp)

上面利用`< script />`标签在`www.a.com`中写入了`www.c.com`的cookie(username,123)。现在想要`www.a.com`请求的时候携带上`www.c.com`的cookie，也就是说要跨域读cookie。

其实也是同样的方法，在`www.a.com`上利用`< script />`跨域访问`www.c.com`，会自动的带上domain为`www.c.com`的cookie。

`www.a.com/index.js`文件：

```html
<script src="https://www.c.com:3000/readCookie"></script>
```

`www.c.com`文件：

```javascript
router.get('/readCookie', async (ctx, next) => {
  let username = ctx.cookies.get('username')
  console.log('cookie', username)
})
```

可以看到读取到了存储在`www.a.com`里面domain为`www.c.com`的cookie。

## nodejs实现单点登录系统

源码：https://github.com/wantao666/sso-nodejs 



