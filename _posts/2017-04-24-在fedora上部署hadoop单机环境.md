---
title: 在fedora上部署hadoop单机环境
key: 20170424
tags: Linux Hadoop
---

环境：

 - `fedora22 64bit`
 - `jkd1.8.0_11`
 - `hadoop-2.2.0`

<!--more-->

 1. **配置java环境**

	将下载好的**jdk1.8.0_11.tar.gz**解压到`/usr/java`（自己定）（`tar -xzvf xxx.tar.gz`）
	
	设置环境变量，首先切换到root用户（`su root`）（退出root：`exit`）
	
	编辑**/etc/profile**文件（`vi /etc/profile`），添加：

	~~~
    export JAVA_HOME=/usr/java/jdk1.8.0_11
    export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
    export PATH=$PATH:$JAVA_HOME/bin
   ~~~

	保存退出（`:wq`）（*大坑：分隔符是冒号，不是分号*）
	
	生效（`source /etc/profile`）

 2. **配置Hadoop**

	同样将下载好的**hadoop-2.2.0.tar.gz**解压到`/opt/`（自己定）
	
	注意到解压时是普通用户的身份，才能进入hadoop配置目录（`hadoop-2.2.0/etc/hadoop/`）
	
	以普通用户身份编辑以下6个配置文件:
	
	**vi hadoop-env.sh**
	将`export JAVA_HOME={$JAVA_HOME}`改为：

    `export JAVA_HOME=/usr/java/jdk1.8.0_11`

	**vi slaves**（可选）将`localhost`改为`YARN001`

	**vi core-site.xml**
	
	~~~
      <property>
        <name>fs.default.name</name>
        <value>hdfs://YARN001:8020</value>
      </property>
	~~~
	
	端口号8020可改为其他可用端口。

	**vi hdfs-site.xml**
	
	~~~
      <property>
        <name>dfs.replication</name>
        <value>/1</value>
      </property>
      <property>
         <name>dfs.namenode.name.dir</name>  
         <value>file:/home/hadoop/dfs/name</value>
      </property>
      <property>
         <name>dfs.datanode.data.dir</name>  
         <value>file:/home/hadoop/dfs/data</value>
      </property>
   ~~~
   
	后面的两个为`namenode`和`datanode`的存放地址，添加后系统自动生成该目录。

	**vi mapred-site.xml.template**
	
	~~~
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
   ~~~
   
	这是模板文件，可以改为`mapred-site.xml`（也可以不改）

	**vi yarn-site.xml**
	
	~~~
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
    ~~~


----------

**第一次格式化namenode**
	
回到根目录`hadoop-2.2.0/`

`bin/hadoop namenode -format`

注意格式化的意思是清空之前所有namenode文件，所以以后就不要用了。

**启动hadoop**

推荐逐步启动：

    启动namenode与datanode
    sbin/hadoop-daemon.sh start namenode
    sbin/hadoop-daemon.sh start datanode
   
可以进入浏览器查看`http://yarn001:50070`，显示成功启动成功。

![1.bmp](https://i.loli.net/2018/08/20/5b7a22373a4ea.bmp)

![2.bmp](https://i.loli.net/2018/08/20/5b7a22356e4db.bmp)

也可以使用`jps`命令查看。

    启动yarn
    sbin/start-yarn.sh
    或者
    sbin/yarn-daemon.sh start resourcemanager
    sbin/yarn-daemon.sh start nodemanager

可以进入`yarn001:8088`查看是否启动成功。

![4.bmp](https://i.loli.net/2018/08/20/5b7a2239a6e27.bmp)

![3.bmp](https://i.loli.net/2018/08/20/5b7a223445b62.bmp)

退出时注意关闭所有已启动的，只需将start改为stop即可，同样使用`jps`查看是否关闭成功。

至此，hadoop单机环境安装成功。
