+++
title="Linux运维 实战（四）| grafana"
tags=["Linux运维"]
categories=["Linux运维"]
date="2020-05-31T21:00:00+08:00"
+++

## 2.安装
### 2.1 安装地址
```
wget https://dl.grafana.com/oss/release/grafana-7.0.1-1.x86_64.rpm
yum install grafana-7.0.1-1.x86_64.rpm
```
### 2.2 启动
```
service grafana-server start
```
### 2.3 验证
```
http://centos7:3000
```

## 3 配置promethus
- 【Configration】- 【Data Sources】 然后可以按照下图所示进行配置，需要注意的是 prometheus 的地址需要根据实际情况做修改。
![promethus](/assets/promethus.png)

## 4.添加node_expoter 面板
- grafana 的数据源配置完成后，可以导入一个 dashboard 模板文件，建议节点模板使用 [node_exporter](https://grafana.com/grafana/dashboards/8919) 展示面板模板  

![grafana](/assets/grafana.png)

## 4. 修改grafana密码
### 4.1 命令行修改
```
grafana-cli admin reset-admin-password <new password>
```
如果上述方法报错：Could not find config defaults, make sure homepath command line parameter is set or working directory is homepath

那么使用如下 flag 来指定参数
```
grafana-cli admin reset-admin-password --homepath "/usr/share/grafana" newpass
```
