+++
title="go 实战（十一）| go web开发"
tags=["go"]
categories=["go"]
date="2020-04-27T21:00:00+08:00"
+++
## 1.web服务器
### 1.1 创建web服务器
- 只要调用ListenAndServe 并传入网络地址以及负责处理请求的处理器（handler）作为参数就可以了
```
// 
http.ListenAndServe("", nil)
``` 
### 1.2 带有附加配置的Web服务器
```
server := http.Server{
　　　　Addr:　　"127.0.0.1:8080",
　　　　Handler: nil,
　　}
server.ListenAndServe()
```


## 2.处理器
- 一个处理器就是一个拥有ServeHTTP 方法的接口。
- 任何接口只要拥有一个ServeHTTP 方法，那么它就是一个处理器。
```
ServeHTTP(http.ResponseWriter, *http.Request)
```