+++
title="linux 基础实战（一）| linux 基本命令"
tags=["linux"]
categories=["linux"]
date="2020-05-10T09:00:00+08:00"
toc=true
+++

## 1. 用户管理
### 1.1 查询某个用户是否存在
$ id -u root

### 1.2 用户ssh 免密码失败
```sh
$ tail -10f  /var/log/secure
Authentication refused: bad ownership or modes for file /home/xxxxxx/.ssh/authorized_keys
#解决方法：chmod 600  /home/hadoop/.ssh/authorized_keys
```

## 2. 拷贝远端文件
### 2.1 拷贝本机/home/administrator/test整个目录至远程主机192.168.1.100的/root目录下
```sh 
$ scp -r /home/administrator/test/ root@192.168.1.100:/root/
``` 

### 2.2 拷贝单个文件至远程主机
```
$ scp /home/administrator/Desktop/old/driver/test/test.txt root@192.168.1.100:/root/
``` 

### 2.3 远程文件/文件夹下载
```
$ scp -r root@192.168.62.10:/root/ /home/administrator/Desktop/new/
```
## 3. 压缩拷贝
```
$ gzip -d 
```

### 3.2 gzip 压缩
```
把test6目录下的每个文件压缩成.gz文件
$ gzip *
把上例中每个压缩的文件解压，并列出详细的信息
$ gzip -dv *
详细显示例1中每个压缩的文件的信息，并不解压
$ gzip -l *
压缩一个tar备份文件，此时压缩文件的扩展名为.tar.gz
$ gzip -r log.tar
递归的压缩目录
$ gzip -rv test6
这样，所有test下面的文件都变成了.gz，目录依然存在只是目录里面的文件相应变成了.gz.这就是压缩，和打包不同。因为是对目录操作，所以需要加上-r选项，这样也可以对子目录进行递归了。
递归地解压目录
$ gzip -dr test6
```