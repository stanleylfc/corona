+++
title="go 实战（二）| go 常用命令"
tags=["golang"]
categories=["golang"]
date="2020-06-30T11:00:00+08:00"
+++

## go tool
### 查看汇编代码
```
go tool compile -S test.golang
```
### 参数-m是打印出编译优化
```
go tool compile -m test.go
```