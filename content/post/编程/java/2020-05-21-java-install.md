+++
title="java 实战（一）| java 环境安装"
tags=["java"]
categories=["java"]
date="2020-05-20T21:00:00+08:00"
+++

## 1. 安装Java 
### 1.1 下载JDK

### 1.2 配置环境变量
- 安装好JDK后，通过"/usr/libexec/java_home -V"查看安装路径
- 在mac上配置Java环境变量
vim ~/.zshrc
```
export JAVA_HOME="/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home"
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
PATH=$JAVA_HOME/bin:$PATH

export JAVA_HOME
export CLASSPATH
export PATH
```
### 测试配置
```
$ java -version
```
## 2. 安装idea
### 2.1 安装intellj