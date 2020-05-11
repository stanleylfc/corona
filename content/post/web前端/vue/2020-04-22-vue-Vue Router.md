+++
title="vue 实战（六）| Vue Router"
tags=["vue"]
categories=["vue"]
date="2020-04-19T09:00:00+08:00"
+++

## 1 html 
### 1.1 router-link
- 使用 router-link 组件来导航。
- 通过传入 `to` 属性指定链接。
- <router-link> 默认会被渲染成一个 `<a>` 标签。
```
<router-link to="/foo">Go to Foo</router-link>
```

### 1.2 router-view
- 路由出口
- 路由匹配到的组件将渲染在这里
```
<router-view></router-view>
```
## 2 javascript
### 2.1  初始化
- 如果使用模块化机制编程，导入Vue和VueRouter，要调用 Vue.use(VueRouter)

### 2.2 定义组件
- 定义 (路由) 组件。
- 可以从其他文件 import 进来
```
const Foo = { template: '<div>foo</div>' }
const Bar = { template: '<div>bar</div>' }
```

### 2.3 定义路由
- 每个路由应该映射一个组件。 其中"component" 可以是
- 通过 Vue.extend() 创建的组件构造器， 或者，只是一个组件配置对象。
```
const routes = [
  { path: '/foo', component: Foo },
  { path: '/bar', component: Bar }
]
```
### 2.4 创建 router 实例，然后传 `routes` 配置
```
const router = new VueRouter({
  routes // (缩写) 相当于 routes: routes
})
```
### 2.5 创建和挂载根实例。
- 通过 router 配置参数注入路由，从而让整个应用都有路由功能
```
const app = new Vue({
  router
}).$mount('#app')
```