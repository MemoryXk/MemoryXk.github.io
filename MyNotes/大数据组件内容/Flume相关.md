# Flume相关

## 一.介绍

> **Flume是Cloudera提供的一个高可用的，高可靠的，分布式的海量日志采集、聚合和传输的软件。Flume基于流式架构，灵活简单。
>  Flume的核心是把数据从数据源(source)收集过来，再将收集到的数据送到指定的目的地(sink)。为了保证输送的过程一定成功，在送到目的地(sink)之前，会先缓存数据(channel),待数据真正到达目的地(sink)后，flume再删除自己缓存的数据。**

![flume运行流程](https://ae01.alicdn.com/kf/H2cf222625d9b47e39faa4afb56ec2b02n.png))

* **Flume的优点**
  1.可以高速采集数据，采集的数据能够以想要的文件格式及压缩方式存储在hdfs上

  2.事务功能保证了数据在采集的过程中数据不丢失

  3.部分Source保证了Flume挂了以后重启依旧能够继续在上一次采集点采集数据，真正做到数据零丢失

* **Flume的组成架构**

![Flume组成架构](https://ae01.alicdn.com/kf/H3291726f1ac54bd08c8da12d0daace2bF.png)

![flume组成架构2](https://ae01.alicdn.com/kf/H9dca9cbf9c8443fe8979579ecdbf32e2D.png)

* **Agent**

  Agent是一个JVM进程，它以事件的形式将数据从源头送至目的

  Agent主要有3个部分组成：**Source**、**Channel**、**Sink**

* **Source**

  **Source是负责接收数据的Flume Agent的组件**。Source组件可以处理各种类型、各种格式的日志数据，包括：avro、thrift、**exec**、jms、**spooling directory(spooldir文件夹1.7之后出现)**、netcat、sequence generator、syslog、http、legacy。

* **Channel**

  **Channel是位于Source和Sink之间的缓冲区。**因此，Channel允许Source和Sink运作在不同的速率上。Channel是线程安全的，可以处理几个Source的写入操作和几个Sink的读取操作

  Flume自带两种Channel：

  **Memory Channel（内存中的队列）** 

  **File Channel（将所有事件写到磁盘）**

* **Sink**

  **Sink不断地轮询Channel中的是事件且批量地移除它们，并将这些事件批量写入到储存或索引系统、或者被发送到另一个Flume Agent。**

  Sink组件目的地包括：**hdfs、logger、avro、thrift、ipc、file、null、Hbase、solr、自定义。**

* **Flume Agent的内部原理**

![Flume Agent的内部原理](https://ae01.alicdn.com/kf/Hc93ee0711b0b4b3ea72e3e45449a9ab8Y.png))

## 二.搭建

**Flume镜像网:http://mirror.bit.edu.cn/apache/flume/**

* **配置Flume环境**

```python
wget http://mirror.bit.edu.cn/apache/flume/1.6.0/apache-flume-1.6.0-bin.tar.gz
tar -zxvf apache-flume-1.6.0-bin.tar.gz -C /usr/local/src
mv apache-flume-1.6.0-bin.tar.gz flume
```

* **修改flume-env.sh**

```python
cd /usr/local/src/flume/conf
cp -p flume-env.sh.template flume-env.sh
vim flume-env.sh
#修改为：
JAVA_HOME=/usr/local/src/java
```

* **启动（具体看案例）**

## 三.案例

### 监控端口数据官方案例

* **案例需求**

  > **服务端：首先启动Flume任务，监控本机55555端口**
  >
  > **客户端：然后通过netcat工具向本机55555端口发送消息**
  >
  > **最后Flume将监听的数据实时显示在控制台。**

* **实现步骤**
  * **安装netcat工具**

  ```python
  yum -y install nc
  ```

  * **创建Flume Agent配置文件flume-netcat-logger.conf**

  ```python
  #在flume配置文件根目录创建Agent配置文件夹
  mkdir job
  cd job
  vim flume-netcat-logger.conf
  ```

  ​	**添加以下内容：**

  ```python
  # Name the components on this agent
  a1.sources = r1
  a1.sinks = k1
  a1.channels = c1
  
  # Describe/configure the source
  a1.sources.r1.type = netcat
  a1.sources.r1.bind = 0.0.0.0
  a1.sources.r1.port = 55555
  
  # Describe/configure the sink
  a1.sinks.k1.type = logger
  
  # Use a channel which buffers events in memory
  a1.channels.c1.type = memory
  a1.channels.c1.capacity = 1000
  a1.channels.c1.transactionCapacity = 100
  
  # Bind the source and sink to rhe channel
  a1.sources.r1.channels = c1
  a1.sinks.k1.channel = c1
  ```
  ![Agent配置文件](https://ae01.alicdn.com/kf/He653709f82e147a39e48c6ea5b6c3b61p.png)

  * **启动**

  ```bash
  #在flume配置文件根目录下
  #第一种写法：
  bin/flume-ng agent --conf conf/ --name a1 --conf-file job/flume-netcat-logger.conf -Dflume.root.logger=INFO,console
  #第二种写法：
  bin/flume-ng agent -c conf/ -n a1 -f job/flume-netcat-logger.conf -Dflume.root.logger=INFO,console
  ```

### 实时读取本地文件到HDFS案例

* **案例需求**

  > **实时监控apache日志，并上传到HDFS中**

* **实现步骤**

  * **创建Flume Agent配置文件flume-file-hdfs.conf**

  ```bash
  #在flume配置文件根目录创建Agent配置文件夹
  cd job
  vim flume-file-hdfs.conf
  ```

  ​	**添加以下内容：**

  ```python
  # Name the components on this agent
  a2.sources = r2
  a2.sinks = k2
  a2.channels = c2
  
  # Describe/configure the source
  a2.sources.r2.type = exec
  a2.sources.r2.command = tail -F /etc/httpd/logs/access_log
  a2.sources.r2.shell = /bin/bash -c
  
  # Describe/configure the sink
  a2.sinks.k2.type = hdfs
  a2.sinks.k2.hdfs.path = hdfs://node01:9000/flume/%Y%m%d/%H
  #上传文件的前缀
  a2.sinks.k2.hdfs.filePrefix = logs-
  #是否按照时间滚动文件夹
  a2.sinks.k2.hdfs.round = true
  #多少时间单位创建一个新的文件夹
  a2.sinks.k2.hdfs.roundValue = 1
  #重新定义时间单位
  a2.sinks.k2.hdfs.roundUnit = hour
  #是否使用本地时间戳
  a2.sinks.k2.hdfs.useLocalTimeStamp = true
  #积攒多少个Event才flush到HDFS一次
  a2.sinks.k2.hdfs.batchSize = 1000
  #设置文件类型，可支持压缩
  a2.sinks.k2.hdfs.fileType = DataStream
  #多久生成一个新的文件
  a2.sinks.k2.hdfs.rollInterval = 60
  #设置每一个文件的滚动大小
  a2.sinks.k2.hdfs.rollSize = 134217700
  #文件的滚动与Event数量无关
  a2.sinks.k2.hdfs.rollCount = 0
  
  # Use a channel which buffers events in memory
  a2.channels.c2.type = memory
  a2.channels.c2.capacity = 1000
  a2.channels.c2.transactionCapacity = 100
  
  # Bind the source and sink to rhe channel
  a2.sources.r2.channels = c2
  a2.sinks.k2.channel = c2
  ```

  * **启动**

  ```bash
  #在flume配置文件根目录下
  bin/flume-ng agent -c conf/ -n a2 -f job/flume-file-hdfs.conf
  ```

### 实时读取本地文件夹文件到HDFS案例

* **实现步骤**

  * **创建Flume Agent配置文件flume-dir-hdfs.conf**

  ```bash
  #在flume配置文件根目录创建Agent配置文件夹
  cd job
  vim flume-dir-hdfs.conf
  ```

  ​	**添加以下内容：**

  ```python
  # Name the components on this agent
  a3.sources = r3
  a3.sinks = k3
  a3.channels = c3
  
  # Describe/configure the source
  a3.sources.r3.type = spooldir
  a3.sources.r3.spoolDir = /usr/local/test
  a3.sources.r3.fileSuffix = .COMPLETED
  a3.sources.r3.fileHeader = true
  #忽略所以以.tmp结尾的文件，不上传
  a3.sources.r3.ignorePattern = ([^ ]*\.tmp)
  
  # Describe/configure the sink
  a3.sinks.k3.type = hdfs
  a3.sinks.k3.hdfs.path = hdfs://node01:9000/flume/upload/%Y%m%d/%H
  #上传文件的前缀
  a3.sinks.k3.hdfs.filePrefix = upload-
  #是否按照时间滚动文件夹
  a3.sinks.k3.hdfs.round = true
  #多少时间单位创建一个新的文件夹
  a3.sinks.k3.hdfs.roundValue = 1
  #重新定义时间单位
  a3.sinks.k3.hdfs.roundUnit = hour
  #是否使用本地时间戳
  a3.sinks.k3.hdfs.useLocalTimeStamp = true
  #积攒多少个Event才flush到HDFS一次
  a3.sinks.k3.hdfs.batchSize = 1000
  #设置文件类型，可支持压缩
  a3.sinks.k3.hdfs.fileType = DataStream
  #多久生成一个新的文件
  a3.sinks.k3.hdfs.rollInterval = 60
  #设置每一个文件的滚动大小
  a3.sinks.k3.hdfs.rollSize = 134217700
  #文件的滚动与Event数量无关
  a3.sinks.k3.hdfs.rollCount = 0
  
  # Use a channel which buffers events in memory
  a3.channels.c3.type = memory
  a3.channels.c3.capacity = 1000
  a3.channels.c3.transactionCapacity = 100
  
  # Bind the source and sink to rhe channel
  a3.sources.r3.channels = c3
  a3.sinks.k3.channel = c3
  ```

  * **启动**

  ```python
  #在flume配置文件根目录下
  bin/flume-ng agent -c conf/ -n a2 -f job/flume-dir-hdfs.conf
  ```

### 单数据源多出口案例(选择器)

* **案例需求**

  > **使用Flume-1监控文件变动，Flume-1将变动内容传递给Flume-2，Flume-2负责储存到HDFS。同时Flume-1将变动内容传递给Flume-3，Flume-3负责输出到Local FileSystem（2个sink，2个channel）**

* **实现步骤**

  * **创建Flume Agent配置文件flume-file-avro.conf**

  ```bash
  mkdir job/project1
  cd job/project1
  vim flume-file-avro.conf
  ```

  ​	**添加以下内容：**

  ```python
  # Name the components on this agent
  flume1.sources = r1
  flume1.sinks = k1 k2
  flume1.channels = c1 c2
  #将数据流复制给所有channel
  flume1.sources.r1.selector.type = replicating
  
  # Describe/configure the source
  flume1.sources.r1.type = exec
  flume1.sources.r1.command = tail -F /etc/httpd/logs
  flume1.sources.r1.shell = /bin/bash -c
  
  # Describe/configure the sink
  flume1.sinks.k1.type = avro
  flume1.sinks.k1.hostname = node01
  flume1.sinks.k1.port = 4210
  
  flume1.sinks.k2.type = avro
  flume1.sinks.k2.hostname = node01
  flume1.sinks.k2.port = 4211
  
  # Use a channel which buffers events in memory
  flume1.channels.c1.type = memory
  flume1.channels.c1.capacity = 1000
  flume1.channels.c1.transactionCapacity = 100
  
  flume1.channels.c2.type = memory
  flume1.channels.c2.capacity = 1000
  flume1.channels.c2.transactionCapacity = 100
  
  # Bind the source and sink to rhe channel
  flume1.sources.r1.channels = c1 c2
  flume1.sinks.k1.channel = c1
  flume1.sinks.k2.channel = c2
  ```

  * **创建Flume Agent配置文件flume-avro-local.conf**

  ```bash
  cd job/project1
  vim flume-avro-local.conf
  ```

  ​	**添加以下内容：**

  ```python
  # Name the components on this agent
  flume2.sources = r2
  flume2.sinks = k2
  flume2.channels = c2
  
  # Describe/configure the source
  flume2.sources.r2.type = avro
  flume2.sources.r2.bind = node01
  flume2.sources.r2.port = 4210
  
  # Describe/configure the sink
  flume2.sinks.k2.type = file_roll
  flume2.sinks.k2.directory = /usr/local/flume2
  
  # Use a channel which buffers events in memory
  flume2.channels.c2.type = memory
  flume2.channels.c2.capacity = 1000
  flume2.channels.c2.transactionCapacity = 100
  
  # Bind the source and sink to rhe channel
  flume2.sources.r2.channels = c2
  flume2.sinks.k2.channel = c2
  ```

  **提示：输出的本地目录必须是已经存在的目录，如果该目录不存在，并不会创建新的目录**

  * **创建Flume Agent配置文件flume-avro-hdfs.conf**

  ```bash
  cd job/project1
  vim flume-avro-hdfs.conf
  ```

  ​	**添加以下内容：**

  ```python
  # Name the components on this agent
  flume3.sources = r3
  flume3.sinks = k3
  flume3.channels = c3
  
  # Describe/configure the source
  flume3.sources.r3.type = avro
  flume3.sources.r3.bind = node01
  flume3.sources.r3.port = 4211
  
  # Describe/configure the sink
  flume3.sinks.k3.type = hdfs
  flume3.sinks.k3.hdfs.path = hdfs://node01:9000/flume/project1/%Y%m%d/%H
  #上传文件的前缀
  a3.sinks.k3.hdfs.filePrefix = upload-
  #是否按照时间滚动文件夹
  a3.sinks.k3.hdfs.round = true
  #多少时间单位创建一个新的文件夹
  a3.sinks.k3.hdfs.roundValue = 1
  #重新定义时间单位
  a3.sinks.k3.hdfs.roundUnit = hour
  #是否使用本地时间戳
  a3.sinks.k3.hdfs.useLocalTimeStamp = true
  #积攒多少个Event才flush到HDFS一次
  a3.sinks.k3.hdfs.batchSize = 1000
  #设置文件类型，可支持压缩
  a3.sinks.k3.hdfs.fileType = DataStream
  #多久生成一个新的文件
  a3.sinks.k3.hdfs.rollInterval = 60
  #设置每一个文件的滚动大小
  a3.sinks.k3.hdfs.rollSize = 134217700
  #文件的滚动与Event数量无关
  a3.sinks.k3.hdfs.rollCount = 0
  
  # Use a channel which buffers events in memory
  flume3.channels.c3.type = memory
  flume3.channels.c3.capacity = 1000
  flume3.channels.c3.transactionCapacity = 100
  
  # Bind the source and sink to rhe channel
  flume3.sources.r3.channels = c3
  flume3.sinks.k3.channel = c3
  ```

  * **启动**

  ```bash
  #先启动flume2或者flume3，最后再启动flume1
  bin/flume-ng agent -c conf/ -n flume2 -f job/project1/flume-avro-local.conf
  bin/flume-ng agent -c conf/ -n flume3 -f job/project1/flume-avro-hdfs.conf
  bin/flume-ng agent -c conf/ -n flume1 -f job/project1/flume-file-avro.conf
  ```

### 单数据源多出口案例(sinks组)

* **案例需求**

  > **使用Flume-1监控文件变动，Flume-1将变动内容传递给Flume-2，Flume-2负责储存到控制台。同时Flume-1将变动内容传递给Flume-3，Flume-3负责输出到控制台（2个sink，1个channel,1个sinkgroup组,轮循原理）**

* **实现步骤**

  * **创建Flume Agent配置文件flume-netcat-avro.conf**

  ```bash
  mkdir job/project2
  cd job/project2
  vim flume-netcat-avro.conf
  ```

  ​	**添加以下内容：**

  ```python
  # Name the components on this agent
  flume1.sources = r1
  flume1.sinks = k1 k2
  flume1.channels = c1
  flume1.sinkgroups = g1
  
  #configure the sinkgroups
  #设置类型为负载均衡
  flume1.sinkgroups.g1.processor.type = load_balance
  #设置是否回滚
  flume1.sinkgroups.g1.processor.backoff = true
  #设置为轮循
  flume1.sinkgroups.g1.processor.selector = round_robin
  #设置超时的时间
  flume1.sinkgroups.g1.processor.selector.maxTimeOut = 10000
  
  # Describe/configure the source
  flume1.sources.r1.type = netcat
  flume1.sources.r1.bind = 0.0.0.0
  flume1.sources.r1.port = 4455
  
  # Describe/configure the sink
  flume1.sinks.k1.type = avro
  flume1.sinks.k1.hostname = node01
  flume1.sinks.k1.port = 4210
  
  flume1.sinks.k2.type = avro
  flume1.sinks.k2.hostname = node01
  flume1.sinks.k2.port = 4211
  
  # Use a channel which buffers events in memory
  flume1.channels.c1.type = memory
  flume1.channels.c1.capacity = 1000
  flume1.channels.c1.transactionCapacity = 100
  
  # Bind the source and sink to rhe channel
  flume1.sources.r1.channels = c1
  flume1.sinkgroups.g1.sinks = k1 k2
  flume1.sinks.k1.channel = c1
  flume1.sinks.k2.channel = c1
  ```

  * **创建Flume Agent配置文件flume-avro-console1.conf**

  ```bash
  cd job/project2
  vim flume-avro-console1.conf
  ```

  ​	**添加以下内容：**

  ```python
  # Name the components on this agent
  flume2.sources = r2
  flume2.sinks = k2
  flume2.channels = c2
  
  # Describe/configure the source
  flume2.sources.r2.type = avro
  flume2.sources.r2.bind = node01
  flume2.sources.r2.port = 4210
  
  # Describe/configure the sink
  flume2.sinks.k2.type = logger
  
  # Use a channel which buffers events in memory
  flume2.channels.c2.type = memory
  flume2.channels.c2.capacity = 1000
  flume2.channels.c2.transactionCapacity = 100
  
  # Bind the source and sink to rhe channel
  flume2.sources.r2.channels = c2
  flume2.sinks.k2.channel = c2
  ```

  * **创建Flume Agent配置文件flume-avro-console2.conf**

  ```bash
  cd job/project2
  vim flume-avro-console2.conf
  ```

  ​	**添加以下内容：**

  ```python
  # Name the components on this agent
  flume3.sources = r3
  flume3.sinks = k3
  flume3.channels = c3
  
  # Describe/configure the source
  flume3.sources.r3.type = avro
  flume3.sources.r3.bind = node01
  flume3.sources.r3.port = 4211
  
  # Describe/configure the sink
  flume3.sinks.k3.type = logger
  
  # Use a channel which buffers events in memory
  flume3.channels.c3.type = memory
  flume3.channels.c3.capacity = 1000
  flume3.channels.c3.transactionCapacity = 100
  
  # Bind the source and sink to rhe channel
  flume3.sources.r3.channels = c3
  flume3.sinks.k3.channel = c3
  ```

  * **启动**

  ```bash
  #先启动flume2或者flume3，最后再启动flume1
  bin/flume-ng agent -c conf/ -n flume2 -f job/project2/flume-avro-console1.conf -Dflume.root.logger=INFO,console
  bin/flume-ng agent -c conf/ -n flume3 -f job/project2/flume-avro-console2.conf -Dflume.root.logger=INFO,console
  bin/flume-ng agent -c conf/ -n flume1 -f job/project2/flume-netcat-avro.conf
  ```

### 多数据源汇总案例

* **案例需求**

  > **node01上的Flume-1监控文件/etc/httpd/logs/access_log**
  >
  > **node02上的Flume-2监控某一个端口的数据流**
  >
  > **Flume-1和Flume-2将数据发送给node03上的Flume-3**
  >
  > **Flume-3最终数据打印到控制台**

* **实现步骤**

  * **在node01上创建Flume Agent配置文件flume-exec-avro.conf**

  ```bash
  mkdir /usr/local/src/flume/job/project3
  cd /usr/local/src/flume/job/project3
  vim flume-exec-avro.conf
  ```

  ​	**添加以下内容：**

  ```python
  # Name the components on this agent
  flume1.sources = r1
  flume1.sinks = k1
  flume1.channels = c1
  
  # Describe/configure the source
  flume1.sources.r1.type = exec
  flume1.sources.r1.command = tail -F /etc/httpd/logs/access_log
  flume1.sources.r1.shell = /bin/bash -c
  
  # Describe/configure the sink
  flume1.sinks.k1.type = avro
  flume1.sinks.k1.hostname = node03
  flume1.sinks.k1.port = 4210
  
  # Use a channel which buffers events in memory
  flume1.channels.c1.type = memory
  flume1.channels.c1.capacity = 1000
  flume1.channels.c1.transactionCapacity = 100
  
  # Bind the source and sink to rhe channel
  flume1.sources.r1.channels = c1
  flume1.sinks.k1.channel = c1
  ```

  * **在node02上创建Flume Agent配置文件flume-netcat-avro.conf**

  ```bash
  mkdir /usr/local/src/flume/job/project3
  cd /usr/local/src/flume/job/project3
  vim flume-netcat-avro.conf
  ```

  ​	**添加以下内容：**

  ```python
  # Name the components on this agent
  flume1.sources = r1
  flume1.sinks = k1
  flume1.channels = c1
  
  # Describe/configure the source
  flume1.sources.r1.type = netcat
  flume1.sources.r1.bind = 0.0.0.0
  flume1.sources.r1.port = 5656
  
  # Describe/configure the sink
  flume1.sinks.k1.type = avro
  flume1.sinks.k1.hostname = node03
  flume1.sinks.k1.port = 4210
  
  # Use a channel which buffers events in memory
  flume1.channels.c1.type = memory
  flume1.channels.c1.capacity = 1000
  flume1.channels.c1.transactionCapacity = 100
  
  # Bind the source and sink to rhe channel
  flume1.sources.r1.channels = c1
  flume1.sinks.k1.channel = c1
  ```

  * **在node03上创建Flume Agent配置文件flume-avro-console.conf**

  ```
  mkdir /usr/local/src/flume/job/project3
  cd /usr/local/src/flume/job/project3
  vim flume-avro-console.conf
  ```

  ​	**添加以下内容：**

  ```python
  # Name the components on this agent
  flume1.sources = r1
  flume1.sinks = k1
  flume1.channels = c1
  
  # Describe/configure the source
  flume1.sources.r1.type = avro
  flume1.sources.r1.bind = node03
  flume1.sources.r1.port = 4210
  
  # Describe/configure the sink
  flume1.sinks.k1.type = logger
  
  # Use a channel which buffers events in memory
  flume1.channels.c1.type = memory
  flume1.channels.c1.capacity = 1000
  flume1.channels.c1.transactionCapacity = 100
  
  # Bind the source and sink to rhe channel
  flume1.sources.r1.channels = c1
  flume1.sinks.k1.channel = c1
  ```

  * **启动**

  ```bash
  #先启动node03的flume
  bin/flume-ng agent -c conf/ -n flume1 -f job/project3/flume-avro-console.conf -Dflume.root.logger=INFO,console
  #再启动node01的flume
  bin/flume-ng agent -c conf/ -n flume1 -f job/project3/flume-exec-avro.conf
  #最后启动node02的flume
  bin/flume-ng agent -c conf/ -n flume1 -f job/project3/flume-netcat-avro.conf
  ```

## 四.Flume监控

### Ganglia的安装与部署

* **安装httpd服务和php**

```bash
yum -y install httpd php
```

* **安装其他依赖**

```bash
yum -y install rrdtool perl-rrdtool rrdtool-devel
```

* **安装ganglia**

```bash
#更新源
yum -y install epel-release  
#安装Ganglia的组件（gmetad、web、gmod）
yum -y install ganglia-gmetad ganglia-web ganglia-gmond  
```

![ganglia](https://ae01.alicdn.com/kf/H5db20c7dec854b62bef73f6ff121b82fX.png)

* **修改配置文件/etc/httpd/conf.d/ganglia.conf**

```python
vim /etc/httpd/conf.d/ganglia.conf
#配置ganglia前端访问的权限
<Location /ganglia>
  Order deny,allow
  Allow from all
  # Require ip 10.1.2.3
  # Require host example.org
</Location>
```

* **修改配置文件 /etc/ganglia/gmetad.conf**

```python
vim /etc/ganglia/gmetad.conf
#修改为：
data_source "node01" 192.168.1.200
```

* **修改配置文件 /etc/ganglia/gmond.conf**

```python
vim  /etc/ganglia/gmond.conf
#修改为(和gmetad文件一一对应)：
cluster {
  name = "node01"	
  owner = "unspecified"
  latlong = "unspecified"
  url = "unspecified"
}
udp_send_channel {
  #bind_hostname = yes # Highly recommended, soon to be default.
                       # This option tells gmond to use a source address
                       # that resolves to the machine's hostname.  Without
                       # this, the metrics may appear to come from any
                       # interface and the DNS names associated with
                       # those IPs will be used to create the RRDs.
#  mcast_join = 239.2.11.71
  host = 192.168.1.200
  port = 8649
  ttl = 1
}
udp_recv_channel {
 # mcast_join = 239.2.11.71
  port = 8649
  bind = 192.168.1.200
  retry_bind = true
  # Size of the UDP buffer. If you are handling lots of metrics you really
  # should bump it up to e.g. 10MB or even higher.
  # buffer = 10485760
}
```

* **关闭selinux服务**

```python
#临时关闭
setenforce 0
```

* **启动Ganglia**

```python
service httpd start
service gmetad start
service gmond start
```

* **打开网页浏览ganglia页面**

  http://192.168.1.200/ganglia

  ***提示：如果以上操作出现权限不足，请修改/var/lib/ganglia目录的权限：***

```
chmod 777 /var/lib/ganglia
```

### 操作Flume测试监控

* **修改flume配置文件中的flume-env.sh**

```python
export JAVA_OPTS="-Dflume.monitoring.type=ganglia
-Dflume.monitoring.hosts=192.168.1.200:8649
-Xms100m
-Xmx200m"
```

* **启动Flume任务**

