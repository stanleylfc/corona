+++
title="Linux运维 实战（一）| ansible"
tags=["Linux运维"]
categories=["Linux运维"]
date="2020-05-01T21:00:00+08:00"
+++
## 1. ansible 
## 2. ansible 基本介绍
### 2.1 ansible 组件架构
### 2.2 ansbile 配置文件
### 2.3 ansible inventory
### 2.4 ansible ad-hoc模块
```
#
ansible all -m shell -a "rm /opt/hadoop-2.7.7.tar.gz"
// 拷贝配置文件
ansible all -m copy -a "src=/opt/hadoop-2.7.7.tar.gz dest=/opt owner=hadoop group=hadoop mode=0755"
ansible all -m unarchive -a "src=/opt/hadoop-2.7.7.tar.gz dest=/opt/ copy=no mode=0755"
ansible all -m file -a "path=/root/hadoop/dfs/name state=directory mode=755"
ansible all -m command -a ""
```

## 3.ansible playbook



## 4.ansible变量详解
## 5.ansible Task 任务控制
## 6.ansible jinja2
## 7.ansible Roels构建中线企业集群架构