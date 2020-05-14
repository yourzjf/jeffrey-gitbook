# Kafka Command

## Version 2.1.0

### kafka-consumer-groups.sh

#### kafka group

##### 消费者组：list

```shell
$ ./kafka-consumer-groups.sh --bootstrap-server hadoop1:59092,hadoop2:59092,hadoop3:59092 --list
trajectoryGroup
myGroup
```

##### 消费者组：state

```shell
$ ./kafka-consumer-groups.sh --bootstrap-server hadoop1:59092,hadoop2:59092,hadoop3:59092 --group trajectoryGroup --describe --state

COORDINATOR (ID)          ASSIGNMENT-STRATEGY       STATE                #MEMBERS
hadoop1:59092 (0)         range                     Stable               4
```

##### 消费者组：describe 

> 查看消息堆积

```shell
$ ./kafka-consumer-groups.sh --bootstrap-server hadoop1:59092,hadoop2:59092,hadoop3:59092 --group trajectoryGroup --describe

TOPIC               PARTITION  CURRENT-OFFSET  LOG-END-OFFSET  LAG             CONSUMER-ID                                     HOST            CLIENT-ID
zhejiang_trajectory 0          143693805       143693905       100             consumer-1-02f47385-5496-4094-9513-09c0b66b47bc /59.202.28.243  consumer-1
zhejiang_trajectory 1          137186261       137186348       87              consumer-1-02f47385-5496-4094-9513-09c0b66b47bc /59.202.28.243  consumer-1
```

##### 消费者组：members

```shell
$ ./kafka-consumer-groups.sh --bootstrap-server hadoop1:59092,hadoop2:59092,hadoop3:59092 --group trajectoryGroup --describe --members

CONSUMER-ID                                     HOST            CLIENT-ID     #PARTITIONS
consumer-1-2de6119e-fc4f-41a6-baa1-a702665d9c6f /59.202.28.237  consumer-1    3         
consumer-1-02f47385-5496-4094-9513-09c0b66b47bc /59.202.28.243  consumer-1    3
```

##### 消费者组：members verbose

```shell
$ ./kafka-consumer-groups.sh --bootstrap-server hadoop1:59092,hadoop2:59092,hadoop3:59092 --group trajectoryGroup --describe --members --verbose

CONSUMER-ID                                     HOST            CLIENT-ID       #PARTITIONS     ASSIGNMENT
consumer-1-2de6119e-fc4f-41a6-baa1-a702665d9c6f /59.202.28.237  consumer-1      3               zhejiang_trajectory(3,4,5)
consumer-1-02f47385-5496-4094-9513-09c0b66b47bc /59.202.28.243  consumer-1      3               zhejiang_trajectory(0,1,2)
```

#### kafka topic

##### 主题：list

```shell
$ ./kafka-topics.sh --zookeeper hadoop1:2181,hadoop2:2181,hadoop3:2181/kafka_2.11-2.1.0 --list
__consumer_offsets
zhejiang_trajectory
```

##### 主题：describe

> 查看主题分区信息、副本信息、同步副本信息

```shell
./kafka-topics.sh --zookeeper hadoop1:2181,hadoop2:2181,hadoop3:2181/kafka_2.11-2.1.0 --topic zhejiang_trajectory --describe
Topic:zhejiang_trajectory	PartitionCount:6	ReplicationFactor:2	Configs:
	Topic: zhejiang_trajectory	Partition: 0	Leader: 2	Replicas: 2,1	Isr: 1,2
	Topic: zhejiang_trajectory	Partition: 1	Leader: 2	Replicas: 0,2	Isr: 2,0
	Topic: zhejiang_trajectory	Partition: 2	Leader: 1	Replicas: 1,0	Isr: 1,0
	Topic: zhejiang_trajectory	Partition: 3	Leader: 2	Replicas: 2,0	Isr: 2,0
	Topic: zhejiang_trajectory	Partition: 4	Leader: 1	Replicas: 0,1	Isr: 1,0
	Topic: zhejiang_trajectory	Partition: 5	Leader: 1	Replicas: 1,2	Isr: 1,2
```

#### kafka console consumer

##### 控制台消费者：

> 控制台命令行消费者：消费主题消息

```shell
$ ./kafka-console-consumer.sh --zookeeper hadoop4:59092,hadoop5:59092,hadoop6:59092 --topic zhejiang_trajectory --describe
```