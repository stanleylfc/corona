+++
title="vue 实战（三）| vuecli"
tags=["vue"]
categories=["vue"]
date="2020-04-23T09:00:00+08:00"
+++

## 1. 安装
### 1.1 安装vue-cli
```
npm install -g @vue/cli  # -g 全局安装
```
### 1.2 版本查看  
这个命令来检查其版本是否正确
```
vue --version  
```

## 2. 创建项目
### 2.1 创建项目命令
```
vue create testapp
```
### 2.2 选择自定义
```
  default (babel, eslint)
❯ Manually select features
```
### 2.3 空格选择，enter确认
```
❯◉ Babel
 ◯ TypeScript
 ◯ Progressive Web App (PWA) Support
 ◯ Router
 ◯ Vuex
 ◯ CSS Pre-processors
 ◉ Linter / Formatter
 ◯ Unit Testing
 ◯ E2E Testing
```
### 2.4 配置独立一个文件还是一个文件
```
  In dedicated config files
> In package.json
```

### 2.5 tree -L 1 查看项目目录
```
├── README.md
├── babel.config.js # 编译的配置
├── node_modules   # node依赖包
├── package-lock.json
├── package.json # 包相关的内容
├── public  # 静态文件放置的地方
└── src  # 源代码


├── App.vue  # App.vue 挂载到main.js
├── assets
├── components
└── main.js  # 入口文件
```
### 2.6 编译
vue文件转化成html文件
```
npm run serve  # 运行
npm run build  # 打包生成dist 文件夹 
# 需要更改index.html 里 "/"的路径改成"./"

```

## 3. 图形化界面创建和管理项目
### 3.1 新建文件夹
```
> mkdir -p testapp2
> vue ui  # 启动可视化界面
```

## 4. Vue cli 运行原理
### 4.1 require.js
- 异步模块化加载（AMD)
- 解决依赖关系
- require会定义三个变量：define,require, requirejs。其中reqiure == requirejs
- require 加载依赖模块，并执行加载完后的回调函数
- define 定义一个模块
例子1：加载本地文件
```
a.js
# define 定义一个模块
define(function(){
  function fun1() {
    console.log("a.js");
  }
  func1();
})

<script src="js/require.js" type="text/javascript"></script>
		<script type="text/javascript">
			require(['js/a']);
		</script>
```
例子2：加载服务器文件
```
require.config({
  paths:{
    "jquery":["http://libs.baidu.com/jquery/2.0.3/jquery"]
  }
})

require("jquery"], function($){
  console.log("load finished");
})
```

### 4.2 安装Webpack
```
> npm install webpack -g
> npm install webpack-cli -g
> webpack -v 检查安装
```
#### 4.2.1 js 文件打包
```
index.js
console.log('hello stanley')
require('./hello.js')

> webpack index.js
```
#### 4.2.2 书写 webpack.config.js
- 配置文件
- 输入webpack 自定执行目录下webpack.config.js
```
const path = require('path')  # 引入path 模块

module.exports = {
  entry: 'index.js'
  output: {
    path: path.resolve(__dirname, 'dist'),  # __dirname 当前目录
    filename: 'my-first-webpack.bundle.js'
  }
};
```
### 4.3 开启启动监听模式
```
> npm install webpack-dev-server -g 
> webpack-dev-server
```
