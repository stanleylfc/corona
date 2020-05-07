+++
title="hadoop 实战（三）| redis cluster 安装"
tags=["hadoop"]
categories=["hadoop"]
date="2020-04-27T21:00:00+08:00"
+++
Redis Cluster的两种搭建和简单使用
Redis Cluster是Redis官方的一个高可用分布式解决方案。Redis Cluster中共有2 ^ 14（16384）个槽，创建集群后，需要将这些槽均分给各个节点。

准备节点
节点配置
Redis集群一般由多个节点组成，节点数量至少为 6 个，才能保证组成完整高可用的集群。Redis集群节点规划如下：

节点名称	端口号	主/从节点	复制的节点
redis-6379	6379	主节点	---
redis-6378	6378	主节点	---
redis-6377	6377	主节点	---
redis-6376	6376	从节点	redis-6379
redis-6375	6375	从节点	redis-6378
redis-6374	6374	从节点	redis-6377
建议为集群内所有节点统一目录，一般划分三个目录：conf、data、log，分别存放 配置、数据 和 日志 相关文件。

$ tree -L 2
.
├── conf
│   ├── redis-6374.conf
│   ├── redis-6375.conf
│   ├── redis-6376.conf
│   ├── redis-6377.conf
│   ├── redis-6378.conf
│   └── redis-6379.conf
├── data
│   ├── redis-6374
│   ├── redis-6375
│   ├── redis-6376
│   ├── redis-6377
│   ├── redis-6378
│   └── redis-6379
└── log
    ├── redis-6374.log
    ├── redis-6375.log
    ├── redis-6376.log
    ├── redis-6377.log
    ├── redis-6378.log
    └── redis-6379.log

9 directories, 12 files
复制代码
这里只需将redis.conf复制6份到conf目录下，更改为cluster所需的配置。data下新建6个文件夹，分别对应6个节点(用节点名命名)。log下新建6个日志文件(也是用节点名命名)。

修改配置文件如下，这是redis-6379的配置，其它五个配置类似。

# redis后台运行
daemonize yes

# 数据存放目录
dir /usr/local/redis-cluster/data/redis-6379

# 日志文件
logfile /usr/local/redis-cluster/log/redis-6379.log

# 端口号
port 6379

# 开启集群模式
cluster-enabled yes

# 集群的配置，配置文件首次启动自动生成
# 这里只需指定文件名即可，集群启动成功后会自动在data目录下创建
cluster-config-file "nodes-6379.conf"

# 请求超时，设置10秒
cluster-node-timeout 10000
复制代码
启动节点
分别启动6个节点：

sudo redis-server conf/redis-6379.conf
sudo redis-server conf/redis-6378.conf
sudo redis-server conf/redis-6377.conf
sudo redis-server conf/redis-6376.conf
sudo redis-server conf/redis-6375.conf
sudo redis-server conf/redis-6374.conf
复制代码
查看redis-6379的日志：

11003:C 02 Feb 2020 22:45:04.655 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
11003:C 02 Feb 2020 22:45:04.656 # Redis version=5.0.7, bits=64, commit=00000000, modified=0, pid=11003, just started
11003:C 02 Feb 2020 22:45:04.657 # Configuration loaded
11004:M 02 Feb 2020 22:45:04.674 * Increased maximum number of open files to 10032 (it was originally set to 1024).
11004:M 02 Feb 2020 22:45:04.679 * No cluster configuration found, I'm 7d480c106752e0ba4be3efaf6628bd7c8c124013
11004:M 02 Feb 2020 22:45:04.788 * Running mode=cluster, port=6379.
11004:M 02 Feb 2020 22:45:04.789 # Server initialized
11004:M 02 Feb 2020 22:45:04.790 * Ready to accept connections
复制代码
可以看到Redis在集群模式下启动，因为初始的时候没有集群配置，所以自动创建了配置(文件名就是在redis-6379.conf中指定的)。

然后来看一下自动创建的及集群配置。这个文件记录了集群的初始状态，前面那个很长的字符串就是节点ID。

7d480c106752e0ba4be3efaf6628bd7c8c124013 127.0.0.1:6379@16379 myself,master - 0 1580657311000 0 connected 
vars currentEpoch 5 lastVoteEpoch 0
复制代码
手动搭建
节点握手
Redis Cluster集群模式下的节点彼此通过Gossip协议进行通信，但集群中的节点需要先进行握手通信。

节点握手需要由客户端发起命令：cluster meet {ip} {port}，一个客户端可以完成整个集群的握手(握手实际上是将对方节点加入集群中，握手成功后该状态会通过下消息在集群中传播，其它节点就会自动发现新节点然后发起握手，最后所有节点都彼此感知并组成集群)。

进入redis-6379客户端发起握手：

127.0.0.1:6379> cluster meet 127.0.0.1 6378
127.0.0.1:6379> cluster meet 127.0.0.1 6377
127.0.0.1:6379> cluster meet 127.0.0.1 6376
127.0.0.1:6379> cluster meet 127.0.0.1 6375
127.0.0.1:6379> cluster meet 127.0.0.1 6374
复制代码
查看集群的节点：

127.0.0.1:6379> cluster nodes
13793dee9c570fd88297fd406ecb9c6a83ab670d 127.0.0.1:6374@16374 master - 0 1580655577754 5 connected
7d480c106752e0ba4be3efaf6628bd7c8c124013 127.0.0.1:6379@16379 myself,master - 0 1580655577000 0 connected
359acf2a5134bdc0059464100ad712564062b731 127.0.0.1:6375@16375 master - 0 1580655576000 4 connected
2f2b41c194343fe88845fea5caa3796c50f71bbb 127.0.0.1:6378@16378 master - 0 1580655575000 1 connected
169fae6f05e58bd944057d5f814abefdd34eb4b2 127.0.0.1:6377@16377 master - 0 1580655576000 2 connected
3fe3f423e8ba0c289a71702a78ad44a585c4a0fe 127.0.0.1:6376@16376 master - 0 1580655576745 3 connected
复制代码
但是此时集群还不能使用，因为还没有给节点分配槽，所以不能对数据进行读写操作。
可以查看集群的信息(集群的状态处于fail状态，且分配的槽为0)：

127.0.0.1:6379> cluster info
cluster_state:fail
cluster_slots_assigned:0
...
复制代码
分配槽
Redis集群将所有的数据映射到16384个槽中，每个key都会对应一个槽，只有把槽分配给了节点，节点才能响应与槽相关的命令。
这里为了方便，直接用命令行进行槽的分配。

redis-cli -p 6379 cluster addslots {0..5461}
redis-cli -p 6378 cluster addslots {5462..10922}
redis-cli -p 6377 cluster addslots {10922..16383}
复制代码
将16384个槽平均的分给3个节点，然后查看集群的状态，可以发现变成了OK。

127.0.0.1:6379> cluster info
cluster_state:ok
cluster_slots_assigned:16384
cluster_slots_ok:16384
...
复制代码
再查看集群的槽，可以看到每个节点分配到的槽的范围和节点的信息。

127.0.0.1:6379> cluster slots
1) 1) (integer) 0
   2) (integer) 5461
   3) 1) "127.0.0.1"
      2) (integer) 6379
      3) "7d480c106752e0ba4be3efaf6628bd7c8c124013"
2) 1) (integer) 5462
   2) (integer) 10922
   3) 1) "127.0.0.1"
      2) (integer) 6378
      3) "2f2b41c194343fe88845fea5caa3796c50f71bbb"
3) 1) (integer) 10923
   2) (integer) 16383
   3) 1) "127.0.0.1"
      2) (integer) 6377
      3) "169fae6f05e58bd944057d5f814abefdd34eb4b2"
复制代码
我们一共启动了6个节点，但这里分配槽只用了3个节点，其它3个节点就是用于主从复制的，保证主节点出现故障时可以进行故障转移，从节点负责复制主节点槽信息和相关数据。可以用以下命令让一个节点变成从节点(该命令一定要在从节点客户端上执行)。

redis-cli -p 6376
127.0.0.1:6376> cluster replicate 7d480c106752e0ba4be3efaf6628bd7c8c124013（这个是6379节点的ID）
OK
127.0.0.1:6376> exit

redis-cli -p 6375
127.0.0.1:6375> cluster replicate 2f2b41c194343fe88845fea5caa3796c50f71bbb（这个是6378节点的ID）
OK
127.0.0.1:6375> exit

redis-cli -p 6374
127.0.0.1:6374> cluster replicate 169fae6f05e58bd944057d5f814abefdd34eb4b2（这个是6377节点的ID）
OK
127.0.0.1:6374> exit
复制代码
到此为止，Redis Cluster集群就搭建成功了，最后来看一下集群节点的状态（三主三从）

127.0.0.1:6379> cluster nodes
13793dee9c570fd88297fd406ecb9c6a83ab670d 127.0.0.1:6374@16374 slave 169fae6f05e58bd944057d5f814abefdd34eb4b2 0 1580657364167 5 connected
7d480c106752e0ba4be3efaf6628bd7c8c124013 127.0.0.1:6379@16379 myself,master - 0 1580657362000 0 connected 0-5461
359acf2a5134bdc0059464100ad712564062b731 127.0.0.1:6375@16375 slave 2f2b41c194343fe88845fea5caa3796c50f71bbb 0 1580657360000 4 connected
2f2b41c194343fe88845fea5caa3796c50f71bbb 127.0.0.1:6378@16378 master - 0 1580657363000 1 connected 5462-10922
169fae6f05e58bd944057d5f814abefdd34eb4b2 127.0.0.1:6377@16377 master - 0 1580657365175 2 connected 10923-16383
3fe3f423e8ba0c289a71702a78ad44a585c4a0fe 127.0.0.1:6376@16376 slave 7d480c106752e0ba4be3efaf6628bd7c8c124013 0 1580657363160 3 connected
复制代码
自动搭建
可以看到上面的集群搭建非常的麻烦(先进行节点通信，然后分配槽，最后还要指定从节点去复制主节点)。好在Redis为我们提供了工具可以让我们轻松的搭建集群。

我使用的是Redis5，如果是更低版本有一个工具redis-trib.rb，使用它时需要先安装redis gem依赖（gem install redis如果没有ruby环境请先安装ruby）。

先把上面手动搭建的集群节点全部停止，并删除数据目录下的所有文件。
如果在之前的集群中的某个节点存储过数据或者集群配置没有删除，会报类似错误[ERR] Node 127.0.0.1:6378 is not empty. Either the node already knows other nodes (check with CLUSTER NODES) or contains some key in database 0.如果集群配置未删除，则删除所有集群配置，否则进入之前存储数据的客户端执行flushall和cluster reset清空数据并重置集群。
然后重新启动所有节点。只需键入以下内容即可为Redis 5创建集群：

redis-cli --cluster create 127.0.0.1:6379 127.0.0.1:6378 127.0.0.1:6377 127.0.0.1:6376 127.0.0.1:6375 127.0.0.1:6374 --cluster-replicas 1
复制代码
create 后面跟着的是6个节点的地址和端口，选项--cluster-replicas 1意味着为每个主节点都提供一个从节点。创建结果如下：

>>> Performing Cluster Check (using node 127.0.0.1:6379)
M: 872e3876ea53a1ba1a77af3fb5a8c72704ac0eab 127.0.0.1:6379
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
S: e80c00d029005bb132b08ddbdb4029a10feff839 127.0.0.1:6376
   slots: (0 slots) slave
   replicates a6651e15af616cfe720c7731f8748fffbde56822
M: a6651e15af616cfe720c7731f8748fffbde56822 127.0.0.1:6378
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: 10e8962e8ce4b0200c3ee5ffcf03d196f72f72fc 127.0.0.1:6374
   slots: (0 slots) slave
   replicates 872e3876ea53a1ba1a77af3fb5a8c72704ac0eab
M: 74af8a6c3dce0420707e5253fdb0a2f0c039b1a3 127.0.0.1:6377
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
S: f96b0261c775cf10a254f28374a4ed73c2977b1b 127.0.0.1:6375
   slots: (0 slots) slave
   replicates 74af8a6c3dce0420707e5253fdb0a2f0c039b1a3
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
复制代码
简单使用
redis-cli使用集群
redis-cli加个-c以switch模式启动，将支持集群的使用。

$ redis-cli -c -p 6379
127.0.0.1:6379> set name cluster
-> Redirected to slot [5798] located at 127.0.0.1:6378
OK
127.0.0.1:6378> get name
"cluster"
复制代码
当在redis-6379使用键命令来设置name的值时，对应的槽为5798，这个槽是属于redis-6378节点负责的，所以switch模式会自动重定向到redis-6378节点来完成键命令。

重新分片
重新分片其实也就是槽的迁移。将哈希槽从一个或几个节点迁移到另一个节点。
Redis官方也是提供了命令用来重新分片的，如果Redis的版本低于5的话还是使用redis-trib.rb这个工具。

redis-cli --cluster reshard 127.0.0.1:6379
复制代码
在这里只需要指定一个节点，redis-cli将自动找到其他节点。
首先会测试集群的运行状态，然后询问你要重新分配多少个槽(我这里指定了1000个槽)。 How many slots do you want to move (from 1 to 16384)?
然后我们需要指定重新分片的目标ID(也就是指定哪个节点来接收这些重新分配的槽)。
可以通过以下命令来查看某个节点的信息(里面包括了节点的ID)。

redis-cli -p 6379 cluster nodes | grep myself
872e3876ea53a1ba1a77af3fb5a8c72704ac0eab 127.0.0.1:6379@16379 myself,master - 0 1580801413000 7 connected 0-5961 10923-11421
复制代码
最终需要指定从哪些节点获取槽(这里简单的指定all也就是所有的节点)。就会开始执行重新分片了，通过终端可以看到槽正在从一个节点移动到另一个节点。
最后检查集群状态可以发现目标节点的槽数量变成了6461。

redis-cli --cluster check 127.0.0.1:6379
127.0.0.1:6379 (872e3876...) -> 1 keys | 6461 slots | 1 slaves.
127.0.0.1:6378 (a6651e15...) -> 0 keys | 4961 slots | 1 slaves.
127.0.0.1:6377 (74af8a6c...) -> 0 keys | 4962 slots | 1 slaves.
复制代码
故障转移
测试故障转移
先来看一下当前集群中的节点：

redis-cli -a redis666 cluster nodes
872e3876ea53a1ba1a77af3fb5a8c72704ac0eab 127.0.0.1:6379@16379 myself,master - 0 1580823411000 7 connected 0-5961 10923-11421
74af8a6c3dce0420707e5253fdb0a2f0c039b1a3 127.0.0.1:6377@16377 slave f96b0261c775cf10a254f28374a4ed73c2977b1b 0 1580823412846 9 connected
e80c00d029005bb132b08ddbdb4029a10feff839 127.0.0.1:6376@16376 slave a6651e15af616cfe720c7731f8748fffbde56822 0 1580823411000 4 connected
f96b0261c775cf10a254f28374a4ed73c2977b1b 127.0.0.1:6375@16375 master - 0 1580823409820 9 connected 11422-16383
a6651e15af616cfe720c7731f8748fffbde56822 127.0.0.1:6378@16378 master - 0 1580823412000 2 connected 5962-10922
10e8962e8ce4b0200c3ee5ffcf03d196f72f72fc 127.0.0.1:6374@16374 slave 872e3876ea53a1ba1a77af3fb5a8c72704ac0eab 0 1580823410835 7 connected
复制代码
这里用命令debug segfault让master节点6378崩溃（根据上面的信息可以看到redis-6378的从节点是redis-6376）。

redis-cli -p 6378 debug segfault
复制代码
然后等待一段时间，会发现已经自动完成了故障转移，redis-6376成了新的master节点(redis-6378处于fail状态)：

e80c00d029005bb132b08ddbdb4029a10feff839 127.0.0.1:6376@16376 master - 0 1580823678628 10 connected 5962-10922
a6651e15af616cfe720c7731f8748fffbde56822 127.0.0.1:6378@16378 master,fail - 1580823551245 1580823549000 2 disconnected
复制代码
再次启动redis-6378，cluster会重新将它加入集群，并让它成为从节点最少的那个master节点的从节点(在这里就是redis-6376)。

e80c00d029005bb132b08ddbdb4029a10feff839 127.0.0.1:6376@16376 master - 0 1580823829221 10 connected 5962-10922
a6651e15af616cfe720c7731f8748fffbde56822 127.0.0.1:6378@16378 slave e80c00d029005bb132b08ddbdb4029a10feff839 0 1580823830226 10 connected
复制代码
手动故障转移
随便进入一个从节点(只能是从节点)中，执行cluster failover进行手动故障转移(手动故障转移只是将主节点和从节点的关系换了一下)。
这里对redis-6374进行手动故障转移，执行之前节点的状态（redis-6374是从节点，redis-6379是主节点）。

10e8962e8ce4b0200c3ee5ffcf03d196f72f72fc 127.0.0.1:6374@16374 myself,slave 872e3876ea53a1ba1a77af3fb5a8c72704ac0eab 0 1580825791000 6 connected
872e3876ea53a1ba1a77af3fb5a8c72704ac0eab 127.0.0.1:6379@16379 master - 0 1580825792000 7 connected 789-5961 10923-11421
复制代码
执行之后的状态(redis-6374是主节点，redis-6379是从节点)

10e8962e8ce4b0200c3ee5ffcf03d196f72f72fc 127.0.0.1:6374@16374 myself,master - 0 1580825812000 12 connected 789-5961 10923-11421
872e3876ea53a1ba1a77af3fb5a8c72704ac0eab 127.0.0.1:6379@16379 slave 10e8962e8ce4b0200c3ee5ffcf03d196f72f72fc 0 1580825813000 12 connected
复制代码
增加/删除节点
增加节点
按照之前的配置，新增加一个节点redis-6373，然后启动它。使用以下命令将它加入到集群中。

# 127.0.0.1:6373指的是新加入集群中的节点的ip和端口
# 127.0.0.1:6379指的是集群中的某个节点的ip和地址(随便哪个节点都行)
redis-cli --cluster add-node 127.0.0.1:6373 127.0.0.1:6379
复制代码
其实这个命令的作用和cluster meet是一样的，就是用于节点握手通信(唯一不一样的就是执行前会检查集群的状态)。
还记得之前手动搭建集群的时候，节点握手完成后需要给节点分配槽。因为这里的16384个槽都已经分完了，所以这里使用重新分片来给新节点分配槽(新节点就是重新分片的目标节点)。
重新分片后来看一下新节点所负责的槽(所有的节点分了1999个槽给新节点，可以发现新节点的槽有三个范围) .

M: 533168c99a03b5593408818dabc1db36396a221f 127.0.0.1:6373
   slots:[0-788],[5962-6566],[11422-12026] (1999 slots) master
复制代码
删除节点
要删除一个从节点，只需使用del-node命令：

# 127.0.0.1:6379是集群中的任意一个节点Ip和端口
# node-id就是要删除的节点ID
redis-cli --cluster del-node 127.0.0.1:6379 <node-id>
复制代码
也可以用相同的方法删除主节点，但是要删除主节点，它必须为空。如果主节点不为空，则需要先将数据从其重新分片到所有其他主节点。否则可以使用手动故障转移的方式将主节点降为从节点，然后再执行删除。

这里以删除之前添加的redis-6373节点为例。该节点共有1999个槽，所以在我们删除节点之前需要将这些槽迁移到其它节点上。这里我将槽全部迁移到目标节点redis-6375上，将被删除的节点redis-6373作为源节点。

redis-cli --cluster reshard 127.0.0.1:6374

...

How many slots do you want to move (from 1 to 16384)? 1999
What is the receiving node ID?
f96b0261c775cf10a254f28374a4ed73c2977b1b # 目标节点redis-6375的ID
Please enter all the source node IDs.
  Type 'all' to use all the nodes as source nodes for the hash slots.
  Type 'done' once you entered all the source nodes IDs.
Source node #1: 533168c99a03b5593408818dabc1db36396a221f # 源节点redis-6373的ID
Source node #2: done # 只有一个源节点，输入done结束
复制代码
迁移结束后检查集群状态,可以发现redis-6373节点的槽为0，然后就可以放心的删除节点啦。

redis-cli --cluster check 127.0.0.1:6375
127.0.0.1:6375 (f96b0261...) -> 0 keys | 6356 slots | 1 slaves.
127.0.0.1:6374 (10e8962e...) -> 1 keys | 5672 slots | 1 slaves.
127.0.0.1:6373 (533168c9...) -> 0 keys | 0 slots | 0 slaves.
127.0.0.1:6376 (e80c00d0...) -> 0 keys | 4356 slots | 1 slaves.
复制代码
安全的将redis-6373节点从集群中删除（通过日志可以发现，其实redis是先调用 cluster forget忘记节点，然后再将节点下线）

redis-cli --cluster del-node 127.0.0.1:6375 533168c99a03b5593408818dabc1db36396a221f
>>> Removing node 533168c99a03b5593408818dabc1db36396a221f from cluster 127.0.0.1:6375
>>> Sending CLUSTER FORGET messages to the cluster...
>>> SHUTDOWN the node.
复制代码
再次查看集群节点，可以发现redis-6373节点已经不在了。

redis-cli cluster nodes
f96b0261c775cf10a254f28374a4ed73c2977b1b 127.0.0.1:6375@16375 master - 0 1580965689000 13 connected 0-788 5962-6566 11422-16383
e80c00d029005bb132b08ddbdb4029a10feff839 127.0.0.1:6376@16376 master - 0 1580965690554 10 connected 6567-10922
a6651e15af616cfe720c7731f8748fffbde56822 127.0.0.1:6378@16378 slave e80c00d029005bb132b08ddbdb4029a10feff839 0 1580965685000 10 connected
74af8a6c3dce0420707e5253fdb0a2f0c039b1a3 127.0.0.1:6377@16377 slave f96b0261c775cf10a254f28374a4ed73c2977b1b 0 1580965688000 13 connected
872e3876ea53a1ba1a77af3fb5a8c72704ac0eab 127.0.0.1:6379@16379 myself,slave 10e8962e8ce4b0200c3ee5ffcf03d196f72f72fc 0 1580965686000 12 connected
10e8962e8ce4b0200c3ee5ffcf03d196f72f72fc 127.0.0.1:6374@16374 master - 0 1580965689549 12
