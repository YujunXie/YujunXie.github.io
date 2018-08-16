---
title: 学习SQLServer
key: 20161229
tags: SQLServer 数据库
---

- 解决中文乱码

1. 数据库名称 右击->属性->选项->排序规则->选择为`Chinese_PRC_CI_AS`。
2. 使用汉字的字段，字符串类型应为`nchar`,`nvarchar`,`nnext`等。
3. 在进行中文字符插入时在中文字符串前面加上一个大写字母N。如`select name from users where name=N'小明'`。

- 时间数据类型和操作函数

1. 数据类型
2. 函数操作

<!--more-->

时间类的数据类型总是让人比较头痛，所以简单总结了一下，以方便使用。

1. 主要数据类型

	~~~
	time: hh:mm:ss
	date: YYYY-MM-DD
	smalldatetime(4字节): YYYY-MM-DD hh:mm:ss
	datetime(8字节): YYYY-MM-DD hh:mm:ss[.nnn]
	~~~

2. 日期和时间函数

 - 获取系统日期和时间的函数
 
   		`getdate();` 返回`datetime`

 - 获取日期和时间部分的函数
 
    `datename(week/weekDay/year/month/hour/minute/seconds/datepart,date);` 返回`nvarchar`
    
    `datepart(week/weekDay/year/month/hour/minute/seconds/datepart,date);` 返回`int`
    
    `day(date);month(date);``year(date);` 返回`int`

 - 获取日期和时间差的函数

    `date(datepart, startdate, enddate);` 返回`int`

 - 修改日期和时间值的函数

    `dateadd(datepart, number, date);` 返回date参数的数据类型

 - CAST 和 CONVERT (Transact-SQL)

    提供有关在日期和时间值与字符串文字及其他日期和时间格式之间进行相互转换的信息。
    CAST一般更容易使用,CONVERT的优点是可以格式化日期和数值.
    
    `CAST(expression AS data_type);`
    
    `CONVERT(data_type[(length)], expression [,style]);`
    
    [CONVERT参数选择](http://www.cnblogs.com/weiqt/articles/20
    40800.html)
    

	![图片1.png](https://i.loli.net/2018/08/16/5b752d1e4fe65.png)
