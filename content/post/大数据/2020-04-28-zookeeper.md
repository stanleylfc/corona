+++
title="大数据 实战（二）| zookeeper 安装"
tags=["zookeeper"]
categories=["大数据"]
date="2020-04-28T21:00:00+08:00"
+++
## 1. zookeeper 安装
### 1.1 zookeeper 环境变量配置
- 下载zookeeper-3.6.1.tar
- 解压到/opt/zookeeper目录下(如果目录不存在可以使用命令:mkdir -p /opt/zookeeper 创建)
- 配置zookeeper的环境变量(hadoop 用户配置)如下
```
ZOOKEEPER_HOME=/opt/zookeeper/zookeeper-3.4.14
JAVA_HOME=/usr/java/jdk1.8.0_211-amd64/
PATH=$PATH:$HOME/bin:$JAVA_HOME/bin:$ZOOKEEPER_HOME/bin

export JAVA_HOME
export ZOOKEEPER_HOME

export PATH
```

### 1.2 修改zookeeper 集群配置文件
- 将目录切换到zookeeper目录下的conf目录下复制zoo_sample.cfg文件为zoo.cfg。
```
1. 修改dataDir 的值为dataDir=/var/lib/zookeeper
2. 在配置文件末尾添加如下配置：
    server.1=centos701:2888:3888
    server.2=centos702:2888:3888
    server.3=centos703:2888:3888
3. 修改权限
    [root@centos701 ~]# chown -R hadoop:hadoop /opt/zookeeper
    [root@centos701 ~]# chmod -R 755 /opt/zookeeper
```
### 1.3 创建myid 文件
- 在节点centos701,centos702,centos703对应的/var/lib/zookeeper目录下(dataDir配置的目录/var/lib/zookeeper)创建myid文件,几个文件内容一次为1,2,3

### 1.4 修改myid 目录权限
```
[root@centos701 lib]# chown -R hadoop:hadoop zookeeper/
[root@centos701 lib]# chmod -R 755 zookeeper/
```

## 2. 复制zookeeper 

### 2.1 复制/var/lib 目录下的zookeeper目录到centos702和centos703的/var/lib
```
[root@centos701 lib]# scp -r zookeeper centos702:$PWD
[root@centos701 lib]# scp -r zookeeper centos703:$PWD
```
### 2.2 复制zookeeper安装目录到centos702和centos703的安装目录下
```
[root@centos701 lib]# scp -r  /opt/apache-zookeeper-3.6.1-bin/ centos702:/opt/
[root@centos701 lib]# scp -r  /opt/apache-zookeeper-3.6.1-bin/ centos703:/opt/
```
### 2.3 修改cnetos702 centos703 目录权限 和 myid 内容
```
[root@centos702] chown -R hadoop:hadoop /var/lib/zookeeper
[root@centos702] chmod -R 755 /var/lib/zookeeper
[root@centos702] chown -R hadoop:hadoop /opt/zookeeper
[root@centos702] chmod -R 755 /opt/zookeeper

[root@centos703] chown -R hadoop:hadoop /var/lib/zookeeper
[root@centos703] chmod -R 755 /var/lib/zookeeper
[root@centos703] chown -R hadoop:hadoop /opt/zookeeper
[root@centos703] chmod -R 755 /opt/zookeeper
```

### 2.4 配置centos702和centos703 的zookeeper环境变量
```
[hadoop@centos701] scp .bash_profile centos702:$PWD
[hadoop@centos701] scp .bash_profile centos703:$PWD

[hadoop@centos702] source .bash_profile

[hadoop@centos703] source .bash_profile
```

## 3.启动zookeeper 集群
### 3.1 启动 zookeeper 集群
- 启动zookeeper 集群需要手动分别依次在三台机器上启动，启动前需要在三台机器上都将用户切换为hadoop用户
```
[hadoop@centos701 zookeeper]# zkServer.sh start
[hadoop@centos702 zookeeper]# zkServer.sh start
[hadoop@centos703 zookeeper]# zkServer.sh start

[hadoop@centos701 zookeeper]# zkServer.sh stop
[hadoop@centos702 zookeeper]# zkServer.sh stop
[hadoop@centos703 zookeeper]# zkServer.sh stop

```
### 3.2 连接 zookeeper 集群
```
# zkCli.sh 连接到集群
[hadoop@centos701 zookeeper]# zkCli.sh -server centos701:2181,centos702:2181,centos703:2181
```

## 4. 常见问题
### 4.1 占用端口
```
（1）.删除jetty
（2）修改端口修改方法的方法有两种
一种是在启动脚本中增加 -Dzookeeper.admin.serverPort=你的端口号。
一种是在zoo.cfg中 admin.serverPort=8080
（3）停用这个服务，在启动脚本中增加"-Dzookeeper.admin.enableServer=false"
```