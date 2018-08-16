---
title: Windows下php+sqlserver环境配置
key: 20161220
tags: PHP
---

-为什么使用的数据库是微软的SqlServer而不是和蔼可亲的MySQL呢？
        -因为这是老师要求的数据库课设必须使用的数据库:)。
已有环境: PHP5.3以上版本+SqlServer2012+Win10
操作：连接PHP与SqlServer


<!--more-->


 1. 安装驱动

	第一个是微软的ODBC(开放数据库互联)驱动，也就是微软的数据库访问接口标准。[下载地址1](https://www.microsoft.com/zh-CN/download/details.aspx?id=36434)

	第二个是微软提供的用于PHP与SqlServer连接驱动，[下载地址2](https://www.microsoft.com/en-us/download/details.aspx?id=20098)

	第二个安装完成后运行，生成好些动态链接文件,如下图：

	![动态链接文件](https://i.loli.net/2018/08/16/5b752a740b79a.png)

	54，55，56对应的是PHP的版本；ts和nts的选择要看你安装的php版本是线程安全版的还是非线程安全版，ts是线程安全，nts是非线程安全，如果不知道的可以在phpinfo里查看Zend Extension Build这个属性。

2. 配置

 - 将`php_pdo_sqlsrv_xx_ts_vcx.dll`文件和`php_sqlsrv_xx_ts_vcx.dll`文件复制到php安装目录下的ext文件夹中。
 - 在php.ini里添加`extension=php_sqlsrv_xx_ts_vcx.dll`
 - `extension=php_pdo_sqlsrv_xx_ts_vcx.dll`

3. 最后重启服务器(apache等)，打开phpinfo,看到以下状态说明配置正确。

	![phpinfo](https://i.loli.net/2018/08/16/5b752a740d00b.png)

	其他的就自己写个demo测试一下把！
	
**在安装和配置的过程中，一定要注意的是你的软件，系统的版本和驱动是否配套。**

如果仍然无法连接或者想要了解更多(如nodejs,python,ruby等与sqlserver的连接)，可以直接上[微软的sqlserver连接php的技术支持网站](https://docs.microsoft.com/en-us/sql/connect/php/microsoft-php-driver-for-sql-server)查看相关信息，当然前提得是你英语不错。

最后提供一个用于nodejs与sqlserver连接的福利：[mssql模块文档](https://github.com/patriksimek/node-mssql)
