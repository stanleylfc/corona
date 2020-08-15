+++
title="java 实战（四）| java tomcat安装"
tags=["java"]
categories=["java"]
date="2020-05-20T21:00:00+08:00"
+++

## 1. 安装tomcat
### 1.1 下载
- 登录Apache Tomcat官网，地址 http://tomcat.apache.org ，点击左边的Download，选择需要下载的版本。
- 解压目录到/opt 下
```
$ sudo tar -C /opt/ -vxf apache-tomcat-9.0.15.tar.gz
$ sudo ln -s apache-tomcat-9.0.15 apache-tomcat
```

### 1.2 启动
### 1.2.1.切换到安装目录
```
$ ./startup.sh
# 如果失败
$ sudo chmod 755 *.sh
$ ./startup.sh
访问：http://localhost:8080/
```
如果启动失败查看日志文件

### 1.3 关闭
```
./shutdown.sh
```

### 1.4 tomcat的目录结构及作用
- tree -L 1
```
├── BUILDING.txt
├── CONTRIBUTING.md
├── LICENSE
├── NOTICE
├── README.md
├── RELEASE-NOTES
├── RUNNING.txt
├── bin    // 存放tomcat的命令。
├── conf  // 配置文件 server.xml核心配置
├── lib   // 支持tomcat软件运行的jar包
├── logs  // 运行过程的日志信息
├── temp  // 临时目录
├── webapps // 共享资源目录， 存放web应用目录。
└── work  // 存放运行是目录。jsp运行时产生的临时文件就存放在这里
```

### 1.5 配置
### 1.5.1 直接部署
- 直接将项目放到tomcat9目录下的webapps目录下， 重启服务器。
```
*/hello: 项目的访问路径 ---> 虚拟目录
*/hello.html 
简化部署：将项目打包成一个war包，再将war包放置到webapps目录下。war包会自动解压缩。
```
### 1.5.2 配置config/server.xml文件  
在 Host 标签下添加
```
<Context docBase="/xxx/xxx" path="/hello" />
```
- docBase: 项目存放的路径
- path 虚拟目录

### 1.5.3 在conf/catalina/localhost 创建任意名称xml文件。在文件中编写
```
<Context docBase="\hello" />
```
- 虚拟目录：xml 文件名称

### 1.5.4 动态项目静态项目
- WEB-INF
```
-- web.xml
-- classes
-- lib
```

### 1.6 将Tomcat 集成到intellij
### 1.6.1 配置Tomcat 
![tomcat](/assets/tomcat-server.png)
### 1.6.2 建立javaEE 项目
![tomcat](/assets/javaEE.png)
### 1.6.3 热部署
![javaEE-tomcat](/assets/javaEE-tomcat.png)