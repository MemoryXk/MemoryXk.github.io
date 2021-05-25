# Hive相关

## 一、hive相关资料

<http://blog.csdn.net/u013310025/article/details/70306421> <https://www.cnblogs.com/guanhao/p/5641675.html> <http://blog.csdn.net/wisgood/article/details/40560799> <http://blog.csdn.net/seven_zhao/article/details/46520229>

## 二、单用户配置

* **安装Mysql**

```powershell
yum -y install mariadb-server
systemctl start mariadb
mysqladmin -u root password root
mysql -u root -p  root
```

* **解压Hive包到具体位置**

```powershell
tar -zxvf  apache-hive-1.1.0-bin.tar.gz  -C  /usr/local/src/hive
cd /usr/local/src/hive
```

> **从MySQL 8开始,您不再可以(隐式)使用GRANT命令创建用户.请改用CREATE USER,然后使用GRANT声明：**

```bash
mysql> CREATE USER 'root'@'%' IDENTIFIED BY 'root';
mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
```

* 配置环境变量  (vim /etc/profile)**

```python
export HIVE_HOME=/usr/local/src/hive
在export PATH=$PATH:后面添加$HIVE_HOME/bin
source /etc/profile
```

* **复制配置文件**

```powershell
cp hive-env.sh.template hive-env.sh
cp hive-default.xml.template hive-site.xml

# 创建文件夹，hive-site.xml 中配置需要
mkdir -p /usr/local/src/hive/tmp
```

* **编辑hive-env.sh文件 **

```bash
export JAVA_HOME=/usr/local/src/jdk
export HADOOP_HOME=/usr/local/src/hadoop
export HIVE_HOME=/usr/local/src/hive
export HIVE_CONF_DIR=/usr/local/src/hive/conf
```

* **编辑hive-site.xml文件**

> **数据库密码：root**
>
> **没有的 property 则添加，有的则根据需求修改**
>
> **或者把原有的全部删除：vim中 .,$-1d删除，添加以下内容**

```powershell
<configuration>
    <property>
        <name>system:java.io.tmpdir</name>
        <value>/usr/local/src/hive/tmp</value>
    </property>
     <!--mysql信息-->
    <property>
        <name>javax.jdo.option.ConnectionURL</name>
        <value>jdbc:mysql://node01:3306/hive?createDatabaseIfNotExist=true</value>
    </property>
     <!--mysql驱动-->
    <property>
        <name>javax.jdo.option.ConnectionDriverName</name>
        <value>com.mysql.jdbc.Driver</value>
    </property>
    <!--数据库账号-->
    <property>
        <name>javax.jdo.option.ConnectionUserName</name>
        <value>root</value>
    </property>
    <!--数据库密码-->
    <property>
        <name>javax.jdo.option.ConnectionPassword</name>
        <value>root</value>
    </property>
</configuration>
```

```bash
sed -i "s#\${system:java.io.tmpdir}/\${system:user.name}#/usr/local/src/hive/iotmp#g" /usr/local/src/hive/conf/hive-site.xml
```

* **复制mysql驱动包到指定文件路径**

```bash
cp mysql-connector-java-5.1.46-bin.jar /usr/local/src/hive/lib/
```

* ##### 在 hdfs 中创建下面的目录 ，并赋予所有权限

```bash
hdfs dfs -mkdir -p /usr/local/src/hive/warehouse
hdfs dfs -mkdir -p /usr/local/src/hive/tmp
hdfs dfs -mkdir -p /usr/local/src/hive/log
hdfs dfs -chmod -R 777 /usr/local/src/hive/warehouse
hdfs dfs -chmod -R 777 /usr/local/src/hive/tmp
hdfs dfs -chmod -R 777 /usr/local/src/hive/log
```

* ##### 初始化 hive

```bash
schematool -dbType mysql -initSchema

#出现以下内容就证明初始化成功
Metastore connection URL:     jdbc:mysql://hp:3306/hive?createDatabaseIfNotExist=true
Metastore Connection Driver :     com.mysql.jdbc.Driver
Metastore connection User:     root
Starting metastore schema initialization to 1.1.0
Initialization script hive-schema-1.1.0.mysql.sql
Initialization script completed
schemaTool completed
```

* ##### 安装hive到此结束，进入hive命令行

```
hive
```

* ##### 常见问题

  [常见问题](http://www.itkeyword.com/doc/985187200597055x509)

```python
com.google.common.base.Preconditions.checkArgument 
原因：这是因为hive内依赖的guava.jar和	hadoop内的版本不一致造成的。
解决方法：查看/share/hadoop/common/lib内guava.jar版本是否和hive目录下lib内guava.jar一致，若	hive目录下lib内版本不对，则将Hadoop中的复制到hive中
```

> **<span id="jar">因为版本原因，需要重新拷贝一个 jar 包</span>**

```bash
rm -rf /usr/local/src/hadoop/share/hadoop/yarn/lib/jline-0.9.94.jar
cp /usr/local/src/hive/lib/jline-2.12.jar /usr/local/src/hadoop/share/hadoop/yarn/lib/
```

> **初始化后如果想要再重启服务**

```shell
hive --service metastore &
hive --service hiveserver2 &
```

> **这个在1.4.7中需要配置，否则在执行数据导入到hive时会报错**
>
> ```
> Could not load org.apache.hadoop.hive.conf.HiveConf. Make sure HIVE_CONF_DIR is set correctly.
> ```

```bash
cp /usr/local/src/hive/lib/hive-exec-1.1.0.jar /usr/local/src/sqoop/lib/
```

## 三、多用户配置

* **把单用户配置的hive文件夹复制给node04(hive客户端)**

```bash
scp -r /usr/local/hive node04:/usr/local/src
```

* **启动服务端**

```shell
hive --service metastore
#之后会发现阻塞因为客户端正在监听它
```

* **查看监听端口，默认为9083,这个在客户端要配置**

```python
ss -nal
```

* **修改客户端hadoop04中的hive-site.xml文件（把原有的删除）**

```powershell
<!--Hive数据上传到hdfs中的存储目录-->
<property>			
        <name>hive.metastore.warehouse.dir</name>     
        <value>/user/hive_remote/warehouse</value>  
</property>  
<!--是否为本地配置（也就是Hive和mysql在同一台服务器上）-->
<property>			
        <name>hive.metastore.local</name>  
        <value>false</value>  
</property>
<!--配置服务的监听端口9083-->
<property>                      
        <name>hive.metastore.uris</name>  
        <value>thrift://node02:9083</value>  
</property>
```

* **[因为版本原因,需要复制jar包](#jar)**
* **先在hive启动服务端（此步骤已在上面已做完），然后在hive客户端输入hive访问**

```
hive
```

* **常见问题**

```python
Could not create ServerSocket on address 0.0.0.0/0.0.0.0:9083.
说明hive服务器端已经启动，需要杀死后，重新启动，或者重启hadoop集群

com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: Specified key was too long; max key length is 767 bytes
解决：需要进入mysql目录下删除创建的hive库
```

## 四、使用

```shell
hive
进入hive数据库
```

* ### 查看数据库，使用数据库

```mysql
show databases;
use default;
```

* ### 查看表

```mysql
show tables;
```

* ### 创建hive表

> **";" ASCI码为 "\073"**
>
> **ROW FORMAT DELIMITED 每条数据按行拆分**
>
> **FIELDS TERMINATED BY '\073' 每行数据字段按 ";" 拆分**
>
> **STORED AS TEXTFILE 保存为文本文件**

```mysql
create table film
(name string,
startTime date,
endTime date,
company string,
director string,
actor string,
type string,
price float,
score float)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\073'
STORED AS TEXTFILE;
```

* ### 将本地文本导入 hive

> **先在 shell 中生成文本文件**

```shell
echo "电影名称;上映时间;闭映时间;出品公司;导演;主角;影片类型;票房/万;评分;
《熊出没之夺宝熊兵》;2014.1.17;2014.2.23;深圳华强数字动漫有限公司;丁亮;熊大，熊二，;
《菜鸟》;2015.3.27;2015.4.12;麒麟影业公司;项华祥;柯有伦，崔允素，张艾青，刘亚鹏，张星宇;爱情/动作/喜剧;192.0 ;4.5 ;
《栀子花开》;2015.7.10;2015.8.23;世纪百年影业，文投基金，华谊兄弟，千和影业，剧角映画，合一影业等;何炅;李易峰，张慧雯，蒋劲夫，张予曦，魏大勋，李心艾，杜天皓，宋轶，王佑硕，柴格，张云龙;青春，校园，爱情;37900.8 ;4.0 ;
《我是大明星》;2015.12.20;2016.1.31;北京中艺博悦传媒;张艺飞;高天，刘波，谭皓，龙梅子;爱情 励志 喜剧;9.8 ;2.5 ;
《天将雄师》;2015.2.19;2015.4.6;耀莱文化，华谊兄弟，上海电影集团;李仁港;成龙，约翰·库萨克，阿德里安·布劳迪，崔始源 ，林鹏，王若心，筷子兄弟，西蒙子，冯绍峰，朱佳煜;动作，古装，剧情，历史;74430.2 ;5.9 ;" > /root/film.csv

#把数据放到root目录下的film.csv
```

> **写到film表中**

```msyql
load data local inpath '/root/film.csv' overwrite into table film;
```

* ### 将hdfs中数据导入hive

```mysql
hdfs dfs -put /root/film.csv /home
hdfs dfs -cat /home/film.csv
load data inpath '/home/film.csv'into table film;
```

* ### 从 hive 导出到本地文件系统

```mysql
insert overwrite local directory '/root/hl' select * from film;
```

> 或直接在shell中执行

```mysql
hive -e "select * from film" >> film1.csv
```

> 或者是以下命令， 其中文件sql.q写入你想要执行的查询语句

```mysql
hive -f sql.q >> film1.csv
```

* ### 从 hive 导出到 hdfs 中

> 注意，和导出文件到本地文件系统的HQL少一个local，数据的存放路径就不一样了。

```mysql
insert overwrite directory '/root/hl' select * from film;
```

* ### hive 中使用dfs命令

```mysql
hive> dfs -ls /user;
```

* ### 查看表结构信息

```mysql
desc formatted film;
desc film;
```

* ### 查看表信息

```mysql
select * from film;

# 查看前十项
select * from film limit 10;

select count(price) from film;
# distinct 去重
select count(distinct  price) from film;
select sum(price) from film;
select avg(price) from film;
```

* ### 将提取到的数据保存到临时表中

```mysql
insert overwrite table movies

本地加载  load data local inpath '/Users/tifa/Desktop/1.txt' into table test;

从hdfs上加载数据  load data inpath '/user/hadoop/1.txt' into table test_external;  
 抹掉之前的数据重写  load data inpath '/user/hadoop/1.txt' overwrite into table test_external;
```

```mysql
CREATE TABLE movies(
    name string,
    data string,
    record int
) COMMENT '2014全年上映电影的数据记录' FIELDS TERMINATED BY '\t' STORED AS TEXTFILE;

load data local inpath 'dat0204.log' into table movies;
```

* ### 删除表

```mysql
DROP TABLE if exists film;
```

