
## 1. 系统中的ssh与sshd服务
### 1.1 概念简介  
openssh在主机中开启了openssh服务，那么就对外开放了远程连接的接口
### 1.2 架构模式
- openssh的服务端：sshd
- openssh的客户端：ssh
## 2. 服务端
-  查看sshd服务状态   
```sh
service sshd status
```
- 停止sshd服务
```sh
service sshd stop
```
- 启动sshd服务
```sh
service sshd start
```
### 2.1 修改端口
默认是端口是：22 
可以通过修改 /etc/ssh/sshd_config文件，修改默认端口,修改改Port 后的数字为 需要的端口，然后去掉#号注释

- 重新启动sshd服务器
```sh
servcie sshd restart
```
备注：如果用pts终端已经远程到Linux服务器上，此时，停止sshd服务，原来的服务，不受影响。但是新的连接连接不上。

## 3.客户端
### 3.3 连接方式
```
 ssh    username@ip    文本模式的连接
 ssh -x  username@ip   可以在连接成功后开机图形
``` 