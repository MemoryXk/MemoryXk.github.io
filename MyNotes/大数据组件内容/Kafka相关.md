# Kafka相关

## 一.介绍

> **Kafka是一个分布式的基于发布/订阅模式的消息队列，主要应用于大数据实时处理领域**

### 消息队列

* **消息队列的两种模式**

  * **点对点模式**（**一对一**，消费者主动拉取数据，消息收到后消息清除）

    消息生产者生产消息发送到Queue中,然后消息消费者从Queue中取出并且消费消息。

    消息被消费以后，queue中不再有存储，所以消息消费者不可能消费到已经被消费的消息。Queue支持存在多个消费者，但是对一个消息而言，只会有一个消费者可以消费。

  

![消息队列点对点模式.png](https://ae01.alicdn.com/kf/H8a0e826b7e6b479f98cc8e2d23dceaa2V.png)

  * **发布/订阅模式**（**一对多**，消费者消费数据之后不会清除消息）
  
    消息生产者（发布）将消息发布到topic中，同时有多个消息消费者（订阅）消费该消息。和点对点方式不同，发布到topic的消息会被所有订阅者消费。

![消息队列发布订阅模式.png](https://ae01.alicdn.com/kf/H8ac2144074ed47d2b2ff3222cc30ec642.png)

### Kafka基础架构

![kafka基础架构](https://ae01.alicdn.com/kf/Hf18fc7d88109466b8fa52bce1bfeee766.png)

* **Producer**：消息生产者，就是向Kakfa broker发消息的客户端
* **Consumer：**消息消费者，向kafka取消息的客户端
* **Consumer Group（CG）：**消费者组，由多个consumer组成。**消费者组内每个消费者负责消费不同分区的数据，一个分区只能由一个消费者消费；消费者组之间互不影响。**所有的消费者都属于某个消费者组，既**消费者组是逻辑上的一个订阅者**。
* **Broker：**一台Kafka服务器就是一个broker。一个集群由多个broker组成，一个broker可以容纳多个topic。
* **Topic：**可以理解为一个队列，**生产者和消费者面向的都是一个topic**。
* **Partition：**为了实现扩展性，一个非常大的topic可以分布到多个broker（服务器）上，**一个topic可以分为多个partition**，每一个partition是一个有序的队列。
* **Replica：**副本，为保证集群中的某个节点发生故障，**该节点上的patition数据不丢失，且kafka仍然能够继续工作**，kafka提供了副本机制，一个topic的每个分区都有若干个副本，一个leader和若干个follower。

## 安装部署

​	**Kafka镜像网址：http://mirror.bit.edu.cn/apache/kafka/**

> **Kafa端口号：9092**

* **集群规划**

  | node01 | node02 | node03 |
  | :----: | :----: | :----: |
  |   zk   |   zk   |   zk   |
  | kafka  | kafka  | kafka  |

* **[搭建Zookeeper](https://memoryxk.github.io/MyNotes/大数据组件内容/Hadoop相关.html#Zookeeper)**

* **解压安装包**

```bash
tar -zxvf kafka_2.12-1.0.2.tgz -C /usr/local/src
cd /usr/local/src/
mv kafka_2.12-1.0.2 kafka
mkdir kafka/logs
```

* **配置环境变量**

```python
vim /etc/profile
#添加以下内容
export KAFKA_HOME=/usr/local/src/kafka
export PATH=$PATH:$KAFKA_HOME/bin
```

* **修改配置文件server.properties**

```python
cd /usr/local/src/kafka/config
vim server.properties
#修改以下内容：
log.dirs=/usr/local/src/kafka/logs
delete.topic.enable=true
zookeeper.connect=node01:2181,node02:2181,node03:2181
```

* **分发给其他节点**

```bash
scp -r /usr/local/src/kafka node02:/usr/local/src/
scp -r /usr/local/src/kafka node03:/usr/local/src/
#修改每一个节点server.properties配置文件中的broker.id（每一个节点的id不能一样）
```

* **启动三个节点的Kafka**

```bash
#启动kafka之前一定先要启动Zookeeper
kafka-server-start.sh -daemon /usr/local/src/kafka/config/server.properties
```

* **Kafka命令行测试**

```bash
#创建一个topic
kafka-topics.sh --zookeeper node02:2181 --create --topic first --partitions 3 --replication-factor 2

#查看topic
kafka-topics.sh --zookeeper node02:2181 --list

#查看topic中的描述信息
kafka-topics.sh --zookeeper node03:2181 --describe --topic first

#发送消息到节点
kafka-console-producer.sh --topic first --broker-list node02:9092,node03:9092 

#消费节点的消息
kafka-console-consumer.sh --topic first --bootstrap-server node01:9092,node02:9092

#修改分区，partitions数量只能增加不能减少
kafka-topics.sh --zookeeper node02:2181 --topic first --alter --partitions 4
```

## Kafka架构深入

### Kafka工作流程及文件储存机制

* **Kafka工作流程**

![kafka工作流程](https://ae01.alicdn.com/kf/H2465bce8c84c4bf3a2d72989b8182ac0I.png)

Kafka中消息是以**topic**进行分类的，生产者生产消息，消费者消费消息，都是面向**topic**的。

topic是逻辑上的概念，而**partition**是物理上的概念，每个**partition**对应于一个log文件，该log文件中存储的就是**producer**生产的数据。**Producer**生产的数据会被不断追加到该log文件末端，且每条数据都有自己的**offset**。消费者组中的每个消费者，都会实时记录自己消费到了哪个offset，以便出错恢复时，从上次的位置继续消费。

https://blog.csdn.net/qq_35641192/article/details/80956244

https://www.jianshu.com/p/f0687411f631

* **Kafka文件储存机制**

![kafka文件储存机制](https://ae01.alicdn.com/kf/H235c31a640b04a959c08d04765b0a2b1v.png)
	 由于生产者生产的消息会不断追加到log文件末尾，为防止log文件过大导致数据定位效率低下，Kaka 采取了**分片**和**索引**机制，将每个partition分为多个**segment**。每个segment对应两个文件-- -- **“ .index"**文件和**“.log”**文件。这些文件位于一个文件夹下，该文件夹的命名规则为: topic 名称+分区序号。例如，first 这个topic有三个分区，则其对应的文件夹为first-0,first-1 ,first-2。

```bash
0000000.index
0000000.log
0000005.index
0000005.log
00000010.index
00000010.log
```

​		index和log文件以当前segment的第一条消息的offset命名。下图为index文件和log文件的结构示意图。

![获取offset示意图](https://ae01.alicdn.com/kf/Ha56df60b906d41f3a529b6dfce6c3aa8y.png)

​	“.index“文件储存大量的索引信息，”.log“文件储存大量的数据，索引文件中的元数据指向对应数据文件中message的物理偏移地址。

https://www.jianshu.com/p/73d49083c896

### Kafka生产者

* **分区策略**

  * **分区的原因**

    * **方便在集群中扩展**。每个Partition可以通过调整以适应它所在的机器，而一个topic又可以有多个Partition组成，因此整个集群就可以适应任意大小的数据了；
    * **可以提高并发。**因为可以以Partition为单位读写了。

  * **分区的原则**

    我们需要将producer发送的数据封装成一个**ProducerRecord**对象。

    * 指明partition的情况下，直接将指明的值作为partition值；
    * 没有指明partition值但有key的情况下，将key的hash值与topic的partition数进行取余得到partition值
    * 既没有partition值又没有key值的情况下，第一次调用时随机生成一个整数（后面每次调用在这个整数上自增）,将这个值与topic可以用的partition总数取余得到partition值，也就是常说的round-robin(轮循)算法

* **数据可靠性保证**

  > **为保证producer发送的数据，能可靠的发送到指定的topic, topic的每个patition收到produce发送的数据后,都需要向producer发送ack（acknowledgement确认收到），如果producer收到ack，就会进行下一轮的发送，否则重新发送数据。**

https://baijiahao.baidu.com/s?id=1658221660132943620&wfr=spider&for=pc