#### 机器列表

##### zookeeper

| hostname | ip          | ZK          |
| -------- | ----------- | ----------- |
| app21    | 10.10.10.21 | zookeeper.1 |
| app22    | 10.10.10.22 | zookeeper.2 |
| app23    | 10.10.10.23 | zookeeper.3 |

##### hadoop

| hostname | ip          | NN       | SNN               | DN       |
| -------- | ----------- | -------- | ----------------- | -------- |
| app21    | 10.10.10.21 | NameNode |                   | DataNode |
| app22    | 10.10.10.22 |          | SecondaryNameNode | DataNode |
| app23    | 10.10.10.23 |          |                   | DataNode |

##### yarn

| hostname | ip          | RM              | NM          |
| -------- | ----------- | --------------- | ----------- |
| app21    | 10.10.10.21 |                 | NodeManager |
| app22    | 10.10.10.22 |                 | NodeManager |
| app23    | 10.10.10.23 | ResourceManager | NodeManager |

#### 服务安装（在某一台机器上进行）

> 下载zookeeper安装包（hadoop-2.7.7.tar.gz）`http://mirror.bit.edu.cn/apache/hadoop/common/hadoop-2.7.7/hadoop-2.7.7.tar.gz`
>
> 解压到用户目录下的software文件夹中（/home/admin/software/hadoop-2.7.7）
>
> 官方资源列表：

| 资源               | 资源路径                                                     |
| ------------------ | ------------------------------------------------------------ |
| 官方文档           | http://hadoop.apache.org/docs/r2.7.6/hadoop-project-dist/hadoop-common/ClusterSetup.html |
| core-default.xml   | http://hadoop.apache.org/docs/r2.7.6/hadoop-project-dist/hadoop-common/core-default.xml |
| hdfs-default.xml   | http://hadoop.apache.org/docs/r2.7.6/hadoop-project-dist/hadoop-hdfs/hdfs-default.xml |
| mapred-default.xml | http://hadoop.apache.org/docs/r2.7.6/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml |
| yarn-default.xml   | http://hadoop.apache.org/docs/r2.7.6/hadoop-yarn/hadoop-yarn-common/yarn-default.xml |
| 配置属性变化       | http://hadoop.apache.org/docs/r2.7.6/hadoop-project-dist/hadoop-common/DeprecatedProperties.html |

core-site.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://app21:9000</value>
    <description>namenode address</description>
  </property>
  <property>
    <name>io.file.buffer.size</name>
    <value>131072</value>
  </property>
  <property>
    <name>hadoop.tmp.dir</name>
    <value>/home/admin/software/hadoop-2.7.7/datadir/tmp</value>
  </property>
</configuration>

```

hdfs-site.xml

