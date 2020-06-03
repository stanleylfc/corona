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

## 3. 安装tomcat
### 3.1 下载
- 登录Apache Tomcat官网，地址 http://tomcat.apache.org ，点击左边的Download，选择需要下载的版本。
- 解压目录到/opt 下
```
$ sudo tar -C /opt/ -vxf apache-tomcat-9.0.35.tar.gz
$ sudo ln -s apache-tomcat-9.0.35 apache-tomcat
```

### 3.2 启动
### 3.2.1.切换到安装目录
```
$ ./startup.sh
# 如果失败
$ sudo chmod 755 *.sh
$ ./startup.sh
访问：http://localhost:8080/
```
如果启动失败查看日志文件

### 3.3 关闭
```
./shutdown.sh
```

### 3.4 tomcat的目录结构及作用
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

### 3.5 配置
### 3.5.1 直接部署
- 直接将项目放到tomcat9目录下的webapps目录下， 重启服务器。
```
*/hello: 项目的访问路径 ---> 虚拟目录
*/hello.html 
简化部署：将项目打包成一个war包，再将war包放置到webapps目录下。war包会自动解压缩。
```
### 3.5.2 配置config/server.xml文件  
在 Host 标签下添加
```
<Context docBase="/xxx/xxx" path="/hello" />
```
- docBase: 项目存放的路径
- path 虚拟目录

### 3.5.3 在conf/catalina/localhost 创建任意名称xml文件。在文件中编写
```
<Context docBase="\hello" />
```
- 虚拟目录：xml 文件名称

### 3.5.4 动态项目静态项目
- WEB-INF
```
-- web.xml
-- classes
-- lib
```

### 3.6 将Tomcat 集成到intellij
### 3.6.1 配置Tomcat 
![tomcat](/assets/tomcat-server.png)
### 3.6.2 建立javaEE 项目
![tomcat](/assets/javaEE.png)
### 3.6.3 热部署
![javaEE-tomcat](/assets/javaEE-tomcat.png)

## 4. 安装maven
### 4.1 下载
- 从Maven官方地址：[maven](http://maven.apache.org/download.cgi)下载最新版本apache-maven-3.3.9-bin.tar.gz。
### 4.2 配置环境变量
```
export M3_HOME=/opt/apache-maven
export PATH=$M3_HOME/bin:$PATH
```

### 4.3测试配置
```
$ echo $M3_HOME
$ echo $PATH
$ maven -version
```

### 2.4 依赖管理
- maven工程 对jar包管理过程 

### 2.4 一键构建


### 2.5 maven 集成到intellij
- maven骨架仅需联网一次
```
-DarchetypeCatalog=internal
```
### 2.5.1 maven 配置
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