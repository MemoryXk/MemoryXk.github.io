# Hadoop相关

## Hadoop介绍

> **Hadoop生态镜像网站：http://archive.apache.org/dist** 

 ### Hadoop

* **HDFS  海量储存**

* **MapReduce  海量计算**

### YARN

* **ResouceManager   管理和调度**
* **NodeManager    执行任务**
* **ApplicationMaster  向RM申请任务**

### HDFS

* **NameNode   管理和分配**
* **SecondaryNameNode   辅助NameNode（不是副本）**
* **DataNode  用来存储管理**

### MapReduce

* **Map  拆分**
* **Reduce  合成**




## Hadoop搭建

### <span id="djd">一.单机伪分布搭建</span>

* **配置JDK环境**

```bash
方法一RPM安装：
rpm -ivh --relocate /usr/java=/usr/local/jdk1.8 jdk-8u101-linux-x64.rpm 
#--relocate指定位置，等号左边路径表示你要安装的路径以及文件名，等号右边指定的是安装包路径
rpm -ivh jdk1.8 jdk-8u101-linux-x64.rpm 
#直接进入要安装的路径，并且把rpm包移动到安装路径上一级，mv jdk1.8 /usr/local/src/java

方法二安装Tar解压：
tar -zxvf jdk-8u231-linux-x64.tar.gz -C /usr/local/src #解压到/usr/local/src目录中
mv /usr/local/src/jdk-8u231-linux-x64 /usr/local/src/jdk
```

* **<span id="ssh">SSH免密钥</span>**


```shell
方法一：
ssh-keygen -t rsa -P '' -f 〜/ .ssh/id_rsa       
创建id_rsa公钥和私钥
cat ~/.ssh/id_rsa.public >> ~/.ssh/authorized_keys   
把id_rsa公钥放到authorized_keys认证文件里面

方法二：
ssh-keygen
ssh-copy-id 192.168.1.120 
```

* **配置Hadoop环境**
* **Hadoop清华镜像网: http://mirror.bit.edu.cn/apache/hadoop/common/**


```python
tar -zxvf  hadoop-2.7.7.tar.gz -C /usr/local/src (解压到/usr/local/src目录中)
mv /usr/local/src/hadoop-2.7.7 /usr/local/src/hadoop
#配置系统环境变量，若需要配置用户环境变量，则修改 ./bashrc
vim /etc/profile 添加：
export PATH=$PATH:/usr/local/src/hadoop/bin:/usr/local/src/hadoop/sbin:/usr/local/src/jdk/bin
source /etc/profile
#（或者.bashrc,按情况来选择） 让环境变量生效 
```

* **配置Hadoop文件**

  * **<span id="hadoop-env">编辑hadoop-env.sh   (修改配置文件中环境变量)</span>**

```python
#在文件中找到JAVA_HOME 并且指定jdk绝对路径 若没有JAVA_HOME ,则添加export 
JAVA_HOME=/usr/local/src/jdk
#(在文件中配置java环境，这个文件是修改在Hadoop管理脚本远程连接其他服务器中，让java命令有效) 
#ps：Hadoop管理脚本远程连接其他服务器时是找不到另外一台的java环境，只能在hadoop脚本中添加java环境，就可以使用java命令了
```

  * **编辑core-site.xml（2.6.0版本，每个版本具体怎么配看官方文档）https://hadoop.apache.org/docs/r2.6.0 （r后面接版本号)**

```python
#添加（configuration文件中本来就有，只要复制标签内的就行）：
<configuration> 
	<property> 
		<name> fs.defaultFS </name>      
		#默认为namenode,配置namenode
		<value> hdfs://node01:9000 </value>   
		#如果是完全分布式的话，这里必须设置成主节点的主机名
	</property> 
	<property>
    	<name>hadoop.tmp.dir</name>    #配置持久化目录
 		<value>/var/hadoopData/local</value>
		#hadoopData是空的，随后格式化会自动生成   
		#（local名字根据搭建什么分布式类型来取，单分布式和完全分布式）
	</property>
</configuration>
```

  * **编辑hdfs-site.xml**

```python
#添加（configuration文件中本来就有，只要复制标签内的就行）：
<configuration> 
	<property> 
		<name> dfs.replication </name>    
 		<value> 1 </value>   #副本数
	</property>
	<!--#配置secondary地址（看情况，看你这台主机上跑什么服务）
	<property>  
		<name>dfs.namenode.secondary.http-address</name>   
		<value>node01:50090</value>
	</property>
	-->
	<property>  #只在主节点配置，其他节点可配可不配
 		<name>dfs.http.address</name>    #避免用浏览器访问时发生错误
		<value>node01:50070</value>    #node01为主机名        
	</property>
</configuration>
```

  * **编辑slaves  (3.0版本之后改名为workers,设置从服务器的主机名)**

```python
#因为这里是单机伪分布，所以只需要写上本机的主机名就行了，如果是完全分布式，则添加从服务器
localhost
```

  * **启动**

```python
hdfs namenode -format    #格式化namenode
start-dfs.sh        #启动hdfs
```

* **常见错误**


```python
ERROR: Attempting to operate on hdfs datanode as root
ERROR: but there is no HDFS_DATANODE_USER defined. Aborting operation.
说明没有为各个角色（进程）设置用户，设成root
编辑 /sbin/start-dfs.sh 和 stop-dfs.sh 在最上面添加：
HDFS_DATANODE_USER=root
HDFS_NAMENODE_USER=root
HDFS_SECONDARYNAMENODE_USER=root

若出现浏览器访问 主机名:50070 不成功 则是没有配置dfs.http.address
在hdfs-site.xml添加:
<property>
  <name>dfs.http.address</name>
  <value>node01:50070</value>    node01为主机名
</property>
```

### 二.完全分布式搭建

> **假设有3台服务器，1台主，2台从：node01(主192.168.1.108) node02(从192.168.1.110) node03(从192.168.1.112)**

* **[配置node01 Hadoop和JAVA环境和文件（具体看单节点分布式）](#djd)**
* **<span id="hosts">修改/etc/hosts文件</span>**

```python
192.168.1.108 node01
192.168.1.110 node02
192.168.1.112 node03
```

* **修改slaves**

```python
node02
node03
```

* **[配置免密钥](#ssh)**
* **复制文件到各个节点**

```bash
scp -r /usr/local/hadoop node02: /usr/local/hadoop    
scp -r /usr/src/jdk node02:  /usr/src/jdk 
scp -r /etc/profile node02:  /etc/profile 
	
scp -r /usr/local/hadoop node03: /usr/local/hadoop    
scp -r  /usr/src/jdk node03:  /usr/src/jdk 
scp -r /usr/src/profile node03:  /etc/profile
```

## Hadoop HA搭建

### <span id="Zookeeper">Zookeeper搭建</span>

* **解压Zookeeper （tar包）并且配置环境变量**

```python
tar -zxvf zookeeper-3.4.5.tar.gz -C /usr/local/src
cd /usr/local/src
mv zookeeper-3.4.5 zookeeper

vim /etc/profile
export ZOOKEEPER_HOME=/usr/local/src/zookeeper
#追加在PATH后面
export PATH=$PATH:$ZOOKEEPER_HOME/bin
```

* **进入到Zookeeper目录下的conf目录 /usr/local/src/zookeeper/conf**

```python
cd /usr/local/src/zookeeper/conf
#复制配置文件
cp zoo_sample.cfg zoo.cfg

vim zoo.cfg
dataDir=/usr/local/src/zookeeper/data    #日志的存放目录，手动创建
#看你有几台zookeeper服务器
server.0=node01:2888:3888
server.1=node02:2888:3888
server.2=node03:2888:3888
```

* **创建日志的存放目录和myid文件**

```python
mkdir /usr/local/src/zookeeper/data
touch /usr/local/src/zookeeper/data/myid
echo 0 > /usr/local/src/zookeeper/data/myid
#向myid文件里面写入0，这里要看配置文件对应的服务id
```

* **[配置hosts文件](#hosts)**

* **复制文件给其他主机**

```shell
scp -r /usr/local/src/ node02:/usr/local/src
scp -r /usr/local/src/ node03:/usr/local/src
#传过去之后需要修改myid文件里面的id，而且需要把环境变量文件一起传过去
```

* **启动**

```shell
#三台都启动zookeeper
zkServer.sh start
```

### 配置HA、NameNode、JournalNode等服务

* **[在单节点配置之上进行修改](#djd)**
* [**编辑hadoop-env.sh**](#hadoop-env)


* **修改hdfs-site.xml文件  （vim hdfs-site.xml）**


```python
cd /usr/local/src/hadoop/etc/hadoop
vim hdfs-site.xml
<!-- 1、配置逻辑到物理映射 -->
<property>
        <name>dfs.replication</name>
        <value>1</value>
</property>
<!--配置集群名字-->
<property>
        <name>dfs.nameservices</name> 
        <value>mycluster</value>
</property>
<!--置集群里2个NameNode的名字-->
<property>
        <name>dfs.ha.namenodes.mycluster</name> 
        <value>namenode01,namenode02</value>
</property>
<!--配置2个NameNode对应的主机以及端口8020-->
<property>
        <name>dfs.namenode.rpc-address.mycluster.namenode01</name>
        <value>node01:8020</value>
</property>
<property>
        <name>dfs.namenode.rpc-address.mycluster.namenode02</name>
        <value>node02:8020</value>
</property>
<!--配置2个NameNode 管理地址50070端口-->
<property>
        <name>dfs.namenode.http-address.mycluster.namenode01</name>
        <value>node01:50070</value>
</property> 
<property>
        <name>dfs.namenode.http-address.mycluster.namenode02</name>
        <value>node02:50070</value>
</property>

<!-- 2、配置JournalNode在哪些主机以及持久化目录设置 -->
<property>
        <name>dfs.namenode.shared.edits.dir</name>
        <value>qjournal://node01:8485;node02:8485/mycluster</value>
</property>
<!--配置journalnode的持久化目录-->
<property>
        <name>dfs.journalnode.edits.dir</name>
        <value>/var/hadoopData/ha/jn</value>
</property>

<!-- 3、故障切换的实现与代理 -->
<property>
        <name>dfs.client.failover.proxy.provider.mycluster</name>
        <value>org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider</value>
</property>
<property>
        <name>dfs.ha.fencing.methods</name>
        <value>
        sshfence
        shell(/bin/true)
        </value>
</property>
<property>
        <name>dfs.ha.fencing.ssh.private-key-files</name>
        <value>/root/.ssh/id_rsa</value>
</property>

<!-- 4、自动开启ZKFC -->
<property>
        <name>dfs.ha.automatic-failover.enabled</name>
        <value>true</value>
</property> 
```

* **修改core-site.xml文件  （vim core-site.xml）**

```python
<!--配置主namenode集群名字Mycluster以及zookeeper主机设置-->
<property>
	<name>fs.defaultFS</name>
	<value>hdfs://mycluster</value>   
</property>
<property>
	<name>hadoop.tmp.dir</name>
 	<value>/var/hadoopData/ha</value>
</property>
<property>
	<name>ha.zookeeper.quorum</name>
	<value>node02:2181,node03:2181</value>
</property>
```

* **编辑slaves（workers） 配置从节点  (vim slaves)**

```python
node02
node03
```

* **两个namenode(node01,node02)之间设置ssh免密钥**


```python
node01:
cd ~/.ssh/
ssh-keygen
ssh-copy-id node02

node02:
cd ~/.ssh/
ssh-keygen
ssh-copy-id node01
```

### 部署

* **启动所有主机的JournalNode（node01,node02）**


```shell
hadoop-daemon.sh start journalnode
```

* **格式化并且启动第一台Namenode 【node01】（初始化，在这之前必须启动JournalNode）**


```shell
hdfs namenode -format
hadoop-daemon.sh start namenode
```

* **第二台Namenode同步第一台Namenode（要在第一台格式完启动）**



```shell
hdfs namenode -bootstrapStandby
```

* **格式化ZKFC并且启动所有服务（第一台NameNode上【node01】）**



```shell
hdfs zkfc -formatZK
start-dfs.sh
```

* **查看 namenode 状态**



```shell
hdfs haadmin -getServiceState nn1
hdfs haadmin -getServiceState nn2
```

* **查看集群状态**



> **如果 Live datanode 不为 0，则说明集群启动成功**

```shell
hdfs dfsadmin -report
```

### Hadoop使用

```shell
mkdir input

echo "hello world">./input/test1.txt
echo "hello hadoop">./input/test2.txt

hdfs dfs -mkdir /in

hdfs dfs -put input/ /in

hadoop jar /usr/local/src/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.6.0.jar wordcount /in/input /out

hdfs dfs -cat /out/*
```

## Yarn搭建（基于Hadoop）

> **因Yarn是在Hadoop里面的，所有先要配置Hadoop环境**

### Yarn单节点配置（基本配置，必配）

* **编辑mapred-site.xml  （vim /usr/local/src/hadoop/etc/hadoop/mapred-site.xml）**


```python
<property>
	<name>mapreduce.framework.name</name>
	<value>yarn</value>
</property>
<!-- hadoop3.0以后Classpath一定要配，mapreduce才能用。hadoop classpath命令是查看	classpath，复制到以下对应位置 -->
<property>
	<name>mapreduce.application.classpath</name>
	<value>$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*:$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib/*</value>
</property>
```

* **编辑yarn-site.xml文件 （vim yarn-site.xml）**


```python
<property>
	<name>yarn.nodemanager.aux-services</name>
	<value>mapreduce_shuffle</value>
</property>
```

### Yarn HA配置

* **在单节点的基础上配置**

* **修改yarn-site.xml**


```python
<property>
  <name>yarn.resourcemanager.ha.enabled</name>
  <value>true</value>
</property>
<property>
  <name>hadoop.zk.address</name>
  <value>zk1:2181,zk2:2181,zk3:2181</value>
</property>
<property>
  <name>yarn.resourcemanager.cluster-id</name>
  <value>cluster1</value>
</property>
<property>
  <name>yarn.resourcemanager.ha.rm-ids</name>
  <value>rm1,rm2</value>
</property>
<property>
  <name>yarn.resourcemanager.hostname.rm1</name>
  <value>master1</value>
</property>
<property>
  <name>yarn.resourcemanager.hostname.rm2</name>
  <value>master2</value>
</property>
<property>
  <name>yarn.resourcemanager.webapp.address.rm1</name>
  <value>master1:8088</value>
</property>
<property>
  <name>yarn.resourcemanager.webapp.address.rm2</name>
  <value>master2:8088</value>
</property>
```

* **启动 （第一台NameNode）**


```shell
start-yarn.sh
```

### Yarn计算测试

* **计算hdfs中text.txt文件所有的单词数量（wordcount功能） /data/wc/output 输出目录**



```shell
hadoop jar /usr/local/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.2.1.jar wordcount /user/root/text.txt /data/wc/output
```

