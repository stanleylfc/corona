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
## 2.maven解决的问题
### 2.1 依赖管理
- maven工程 对jar包管理过程
- 传统java工程jar由项目管理，maven项目工程由同一的jar包仓库管理，代码可重用

### 2.2 一键构建
- 项目从编译，测试，报告，打包，部署过程由maven管理
```
$ mvn tomcat:run
```

### 2.3 仓库中类和此关系
- maven 工程
- jar 包坐标 --》 系统盘本地仓库 《--》远程仓库（私服） --》 中央仓库（所有开源的jar包）

### 2.4 maven 常用命令

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