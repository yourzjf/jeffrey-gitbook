#### 机器列表

| hostname | ip          |
| -------- | ----------- |
| app21    | 10.10.10.21 |
| app22    | 10.10.10.22 |
| app23    | 10.10.10.23 |

#### 服务安装（在某一台机器上进行）

> 下载zookeeper安装包（zookeeper-3.4.9.tar.gz）
>
> 解压到用户目录下的software文件夹中（/home/admin/software/zookeeper-3.4.9）
>
> 编辑conf/zoo.cfg

```shell
# The number of milliseconds of each tick
tickTime=2000
# The number of ticks that the initial 
# synchronization phase can take
initLimit=10
# The number of ticks that can pass between 
# sending a request and getting an acknowledgement
syncLimit=5
# the directory where the snapshot is stored.
# do not use /tmp for storage, /tmp here is just 
# example sakes.
# dataDir=/tmp/zookeeper
dataDir=/home/admin/software/zookeeper-3.4.9/zkdata
dataLogDir=/home/admin/software/zookeeper-3.4.9/logs
# the port at which the clients will connect
clientPort=2181
# the maximum number of client connections.
# increase this if you need to handle more clients
#maxClientCnxns=60
#
# Be sure to read the maintenance section of the 
# administrator guide before turning on autopurge.
#
# http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance
#
# The number of snapshots to retain in dataDir
#autopurge.snapRetainCount=3
# Purge task interval in hours
# Set to "0" to disable auto purge feature
#autopurge.purgeInterval=1
server.1=app21:2888:3888
server.2=app22:2888:3888
server.3=app23:2888:3888
```

> 创建数据文件夹：/home/admin/software/zookeeper-3.4.9/zkdata
>
> 创建日志文件夹：/home/admin/software/zookeeper-3.4.9/logs

#### 分发文件

```shell
$ scp -r ~/software/zookeeper-3.4.9 app22:~/software
$ scp -r ~/software/zookeeper-3.4.9 app23:~/software
```

> 分别配置每个服务节点（zoo.cfg的server列表中的编号与机器对应）

```shell
# app21
$ echo "1" > ~/software/zookeeper-3.4.9/zkdata/myid
# app22
$ echo "2" > ~/software/zookeeper-3.4.9/zkdata/myid
# app23
$ echo "3" > ~/software/zookeeper-3.4.9/zkdata/myid
```

#### 服务启停

```shell
# 启动命令
$ ./zkServer.sh start
# 服务节点状态
$ ./zkServer.sh status
# 停止命令
$ ./zkServer.sh stop
```

