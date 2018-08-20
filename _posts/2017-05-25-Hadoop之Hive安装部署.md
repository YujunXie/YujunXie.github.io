---
title: Hadoop之Hive安装部署
key: 20170525
tags: Hadoop
---

采用的运行模式为Hadoop单机模式+Mysql+Hive本地模式。

参考这篇文章的两个前提：

- 已安装部署好Hadoop.(`hadoop version`)
- 已安装部署好Mysql.(`mysql`/`mysql -u root -p`)

接下来就是Hive的安装和部署。

<!--more-->


 - **首先**

下载Hive解压至一目录下(如`/opt/hadoop/apache-hive-2.1.1-bin`)
`tar -xvzf apache-hive-2.1.1-bin.tar.gz`

配置环境变量：

    vi /etc/profile
    export HIVE_HOME=/home/hadoop/cloud/apache-hive-2.1.1-bin  
    export PATH=$HIVE_HOME/bin:$HIVE_HOME/conf:$PATH
    source /etc/profile

 - **其次**

修改配置文件`$HIVE_HOME/conf`下的`hive-site.xml`，`hive-env.sh`两个文件。

***hive_site.xml***

原目录下不存在，拷贝模版文件`cp hive-default.xml.template hive-site.xml`

`name: hive.metastore.warehouse.dir`
(该参数指定了 Hive 的数据存储目录，默认位置在 HDFS 上面的 `/user/hive/warehouse` 路径下。)

`value: /user/hive/warehouse`

`name: hive.exec.scratchdir`
(该参数指定了 Hive 的数据临时文件目录，默认位置为 HDFS 上面的 `/tmp/hive` 路径下。)

`value: /tmp/hive`

`name: javax.jdo.option.ConnectionURL`

`value: jdbc:mysql://192.168.169.134:3306/hive?createDatabaseIfNotExist=true`
（如果之后出现错误 FAILED: Error in metadata: javax.jdo.JDOFatalDataStoreException: Communications link failure，将192.168.169.134改为主机ip, 如localhost或127.0.0.1）

`name: javax.jdo.option.ConnectionDriverName`
（本地数据库使用mysql）

`value: com.mysql.jdbc.Driver`

`name: javax.jdo.option.ConnectionUserName`
（自定义连接本地mysql数据库账号）

`value: root`

`name: javax.jdo.option.ConnectionPassword`
(自定义连接本地mysql数据库密码)

`value: root`

`name: hive.metastore.schema.verification`

`value: false`

***hive-env.sh***

    # Set HADOOP_HOME to point to a specific hadoop install directory  
    HADOOP_HOME=/home/hadoop/cloud/hadoop-2.7.3  
      
    # Hive Configuration Directory can be controlled by:  
    export HIVE_CONF_DIR=/home/hadoop/cloud/apache-hive-2.1.1-bin/conf  
      
    # Folder containing extra ibraries required for hive compilation/execution can be controlled by:  
    export HIVE_AUX_JARS_PATH=/home/hadoop/cloud/apache-hive-2.1.1-bin/lib

***创建HDFS目录***

即Hive的数据存储目录`/user/hive/warehouse`和数据临时存放目录`/tmp/hive`

先查看是否存在：`hadoop fs -ls /`

不存在，建立并赋予用户写权限

    $HADOOP_HOME/bin/hadoop fs -mkdir -p /user/hive/warehouse  
    $HADOOP_HOME/bin/hadoop fs -mkdir -p /tmp/hive/  
    hadoop fs -chmod 777 /user/hive/warehouse  
    hadoop fs -chmod 777 /tmp/hive  

查看是否建立成功：

`hadoop fs -ls /tmp`

`hadoop fs -ls /user/hive`

**修改io.tmpdir路径***

将`hive-site.xml`中的`${system:java.io.tmpdir}`全部替换成你新建的目录`$HIVE_HOME/iotmp`，并赋予用户写权限。

***添加mysql jar包***

下载如`mysql-connector-Java-5.1.26-bin.jar`，放进`$HIVE_HOME/lib`中。

***初始化运行***

`bin/schematool -initSchema -dbType mysql`

`bin/hive`

若出现问题，尝试删除`$HIVE_HOME/metastore_db/`，再重新初始化。（若解决不了请自行google）

 - **最后**

测试，启动mysql，发现多了一个hive数据库。

`sudo service mysqld start`

`mysql -u root -p`

提前建立一个文本文件student.txt，内容：

~~~
1001    zhangsan  
1002    lisi  
1003    wangwu  
1004    zhaoli 
~~~

(注意id与name间为tab键，否则插入数据为NULL)

`bin/hive`启动hive后，创建数据库`db_hive_test`:

`hive> create database db_hive_test;`

使用该数据库：`hive> use db_hive_test;`

创建测试表：`hive> create table student;`

加载文本数据到测试表：`hive> load data local inpath '/opt/hadoop/apache-hive-2.1.1-bin/student.txt' into table  db_hive_test.student;` 

查询表信息：`hive> select * from student;`

通过mysql查看生成的表：`mysql> use hive; 
mysql> select * from TBLS;`

----------

最后，我们来了解下Hive的三种运行模式：

> 

    1. 内嵌模式

    将元数据保存在本地内嵌的 Derby 数据库中，这是使用 Hive 最简单的方式。但是这种方式缺点也比较明显，因为一个内嵌的 Derby 数据库每次只能访问一个数据文件，这也就意味着它不支持多会话连接。

    2. 本地模式

    这种模式是将元数据保存在本地独立的数据库中（一般是 MySQL），这用就可以支持多会话和多用户连接了。

    3. 远程模式

    此模式应用于 Hive 客户端较多的情况。把 MySQL 数据库独立出来，将元数据保存在远端独立的 MySQL 服务中，避免了在每个客户端都安装 MySQL 服务从而造成冗余浪费的情况。
