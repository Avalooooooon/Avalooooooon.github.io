---
title: 手写promise
updated: 2022-11-01 11:32:14
date: 2022-11-01 11:32:14
tags: [JS]
categories: [前端,JS]
---

从初识结构开始，依次实现Promise的实例方法、静态方法、then方法指定的回调函数的异步执行和封装成class。
<!-- more -->
## Promise的实例方法实现

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

接下来分析这两个函数内部应该有的功能代码。首先思考，```resolve(data)```函数执行完毕后有什么效果？效果有二，一是让promise的状态从pending改成fulfilled；二是设置这个promise的成功结果为它传进来的data。即<span style="color:red">改变状态（promiseState）+ 设置结果值（promiseResult）</span>。通过查看内置promise的结构可以看出，这俩都是promise实例的内置属性、又有起始值，所以还需要给我们自己的promise添加这两个属性，通过```this.PromiseState = 'xxx'```进行添加。默认设置```PromiseState = 'pending'```以及```PromiseResult = null```，这就是promise状态基础。

如果在函数```function resolve(data)```中也直接通过```this.xxx = 'xxx'```改变这两个属性，打印p之后会发现没有改成功，这是因为```resolve()```在调用的时候，是直接调用的```resolve('OK');```，它的```this```指向的是```window```，也就是```resolve()```函数执行是在实例化后的windows执行，而不是构造函数执行的，this指向它的调用者windows。
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
自然想到使用```try-catch()```，问题就是它加在哪，实际上就是得分析函数```(resolve, reject) => { }```在哪里执行。这个函数其实就是```executor```，在js文件中的 ```executor(resolve, reject);``` 这里执行；所以把这句包在try-catch里面。

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
promise的```then()```方法接收两个函数作为参数，具体执行哪个需要进行判断。判断指标就是这个promise当前的状态```this.PromiseState```。当其为成功时调用```成功回调```，失败时调用```失败回调```。

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
现在要把执行器函数中的```then()```由同步变成异步。就是不直接用```resolve('OK');```改变代码状态了，而是把它放进一个异步，可能是文件/数据库/网络请求的IO，先用```setTimeout```进行模拟。在上节代码上进行模拟，会发现不能正常实现功能，原因在于当执行函数内部是一个异步任务且已提前指定好回调函数的时候，运行异步代码后，执行器内部代码还未返回(因为用了定时器,里面的代码进入了异步队列)，所以当下面的```.then()```运行时，p为```pending```状态，所以根本不会执行```resolve```与```reject```方法。正确的应该是等执行器函数执行完毕改变状态之后，在执行指定的回调函数。

+ 所以为了让回调函数可以正确执行，还需要进行一个状态是pending的判断。

那么重点来了，这个判断内部的逻辑需要写些什么呢？这里需要思考一下回调的执行时机。如果说上面是一个同步任务，可以立即执行，那then里的回调是在then调用的时候立即执行的；如果上面是异步任务，那就应该是改变状态的时候再执行回调。改变状态在哪里？是在声明构造函数中的```resolve```、```reject```内部。所以<span style="color:red">真正调用回调函数的位置应该也是在```resolve```、```reject```内部。</span>可是在它们内部怎么调到then里的两个参数函数呢？

+ 解：在then方法中<span style="color:red"> 保存回调函数。 </span>
+ 将当前回调函数保存到实例对象（存到实例上是为了更方便）中，为promise对象增加一个属性：```this.callback = {}```，然后在then方法判断pending状态内增加```this.callback = { onResolved,onRejected }```。这样后续改变状态时候才调用得到。

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
上一节保存回调函数的方式有bug，就是在异步的情况下，如果有多个```.then()```，后面加载的回调函数会覆盖之前的回调函数，导致最后回调函数有且只有最后一个。

+ 原因在于保存回调的时候，保存了两次，第二次保存的就把第一次的覆盖了，等异步完了再回来只有最后一次的回调。
+ 解决：<span style="color:red">使用数组的方式进行存储回调函数，调用时也是用数组循环取出。</span>

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
运行html文件测试代码，查看使用```then()```后的 **返回结果** ：
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
发现使用```then()```后的返回结果(```res```)是其回调函数的返回结果，而我们需要的返回结果```res```是一个新的promise对象。并且<span style="color:red">```res```的最终返回状态 == 回调函数中的新promise状态</span>。

+ 解：```then()```里```return new Promise()```，使其返回个新的promise对象

新问题：新的promise对象因为没有用```reject()```与```resolve()```方法（这里的```reject()```与```resolve()```是then返回的新promise的！不是上面定义好的！），导致返回的这个新的promise对象的状态一直是```pending```。

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
在上一节代码上，在html文件中将promise内的执行器改为异步任务。直接打印then方法返回值```res```，会发现它的状态是pending。这显然是不对的，因为需要根据then里回调函数的返回值决定它的状态。之所以没变，是因为现在走到```then()```方法时，我们自己的实例p进入了异步队列，p的状态是pending，然而在我们自定义的then方法中，它只会进入```if (this.PromiseState === 'pending')```的判断；而在这个判断内部的逻辑中没有对```res```这个promise对象的```resolve()```、```reject()```两个方法的执行，自然无法改变状态。
显然一直```pending```是不对的；正确的应该是根据回调函数的返回值改变状态。注意<span style="color:red">回调函数真正执行的地方是在promise的构造函数里的```resolve()```和```reject()```方法中</span>（回调函数放进了```callback```数组；通过在这两个方法中遍历，通过```item.onResolved(data)```执行）。
需要修改```then()```方法中的```if(this.PromiseState === 'pending')```内部逻辑。

此时的html代码如下：
```javascript
let p = new Promise((resolve, reject) => {
    setTimeout(() => {
        // reject("Error");
        resolve('OK');
    }, 1000)
});

const res = p.then(value => {
    console.log(value)
}, reason => {
    console.warn(reason);
    return "sss"   // throw 'error';
})
console.log(res)
```

+ 那么如何进行```if(this.PromiseState === 'pending')```内部逻辑修改？进行如下考虑。
  1. 肯定不能简单的直接把```onResolved()```等俩函数添到```callback```数组的对象中去，因为需要增加新逻辑。在这里声明新函数：
```javascript
  this.callbacks.push({
    onResolved: function () {
        // 执行成功的回调函数
    },
    onRejected: function () {
        console.log('error')
    }
  });
```
2. 那如何执行成功的回调函数？这个回调函数即then方法中传入的参数```onResolved```。自然的想到可以通过 
  ```javascript
  onResolved: function () {
    // 执行成功的回调函数
    onResolved(this.PromiseResult)
  },
```
的方式调用。但是这里的```this```存在问题：由于第二个```onResolved```是个<span style="color:red">函数，是在有着第一个```onResolved```作为一个属性名的 ** 对象 **中调用的，所以是这里的```this```指向的是要push进```callback```数组中的那个 ** 对象 ** ，而不是我们的promise！</span>所以为了能够获取到我们的promise对象，依然是通过在```then()```方法的最前面添加```const self = this```来保存一下this值。此时试运行，可以看到回调函数正常执行，但状态依然为```pending```。
3. 状态怎么变？首先，状态是由```onResolved(self.PromiseResult)```的执行结果决定的。所以再来一轮上一节中的```let result = onResolved(self.PromiseResult)...```这一堆。同样在外面套一层```try...catch```进行异常处理。

promise.js修改与实现：
```javascript
Promise.prototype.then = function (onResolved, onRejected) {
    const self = this;
    return new Promise((resolve, reject) => {
        // 调用回调函数
        if (this.PromiseState === 'fulfilled') {
            ...
        }
        if (this.PromiseState === 'rejected') {
            ...
        }
        // 判断 pending 状态
        if (this.PromiseState === 'pending') {
            // 保存回调函数
            this.callbacks.push({
                onResolved: function () {
                    try {
                        // 执行成功的回调函数
                        let result = onResolved(self.PromiseResult)
                        // 判断
                        if (result instanceof Promise) {
                            result.then(v => {
                                resolve(v)
                            }, r => {
                                reject(r)
                            })
                        } else {
                            resolve(result)
                        }
                    } catch (e) {
                        reject(e)
                    }
                },
                onRejected: function () {
                    // 和onResolved一样
                },
            });
        }
    })
}
```

### then方法代码优化
在8、9、10中可以看出，```then()```方法中判断与改变返回结果状态的代码块（```try...catch```里面那堆基本重复，所以可以将其抽出，封装成函数```callback()```。
注意这段代码
```javascript
// 获取回调函数的执行结果
let result = onResolved(this.PromiseResult);
```
中等号后面可能是```onResolved```，也可能是```onRejected```。把这俩选项作为一个参数```type```传进来。
修改好后直接运行，发现无法正常输出。这是因为``` let result = type(this.PromiseResult);```中的this指向又出了问题，之前的this指向的是要push进```callback```数组中的对象，而这里this在函数里，函数是直接调用的，所以this指向的是window！也一样得做处理。所以把这一句里的```this```改成```self```。

在promise.js中修改：
```javascript
Promise.prototype.then = function (onResolved, onRejected) {
    const self = this;
    return new Promise((resolve, reject) => {
        // 封装函数
        function callback(type) {
            try {
                // 获取回调函数的执行结果
                let result = type(self.PromiseResult);
                if (result instanceof Promise) {
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
        // 调用回调函数
        if (this.PromiseState === 'fulfilled') {
            callback(onResolved)
        }
        if (this.PromiseState === 'rejected') {
            callback(onRejected)
        }
        // 判断 pending 状态
        if (this.PromiseState === 'pending') {
            // 保存回调函数
            this.callbacks.push({
                onResolved: function () {
                    callback(onResolved)
                },
                onRejected: function () {
                    callback(onRejected)
                },
            });
        }
    })
}
```

### catch 方法——异常穿透与值传递
在现有的代码中，把```p.then()```的部分换成```p.catch(reason => { console.warn(reason) })```，会出现报错```p.catch is not a function```。因为我们在自定义promise时就没添加catch方法。
需要添加```catch()```方法。它相当于接收了then方法中处理失败情况的回调函数，所以我们在catch方法中传入一个参数```onRejected```。需要注意的是<span style="color:red">```catch()```方法的返回值也是个Promise！！！</span>可以在里面直接用```then()```方法。到此为止catch的基本功能实现，不需要进行异常穿透的情况下功能正常。

promise.js修改与实现：
```javascript
// 添加catch方法
Promise.prototype.catch = function(onRejected){
    return this.then(undefined,onRejected)
}
```

还需要实现异常穿透。异常穿透：在链式调用的过程中，中间的任务不需要对失败的结果做处理，只需要最后加个```catch()```方法处理失败结果就行。现在执行下面的html：
```javascript
let p = new Promise((resolve, reject) => {
    setTimeout(() => {
        reject('OK');
    }, 1000)
});

p.then(value => { console.log(111);})
.then(value => { console.log(222);})
.then(value => { console.log(333);})
.catch(reason => {
    console.warn(reason)
})
```

会报错```type is not a function```。考虑.html中代码的执行顺序。执行完```new Promise()```后，p的状态仍为```pending```，此时调用```then()```时会通过```onRejected: function () { callback(onRejected) }```对这几个回调函数都进行保存，保存的地方是```p```这个对象的属性；成功的回调就是那几个```console.log(111)```，失败的回调就是undefined（因为第二个参数没有传），也就是```type```是```undefined```。所以会报错。
继续分析的话，1000ms过去状态改完之后开始调用回调；但在```item```中只有成功的没失败的，失败的那个还是undefined，所以在```item.onRejected()```这一句就会报错，因为```onRejected()```这个位置是undefined，而不是个函数。所以第一个```.then()```的返回结果就是一个<span style="color:red">失败的Promise</span>，第二三个也一样，直到```catch()```去处理它们失败的结果。

总之 ** 产生这个问题的原因就是```then()```里没传第二个回调函数。 ** 不过原生的promise允许不传，所以 ** 在```then()```方法添加回调函数为```undefined```的处理。 **

+ 解决方法：进行回调函数判断：当其为空时，基于默认回调函数内容直接往外抛出。这样下方的```then()```or ```catch()```就可以承接到异常或者值。

promise.js修改与实现：
```javascript
Promise.prototype.then = function (onResolved, onRejected) {
    const self = this;
    // 判断回调函数参数
    if(typeof onRejected !== 'function'){
        onRejected = reason => {
            throw reason;
        }
    }
    return new Promise((resolve, reject) => {...})
}
```
还需要实现promise的另一个特性：值传递。就是如果第一个回调函数不传，像这样：```p.then().then( ... )```，整个链式调用也可以正常推进。此时用上面的代码依然不行，而且原因都是由于```.then()```传入的参数undefined，这里的情况是在第一个```then()```里两个回调都是```undefined```，不止```onRejected()```是。处理方法也和上面类似，就是再加一组：
```javascript
Promise.prototype.then = function (onResolved, onRejected) {
    const self = this;
    // 判断回调函数参数
    if(typeof onRejected !== 'function'){
        onRejected = reason => {
            throw reason;
        }
    }
    if(typeof onResolved !== 'function'){
        onResolved = value => value;
        // 是 value => {return value}的简写。
    }
    return new Promise((resolve, reject) => {...})
}
```


## Promise的静态方法实现
### Promise.resolve 封装

+ 这个方法在API里介绍过，它返回一个promise对象，且状态由传入的值确定。
  1. 如果为promise：将其状态与结果赋值给外层promise对象
  2. 如果为非promise：状态设置为成功，结果就是这个值

现在会报错```Promise.resolve is not a function```。添加该方法。注意这个方法是Promise函数对象的，而非实例对象的，所以```Promise.resolve = function(){}```的形式添加；接受一个参数```value```，返回一个promise对象。状态由传入的```value```决定。

html调用：
```javascript
// const p = Promise.resolve('OK');
// const p = Promise.resolve(new Promise((resolve, reject) => {
//     reject("error");
//     // resolve('Success');
// }));
const p = Promise.resolve(Promise.resolve('Oh Yeah'));
console.log(p);
```

promise.js修改与实现：
```javascript
// 添加resolve方法
Promise.resolve = function (value) {
    // 返回promise对象
    return new Promise((resolve, reject) => {
        if (value instanceof Promise) {
            value.then(v => {
                resolve(v);
            }, r => {
                reject(r)
            })
        } else {
            // 状态设置为成功
            resolve(value);
        }
    })
}
```

### Promise.reject 封装
不同于```resolve()```，这个方法只要把传入参数再次传出去并将状态改为失败即可。
html调用：
```javascript
const p = Promise.reject('Error');
// const p = Promise.reject(new Promise((resolve, reject) => {
//     resolve('OK');
// }));
console.log(p);
```
promise.js修改与实现：
```javascript
/// 添加reject方法
Promise.reject = function (reason) {
    return new Promise((resolve, reject) => {
        reject(reason)
    })
}
```
注意上面的第二种p，输出结果的```PromiseResult```是一个状态成功，值“OK”的promise。

### Promise.all 封装
+ 遍历传入的promise数组，即参数是promise数组，返回结果是一个promise对象：
  1. 每当遍历结果是成功，则用计数器记录，当计数器等同于数组长度（数组全部成员都成功），返回成功状态，且 **成功结果** 是这一堆promise **成功结果的数组** 。
  2. 如果当数组中任意一个promise的执行结果是reject，直接中断，返回失败状态。
  需要给```all()```函数增加一个计算成功数的变量 + 一个保存成功结果的数组。

html调用：
```javascript
let p1 = new Promise((resolve, reject) => {
    setTimeout(() => { resolve('OK'); }, 1000)
})

let p2 = Promise.resolve('Success');
// let p2 = Promise.reject('Success');
let p3 = Promise.resolve('Oh Yeah');
//调用 all 方法
let result = Promise.all([p1, p2, p3]);
console.log(result);
```

promise.js修改与实现：
```javascript
Promise.all = function (promises) {
    return new Promise((resolve, reject) => {
        //声明变量
        let count = 0;
        let arr = [];
        //遍历
        for (let i = 0; i < promises.length; i++) {
            promises[i].then(v => {
                // 在这就可以得知当前对象的状态是成功
                count++;
                arr[i] = v;
                if (count === promises.length) {
                    resolve(arr);  // 修改状态
                }
            }, r => {
                reject(r);
            });
        }
    });
}
```

> 将当前promise对象成功的结果存入到数组这一步，<span style="color:red">不能用```arr.push(v)```！</span>
> 这是因为这几个promise执行的顺序可能有先有后，所以不一定是按照p1 - p2 - p3的先后顺序压入数组的。

### Promise.race 封装
直接谁先执行就返回谁的运行结果即可。总之就是无论返回的是谁，直接```resolve()```或者```rejected()```。一样得先遍历。
注意是<span style="color:red">改变返回结果的状态</span>为成功/失败。

html调用：
```javascript
let p1 = new Promise((resolve, reject) => {
    setTimeout(() => { resolve('OK'); }, 1000)
})
let p2 = Promise.resolve('Success');
// let p2 = Promise.reject('Success');
let p3 = Promise.resolve('Oh Yeah');
//调用 race 方法
let result = Promise.race([p1, p2, p3]);
console.log(result);
```

promise.js修改与实现：
```javascript
Promise.race = function (promises) {
    return new Promise((resolve, reject) => {
        for (let i = 0; i < promises.length; i++) {
            promises[i].then(v => {
                //修改返回对象的状态为 『成功』
                resolve(v);
            }, r => {
                //修改返回对象的状态为 『失败』
                reject(r);
            })
        }
    });
}
```

## then方法指定的回调函数「异步执行」
如果我们运行下面代码，正确顺序是：```111 --> 333 -->444```：
```javascript
 let p1 = new Promise((resolve, reject) => {
     reject('OK');
     console.log(111);
   });

   p1.then(value => {
     console.log(222);
   }, reason => {
     console.log(444);
   });

   console.log(333);
```

这是因为```then()```指定的回调函数是异步执行的，需要等所有同步代码执行完成后才能执行。但当运行之前封装的 Promise 代码时，结果却是：```111 --> 444 --> 333```。需要将我们自己写的的```then()```方法变成异步方法。修改的位置应该在真正去调用回调函数的地方，```then()```中有两处，（同步执行的情况）```promise```的定义中有两处（异步执行的情况，需要进队列）。

只要在以下四处地方的回调函数调用外层包裹一层定时器（不一定是定时器，开启异步即可），即可做到异步操作：
```javascript
function Promise(executor) {
    // ...
    function resolve(data) {
        setTimeout(() => { // ----------------------修改1
            self.callbacks.forEach(item => {
                item.onResolved(data);
            });
        });
    }
    function reject(data) {
        setTimeout(() => { // ----------------------修改2
            self.callbacks.forEach(item => {
                item.onRejected(data);
            });
        });
    }
    // ...
}

Promise.prototype.then = function (onResolved, onRejected) {
    // ...
    return new Promise((resolve, reject) => {
        // ...
        /*  修改前代码
        if (this.PromiseState === 'fulfilled') { callback(onResolved); }
        if (this.PromiseState === 'rejected') { callback(onRejected); }
        */
        if (this.PromiseState === 'fulfilled') {
            setTimeout(() => {   // ----------------------修改3
                callback(onResolved);
            });
        }
        if (this.PromiseState === 'rejected') {
            setTimeout(() => {   // ----------------------修改4
                callback(onRejected);
            });
        }
    })
}
```

> 相关原理参照js事件循环机制、宏任务与微任务。

## 封装成class
大概框架为：
```javascript
class Promise{
    // 构造方法
    constructor(executor){
        // 放之前function Promise(executor) {}内的东西
    }
    
    // then方法封装
    then(onResolved,onRejected){
        // 放之前Promise.prototype.then = function (onResolved, onRejected) {}内的东西
    }
    
    // catch方法
    catch(onRejected){
        // 放之前Promise.prototype.catch = function (onRejected) {}内的东西
    }
    
    // resolve方法:注意这个方法不属于实例对象，它属于这个类
    // 所以需要用static关键字表明这是一个静态成员，属于类而不属于实例对象
    static resolve(value) {
        // 放之前Promise.resolve = function (value) {}内的东西
    }
    
    // reject方法
    static reject(reason) {
        // 放之前Promise.reject = function (value) {}内的东西
    }

    // all 方法
    static all(promises) {
        // 放之前Promise.all = function (value) {}内的东西
    }

    //race 方法
    static race(promises) {
        // 放之前Promise.race = function (value) {}内的东西
    }
}
```
下面为.html文件中的部分测试代码：
```javascript
let p1 = new Promise((resolve, reject) => {
    setTimeout(() => {
        // resolve("OK");
        reject("Error");
    })
});

p1.then(value => {
    console.log(value);
}, reason => {
    console.warn(reason);
});

console.log(Promise.resolve('OK'));
```