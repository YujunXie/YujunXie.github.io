---
title: Windows下Node.js安装与环境配置+express模块安装+sublime运行环境配置
key: 20161003
tags: Nodejs
---

大致步骤：

 - 安装node.js
 - 路径及环境变量的配置
 - express模块的安装
 - Sublime Text3运行环境配置

<!--more-->

----------

1.安装地址：[Node.js官网下载](http://nodejs.org/)

按照默认路径安装后是放在"C:\Program Files\nodejs"下。

打开cmd，`cd C:\Program Files\nodejs`，进入node根目录，分别输入**node -v**和**npm -v**查看是否安装成功。

(现在的nodejs已经集成了**npm**这个强大的包管理工具,相当于npm成为了node的一个全局模块)

----------

2.路径及环境变量的配置

npm的全局模块的存放路径以及cache路径：

在主目录下创建**node_global**和**node_cache**两个文件夹(或直接在cmd中输入`mkdir node_global`和`mkdir node_cache`)。

然后继续在cmd中输入：

    npm config set prefix "C:\Program Files\nodejs\node_global"

以及

    npm config set cache "C:\Program Files\nodejs\node_cache"

配置环境变量：打开计算机，系统属性—高级系统设置—环境变量；

在系统变量下，新建NODE_PATH，值为`C:\Program Files\nodejs\node_global`（全局路径），将`%NODE_PATH%`到path变量值后面即可。(注意用户变量和系统变量里的path都要添加上)

----

3.express模块安装：

同样在cmd中输入`npm install express -g`(**-g**表示进行**全局安装**，也就是安装到node_global目录下）。

查看是否安装成功：进入到node_global目录下，输入node 回车，输入`require("express")`。出现模块信息即安装成功。

express命令的使用，查看版本信息。输入`express -V`(注意这里是大写的**V**),提示express不是内部或外部命令。

这是因为express4.x中将命令工具分离出来了，所有需要先装express-generator。

命令行输入：`npm install -g express-generator`，即安装成功。
如出错一般是环境配置没有正确。
(ps:退出nodejs终端命令行，ctrl+D一次或ctrl+C两次或输入“.exit” 即可)

---

4.运行环境配置

 - **cmd下直接运行**

进入文件所在目录，node+文件名.js直接运行，crtl+c 取消编译。

 - Sublime Text3

下载插件：[Nodejs插件地址](https://github.com/tanepiper/SublimeText-Nodejs)

将此文件夹放在Perference(首选项)-&gt;Browse Packages(浏览插件)下的packages文件夹内。

打开文件夹内文件`Nodejs.sublime-build`，将代码 `"encoding": "cp1252";` 改为 `"encoding": "utf8"` ，将代码 `"cmd": ["taskkill /F /IM node.exe &amp; node", "$file"]` 改为 `"cmd": ["node", "$file"]` ，保存文件。

打开文件夹内文件`Nodejs.sublime-settings`;，将代码 "node_command": false改为 `"node_command": "C:\\Program Files\\nodejs\\node.exe"` ，将代码 "npm_command": false 改为 `"npm_command": "C:\\Program Files\\nodejs\\npm.cmd"` ，保存文件。

打开一个nodejs文件，直接ctrl+b 编译运行，但是发现**取消编译**的快捷键ctrl+break并不起作用，于是打开默认的按键绑定，将`{ "keys": ["ctrl+break"], "command": "exec", "args": {"kill": true} }`,改成`{ "keys": ["ctrl+z"], "command": "exec", "args": {"kill": true} }`。
**注意**自定义的按键不要和默认按键起冲突。

    个人推荐cmd直接运行。
