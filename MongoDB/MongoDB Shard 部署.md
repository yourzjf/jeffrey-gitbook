#  MongoDB 3.4 副本集分片集群搭建

Author：Jeffrey Jhon

Timed：2017-10-16 16:56:24

Company：hangzhoutianque

![](../.markdown-ref-files\mongodb_shard_00001.png)

案例主要[参考](https://zhuanlan.zhihu.com/p/25594963)，然后细化了下安装部署过程，并且贴出详细的配置信息，以便后续翻阅查询。

官方文档：[Sharding - MongoDB Manual 3.4](https://docs.mongodb.com/manual/sharding/)

## 一、测试环境

1. 操作体统：VMware + CentOS7 + 静态IP
2. mongodb版本：mongodb-3.4.4

## 二、布局预览

|    app1    |    app2    |    app3    |    app4    |    app5    |
| :--------: | :--------: | :--------: | :--------: | :--------: |
|   mongos   |   mongos   |   config   |   config   |   config   |
| shard1主节点  | shard1备份节点 | shard1仲裁节点 | shard2仲裁节点 | shard3仲裁节点 |
| shard4仲裁节点 | shard2主节点  | shard2备份节点 | shard3备份节点 | shard4备份节点 |
| shard5备份节点 | shard5仲裁节点 | shard3主节点  | shard4主节点  | shard5主节点  |

## 三、资源分配

1. 机器分配

原则上将相同分片（sharding）的不同副本（replica set）部署在不同的机器上。

​	如：分片shard1分别在app1、app2、app3上部署了主节点、副本节点、仲裁节点。

|  ipaddress   | hostname | mongos | config | shard1 | shard2 | shard3 | shard4 | shard5 |
| :----------: | :------: | :----: | :----: | :----: | :----: | :----: | :----: | :----: |
| 172.10.20.10 |   app1   |   P    |   -    |   P    |   -    |   -    |   A    |   R    |
| 172.10.20.11 |   app2   |   P    |   -    |   R    |   P    |   -    |   -    |   A    |
| 172.10.20.12 |   app3   |   -    |   P    |   A    |   R    |   P    |   -    |   -    |
| 172.10.20.14 |   app4   |   -    |   R    |   -    |   A    |   R    |   P    |   -    |
| 172.10.20.15 |   app5   |   -    |   R    |   -    |   -    |   A    |   R    |   P    |

​	P：primary，主节点

​	R：replication，副本节点

​	A：arbitration，仲裁节点

2. 端口分配


   尽可能的为相同的分片分配相同的端口。

```shell
## port		usefor
   27017	mongosRoute(mongos)
   27001	configServer(config)
   27101	shard1
   27102	shard2
   27103	shard3
   27104	shard4
   27105	shard5
```

## 四、应用部署

### 1）分发MongoDB包

```shell
[admin@app1 ~]$ scp -r cli-mongodb admin@app2:~/
admin@app2's password: 
[admin@app1 ~]$ scp -r cli-mongodb admin@app3:~/
admin@app3's password: 
[admin@app1 ~]$ scp -r cli-mongodb admin@app4:~/
admin@app4's password: 
[admin@app1 ~]$ scp -r cli-mongodb admin@app5:~/
admin@app5's password: 
```

### 2）创建相关目录

​	创建各个节点的根目录。

```shell
# app1
[admin@app1 ~]$ mkdir -p /home/admin/mongodbints/mongos
[admin@app1 ~]$ mkdir -p /home/admin/mongodbints/shard1
[admin@app1 ~]$ mkdir -p /home/admin/mongodbints/shard4
[admin@app1 ~]$ mkdir -p /home/admin/mongodbints/shard5
# app2
[admin@app2 ~]$ mkdir -p /home/admin/mongodbints/mongos
[admin@app2 ~]$ mkdir -p /home/admin/mongodbints/shard1
[admin@app2 ~]$ mkdir -p /home/admin/mongodbints/shard2
[admin@app2 ~]$ mkdir -p /home/admin/mongodbints/shard5
# app3
[admin@app3 ~]$ mkdir -p /home/admin/mongodbints/mongo-config
[admin@app3 ~]$ mkdir -p /home/admin/mongodbints/shard1
[admin@app3 ~]$ mkdir -p /home/admin/mongodbints/shard2
[admin@app3 ~]$ mkdir -p /home/admin/mongodbints/shard3
# app4
[admin@app4 ~]$ mkdir -p /home/admin/mongodbints/mongo-config
[admin@app4 ~]$ mkdir -p /home/admin/mongodbints/shard2
[admin@app4 ~]$ mkdir -p /home/admin/mongodbints/shard3
[admin@app4 ~]$ mkdir -p /home/admin/mongodbints/shard4
# app5
[admin@app5 ~]$ mkdir -p /home/admin/mongodbints/mongo-config
[admin@app5 ~]$ mkdir -p /home/admin/mongodbints/shard3
[admin@app5 ~]$ mkdir -p /home/admin/mongodbints/shard4
[admin@app5 ~]$ mkdir -p /home/admin/mongodbints/shard5
```

​	在以上所有目录下，分别创建**data**、**log** 目录，如：

```shell
[admin@app1 ~]$ cd /home/admin/mongodbints/mongos/
[admin@app1 mongos]$ mkdir data
[admin@app1 mongos]$ mkdir log
[admin@app1 mongos]$ ll
drwxrwxr-x 4 admin admin 4096 10月 13 16:37 data
drwxrwxr-x 2 admin admin   23 5月   4 09:29 log
```

### 3）编写配置文件

​	在以上各个节点的根目录下，分别编辑如下配置文件。

#### mongos.yaml(mongos)

​	/home/admin/mongodbints/mongos/mongos.yaml

```yaml
## mongos
processManagement:
    fork: true
    pidFilePath: /home/admin/mongodbints/mongos/mongos.pid
net:
    port: 27017
systemLog:
    destination: file
    path: "/home/admin/mongodbints/mongos/log/mongos.log"
    logAppend: true
sharding:
    configDB: csRS/172.10.20.12:27001,172.10.20.14:27001,172.10.20.15:27001
```

#### mongod.yaml(mongo-config)

​	/home/admin/mongodbints/mongo-config/mongod.yaml

```yaml
processManagement:
    fork: true
    pidFilePath: /home/admin/mongodbints/mongo-config/mongod.pid
net:
    bindIp: [127.0.0.1,172.10.20.10,172.10.20.11,172.10.20.12,172.10.20.14,172.10.20.15]
    port: 27001
storage:
    dbPath: "/home/admin/mongodbints/mongo-config/data"
    journal:
        enabled: true
systemLog:
    destination: file
    path: "/home/admin/mongodbints/mongo-config/log/mongod.log"
    logAppend: true
sharding:
    clusterRole: configsvr
replication:
    replSetName: csRS
```

#### mongod.yaml(shard[1~5])

​	/home/admin/mongodbints/shard[1~5]/mongod.yaml

```yaml
processManagement:
    fork: true
    pidFilePath: /home/admin/mongodbints/shard1/shard1.pid
net:
    bindIp: [127.0.0.1,172.10.20.10,172.10.20.11,172.10.20.12,172.10.20.14,172.10.20.15]
    port: 27101
storage:
    dbPath: "/home/admin/mongodbints/shard1/data"
    journal:
        enabled: true
    directoryPerDB: true
    engine: wiredTiger
    wiredTiger:
        engineConfig:
            cacheSizeGB: 2
systemLog:
    destination: file
    path: "/home/admin/mongodbints/shard1/log/shard1.log"
    logAppend: true
sharding:
    clusterRole: shardsvr
replication:
    oplogSizeMB: 5120
    replSetName: shard1
    secondaryIndexPrefetch: all
operationProfiling:
    slowOpThresholdMs: 100
    mode: slowOp

```

​	生成shard2 ~ shard5的配置文件，修改以下相应的信息：

​		pidFilePath: /home/admin/mongodbints/**shard1**/**shard1**.pid

​		port: **27101**  //port为之前为每个分片预留的端口

​		dbPath: "/home/admin/mongodbints/**shard1**/data"

​		path: "/home/admin/mongodbints/**shard1**/log/**shard1**.log"

​		replSetName: **shard1**

### 4）启动配置服务

1. 启动每一台**配置**服务

```shell
## mongo-config at app3
[admin@app3 ~]$ ~/cli-mongodb/bin/mongod -f ~/mongodbints/mongo-config/mongod.yaml
## mongo-config at app4
[admin@app4 ~]$ ~/cli-mongodb/bin/mongod -f ~/mongodbints/mongo-config/mongod.yaml
## mongo-config at app5
[admin@app5 ~]$ ~/cli-mongodb/bin/mongod -f ~/mongodbints/mongo-config/mongod.yaml
```

2. 在任意一台配置服务器上执行初始化集群配置

```shell
[admin@app3 ~]$ ~/cli-mongodb/bin/mongo --port 27001
> config = {_id:"csRS", configsvr:true, members:[
	{_id:0, host:"172.10.20.12:27001"},
	{_id:1, host:"172.10.20.14:27001"},
	{_id:2, host:"172.10.20.15:27001"}
]};
>rs.initiate(config);
```

​	注：config的"_id"必须与mongod.yaml(mongo-config)的replication.replSetName的值一致。

### 5）启动数据节点服务

1. 启动每一台数据节点服务

```shell
## app1
[admin@app1 ~]$ ~/cli-mongodb/bin/mongod -f shard1/mongod.yaml
[admin@app1 ~]$ ~/cli-mongodb/bin/mongod -f shard4/mongod.yaml
[admin@app1 ~]$ ~/cli-mongodb/bin/mongod -f shard5/mongod.yaml
## app2
[admin@app2 ~]$ ~/cli-mongodb/bin/mongod -f shard1/mongod.yaml
[admin@app2 ~]$ ~/cli-mongodb/bin/mongod -f shard2/mongod.yaml
[admin@app2 ~]$ ~/cli-mongodb/bin/mongod -f shard5/mongod.yaml
## app3
[admin@app3 ~]$ ~/cli-mongodb/bin/mongod -f shard1/mongod.yaml
[admin@app3 ~]$ ~/cli-mongodb/bin/mongod -f shard2/mongod.yaml
[admin@app3 ~]$ ~/cli-mongodb/bin/mongod -f shard3/mongod.yaml
## app4
[admin@app4 ~]$ ~/cli-mongodb/bin/mongod -f shard2/mongod.yaml
[admin@app4 ~]$ ~/cli-mongodb/bin/mongod -f shard3/mongod.yaml
[admin@app4 ~]$ ~/cli-mongodb/bin/mongod -f shard4/mongod.yaml
## app5
[admin@app5 ~]$ ~/cli-mongodb/bin/mongod -f shard3/mongod.yaml
[admin@app5 ~]$ ~/cli-mongodb/bin/mongod -f shard4/mongod.yaml
[admin@app5 ~]$ ~/cli-mongodb/bin/mongod -f shard5/mongod.yaml
```

2. 初始化各个数据分片节点的集群配置

   在有相关分片配置的任意服务器上，执行初始化集群配置。

   eg：shard1的副本分别部署在app1、app4、app5上，在各服务器上分给shard1的端口均为27101。

```shell
## shard1
[admin@app1 ~]$ ~/cli-mongodb/bin/mongo --port 27101
> use admin;
> config = { "_id" : "shard1", "members" : [
	{"_id" : 0, "host" : "172.10.20.10:27101"},
	{"_id" : 1, "host" : "172.10.20.11:27101"},
	{"_id" : 2, "host" : "172.10.20.12:27101", "arbiterOnly" : true}
]};
>rs.initiate(config);
## shard2
[admin@app2 ~]$ ~/cli-mongodb/bin/mongo --port 27102
> use admin;
> config = { "_id" : "shard2", "members" : [
	{"_id" : 0, "host" : "172.10.20.11:27102"},
	{"_id" : 1, "host" : "172.10.20.12:27102"},
	{"_id" : 2, "host" : "172.10.20.14:27102", "arbiterOnly" : true}
]};
>rs.initiate(config);
## shard3
[admin@app3 ~]$ ~/cli-mongodb/bin/mongo --port 27103
> use admin;
> config = { "_id" : "shard3", "members" : [
	{"_id" : 0, "host" : "172.10.20.12:27103"},
	{"_id" : 1, "host" : "172.10.20.14:27103"},
	{"_id" : 2, "host" : "172.10.20.15:27103", "arbiterOnly" : true}
]};
>rs.initiate(config);
## shard4
[admin@app4 ~]$ ~/cli-mongodb/bin/mongo --port 27104
> use admin;
> config = { "_id" : "shard4", "members" : [
	{"_id" : 0, "host" : "172.10.20.14:27104"},
	{"_id" : 1, "host" : "172.10.20.15:27104"},
	{"_id" : 2, "host" : "172.10.20.10:27104", "arbiterOnly" : true}
]};
>rs.initiate(config);
## shard5
[admin@app5 ~]$ ~/cli-mongodb/bin/mongo --port 27105
> use admin;
> config = { "_id" : "shard5", "members" : [
	{"_id" : 0, "host" : "172.10.20.15:27105"},
	{"_id" : 1, "host" : "172.10.20.10:27105"},
	{"_id" : 2, "host" : "172.10.20.11:27105", "arbiterOnly" : true}
]};
>rs.initiate(config);
```

​	注：shard[1~5]的config的"_id"必须分别与各自的mongod.yaml的replication.replSetName的值一致。

### 6）启动mongos路由服务

1. 启动每一台mongos路由服务

```shell
## mongos at app1
[admin@app1 ~]$ ~/cli-mongodb/bin/mongos -f ~/mongodbints/mongos/mongos.yaml
## mongos at app2
[admin@app2 ~]$ ~/cli-mongodb/bin/mongos -f ~/mongodbints/mongos/mongos.yaml
```

2. 添加分片

​	目前搭建了mongodb配置服务器、路由服务器，各个分片服务器，不过应用程序连接到mongos路由服务器并不能使用分片机制，还需要在程序里设置分片配置，让分片生效。

```shell
[admin@app1 ~]$ ./cli-mongodb/bin/mongo 172.10.20.10:27017
> use admin;
> sh.addShard("shard1/172.10.20.10:27101,172.10.20.11:27101,172.10.20.12:27101");
> sh.addShard("shard2/172.10.20.11:27102,172.10.20.12:27102,172.10.20.14:27102");
> sh.addShard("shard3/172.10.20.12:27103,172.10.20.14:27103,172.10.20.15:27103");
> sh.addShard("shard4/172.10.20.14:27104,172.10.20.15:27104,172.10.20.10:27104");
> sh.addShard("shard5/172.10.20.15:27105,172.10.20.10:27105,172.10.20.11:27105");
```

​	注：如果以上addShard命令有提示错误，请将每个分片的仲裁节点IP去掉重试。

​	查看状态。

```shell
> sh.status();
--- Sharding Status --- 
  sharding version: {
	"_id" : 1,
	"minCompatibleVersion" : 5,
	"currentVersion" : 6,
	"clusterId" : ObjectId("590a9c5d74643311c14ed3e4")
  }
  shards:
	{ "_id" : "shard1", "host" : "shard1/172.10.20.10:27101,172.10.20.11:27101", "state" : 1 }
	{ "_id" : "shard2", "host" : "shard2/172.10.20.11:27102,172.10.20.12:27102", "state" : 1 }
	{ "_id" : "shard3", "host" : "shard3/172.10.20.12:27103,172.10.20.14:27103", "state" : 1 }
	{ "_id" : "shard4", "host" : "shard4/172.10.20.14:27104,172.10.20.15:27104", "state" : 1 }
	{ "_id" : "shard5", "host" : "shard5/172.10.20.10:27105,172.10.20.15:27105", "state" : 1 }
  active mongoses:
	"3.4.4" : 2
  ...... ......
```

### 7）应用启停顺序（待验证）

​	在关闭集群应用时，尽量注意以下顺序：

​		关闭mongos路由服务

​		关闭数据服务从节点、仲裁节点

​		关闭数据服务主节点

​		关闭config配置服务

​	在启动集群应用时，尽量注意以下顺序：

​		启动config配置服务

​		启动数据服务主节点

​		启动数据服务从节点、仲裁节点

​		启动mongos路由服务

​	至此，集群服务配置完毕。

## 五、集群应用

​	配置分片库表。详细使用请参阅官方文档。

```shell
[admin@app1 ~]$ ./cli-mongodb/bin/mongo 172.10.20.10:27017
> sh.enableSharding("testdb");
> sh.shardCollection("testdb.table1",{id:1}});
```

