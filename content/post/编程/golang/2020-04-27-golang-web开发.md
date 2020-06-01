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

### 3.多路复用器
```
muxServer  defaultMuxServer
```

## 4. web客户端
### 4.1 web客户端请求流程
```
func fetch(url string) string {
    // 1. 创建请求
    request, _ := http.NewRequest("GET", url, nil)
    // 2. 创建客户端
    client := http.Client{}
    // 3. 发出请求
    resp, err := client.Do(request)
    if err != nil {
        fmt.Println("Http get err:", err)
        return ""
    }
    if resp.StatusCode != http.StatusOK {
        fmt.Println("Http status code:", resp.StatusCode)
        return ""
    }
    // 4. 读取http 响应正文
    defer resp.Body.Close()
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Println("Read error", err)
        return ""
    }
    return string(body)
}
```
## 5. 中间件

## 6. 高并发在web中的应用