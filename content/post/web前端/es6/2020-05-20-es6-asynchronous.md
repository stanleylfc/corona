+++
title="es6 实战（五）| 异步函数"
tags=["es6"]
categories=["es6"]
date="2020-05-20 T09:00:00+08:00"
+++

## 1. callback
### 1.1 callback 回调函数
- 封装
```
function add(){
    // 这里走接口
    let parameter = {
        // 接口参数
    }
    api(parameter).then(res => {
        // 接口成功要执行的代码
        // 接口成功后如果有回调函数就执行回调函数如果没有就不执行
        callback && callback()
    }).catch(e =>{
        console.log(e)
    })
}
```
- 调用接口
```
add(() =>{
    // 写接口成功后要执行的代码
})
```

## 2.Promise 解决异步代码
### 2.1 封装
```
变量写法：

let promise = new Promise((resolve, reject) =>{
    api(parameter).then(res => {
        // 成功就把res返回出去
        resolve(res)
    }).catch(e =>{
        // 失败就返回错误，可以直接这样写，new Error(' 报错了 ')
        reject(new Error(e))        
    })
})


函数写法：

function promise  (){
    return new Promise((resolve, reject) =>{
        api(parameter).then(res => {
            // 成功就把res返回出去
            resolve(res)
        }).catch(e =>{
            // 失败就返回错误，可以直接这样写，new Error(' 报错了 ')
            reject(new Error(e))        
        })
    })
}
```
### 2.2 调用
```
promise().then(res =>{
    // 这里写成功时候要执行的代码
}).catch(e =>{
    这里写失败时候要执行的代码
})
```


### 3.  async 和 await来解决异步
```
async function Fun3(){
    try{
        let res = await Fun1()
        let res2 = await Fun2()
    } catch (e) {
        // 只要Fn1报错了就错误就会直接抛到这里
    }
}
```

[265948](http://www.imooc.com/article/265948)