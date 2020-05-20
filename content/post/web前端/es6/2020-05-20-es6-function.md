+++
title="es6 实战（二）| 函数"
tags=["es6"]
categories=["es6"]
date="2020-05-20 T09:00:00+08:00"
+++
## 1. 函数
- 函数声明是

## 2. 函数表达式

## 3. 箭头函数

## 4. 函数参数内使用解构
### 4.1 function({}) 定义函数
- 接收可选参数的函数，是很棒的。对于这种用法，你也可以添加默认参数值来填充调用者没有传递或忘记传递的参数值 。
```
var sayHello = function({ name, surname }) {
  console.log(`Hello ${name} ${surname}! How are you?`);
};

sayHello({ name: 'John', surname: 'Smith' })
// -> Hello John Smith! How are you?
```
### 4.2 function({}={}) 默认参数类型
- 表示此参数需要解构的默认对象是一个{}，以防调用者忘记传值，或传递了一个错误类型（大多情况为后者）。
```
var sayHello2 = function({ name = "Anony", surname = "Moose" } = {}) {
  console.log(`Hello ${name} ${surname}! How are you?`);
};
```