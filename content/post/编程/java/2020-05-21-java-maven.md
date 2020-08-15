+++
title="java 实战（三）| java maven安装"
tags=["java"]
categories=["java"]
date="2020-05-20T21:00:00+08:00"
+++


## 1. 安装maven
### 1.1 下载
- 从Maven官方地址：[maven](http://maven.apache.org/download.cgi)下载最新版本apache-maven-3.3.9-bin.tar.gz。
### 1.2 配置环境变量
```
export M3_HOME=/opt/apache-maven
export PATH=$M3_HOME/bin:$PATH
```

### 1.3测试配置
```
$ echo $M3_HOME
$ echo $PATH
$ mvn -version
```

### 2 依赖管理
- maven工程 对jar包管理过程 

### 2.1 一键构建


### 2.5 maven 集成到intellij
- maven骨架仅需联网一次
```
-DarchetypeCatalog=internal
```
### 2.5.1 maven 集成到intellij配置修改
![maven-idea](/assets/maven-idea.png)
### 2.5.2 maven runer配置
![maven-idea](/assets/maven-edit.png)

### 2.6 使用maven 创建Java项目
- 不使用骨架创建maven 工程
- 使用骨架创建maven工程
### 2.6 maven 使用
### 2.6.1 创建模板
![maven-templates](/assets/maven-templates.png)
### 2.6.2 添加插件
![maven-plugin](/assets/maven-plugin.png)