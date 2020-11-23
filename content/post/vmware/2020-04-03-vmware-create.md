+++
title="vmware 实战（一）| 创建"
tags=["vmware"]
categories=["vmware"]
date="2020-04-03T09:00:00+08:00"
toc=true
+++

### 网络配置

文件| 功能
---|---
/etc/hostsname | 修改主机名称
/etc/sysconfig/network-scripts/ifcfg-enoN | 设置网卡参数文件（网卡位置）
/etc/resolv.conf | 设置DNS，用于将域名到ip
/etc/hosts | 计算机ip对应的主机名或者域名
/ect/nsswitch.conf|优先使用DNS解析还是本地设置优先（name server switch configuration）

##### 1.1网络接口文件
```
#显示网络接口文件
ip addr
#编辑接口文件
vi /etc/sysconfig/network-scripts/ifcfg-ens33
#重启网卡
service network restart
```

##### 1.2 ifcfg-ens33
```
HWADDR=00:0c:29:39:12:c7    # ip addr 查看的
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens33
UUID=7b6f986c-6635-4b5b-8d05-af0691156a2e
DEVICE=ens33
ONBOOT=yes

```
```
从上面的配置中有看到虽然
BOOTPROTO=dhcp，但是ONBOOT=no，
2.用vi将ONBOOT=no改成ONBOOT=yes，
3.shutdown -r重启CentOS
```

参数名 | 值|含义
---|---|---
Type | Ethernet| 以太网
BOOTPROTO | [static,none,dhcp] |通过指定方式的办法来获得地址，默认none
ONBOOT|[yes,no] | 开启自动启用网络连接

修改主机名
```
1. vi /etc/hostname
2. hostname
3. vi /etc/sysconfig/network

```

##### 1.3 VMware Fusion DHCP方式下如何指定虚拟机IP地址
```
 默认情况下，vmware fusion中的虚拟机，网卡设置成dhcp（动态分配 ）时，会分配一个IP地址，但这个IP通常很难记，
 如果我们想为某台虚拟机挑一个好记的IP地址，可以按如下步骤操作：
 命令行下，输入
```
```
    sudo vi /Library/Preferences/VMware\ Fusion/vmnet8/dhcpd.conf
```
```
该文件记录了动态分配的IP地址段，以及某个mac地址绑定的IP，参考下面的内容修改：
```
```
allow unknown-clients;
default-lease-time 1800;                # default is 30 minutes
max-lease-time 7200;                    # default is 2 hours
 
subnet 192.168.187.0 netmask 255.255.255.0 {
range 192.168.187.100 192.168.187.110;
option broadcast-address 192.168.187.255;
option domain-name-servers 192.168.187.2;
option domain-name localdomain;
default-lease-time 1800;                # default is 30 minutes
max-lease-time 7200;                    # default is 2 hours
option netbios-name-servers 192.168.187.2;
option routers 192.168.187.2;
}
host vmnet8 {
hardware ethernet 00:50:56:C0:00:08;
fixed-address 192.168.187.1;
option domain-name-servers 0.0.0.0;
option domain-name "";
option routers 0.0.0.0;
}
host centos.01{
hardware ethernet 00:50:56:3C:30:4B;
fixed-address 192.168.187.101;
}
host centos.02{
    hardware ethernet 00:50:56:38:33:7B;
fixed-address 192.168.187.102;
}
host centos.03{
hardware ethernet 00:0C:29:D3:29:77;
fixed-address 192.168.187.103;
}
host centos.04{
hardware ethernet 00:50:56:3a:93:0d;
fixed-address 192.168.187.104;
}
```
```
这样，我们就为centos.01~04这四台机器绑定了IP
(注：虚拟机的mac地址，可以在虚拟机的网卡配置高级选项中查到，或者ifconfig/ipconfig查看)，
这个文件修改好后，保存退出，
然后彻底退出vmare fusion，重新再启动vmware fusion，再启动各虚拟机，顺利的话，就能分配到理想的IP地址。
最后在mac本主系统、以及各虚拟机的hosts文件中做下映射，以后记起来就方便多了。
```

#####  1.4公钥复制到服务器
 
1.4.1 在本地机器生成密钥对：

    ssh-keygen -t rsa
    生成的路径在~/.ssh/
    
1.4.2 将公钥拷贝到服务器上：

    ssh-copy-id root@192.168.2.110
    添加完后要登录服务器就只需输入root@192.168.2.110就可以直接进入服务器，不需要密码。
    
1.4.3 config 四元组：

    Host yllfc
    User root
    Hostname 39.105.84.213
    Port 22

### 克隆虚拟机
1.在已有的虚拟机下右键点击【管理】–>【克隆】，点击【下一步】
2.选择 【创建完整克隆】
3.【网络适配器】-> 【高级选项】-> 【生成】 新的MAC地址由于硬件配置和源主机一样，修改一下网卡。
4.重新生成ssh-keygen
5.查看

### 配置静态ip 地址
```
HWADDR=00:0c:29:39:12:c7    # ip addr 查看的
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=none
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens33
UUID=7b6f986c-6635-4b5b-8d05-af0691156a2e
DEVICE=ens33
ONBOOT=yes
IPADDR=172.16.232.128
PREFIX=24
GATEWAY=172.16.232.2
DNS1=172.16.232.2
```