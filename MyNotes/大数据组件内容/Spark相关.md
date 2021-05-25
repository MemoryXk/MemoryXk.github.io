# Spark相关

## 一.搭建

* ### **解压和配置环境变量**



```python
wget http://archive.apache.org/dist/spark/spark-2.0.2/spark-2.0.2-bin-hadoop2.6.tgz
tar -zxvf spark-2.0.2-bin-hadoop2.6.tgz -C /usr/local/src
cd /usr/local/src
mv spark-2.0.2-bin-hadoop2.6.tgz spark
vim /etc/profile 添加SPARK_HOME
```

* ### **配置**



```python
cd /usr/local/src/spark/conf/

#复制以及修改
cp spark-env.sh.template spark-env.sh
vim spark-env.sh   
添加以下内容：
export JAVA_HOME=/usr/local/src/jdk
export SPARK_MASTER_HOST=node01   //IP或域名都可以
#配置Zookeeper
export SPARK_DAEMON_JAVA_OPTS="-Dspark.deploy.recoveryMode=ZOOKEEPER -Dspark.deploy.zookeeper.url=node01:2181,node02:2181,node03:2181 -Dspark.deploy.zookeeper.dir=/spark"

cp slaves.template slaves
vim slaves  
配置从节点的服务 添加以下内容：
node02
node03

进入Spark启动文件,修改启动和停止的脚本，因为名字和hadoop启动脚本文件冲突
cd /usr/local/src/spark/sbin    
mv ./start-all.sh ./start-spark-all.sh
mv ./stop-all.sh ./stop-spark-all.sh 
mv ./start-master.sh ./start-spark-master.sh 
mv ./stop-master.sh ./stop-spark-master.sh 
```

* ### **启动**



```bash
start-spark-all.sh

[root@node01 conf]# jps
11283 Worker    从节点服务
21695 Master	主节点服务
```

* ### **测试**

  * **用浏览器访问node01:8080**



![spark](https://ae01.alicdn.com/kf/H9cee9e7aa9164a11af6860037c5854a6X.png)

```python
若想用多个主节点，也就是开启HA服务
则在第二台备用节点启动Master服务
vim /usr/local/src/spark/conf/spark-env.sh  修改Master地址
start-spark-master.sh

若想要测试HA的可用性，则关闭或杀死Active的Master主节点服务，查看备用节点的8080端口是否变成Active
```



