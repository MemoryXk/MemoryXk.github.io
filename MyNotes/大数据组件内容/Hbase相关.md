# Hbase相关

## 一、介绍
* #### **与传统数据库的对比**

  ##### 传统数据库遇到的问题：

  * ##### 数据量很大的时候无法存储； 

  * ##### 没有很好的备份机制； 

  * ##### 数据达到一定数量开始缓慢，很大的话基本无法支撑；

  ##### HBASE优势：

  * ##### 线性扩展，随着数据量增多可以通过节点扩展进行支撑；

  * ##### 数据存储在hdfs上，备份机制健全；

  * ##### 通过zookeeper协调查找数据，访问速度快。

  ##### HBase中的表一般有这样的特点：

  * ##### 大：一个表可以有上亿行，上百万列

  * ##### 面向列:面向列(族)的存储和权限控制，列(族)独立检索。

  * ##### 稀疏:对于为空(null)的列，并不占用存储空间，因此，表可以设计的非常稀疏。

## 二、搭建

* **配置环境变量**


* ##### 进入配置文件目录

```shell
cd /usr/local/src/hbase/conf
```

* ##### 编辑hbase-env.sh文件（vim hbase-env.sh）

```python
export JAVA_HOME=/usr/local/src/jdk
```

* ##### 编辑hbase-site.xml 文件（vim hbase-site.xml）

```powershell
<configuration>
  <property>
    <name>hbase.rootdir</name>
    <value>hdfs://hp1:9000/hbase</value>
  </property>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>hp1,hp2,hp3</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.dataDir</name>
    <value>/usr/local/src/zookeeper/data/</value>
  </property>
</configuration>
```

* ##### 编辑regionservers文件 （vim regionservers）

```python
node02
node03
```

* ##### 将hbase所有配置拷贝到其他所有节点

```python
scp -r /usr/local/src/hbase/ node02:/usr/local/src/
scp -r /usr/local/src/hbase/ node03:/usr/local/src/
```

* ##### 启动 hbase

> 启动 HBase 时需要确保 hdfs 已经启动，使用命令 hdfs dfsadmin -report 查看以下 HDFS 集群是否正常
>
> 如果正常，在 master 节点上执行以下命令启动 HBase 集群:

```shell
start-hbase.sh
```

* ##### 测试

```shell
hbase shell
```

* ##### web 访问 hbase

```shell
http://node01:16010
```

## 三、使用

[hbase shell 常用命令](http://blog.csdn.net/scutshuxue/article/details/6988348)

```
hbase shell
```

* ### 创建表

```
create 'users','user_id','address','info'
```

> 说明:表users,有三个列族user_id,address,info

* ### 列出全部表

```
list
```

* ### 得到表的描述

```
describe 'users'
```

* ### 创建表

```
create 'users_tmp','user_id','address','info'
```

* ### 删除表

```
disable 'users_tmp'  
drop 'users_tmp'
```

* ### 添加记录

> put ‘表名’,’行键(标识)’,’列族:字段’,’数值’

```
put 'users','yani','info:age','24';  

put 'users','yani','info:birthday','1987-06-17';
```

* ### 获取一条记录

* ##### 取得一个id的所有数据

```
get 'users','yani'
```

* ##### 获取一个id，一个列族的所有数据

```
get 'users','yani','info'
```

* ##### 获取一个id，一个列族中一个列的所有数据

```
get 'users','yani','info:age'
```

* ### 更新记录

```
put 'users','yani','info:age' ,'29'  

get 'users','yani','info:age'
```

* ### 获取单元格数据的版本数据

```
get 'users','yani',{COLUMN=>'info:age',VERSIONS=>1}
```

* ### 获取单元格数据的某个版本数据

```
get 'users','yani',{COLUMN=>'info:age',TIMESTAMP=>1364874937056}
```

* ### 全表扫描

```
scan 'users'
```

* ### 删除yani值的'info:age'字段

```
delete 'users','yani','info:age'  

get 'users','yani'
```

* ### 删除整行

```
deleteall 'users','yani'
```

* ### 统计表的行数

```
count 'users'
```

* ### 清空表

```
truncate 'users'
```