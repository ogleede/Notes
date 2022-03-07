# Kafka

## Kafka入门

### 1. Kafka概述

* Kafka传统定义：![截屏2022-03-02 上午10.14.26](Kafka.assets/%E6%88%AA%E5%B1%8F2022-03-02%20%E4%B8%8A%E5%8D%8810.14.26.png)

目前主流还是作为，消息队列缓冲使用



* 传统消息队列的应用场景：
    * 缓存/消峰：有助于控制和优化数据流经过系统的速度，解决生产消息和消费消息的处理速度不一致的情况。
    * 解耦：对接数据源与处理引擎。双方只需要实现对消息队列的接口即可。
    * 异步通信：允许用户把一个消息放入队列，但并不立即处理它，在需要的时候再去处理它们。![截屏2022-03-02 上午10.19.47](Kafka.assets/%E6%88%AA%E5%B1%8F2022-03-02%20%E4%B8%8A%E5%8D%8810.19.47.png)
* 消息队列的两种模式![截屏2022-03-02 上午10.21.59](Kafka.assets/%E6%88%AA%E5%B1%8F2022-03-02%20%E4%B8%8A%E5%8D%8810.21.59.png)



点对点模式不利于处理复杂的业务模式

* Kafka的基础架构

    ![截屏2022-03-02 上午10.33.13](Kafka.assets/%E6%88%AA%E5%B1%8F2022-03-02%20%E4%B8%8A%E5%8D%8810.33.13.png)

Producer: 消息生产者

Consumer：消息消费者

Consumer Group（GC）：消费者组，由多个consumer组成。**消费者组内每个消息负责消费不同分区的数据，一个分区只能由一个组内消费者消费**；消费者组之间互不影响。所有的消费者都属于某个消费者组，即消费者组是逻辑上的一个订阅者。

Broker：一台Kafka服务器就是一个broker。一个集群由多个broker组成。一个broker可以容纳多个topic

Topic：可以理解为一个队列，生产者和消费者面向的都是一个topic

Partition：为了实现扩展性，一个非常大的topic可以分布到多个broker（即服务器）上，一个topic可以分为多个partition，每个partition是一个**有序的**队列。

Replica：副本。一个topic的每个分区都有若干个副本，一个Leader和若干个Follower

Leader：每个分区多个副本的”主“，生产者发送数据的对象，以及消费者消费数据的对象都是Leader。

Follower：每个分区多个副本中的”从“，实时从Leader中同步数据，保持和Leader数据的同步。Leader发生故障时，某个Follower会成为新的Leader。



### 2 Kafka入门

#### 2.1 安装部署

*   集群规划

| hadoop1 | haoop2 | hadoop3 |
| ------- | ------ | ------- |
| zk      | zk     | zk      |
| Kafka   | Kafka  | Kafka   |



* 集群部署

安装版本：kafka_2.12-3.0.0.tgz，2.12是scala版本，kafka生产者、消费者使用java写的，topic是用scala写的

* 配置文件设置

    * 

    ```shell
    cd config
    vim server.properties
    
    #broker 的全局唯一编号，不能重复，只能是数字。 
    broker.id=0
    #处理网络请求的线程数量 
    num.network.threads=3
    #用来处理磁盘 IO 的线程数量 
    num.io.threads=8
    #发送套接字的缓冲区大小 
    socket.send.buffer.bytes=102400
    #接收套接字的缓冲区大小 
    socket.receive.buffer.bytes=102400
    #请求套接字的缓冲区大小
    socket.request.max.bytes=104857600
    #kafka 运行日志(数据)存放的路径，路径不需要提前创建，kafka 自动帮你创建，可以 配置多个磁盘路径，路径与路径之间可以用"，"分隔
    log.dirs=/opt/module/kafka/datas
    #topic 在当前 broker 上的分区个数
    num.partitions=1
    #用来恢复和清理 data 下数据的线程数量 
    num.recovery.threads.per.data.dir=1
    # 每个topic创建时的副本数，默认时1个副本 
    offsets.topic.replication.factor=1
    #segment 文件保留的最长时间，超时将被删除
    log.retention.hours=168
    #每个 segment 文件的大小，默认最大 1G
    log.segment.bytes=1073741824
    # 检查过期数据的时间，默认5分钟检查一次是否数据过期 
    log.retention.check.interval.ms=300000
    #配置连接 Zookeeper 集群地址(在 zk 根目录下创建/kafka，方便管理),如果不创建，kafka节点会打散到zk中，不方便管理
    zookeeper.connect=hadoop1:2181,hadoop2:2181,hadoop3:2181/kafka
    
    
    分发kafka文件夹到从节点，更改各自的broker.id
    
    配置环境变量：
    sudo vim /etc/profile.d/my_env.sh
    
    #KAFKA_HOME
    export KAFKA_HOME=/opt/module/kafka
    export PATH=$PATH:$KAFKA_HOME/bin
    
    source /etc/profile
    
    分发环境变量
    sudo /home/ogleede/bin/xsync /etc/profile.d/my_env.sh
    sudo /home/parallels
    ```

* 集群启动

    * 先启动zk集群，zk.sh start
    * 分别在各个机器启动kafka   bin/kafka-server-start.sh -daemon config/server-properties

* 集群启动脚本

```shell
#!/bin/bash

case $1 in
"start")
		for i in hadoop1 hadoop2 hadoop3
		do
				echo "---启动 $i kafka ---"
				ssh $i "/opt/module/kafka/bin/kafka-server-start.sh -daemon /opt/module/kafka/config/server.properties"
		done
;;
"stop")
		for i in hadoop1 hadoop2 hadoop3
		do
				echo "---关闭 $i kafka ---"
				ssh $i "/opt/module/kafka/bin/kafka-server-stop.sh"
		done
;;
esac
```



报错信息：nohup: failed to run command 'java': No such file or directory
我想是找不到java，就在kafka-server-start.sh文件中重新export了java路径


之后的报错信息：
Error: VM option 'UseG1GC' is experimental and must be enabled via -XX:+UnlockExperimentalVMOptions.
Error: Could not create the Java Virtual Machine.
Error: A fatal exception has occurred. Program will exit.

https://blog.csdn.net/weixin_42629445/article/details/120717860
这篇博客说，删除kafka-run-class.sh中的 -XX:+UseG1GC 选项。

删除完，确实好了。。。

这里等复习完JVM再来看一看，这点问题搞了一小时。。

```shell
#!/bin/bash

case $1 in
"start"){
    for i in hadoop102 hadoop103 hadoop104
    do
        echo " --------start $i Kafka-------"
        ssh $i "source /etc/profile;nohup /opt/module/kafka/bin/kafka-server-start.sh -daemon /opt/module/kafka/config/server.properties >/dev/null 2>&1 &"
    done
};;

"stop"){
   for i in hadoop102 hadoop103 hadoop104
   do
        echo "----------stop $i kafka---------"
        ssh $i "source /etc/profile; /opt/module/kafka/bin/kafka-server-stop.sh"
   done
};;

esac


```



#### 2.2 Kafka命令行操作

![截屏2022-03-02 下午2.26.38](Kafka.assets/%E6%88%AA%E5%B1%8F2022-03-02%20%E4%B8%8B%E5%8D%882.26.38.png)



```shell
#创建topic，必须指明分区和副本
bin/kafka-topics.sh --bootstrap-server hadoop1:9092 --topic first --create --partitions 1 --replication-factor 3

#查看topic
bin/kafka-topics.sh --bootstrap-server hadoop1:9092 --list

#查看topic详细信息
bin/kafka-topics.sh --bootstrap-server hadoop1:9092 --topic first --describe

#修改分区，分区只能增加，不能减少
bin/kafka-topics.sh --bootstrap-server hadoop1:9092 --topic first --alter --partitions 3

#不能通过命令行修改副本
```



* 生产者命令行操作

bin/kafka-console-producer.sh --bootstrap-server hadoop1:9092 --topic first

* 消费者命令行操作

bin/kafka-console-consumer.sh --bootstrap-server hadoop1:9092 --topic first

#获取历史数据

bin/kafka-console-consumer.sh --bootstrap-server hadoop1:9092 --topic first --from-beginning



### 3.生产者

#### 3.1 生产者消息发送流程

![截屏2022-03-02 下午2.54.26](Kafka.assets/%E6%88%AA%E5%B1%8F2022-03-02%20%E4%B8%8B%E5%8D%882.54.26.png)



拦截器用得不多，说用flume的拦截器多一些。序列化器不用java的序列化工具，因为java的序列化包含太多其他信息了。

#### 3.2 异步发送API

见工程代码

#### 3.3同步发送

.get();

#### 3.4 生产者分区

* 分区好处：
    * 便于合理使用存储资源，每个Partition在多个Broker上存储，可以将海量数据分割成多块。合理控制分区的任务，可以实现负载均衡。
    * 提高并行度，生产者能以分区为单位发送数据；消费者能以分区为单位消费数据
* 分区策略
    * 默认分区器：DefaultPartitioner![截屏2022-03-02 下午3.41.12](Kafka.assets/%E6%88%AA%E5%B1%8F2022-03-02%20%E4%B8%8B%E5%8D%883.41.12.png)
    * 自定义分区器，可以用来过滤数据，将指定数据放到指定分区。

#### 3.5 生产者提高吞吐量策略

* batch.size:批次大小，默认16k
* linger.ms:等待时间，默认0ms，修改为5-100ms。和批大小配合使用，尽量让一次发送可以达到批次上限。但是如果时间调的太高，延迟也会增大。
* commpression.type：利用压缩
* RecordAccumulator:增大缓冲区大小，修改为64m。在分区特别多的情况下，缓冲区太小，会导致数据量不够，一些send会等待。



#### 3.6 数据可靠性

![截屏2022-03-02 下午4.45.47](Kafka.assets/%E6%88%AA%E5%B1%8F2022-03-02%20%E4%B8%8B%E5%8D%884.45.47.png)





![截屏2022-03-02 下午4.46.17](Kafka.assets/%E6%88%AA%E5%B1%8F2022-03-02%20%E4%B8%8B%E5%8D%884.46.17.png)

![截屏2022-03-02 下午4.47.20](Kafka.assets/%E6%88%AA%E5%B1%8F2022-03-02%20%E4%B8%8B%E5%8D%884.47.20.png)

#### 3.7 数据去重

这里和flink很像

![截屏2022-03-02 下午4.58.32](Kafka.assets/%E6%88%AA%E5%B1%8F2022-03-02%20%E4%B8%8B%E5%8D%884.58.32.png)

![截屏2022-03-02 下午4.58.53](Kafka.assets/%E6%88%AA%E5%B1%8F2022-03-02%20%E4%B8%8B%E5%8D%884.58.53.png)

PID：ProducerID

开启参数enable.idempotence 默认为true，false关闭



![截屏2022-03-02 下午4.59.52](Kafka.assets/%E6%88%AA%E5%B1%8F2022-03-02%20%E4%B8%8B%E5%8D%884.59.52.png)

#### 3.8 数据有序

分区内有序，分区间无序

#### 3.9数据乱序

![截屏2022-03-02 下午6.28.00](Kafka.assets/%E6%88%AA%E5%B1%8F2022-03-02%20%E4%B8%8B%E5%8D%886.28.00.png)

由于数据带着（pid，分区号，序列号），故在服务端可实现有序



### 4.Kafka Broker



#### 4.1 Kafka Broker工作流程

bin/zkCli.sh

ls /kakfa

![截屏2022-03-02 下午6.38.51](Kafka.assets/%E6%88%AA%E5%B1%8F2022-03-02%20%E4%B8%8B%E5%8D%886.38.51.png)

/kafka/brokers/ids   记录有哪些服务器

/kafka/brokers/topics/first/partitions/0/state  记录谁是Leader，有哪些服务器可用

/kafka/controller 辅助选举Leader



![截屏2022-03-02 下午6.41.13](Kafka.assets/%E6%88%AA%E5%B1%8F2022-03-02%20%E4%B8%8B%E5%8D%886.41.13.png)



Kafka节点上下线时，zk中的controller会显示变化

#### 4.2节点服役和退役

* 克隆虚拟机，修改网络配置，删除kafka下的/datas /logs ,修改broker_id
* 将新虚拟机的kafka启动，可以看到之前的first主题，但是没有新节点的信息

![截屏2022-03-02 下午6.57.46](Kafka.assets/%E6%88%AA%E5%B1%8F2022-03-02%20%E4%B8%8B%E5%8D%886.57.46.png)



![截屏2022-03-02 下午6.58.41](Kafka.assets/%E6%88%AA%E5%B1%8F2022-03-02%20%E4%B8%8B%E5%8D%886.58.49.png)



以上一通操作之后，新服役节点已经可以分担压力了。



**节点退役**

![截屏2022-03-02 下午7.02.15](Kafka.assets/%E6%88%AA%E5%B1%8F2022-03-02%20%E4%B8%8B%E5%8D%887.02.15.png)



![截屏2022-03-02 下午7.02.42](Kafka.assets/%E6%88%AA%E5%B1%8F2022-03-02%20%E4%B8%8B%E5%8D%887.02.42.png)



之后在需要退役的节点上，单独执行kafka停止命令即可。

副本数至少2个，节点挂了之后，就可以恢复。



#### 4.3 副本基本信息

##### 4.3.1

![截屏2022-03-02 下午7.07.19](Kafka.assets/%E6%88%AA%E5%B1%8F2022-03-02%20%E4%B8%8B%E5%8D%887.07.19.png)

![截屏2022-03-02 下午7.07.36](Kafka.assets/%E6%88%AA%E5%B1%8F2022-03-02%20%E4%B8%8B%E5%8D%887.07.36.png)

##### 4.3.2 Leader选举流程

leader挂了之后，会根据ar中的顺序，检查isr是否存在。如果存在，按顺序选举新节点。挂掉的节点重新上线，不会影响Leader，会加入到isr的末尾



##### 4.3.3 Follower故障处理

##### ![截屏2022-03-02 下午7.37.57](Kafka.assets/%E6%88%AA%E5%B1%8F2022-03-02%20%E4%B8%8B%E5%8D%887.37.57.png)

截取的数据，是因为数据未校验，不可用



##### 4.3.5 Leader故障处理

![截屏2022-03-02 下午7.38.43](Kafka.assets/%E6%88%AA%E5%B1%8F2022-03-02%20%E4%B8%8B%E5%8D%887.38.43.png)





消费者组分组，同一组只能消费不同分区数据：防止后续去重操作。

不同组可以消费同一分区数据。
