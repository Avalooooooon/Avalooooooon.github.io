---
title: Promise教程
updated: 2022-10-25 17:02:31
date: 2022-10-25 17:02:31
tags: [JS]
categories: [前端,JS]
---

## Production
### promise是什么
ES6引入的异步编程的新解决方案（旧方案是单纯使用回调函数）。语法上看，<span style="color:red">**promise是一个构造函数**</span>；功能上，promise是一个对象，通常用来封装一个异步任务，提供承诺结果（其成功/失败）。
> 常见的异步编程：
> fs文件操作：```require('fs').readFile('./index.html',(err,data)=>{})```
> mysql/mangodb等数据库操作
> AJAX(jquery为例)：```$.get('/server',(data)=>{})```
> 定时器：```setTimeout(()=>{},2000)```

通俗讲，Promise是一个对未来事情的许诺、承诺，承诺不一定能完成，但是无论是否能完成都会有一个结果。Promise 用来预定一个不一定能完成的任务，要么成功，要么失败。

### promise的状态「PromiseState」
> <span style = "color:red">状态是promise实例对象的一个属性：```PromiseState```</span>。

Promise的状态如下，作为promise参数函数的参数时，它们自己是函数类型的数据：

+ pending：未决定的（正在做。。。）
+ resolved/fullfilled：成功（完成这个承诺）。异步任务成功时调用。
+ rejected：失败（承诺没完成，失败了）。异步任务失败时调用。

使promise状态改变的方法和结果如下，<span style = "color:red">只有这两种可能，一个promise对象的状态只能改变一次</span>：

+ 当前是pending ，变为 resolved：
    a. 通过 ```resolve(value)```
+ 当前是pending ，变为 rejected
    a. 通过```reject(reason)```
    b. 通过抛出异常，可以使用```throw```关键字，如```throw "出现错误"```

    > 状态改变只有这 2 种, 且一个 promise 对象只能改变一次。无论变为成功还是失败, 都会有一个结果数据 。成功的结果数据一般称为 value, 失败的结果数据一般称为 reason。

### promise对象的值「PromiseResult」
> <span style = "color:red">是promise实例对象的另一个属性：```PromiseResult```</span>。

保存的是异步任务成功或失败的结果。有两个函数可以修改这个值：

+ ```resolve()```
+ ```reject()```

就是```new Promise((resolve, reject) => {}```中的```resolve```和```reject```。

![promise的基本流程](liucheng.png)

### 特点和优缺点
优势：
1. <span style="color:red">支持链式调用，可以解决**回调地狱**问题。</span>
  ![回调地狱](huidiaodiyu.png)
  > 回调地狱：回调函数嵌套调用，外部回调函数异步执行的结果是套在内部的回调函数执行的条件。即一层一层的嵌套，上一个方法拿到数据后在执行下一个。
  > 回调地狱的缺点：不便于阅读 + 不便于异常处理
  

    不过promise只是简单的改变格式，并没有彻底解决上面的问题。真正解决要利用```promise```再加上```await```和```async```关键字实现异步传同步。
2. 指定回调函数的方式更加灵活
    + 之前是在启动异步任务之前就必须把回调函数绑定好
    + promise可以：启动异步任务=>返回promise对象=>给promise对象绑定回调函数（甚至可以在异步任务结束后指定多个）

特点:
1. 对象的状态不受外界影响。Promise对象代表一个异步操作，有三种状态：Pending（进行中）、Resolved（已完成，又称Fulfilled）和Rejected（已失败）。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。
2. 一旦状态改变，就不会再变，任何时候都可以得到这个结果。Promise对象的状态改变，只有两种可能：从Pending变为Resolved和从Pending变为Rejected。只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果。就算改变已经发生了，你再对Promise对象添加回调函数，也会立即得到这个结果。

缺点:
1. 无法取消Promise，一旦新建它就会立即执行，无法中途取消。和一般的对象不一样，无需调用。
2. 如果不设置回调函数，Promise内部抛出的错误，不会反应到外部。
3. 当处于Pending状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）

### 它的小伙伴：then()
Promise实例生成以后，可以用 ```then()```方法分别指定resolved状态和rejected状态的回调函数。
```javascript
promise.then(function(value) {  // (value)=>{}
    // 当promise状态返回为resolve 时会执行的回调函数
    }, function(error) {  // (reason)=>{}
    // 当promise状态返回为rejected 时会执行的回调函数
});
```
```then()```方法可以接受两个回调函数作为参数。第一个回调函数是Promise对象的状态变为resolved时调用，第二个回调函数是Promise对象的状态变为rejected时调用。
第二个函数是可选的，不一定要提供。这两个函数都接受Promise对象传出的值作为参数。

如果形参只有一个，使用箭头函数时可以简写，不要小括号，即可以将```(value)=>{}```写作```value =>{}```。

### 基本使用方法示例
1. 使用 promise 封装基于定时器的异步

```javascript
function doDelay(time) {
    // 1. 创建 promise 对象(pending 状态), 指定执行器函数
    return new Promise((resolve, reject) => {
        // 2. 在执行器函数中启动异步任务
        console.log('启动异步任务')
        setTimeout(() => {
            console.log('延迟任务开始执行...')
            const time = Date.now() // 假设: 时间为奇数代表成功, 为偶数代表失败
            if (time % 2 === 1) { // 成功了
                // 3. 1. 如果成功了, 调用 resolve()并传入成功的 value
                resolve('成功的数据 ' + time)
            } else { // 失败了
                // 3.2. 如果失败了, 调用 reject()并传入失败的 reason
                reject('失败的数据 ' + time)
            }
        }, time)
    })
}
const promise = doDelay(2000)
promise.then(// promise指定成功或失败的回调函数来获取成功的value或失败的reason
    (value) => {// 成功的回调函数 onResolved, 得到成功的value
        console.log('成功的value: ', value)
    },
    (reason) => { // 失败的回调函数 onRejected, 得到失败的reason
        console.log('失败的reason: ', reason)
    },
)
```
2. fs模块使用promise
```javascript
const fs = require('fs');

//回调函数 形式----------------------------------------------------
fs.readFile('./resource/content.txt', (err, data) => {
    // 如果出错 则抛出错误
    if (err) throw err;
    //输出文件内容
    console.log(data.toString());
});

//Promise 形式-----------------------------------------------------------
/**
 * 封装一个函数 mineReadFile 读取文件内容
 * 参数:  path  文件路径
 * 返回:  promise 对象
 */
function mineReadFile(path) {
    return new Promise((resolve, reject) => {
        //读取文件
        require('fs').readFile(path, (err, data) => {
            //判断
            if (err) reject(err);
            //成功
            resolve(data);
        });
    });
}

mineReadFile('./resource/content.txt')
    .then(value => {
        //输出文件内容
        console.log(value.toString());
    }, reason => {
        console.log(reason);
    });
```

### 使用util.promisify()方法进行风格转化
想使用promise方法时，不用每个函数都要去手动封装，而可以借助```util.promisify()```方法将原来的回调函数风格的方法转变成promise风格。也就是把这个普通异步函数的方法给它，它帮我们转化成promise风格的。
传入一个遵循常见的错误优先的回调风格的函数（即以```(err,value)=...>```回调作为最后一个参数），并返回一个**返回promise的版本**。
```javascript
//引入 util 模块
const util = require('util');
//引入 fs 模块
const fs = require('fs');
//返回一个新的函数
let mineReadFile = util.promisify(fs.readFile);
mineReadFile('./resource/content.txt').then(value => {
  console.log(value.toString());
});
```


## 常用API
### Promise构造函数
Promise构造函数```new Promise()```——```Promise(excutor){}```
通过new关键字使用。
a. Promise的参数```executor```函数:称为执行器函数——```(resolve, reject) => {}```
b. ```resolve```函数: 内部定义成功时调用——```value => {}```
c. ```reject```函数: 内部定义失败时调用——```reason => {}```

> 说明: <span style="color:red">**executor 会在 Promise 内部立即同步调用**</span>，也就是代码```let p = new Promise((resolve,reject)=>{}```执行到new时，executor函数会立即执行。
> 异步操作在执行器中执行，换句话说就是Promise支持同步也支持异步操作。

### then方法
```Promise.prototype.then()```方法——```(onResolved, onRejected) => {}```
a. ```onResolved```函数: 成功的回调函数——```(value) => {}```
b. ```onRejected```函数: 失败的回调函数——```(reason) => {}```
> 说明: 指定用于得到成功value值的成功回调和用于得到失败reason值的失败回调，<span style="color:red">返回一个新的```promise```对象</span>。
>
> 关于```then()```方法的返回结果，<span style="color:red">是由它指定的回调函数的执行结果决定的</span>：   
>  >  回调函数返回了一个非```promise```类型的数据（number、string、undefined...），```then()```的返回结果就是一个成功的promise，它的Result就是回调函数返回的东西；
>  >  回调函数返回的是一个promise，那这个返回的```promise```就决定了```then()```返回的promise。

### catch方法
```Promise.prototype.catch()```方法——```(onRejected) => {}```
只能指定失败的回调，不能指定成功的回调。
a. ```onRejected```函数: 失败的回调函数——```(reason) => {}```
> 说明: ```then()```的语法糖，相当于: ```then(undefined, onRejected)```

b. 异常穿透使用:当运行到最后，没被处理的所有异常错误都会进入这个方法的回调函数中。

### resolve方法
```Promise.resolve()```方法——```(value) => {}```
和```.then()```和```.catch()```不同的是，```.resolve()```方法不是属于实例对象的，而是属于```promise```这个函数对象的。它的作用就是快速得到一个promise对象，而且还能封装一个值```value```，将这个值转化为promise对象。
a. ```value```: 成功的数据或 promise 对象，是```PromiseResult```的值。
> 说明: 返回一个**成功/失败**的promise 对象，即```PromiseState```是成功/失败。直接改变promise状态为fulfilled或rejected。
> 如果传入的参数为非Promise类型的对象（number、string、boolean、undefined等），则返回的结果为成功promise对象；如果参数为Promise对象，则参数的结果决定了resolve的结果。

```javascript
    let p = Promise.reject(new Promise((resolve, reject) => {
    resolve('OK'); }));      
    console.log(p);
```
### reject方法
```Promise.reject()```方法: ```(reason) => {}```
类似```Promise.resolve()```。
a. ```reason```: 失败的原因，是```PromiseResult```的值。
> 说明: 返回一个**失败**的 promise 对象，即```PromiseState```是失败。直接改变promise状态为rejected。代码示例同上。
> 不管传入的是什么类型，都是失败的。

### all方法
```Promise.all()```方法: ```(promises) => {}```
a. ```promises```: 包含 n 个 promise 的数组
> 说明: 返回一个新的 promise，只有所有的 promise 都成功才成功；只要有一个失败就直接失败。
> 返回的结果：如果最终成功，返回值就是所有的promise对象的成功结果组成的数组；最终失败，返回的就是第一个失败的那个promise对象的失败结果。

```javascript
let p1 = new Promise((resolve, reject) => { resolve('成功');  })
let p2 = Promise.reject('错误错误错误');
let p3 = Promise.resolve('也是成功')
const result = Promise.all([p1, p2, p3]);
console.log(result);
```

### race方法
```Promise.race()```方法: ```(promises) => {}```
使用场景：ajax请求和超时回调，ajax在超时回调执行前完成，显示回调结果，否则显示请求超时。
a. ```promises```: 包含 n 个 promise 的数组
> 说明: 返回一个新的 promise，第一个完成的 promise 的结果状态就是最终的结果状态。
> 如p1延时，开启了异步，内部正常是同步进行，所以p2>p3>p1，结果就是P2。

```javascript
    let p1 = new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve('OK');
        }, 1000);
    })
    let p2 = Promise.resolve('Success');
    let p3 = Promise.resolve('Oh Yeah');
    //调用
    const result = Promise.race([p1, p2, p3]);
    console.log(result);
```


## Promise的几个关键问题
### 一个 promise 指定多个成功/失败回调函数，都会调用吗?
当 promise 改变为对应状态时都会调用。改变状态后，多个回调函数都会调用，并不会自动停止。
```javascript
let p = new Promise((resolve, reject) => {  resolve('OK');});
//指定回调1
p.then(value => {  console.log(value); });
//指定回调2
p.then(value => { alert(value);});
```

### 改变 promise 状态（通过resolve、reject、throw）和指定回调函数（通过then、catch）的执行顺序，谁先谁后?
简单举个例子：在promise代码运行时，是```resolve()```改变状态先执行，还是```then()```指定回调先执行？

+ 都有可能, 正常情况下是先指定回调再改变状态, 但也可以先改状态再指定回调
    1. 先指定回调再改变状态(**异步**)：先指定回调--> 再改变状态 -->改变状态后才进入异步队列执行回调函数
    2. 先改状态再指定回调(**同步**)：改变状态 -->指定回调，并马上执行回调
+ 如何先改状态再**指定**回调? 
    > 注意，<span style = "color:red">**指定**并不是**执行**！</span>
    > 指定：在then中，放入成功或者失败状态，对应状态有特定的回调函数，对应关系，但是没实施

    1. 在执行器中**直接**调用 ```resolve()/reject()```，即不使用定时器等方法，执行器内直接**同步**操作
    2. 延迟调用```then()```，即在```.then()```这个方法外再包一层（如延时器）
+ 什么时候才能得到数据?
    这问题其实就等于在问回调函数什么时候执行。
    1. 如果先指定的回调，那当状态发生改变后，再去调用成功或失败的回调函数，得到数据
    2. 如果先改变的状态（立即调```resolve()```）, 那当指定回调（```then()```调用）时，就会立即调用回调函数得到数据。
    总之就是先改变状态；要是回调还没指定好，状态发生改变就去指定回调，回调要是指定好了的话就立即调用。

```javascript
let p = new Promise((resolve, reject) => {
    //1.异步写法,这样写会先指定回调,再改变状态
    setTimeout(() => {resolve('OK'); }, 1000);
    //2.这是同步写法,这样写会先改变状态,再指定回调
    resolve('OK'); 
});
p.then(value => {console.log(value);}, reason => {})
```

+ 个人理解--结合源码
    源码中，promise的状态是通过一个默认为padding的变量进行判断,所以当你resolve/reject延时(异步导致当then加载时,状态还未修改)后,这时直接进行p.then()会发现,目前状态还是进行中,所以只是这样导致只有同步操作才能成功.
    所以promise将传入的回调函数拷贝到promise对象实例上,然后在resolve/reject的执行过程中再进行调用,达到异步的目的
    具体代码实现看下方自定义promise。
    
### promise.then()返回的新promise的结果和状态由什么决定?
+ 由 ```then()```指定的回调函数执行的结果决定
+ 详细表达:
    1. 如果抛出异常， 新promise变为````rejected```, ```reason```为抛出的异常
    2. 如果返回的是非promise的任意值， 新promise变为```resolved```，```value```为返回的值
    3. 如果返回的是另一个新promise， 此promise的结果就会成为新promise的结果

```javascript
let p = new Promise((resolve, reject) => {
    resolve('ok');
});
//执行 then 方法
let result = p.then(value => {
    console.log(value);
    // 1. 抛出错误 ,变为 rejected
    throw '出了问题';
    // 2. 返回结果是非 Promise 类型的对象,新 promise 变为 resolved
    return 521;
    // 3. 返回结果是 Promise 对象,此 promise 的结果就会成为新 promise 的结果
    return new Promise((resolve, reject) => {
        // resolve('success');
        reject('error');
    });
}, reason => {
    console.warn(reason);
});
```

### promise 如何串连多个操作任务?
1. promise 的```then()```返回一个新的 promise，可以开成```then()```的链式调用
2. 通过```then()```的链式调用串连多个同步/异步任务，这样就能用```then()```将多个同步或异步操作串联成一个同步队列。

```javascript
let p = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve('OK');
    }, 1000);
});
let result = p.then(value => {
    return new Promise((resolve, reject) => {
        resolve("success");
    });
}).then(value => {
    console.log(value, "111");
}).then(value => {
    console.log(value, "222");
})

console.log(result)
```
这段代码的输出为：
```javascript
Promise { <pending> }
success 111
undefined 222
```
最后一个then之所以输出```undefined```，因为上一层的resolve没有指定value。上一层的then返回的是一个promise，这个promise的状态由它指定的回调函数的返回值决定；回调函数也是函数，函数有返回值，然而这里没写返回值，返回的是undefined。所以成功的结果是上一个的返回值，返回undefined，结果就是undefined。

### promise 异常传透?
+ 当使用 promise 的 then 链式调用时，可以在 **最后** 指定失败的回调
+ 前面任何操作出了异常，都会传到最后失败的回调中处理
+ 异常穿透的原因：上面的一串```then()```都没有对异常的处理，所以每个then的返回值就是上一个promise的值，所以最后catch了。

之前我们都是在每个```then()```的第二个回调函数中进行err处理；其实也可以利用异常穿透特性，到最后用```catch```去承接统一处理。两者一起用时，只有前者会生效（因为err已经将其处理，就不会再往下穿透）而走不到后面的```catch```。

在每个```.then()```中可以将数据再次传出给下一个```then()```。

```javascript
let p = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve('OK');
        // reject('error!!error!!')
    }, 1000);
});
p.then(value => {
    //    console.log('@@@@@@@@');
    throw '我这里失败啦'
}).then(value => {
    console.log(value, "222");
}).then(value => {
    console.log(value, "333");
}).catch(reason => {
    console.log(reason, "catchcatch")
})
```
这段代码的输出为：
```javascript
我这里失败啦 catchcatch
```

### 中断 promise 链?
1. 含义：当使用 promise 的```then()```链式调用时，在中间中断，不再调用后面的回调函数

2. <span style="color:red">「有且只有这一种办法」： 在回调函数中**返回一个 pendding 状态的promise 对象**</span>。

    在关键问题：“一个 promise 指定多个成功/失败回调函数, 都会调用吗？”中可以得知，当promise状态改变时，他的链式调用都会生效。那如果有这样一个实际需求：有5个```then()```，但其中有条件判断，如当符合或者不符合第三个then条件时，要直接中断链式调用，不再走下面的then，该如何？就是中断 promise 链的问题。
    
```javascript
let p = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve('OK');
    }, 1000);
});
p.then(value => {
    console.log('@@@@@@@@');
    // 有且只有这一个办法！
    return new Promise(() => { })
}).then(value => {
    console.log(222);
}).then(value => {
    console.log(333);
}).catch(reason => {
    console.log(reason, "catchcatch")
})
```
这段代码的输出为：
```javascript
@@@@@@@@
```

> 注意，then里的回调函数只会在状态改变后执行；状态不改变它就不会执行。 

### 调用resolve或reject终结 Promise 的参数函数?
调用resolve或reject并不会终结 Promise 的参数函数的执行。
```javascript
new Promise((resolve, reject) => {
    resolve(1);
    console.log(2);
}).then(r => {
    console.log(r);
});
// 2
// 1
```

上面代码中，调用```resolve(1)```后，后面的```console.log(2)```还是会执行，并且会首先打印出来。这是因为立即resolved的Promise是在**本轮事件循环的末尾**执行，**总是晚于本轮循环的同步任务**。

### 接上：建议在修改状态函数前加return
一般来说，调用```resolve```或```reject```后，Promise 的使命就完成了，后继操作应该放到```then```里面，而不应该直接写在```resolve```或```reject```的后面。
所以，最好在它们前面加上return语句，这样就不会有意外。
```javascript
new Promise((resolve, reject) => {
    return resolve(1);
    // 后面的语句不会执行
    console.log(2);
})
```

###  宏任务与微任务
考虑下面代码段的输出是什么：
```javascript
let promise = new Promise(function (resolve, reject) {
    console.log('Promise');
    resolve();
});

promise.then(function () {
    console.log('resolved.');
});

console.log('Hi!');
```
这段代码的输出为：
```javascript
Promise
Hi!
resolved.
```
可以发现，明明then是在 Hi 前面，却最后打印。
上面代码中，Promise 新建后立即执行，所以首先输出的是Promise。然后，<span style="color:red">then方法指定的回调函数，将在当前脚本所有同步任务执行完才会执行，所以resolved最后输出</span>。
实际上，这个运行结果相关知识点是 [ 宏任务与微任务 ] 。这里可以先初步理解为:

1. JS是单线程的，从上往下运行，在声明 Promise 时实际上已经执行到了内部方法
2. 为何```resolve()```运行后没有立即打印?
    a. JS中用来存储待执行回调函数的队列包含2个不同特定的列队
    - 宏队列：用来保存待执行的宏任务(回调)，比如：定时器回调/ajax回调/dom事件回调
    - 微队列：用来保存待执行的微任务(回调)，比如：Promise的回调/muntation回调

    b. JS执行时会区别这2个队列
    - JS执行引擎首先必须执行所有的初始化同步任务代码
    - 每次准备取出第一个宏任务执行前，都要将所有的微任务一个一个取出来执行


## 自定义Promise
### 初始结构搭建
下面的代码是完整的index.html：（该章html很多重复，非必要不放上来了）
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Promise-封装 | 1 - 初始结构搭建</title>
    <!-- <script src="./promise.js"></script> -->
</head>

<body>
    <script>
        let p = new Promise((resolve, reject) => {
            resolve('OK');
        });
        console.log(p)
        p.then(value => {
            console.log(value);
        }, reason => {
            console.warn(reason);
        })
    </script>
</body>

</html>
```
我们要覆盖上面的原生Promise，所以要在这个promise.js文件中对它进行重写(这里先使用原生写法，最后会改为class写法：
```javascript
// 声明构造函数
function Promise(executor) { }
// 添加 then 方法
Promise.prototype.then = function (onResolved, onRejected) { }
```
此时再使用```new Promise```，就是以我们重写的作为构造函数了。

### resolve与reject结构构建与基础实现
可以看出，在```index.html```对promise进行实例化时，传入的是一个实参```(resolve, reject) => { }```，在js文件中声明时给了它一个形参，即执行器函数```executor```。所以在执行实例化时，实参会传递给形参。
同理，这个执行器函数```executor```应该也得有两个形参去接收实例化时给它的两个实参```(resolve,reject)```。如果不提前声明这俩直接```executor(resolve, reject)```的话，会报错它们undefined；所以应提前声明。可以看出它们是函数，还得有个参数接收实参，所以整体结构搭建如下：
```javascript
// 声明构造函数
function Promise(executor) {
    // resolve函数
    function resolve(data) {
    }
    // reject函数
    function reject(data) {
    }
    // 同步调用「执行器函数」
    executor(resolve, reject)
}
// 添加 then 方法
Promise.prototype.then = function (onResolved, onRejected) { }
```

接下来分析这两个函数内部应该有的功能代码。首先思考，```resolve(data)```函数执行完毕后有什么效果？效果有二，一是让promise的状态从pending改成fulfilled；二是设置这个promise的成功结果为它传进来的data。即改变状态（promiseState）+设置结果值（promiseResult）。通过查看内置promise的结构可以看出，这俩都是promise实例的内置属性、又有起始值，所以还需要给我们自己的promise添加这两个属性通过```this.PromiseState = ```进行添加。默认设置```PromiseState = 'pending'```以及```PromiseResult = null```，这就是promise状态基础。

如果在函数```function resolve(data)```中也直接通过```this.xxx = 'xxx'```改变这两个属性，打印p之后会发现没有改成功，这是因为```resolve()```在调用的时候，是直接调用的：```resolve('OK');```，它的```this```指向的是```window```，也就是```resolve()```函数执行是在实例化后的windows执行，而不是构造函数执行的，this指向它的调用者windows。
使用```const self = this```保存this执行，使function中可以取得当前实例
> 也可以不使用该方法保存，但下方function需要改为箭头函数，否则function默认指向是window。
> 之后代码默认使用self保存this，箭头函数方式将在最后改为class写法时使用

```javascript
// 声明构造函数
function Promise(executor) {
    // 添加属性
    this.PromiseState = 'pending';
    this.PromiseResult = null;
    // 保存实例对象的 this 的值（也可用箭头函数实现）
    const self = this;
    // resolve 函数
    function resolve(data) {
        //1. 修改对象的状态 (promiseState)
        self.PromiseState = 'fulfilled'; // resolved
        //2. 设置对象结果值 (promiseResult)
        self.PromiseResult = data;
    }
    // reject 函数
    function reject(data) {
        // 1. 修改对象的状态 (promiseState)
        self.PromiseState = 'rejected'; // 
        // 2. 设置对象结果值 (promiseResult)
        self.PromiseResult = data;
    }
    // 同步调用『执行器函数』
    executor(resolve, reject);
}
// 添加 then 方法
Promise.prototype.then = function (onResolved, onRejected) { }
```

### throw 抛出异常改变状态
已经知道，改变promise状态有三种方式，就是resolve、reject和throw抛出异常，throw还会把对象状态变成失败的。如果现在在index.html文件中直接加上一句```throw "error~~~~~~throw"```，会发现这个异常直接被抛出来，后面的代码（比如应该被打印出来的p）连执行都没执行，显然是不对的；正常来说打印出来的p应该是一个失败的promise对象才对。
自然想到使用```try-catch()```，问题就是它加在哪，实际上就是得分析函数```(resolve, reject) => { }```在哪里执行。这个函数其实就是```executor```，在js文件中的```executor(resolve, reject);```这里执行；所以把这句包在try-catch里面。

在上一小节代码的基础上进行修改：将执行器放入```try-catch()```中，这样catch就能接到抛出的异常值，而且抛出的数据就是这个promise失败的结果值。在```catch```中使用```reject()```修改 promise 对象状态为『失败』。

```javascript
try {
    // 同步调用『执行器函数』
    executor(resolve, reject);
} catch (e) {
    // 修改promise对象状态为『失败』
    reject();
}
```

### 状态只能修改一次
+ 基于2、3代码中```resolve()```和```reject()```方法进行修改
+ 思路很简单，就是在这俩方法把状态改了之前判断一下状态有没有被改过就行了；加个判断就行。**通过当前状态是否为pending判断。**
+ 在成功与失败函数中的**最开始**都添加判断```if(self.PromiseState !== 'pending') return;```即可。

在.html调用：
```javascript
let p = new Promise((resolve, reject) => {
    reject("error");
    resolve('OK');
    //抛出异常
    // throw "error";
});
console.log(p);
```
在.promise.js修改，俩函数改法一样：
```javascript
function resolve(data){
    //判断状态
    if(self.PromiseState !== 'pending') return;
    self.PromiseState = 'fulfilled';// resolved
    self.PromiseResult = data;
}
```

### then 方法执行回调基础实现
```then()```方法接收两个函数作为参数，具体执行哪个需要进行判断。判断指标就是这个promise当前的状态```this.PromiseState```。当其为成功时调用```成功回调```，失败时调用```失败回调```。

> 注意修改的是<span style="color:red">原型对象上的```Promise.prototype.then```方法</span>。


html调用：
```javascript
let p = new Promise((resolve, reject) => {
    // resolve('OK');// reject("Error");
    throw "ERROR";
});
p.then(
    value => { console.log(value); },
    reason => { console.warn(reason); }
)
```

promise.js修改与实现：
```javascript
// 添加 then 方法
Promise.prototype.then = function (onResolved, onRejected) {
    // 调用回调函数
    if (this.PromiseState === 'fulfilled') {
        onResolved(this.PromiseResult);
    }
    if (this.PromiseState === 'rejected') {
        onRejected(this.PromiseResult);
    }
}
```


### 异步任务回调的执行
现在要把执行器函数中的```then()```由同步变成异步。就是不直接用```resolve('OK');```改变代码状态了，而是把它放进一个异步，可能是文件/数据库/网络请求的IO，先用```setTimeout```进行模拟。在上节代码上进行模拟，会发现不能正常实现功能，原因在于当执行函数内部是一个异步任务的时候，且已经提前指定好了回调函数，运行异步代码后，执行器内部代码还未返回(因为用了定时器,里面的代码进入了异步队列)，所以当下面的```.then()```运行时，p为```pending```状态，所以根本不会执行```resolve```与```reject```方法。正确的应该是等执行器函数执行完毕改变状态之后，在执行指定的回调函数。

+ 所以为了让回调函数可以正确执行，还需要进行一个状态是pending的判断。

那么重点来了，这个判断内部的逻辑需要写些什么呢？这里需要思考一下回调的执行时机。如果说上面是一个同步任务，可以立即执行，那then里的回调是在then调用的时候立即执行的；如果上面是异步任务，那就应该是改变状态的时候再执行回调。改变状态在哪里？是在声明构造函数中的```resolve```、```reject```内部。所以<span style="color:red">真正调用回调函数的位置应该也是在```resolve```、```reject```内部。</span>可是在它们内部怎么调到then里的两个参数函数呢？

+ 解：在then方法中<span style="color:red">保存回调函数。</span>
+ 将当前回调函数保存到实例对象（存到实例上是为了更方便）中，为promise对象增加一个属性：```this.callback = {}```，然后在then方法判断pending状态内增加```this.callback = { onResolved,onRejected }```。在这样后续改变状态时候才调用得到。

那么为什么要将回调保存到实例上而不是直接调用?

+ 因为回调函数需要在promise**状态改变后**（成功或者失败），再根据状态选择运行哪个函数。
+ 所以当调用```then()```时却检测到状态为```pending```，说明这时候的promise在异步队列不能直接运行成功或者失败函数。
+ 解决：因为```resolve```与```reject```方法与```then()```不在同一个作用域中，不能共享```then(成功回调,失败回调)```的参数，所以在判断状态为```pending```时将回调保存到实例对象上，然后将回调函数的调用放在```resolve()```与```reject()```中。

这样当代码运行到异步队列的```resolve()```或```reject()```时，就可以在这个函数中运行回调函数，实现异步then。
此处对于上一小节有四处修改，代码如下（此处的then仍有瑕疵,需要继续完善）。

html调用：
```javascript
 //实例化对象
let p = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve('OK');
    }, 1000)
});
p.then(
    value => { console.log(value); },
    reason => { console.warn(reason); }
)
console.log(p);
```

promise.js修改与实现：
```javascript
function Promise(executor) {
    // 添加属性
    this.PromiseState = 'pending';
    this.PromiseResult = null;
    this.callback = {};			// -----------新添加1

    const self = this;
    // resolve 函数
    function resolve(data) {
        //判断状态
        if (self.PromiseState !== 'pending') return;
        // 1. 修改对象的状态 (promiseState)、设置对象结果值 (promiseResult)
        self.PromiseState = 'fulfilled'; 
        self.PromiseResult = data;
        // 调用成功的回调函数
        // -----------------------------新添加2 最重要 
        if (self.callback.onResolved) {
            self.callback.onResolved(data);
        }
    }
    
    function reject(data) {
        if (self.PromiseState !== 'pending') return;
        self.PromiseState = 'rejected'; 
        self.PromiseResult = data;
        // 调用失败的回调函数
        // -----------------------------新添加3 最重要 
        if (self.callback.onRejected) {
            self.callback.onRejected(data);
        }
    }
    try {
        executor(resolve, reject);
    } catch (e) {
        reject(e);
    }
}

// 添加 then 方法
Promise.prototype.then = function (onResolved, onRejected) {
    // 调用回调函数
    if (this.PromiseState === 'fulfilled') {
        onResolved(this.PromiseResult);
    }
    if (this.PromiseState === 'rejected') {
        onRejected(this.PromiseResult);
    }
    // 判断 pending 状态
    // ------------新添加4
    if (this.PromiseState === 'pending') {
        //保存回调函数
        this.callback = {
            onResolved: onResolved,
            onRejected: onRejected
        }
    }
}
```

### 指定多个回调
上一小节中保存回调函数的方式有BUG，就是在异步的情况下，如果有多个```.then()```，后面加载的回调函数会覆盖之前的回调函数，导致最后回调函数有且只有最后一个。

+ 原因在于保存回调的时候，保存了两次，第二次保存的就把第一次的覆盖了，等异步完了再回来只有最后一次的回调。
+ 解决：使用数组的方式进行存储回调函数，调用时也是用数组循环取出

代码如下（此处的then仍有瑕疵,需要继续完善）：
html调用：
```javascript
//实例化对象
let p = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve('OK');
        // reject('error~~~')
    }, 1000)
});
p.then(value => { console.log(value);}, reason=>{console.warn(reason);});
p.then(value => { alert(value);}, reason=>{ alert(reason);});
console.log(p);
```

promise.js修改与实现（只展示部分）：
```javascript
function Promise(executor) {
    // -------------------------------修改1 改为复数+数组
    this.callbacks = [];  
    function resolve(data) {
        // 省略
        // 调用成功的回调函数
        // ---------------------------修改2 改为遍历调用
        self.callbacks.forEach(item => {
            item.onResolved(data);
        })
    }
    function reject(data) {
        // 省略
        // 调用失败的回调函数
        // ---------------------------修改3 改为遍历调用
        self.callbacks.forEach(item => {
            item.onRejected(data);
        })
    }
    // 省略
}
    
Promise.prototype.then = function (onResolved, onRejected) {
    // 省略
    if (this.PromiseState === 'pending') {
        // 保存回调函数
        // ---------------------------修改4 改为加入数组
        this.callbacks.push({
            onResolved: onResolved,
            onRejected: onRejected
        });
    }
}
```

### 同步任务 then 返回结果
同步任务，即在执行器函数中直接调```resolve()```/```reject()```或用```throw```改变状态。
运行html文件测试代码，查看使用```then()```后的返回结果：
```javascript
let p = new Promise((resolve, reject) => {
    resolve('OK');
    // reject('error~~~')
});
const res = p.then(
    value => {
        // console.log(value); 
        // return 'hello promise~!'
        // return new Promise((resolve, reject) => {
        //     // resolve("success~~~~")
        //     // reject('oh NO~~~')
        // })
        // 抛出异常
        throw "FAIL!!!"
    },
    reason => { console.warn(reason); }
)
console.log(res)
```
发现使用```then()```后的返回结果是其回调函数的返回结果，而我们需要的返回结果是一个新的promise对象。并且<span style="color:red">最终返回状态==回调函数中的新promise状态</span>。

+ 解：```then()```中```return new Promise()```，使其返回个新的promise对象

新问题：新的promise对象因为没有用```reject()```与```resolve()```方法（这里的```reject()```与```resolve()```是then返回的新promise的！不是上面定义好的！），导致返回的状态一直是```pending```。

+ 解：根据```then()```的回调函数的执行结果改变新的promise的状态。判断运行回调函数后的返回值是什么，然后根据其不同类型给其赋予不同状态

那么```then()```的回调函数是在哪里执行的呢？假如实例化对象中的同步任务为```resolve('OK');```，就意味着执行的是then里的第一个回调函数，这个回调函数执行完后我们自己的promise实例的状态已经成功变为‘fulfilled’，就说明一定会走我们自定义的then方法中的```onResolved(this.PromiseResult);```。在这就可以拿到回调函数的执行结果，用result保存。接下来判断这个结果类型即可。
> 注：关于```then()```方法的返回结果，<span style="color:red">是由它指定的回调函数的执行结果决定的</span>：   
>  >  回调函数返回了一个非```promise```类型的数据（number、string、undefined...），```then()```的返回结果就是一个成功的promise，它的Result就是回调函数返回的东西；
>  >  回调函数返回的是一个promise，那这个返回的```promise```就决定了```then()```返回的promise。

那么怎样才能改变这个```then()```方法返回对象结果（即```res```）的状态呢？事实上，```res```其实就是我们自己写的then方法中return的```new Promise()```=>改变返回对象结果（即```res```）的状态，就是改变我们自己写的then方法中return的```new Promise()```的状态=>要在我们自己写的then方法中return的```new Promise()```的执行器函数内部调用```resolve()```或者```reject()```。

+ .html文件中```then()```方法回调函数里return的```new Promise()```是成功，```res```就是成功；.html文件中```then()```方法回调函数里return的```new Promise()```成功的结果，就是```res```成功的结果。
+ 现在要解决的问题是，让```res```从pending变成成功且有正确的成功结果；转化为让.html文件中```then()```方法回调函数里return的```new Promise()```的状态变成成功且有正确的成功结果。
+ 又因为.html文件中```then()```方法回调函数里return的```new Promise()```，就是我们自己写的then方法中的```result```；问题又转化为让我们自己写的then方法中的```result```的状态变成成功且有正确的成功结果。
+ 我们自己写的then方法中的```result```是个promise，那就肯定有```then()```方法；我们自己写的then方法中的```result```成功了，就会走这个```result```自己的then方法中处理成功的逻辑，在```result.then()```里面走```resolve()```逻辑，就会让这个```result```的状态变成成功且有正确的成功结果。

promise.js修改与实现：
```javascript
Promise.prototype.then = function (onResolved, onRejected) {
    // -------------------------------------------修改1
    return new Promise((resolve, reject) => {
        if (this.PromiseState === 'fulfilled') {
            // -------------------------------------------修改2
            try {
                // 获取回调函数的执行结果
                let result = onResolved(this.PromiseResult);
                // 判断
                if (result instanceof Promise) {
                    // 如果是Promise类型的对象
                    result.then(v => {
                        resolve(v)
                    }, r => {
                        reject(r)
                    })
                } else {
                    // 返回的对象状态为成功
                    resolve(result)
                }
            } catch (e) {
                reject(e)
            }
        }
        if (this.PromiseState === 'rejected') {
            onRejected(this.PromiseResult);
        }
        // 省略
    })
}
```

### 异步任务 then 返回结果
异步任务是修改if(this.PromiseState === 'pending')后面的值,原因参考6,下面代码只举例这部分修改

因为我们需要增加then状态修改,所以在我们保存回调函数这一步我们可以对于回调函数进行加工,添加判断其回调函数的返回值的代码块再存入实例的回调函数中

Ⅰ-声明一个新的函数:其内部功能->先运行onResolved回调函数,再将其返回值取出,进行判断其返回值(这个过程同8)

Ⅱ-加工后存入实例回调函数数组,之后在resolve与reject方法中调用即可(同6)
html调用：
```javascript
   //实例化对象
    let p = new Promise((resolve, reject) => {
      setTimeout(() => {reject("Error");}, 1000)}); // resolve('OK');
    //执行 then 方法
    const res = p.then(value => {
      // return 'oh Yeah';  //如果有返回,根据其返回值得到相应的状态:字符串为成功,抛出为错误
      throw 'error';
    }, reason => {
      console.warn(reason, "xx"); //如果只是打印没返回,则实际上时返回一个undefined,
      //在我们封装js中,undefined会判定为非promise对象,所以状态为成功,结果为undefined
      return "sss"   // throw 'error';
    });
    console.log(res);
```
promise.js修改与实现：
```javascript
    //判断 pending 状态
    if (this.PromiseState === 'pending') {
      //保存回调函数
      this.callbacks.push({
          
        onResolved: function () {
          try {
            //执行成功回调函数
            let result = onResolved(self.PromiseResult);
            //判断 其结果
            if (result instanceof Promise) {
              result.then(
                  v => { resolve(v);},
                  r => {reject(r);}
                 )
            } else {resolve(result);}
          } catch (e) {reject(e);}
        },
          
        onRejected: function () {
          try {
            //执行成功回调函数
            let result = onRejected(self.PromiseResult);
            //判断
            if (result instanceof Promise) {
              result.then(
                  v => {resolve(v); },
                  r => {reject(r);}
                 )
            } else {resolve(result);}
          } catch (e) { reject(e); }
        }
      });
    }
```

### then方法代码优化

在8、9、10中可以看出,其判断与改变返回结果状态的代码块是基本重复的,所以可以将其抽出。在promise.js中修改：
```javascript
//添加 then 方法
Promise.prototype.then = function (onResolved, onRejected) {
  const self = this;
  return new Promise((resolve, reject) => {
    // 封装函数----------------------------------------
    function callback(type) {
      try {
        //获取回调函数的执行结果
        let result = type(self.PromiseResult);
        //判断
        if (result instanceof Promise) {
          //如果是 Promise 类型的对象
          result.then(v => {
            resolve(v);
          }, r => {
            reject(r);
          })
        } else {
          //结果的对象状态为『成功』
          resolve(result);
        }
      } catch (e) {
        reject(e);
      }
    }
  //----------------------------------------------------
    //调用回调函数  PromiseState
    if (this.PromiseState === 'fulfilled') {
      callback(onResolved);
    }
    if (this.PromiseState === 'rejected') {
      callback(onRejected);
    }
    //判断 pending 状态
    if (this.PromiseState === 'pending') {
      //保存回调函数
      this.callbacks.push({
        onResolved: function () {
          callback(onResolved);
        },
        onRejected: function () {
          callback(onRejected);
        }
      });
    }
  })
}
```

### catch 方法与异常穿透与值传递

异常穿透:添加catch 方法 ,并且需要进行回调函数为undefined的处理

当我then()中只传一个回调或者不传回调函数时,运行代码会报错,因为运行时调用的回调函数是undefined

解:进行回调函数判断,当其为空时,基于默认回调函数内容:直接往外抛出这样下方的then() or catch()就可以承接到异常或者值
html调用：
```javascript
//实例化对象
    let p = new Promise((resolve, reject) => {
      setTimeout(() => {resolve('OK'); }, 1000);
    });
    //值传递
    p.then()
    .then(value => {console.log(222);})
      .then(value => {console.log(333);})
        .catch(reason => {console.warn(reason);});
```

promise.js修改与实现：
```javascript
//添加 then 方法
Promise.prototype.then = function (onResolved, onRejected) {
	...				-----------修改1
  if (typeof onRejected !== 'function') {onRejected = reason => { throw reason;}}
  if (typeof onResolved !== 'function') { onResolved = value => value;}
	 ....
}
//添加 catch 方法  
Promise.prototype.catch = function(onRejected){  ---------------异常穿透 修改2
    return this.then(undefined, onRejected);
}
```
