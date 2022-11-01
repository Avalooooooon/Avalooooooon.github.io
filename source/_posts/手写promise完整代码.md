---
title: 手写promise完整代码
updated: 2022-11-01 11:29:40
date: 2022-11-01 11:29:40
tags: [JS]
categories: [前端,JS]
---

给出自定义的```promise.js```文件。
在.html文件中通过```<script src="./promise.js"></script>```引入即可实现完整功能。
<!-- more -->

```javascript
class Promise {
    // 构造方法
    constructor(executor) {
        // 添加属性
        this.PromiseState = 'pending';
        this.PromiseResult = null;
        this.callbacks = [];
        // 保存实例对象的 this 的值（也可用箭头函数实现）
        const self = this;
        // resolve 函数
        function resolve(data) {
            //判断状态
            if (self.PromiseState !== 'pending') return;
            // 1. 修改对象的状态 (promiseState)
            self.PromiseState = 'fulfilled'; // resolved
            // 2. 设置对象结果值 (promiseResult)
            self.PromiseResult = data;
            // 调用成功的回调函数
            setTimeout(() => {
                self.callbacks.forEach(item => {
                    item.onResolved(data);
                })
            })
        }
        // reject 函数
        function reject(data) {
            //判断状态
            if (self.PromiseState !== 'pending') return;
            // 1. 修改对象的状态 (promiseState)
            self.PromiseState = 'rejected'; // 
            // 2. 设置对象结果值 (promiseResult)
            self.PromiseResult = data;
            // 调用失败的回调函数
            setTimeout(() => {
                self.callbacks.forEach(item => {
                    item.onRejected(data);
                })
            })
        }

        try {
            // 同步调用『执行器函数』
            executor(resolve, reject);
        } catch (e) {
            // 修改promise对象状态为『失败』
            reject(e);
        }
    }

    // then方法封装
    then(onResolved, onRejected) {
        const self = this;
        // 判断回调函数参数
        if (typeof onRejected !== 'function') {
            onRejected = reason => {
                throw reason
            }
        }
        if (typeof onResolved !== 'function') {
            onResolved = value => value;
            // 是 value => {return value}的简写。
        }
        return new Promise((resolve, reject) => {
            // 封装函数
            function callback(type) {
                try {
                    // 获取回调函数的执行结果
                    let result = type(self.PromiseResult);
                    // 判断
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
                setTimeout(() => {
                    callback(onResolved)
                })
            }
            if (this.PromiseState === 'rejected') {
                setTimeout(() => {
                    callback(onRejected)
                })
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

    // catch方法
    catch(onRejected) {
        return this.then(undefined, onRejected)
    }

    // resolve方法
    static resolve(value) {
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

    // 添加reject方法
    static reject(reason) {
        return new Promise((resolve, reject) => {
            reject(reason)
        })
    }

    // 添加 all 方法
    static all(promises) {
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

    //添加 race 方法
    static race(promises) {
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
}
```