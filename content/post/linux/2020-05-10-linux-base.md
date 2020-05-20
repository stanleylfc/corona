+++
title="linux 基础实战（一）| 用户管理"
tags=["linux"]
categories=["linux"]
date="2020-05-10T09:00:00+08:00"
toc=true
+++

## 1.用户ssh 免密码失败
```sh
$ tail -10f  /var/log/secure
Authentication refused: bad ownership or modes for file /home/xxxxxx/.ssh/authorized_keys
#解决方法：chmod 600  /home/hadoop/.ssh/authorized_keys
```
## 2. 拷贝远端文件
1、拷贝本机/home/administrator/test整个目录至远程主机192.168.1.100的/root目录下
```sh 
scp -r /home/administrator/test/ root@192.168.1.100:/root/
``` 

2、拷贝单个文件至远程主机
```
scp /home/administrator/Desktop/old/driver/test/test.txt root@192.168.1.100:/root/
``` 

3、远程文件/文件夹下载
```
scp -r root@192.168.62.10:/root/ /home/administrator/Desktop/new/
```