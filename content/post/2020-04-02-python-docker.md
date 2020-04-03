+++
title="python 实战（三）| docker 安装"
tags=["python"]
categories=["Python"]
date="2020-04-02T00:00:00+08:00"
toc=true
+++

### 基础环境
    - macOS
    - Python 3.8
    - Docker

### 1. 容器
[docker](https://www.docker.com/products/docker-desktop)
```
$ docker pull ubuntu

$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              4e5021d210f6        13 days ago         64.2MB
```    

创建容器
```
$ docker create -ti --privileged --name py3 -p 10000:80 -p 10001:8888 -v /mac/py3:/root/py3 -w /root ubuntu bash -l
```

### 启动
```
$ docker start -ai py3
```

### 安装
```
$ apt update

$ apt install curl
$ apt install python3.8 python3.8-distutils
```