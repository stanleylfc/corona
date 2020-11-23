+++
title="go 实战（十三）| go gateway开发"
tags=["go"]
categories=["go"]
date="2020-04-28T21:00:00+08:00"
+++

## 6 创建自签证书 (为了练习方便使用)
### 6.1 生成证书
- 命令行执行 openssl
- 执行 genrsa -des3 -out server.key 2048 (会生成server.key私钥文件 密码:1234)
- 创建证书请求: req -new -key server.key -out server.csr (会生成server.csr)
其中 common name 也就是域名： 可填写 mystudy.com

### 6.2 
- 删除密码 rsa -in server.key -out server_no_passwd.key
- 执行 x509 -req -days 365 -in server.csr -signkey server_no_passwd.key -out server.crt (会生成server.crt)

## 7. CA 证书
### 7.1 CA 证书
- 根证书（root certificate) 是属于根证书颁发机构（CA) 的公钥证书。
- 执行 openssl
- 执行 genrsa -out ca.key 2048 (生成ca.key)
- req -new -x509 -days 3650 -key ca.key -out ca.pem (生成ca.pem)
```
Common Name (e.g. server FQDN or YOUR name) []:localhost  //这里填localhost 或者 域名
7.2和7.3 Common Name 也要填写localhost
```
### 7.2 CA 服务端证书
- genrsa -out server.key 2048
- req -new -key server.key -out server.csr
- x509 -req -sha256 -CA ca.pem -CAkey ca.key -CAcreateserial -days 3650 -in server.csr -out server.pem

### 7.3 CA 客户端证书
- ecparam -genkey -name secp384r1 -out client.key
- req -new -key client.key -out client.csr
- x509 -req -sha256 -CA ca.pem -CAkey ca.key -CAcreateserial -days 3650 -in client.csr -out client.pem

## 7.gateway
### 7.1 下载安装
```
https://github.com/grpc-ecosystem/grpc-gateway
```
### 7.2 编辑prod.pb.go
#### 7.2.1 引入外部包
```
import "google/api/annotations.proto";
```
#### 7.2.2 增加http代码
```
rpc GetProdStock (ProdRequest) returns (ProdResponse) {
        option (google.api.http) = {
        get: "/v1/prod/{prod_id}"
      };
    }
```

### 7.3 生成两个文件
- 首先cd 进入pbfiles
- 生成prod.pb.go
```
protoc --go_out=plugins=grpc:../services Prod.proto
```
- 生成prod.pb.gw.go
```
protoc --grpc-gateway_out=logtostderr=true:../services Prod.proto
```

### 7.4 新增httpserver 
```
    gwmux := runtime.NewServeMux()
    opt := []grpc.DialOption{grpc.WithTransportCredentials(helper.GetClientCreds())}
    err := services.RegisterProdServiceHandlerFromEndpoint(context.Background(),
        gwmux, "localhost:8081", opt)
    if err != nil {
        log.Fatal(err)
    }

    httpServer := &http.Server{
        Addr:              ":8080",
        Handler:           gwmux,
    }

    httpServer.ListenAndServe()
```
### 7.5 启动服务
- 启动server
- 启动client,httpserver