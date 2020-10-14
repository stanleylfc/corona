+++
title="go 实战（十二）| go grpc开发"
tags=["go"]
categories=["go"]
date="2020-04-28T21:00:00+08:00"
+++

## 1. RPC
- 远程调用
- https://github.com/grpc/grpc-go
```
go get -u google.golang.org/grpc
```

## 2. Protobuf
- Google Protocol Buffer

### 2.1 安装 protobuf 
- 下载对应版本解压后配置环境变量
```
https://github.com/protocolbuffers/protobuf/releases
```

### 2.2 
- protocol 格式教程
```
https://developers.google.com/protocol-buffers/docs/gotutorial
```

## 3. 中间文件
### 3.1 安装插件
- 在GOPATH的bin目录下生产可执行文件.protobuf的编译器插件protoc-gen-go
```
go get github.com/golang/protobuf/protoc-gen-go

```
### 3.2书写中间文件
```
syntax="proto3"
package services
message ProdRequest {
    int32 prod_id=1
}
message ProdResponse {
    int32 prod_stock=1
}

```
### 3.3 编译安装
- 在pbfiles 执行命令

```
├── pbfiles
│   └── Prod.proto
└── services

protoc --go_out=../services/ Prod.proto

├── pbfiles
│   └── Prod.proto
└── services
    └── Prod.pb.go
```
## 4.创建server
### 4.1 prod.proto添加服务协议
```
service ProdService {
    rpc GetProdStock (ProdRequest) returns (ProdResponse);
}
```
### 4.2 重新生成prod文件
```
protoc --go_out=plugins=grpc:../services/ Prod.proto
```
```
命令解释：
protoc --go_out=plugins=grpc:. ./proto/*.proto
// --go_out：设置所生成的Go 代码输出的目录。该指令会加载 p
rotoc-gen-go 插件，以达到生成Go代码的目的。生成的文件以.pb.go
为文件后缀，这里的“：”（冒号）有分隔符的作用，后跟命令所需要的参数集，这意味着把生成的Go代码输出到指向的protoc编译的当前目录。
// plugins=plugin1+plugin2：指定要加载的子插件列表。我们
定义的proto文件是涉及了RPC 服务的，而默认是不会生成RPC代码
的，因此需要在go_out中给出plugins参数，将其传递给protoc-gen-g
o插件，即告诉编译器，请支持RPC（这里指定了内置的grpc插件）

```
### 4.3 实现服务（实现Service接口）
```
package services

import (
    "context"
)

type ProdService struct {

}

func (c *ProdService) GetProdStock(ctx context.Context, request *ProdRequest) (*ProdResponse, error) {
    return &ProdResponse{ProdStock:request.ProdId}, nil
}
```

### 4.4 创建服务
```
func main() {
    // 1. 创建server
    rpcServer := grpc.NewServer()
    //注册服务
    services.RegisterProdServiceServer(rpcServer, new(services.ProdService))
    lis, _ := net.Listen("tcp", ":8081")

    rpcServer.Serve(lis)
}
```
### 4.5 文件目录结构
```
├── pbfiles
│   └── Prod.proto
├── server.go
└── services
    ├── Prod.pb.go
    └── ProdService.go

```

## 5.创建客户端
### 5.1 拷贝服务端proto 协议对应生成的go文件
```
Prod.pb.go
```
### 5.2 连接服务端
```
func main() {
    conn, err := grpc.Dial(":8081", grpc.WithInsecure())
    if err != nil {
        log.Fatal(err)
    }
    defer conn.Close()

    prodClient := services.NewProdServiceClient(conn)
    prodRes, err := prodClient.GetProdStock(context.Background(), &services.ProdRequest{ProdId: 50})
    if err != nil {
        log.Fatal(err)
    }
    fmt.Println(prodRes.ProdStock)
}
```
### 5.5 目录结构
```
├── main.go
└── services
    └── Prod.pb.go
```

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