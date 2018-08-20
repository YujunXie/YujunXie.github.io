---
title: Hadoop初体验-WordCount小程序
key: 20170508
tags: Hadoop
---

> 就像学习一门新语言，第一个程序是写 “Hello World!” 一样。如Hadoop这种分布式数据处理系统，第一个入门程序也就是 “WordCount” 小程序。

之前我们部署好了hadoop单机测试环境，在开启`namenode`, `datanode`, `resourcemanager`, `nodemanager`后，我们就可以直接运行WordCount小程序啦。

1. 先在本地新建一个文本文件(words)，内容为多行字符串，用于统计单词数。

2. 在HDFS中新建一个文件夹，用于上传本地的words文档。在hadoop根目录下输入：

    	bin/hdfs dfs -mkdir /test
	
	查看是否建立成功（查看HDFS根目录下的目录结构）：

    	bin/hdfs dfs -ls /

3. 将本地words文档上传到test目录：

    	bin/hdfs dfs -put /home/leesf/words /test/
    	
4. 运行WordCount(使用hadoop自带的jar包):

    	bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.2.0.jar wordcount /test/words /test/out

5. 运行成功后，查看运行结果：

    	bin/hadoop fs -cat /test/out/part-r-00000

至此，hadoop单机环境测试成功，可以开展下一步学习啦!
