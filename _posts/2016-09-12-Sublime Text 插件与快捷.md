---
title: Sublime Text 插件与快捷
key: 20160912
tags: 编辑器
---

目前sublime text3是我最常使用的编辑器，优点是漂亮轻巧加上简便的插件功能。

 - 主题
 - 编译设置
 - 其他设置
 - 常用快捷键

----------


<!--more-->


**主题**

现在使用的是阿童木主题[gruvbox](https://github.com/morhetz/gruvbox),很有复古韵味
![gruvbox](https://i.loli.net/2018/08/16/5b74eaec4b61e.png)

还有这些主题也很不错：
[spacegray](https://github.com/kkga/spacegray)
![spacegray](https://i.loli.net/2018/08/16/5b74eae83a8b4.png)

[molokai](https://github.com/tomasr/molokai)

![molokai](https://i.loli.net/2018/08/16/5b74eae412522.png)

这篇博客里有更多的主题选择：[主题](http://www.cnblogs.com/tt2015-sz/p/5695638.html)

----------

**编译设置**


 1.如何实现c/c++的窗口化编译运行
    
 - 首先看自己电脑里有没有g++和gcc环境。(gcc将.c文件看作c语言，而g++将其看作c++，.cpp文件gcc和g++都看作c++。可以看作gcc编译c,g++编译c++，但实质并不是这样，想了解更多的：[gcc和g++到底有什么区别](https://www.zhihu.com/question/20940822))。

    查看方式：cmd;输入gcc -v;输入g++ -v
    若不显示版本信息则需要安装Mingw：[安装配置Mingw](http://blog.csdn.net/xhhjin/article/details/8449251)
    
 - 打开 Sublime Text 3, Tools->Build System->New Build System

Windows下：

  在打开的文件中添加以下代码：

    {
    	"working_dir": "$file_path",
    	"cmd": "gcc -Wall \"$file_name\" -o \"$file_base_name\"",
    	"file_regex": "^(..[^:]*):([0-9]+):?([0-9]+)?:? (.*)$",
    	"selector": "source.c, source.c++",
    	"encoding": "cp936", 
    	"variants":
    	[
        	{
        		"name": "Run",
        		"shell_cmd": "gcc -Wall \"$file\" -o \"$file_base_name\" && start cmd /c \"${file_path}/${file_base_name} & pause\""
        	}
    	]
    }

 保存为c.sublime-bulid，重新打开sublime就可以用c编译运行c程序了。(ctrl+shift+B)
 
 重复上一步，Windows下添加以下代码：

    {
		"encoding": "cp936",
		"working_dir": "$file_path",
		"cmd": "g++ -Wall -std=c++11\"$file_name\" -o \"$file_base_name\"",
		"file_regex": "^(..[^:]*):([0-9]+):?([0-9]+)?:? (.*)$",
		"selector": "source.c,source.c++",
		"shell": true,
		"variants": 
		[
    		{   
        		"name": "Run",
        		"shell_cmd": "g++ -Wall -std=c++11\"$file\" -o \"$file_base_name\" && start cmd /c \"\"${file_path}/${file_base_name}\" & pause\""
    		},

    		{
        		"name": "RunInCommand",
        		"cmd": ["cmd", "/c", "g++", "${file}", "-o", "${file_path}/${file_base_name}", "&&", "start", "cmd", "/c", "${file_path}/${file_base_name} & pause"]
    		}
		]
	}
	
macOs下添加一下代码：

	{
		"cmd": ["/usr/bin/g++", "${file}", "-o", "${file_path}/${file_base_name}"],
		"file_regex": "^(..[^:]*):([0-9]+):?([0-9]+)?:? (.*)$",
		"working_dir": "${file_path}",
		"selector": "source.c, source.c++",
		"variants":
		[
	    	{
	        	"name": "Run",
	        	"cmd": ["bash", "-c", "/usr/bin/g++ '${file}' -o '${file_path}/${file_base_name}' && open -a Terminal.app '${file_path}/${file_base_name}'"]
	    	}
		]
	}

保存为c++.sublime-build文件。编译运行操作同上。
这里和c有点不一样的是如果不加上后面那段RunInCommand无法出现控制窗口进行输入输出，找了很久才配置好。
还可以到KeyBindings->User添加以下代码设置快捷键：

    { "keys": ["f5"], "command": "build", "args": {"variant": "RunInCommand"} },

    
编译运行c时选择c编译系统，c++选择c++编译系统。

2.java编译环境设置

Windows下：

 - java环境变量配置
 
    下载好jdk放在Java文件夹中，会出现jdk和jre两个文件夹。
    
    设置三个环境变量：
    >JAVA_HOME:安装路径如D:/Java/jdk1.8.0_51
    
    >PATH(系统变量和用户变量中的path都设置):%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;
    
    >CLASS_PATH: .;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;(注意前面的 . ;)
    
    配置成功：打开cmd，分别输入java和javac，出现用法。
    
 - sublime新建编译系统
 
    与c/c++相似，新建java.sublime-build文件：
   
   ~~~ 
   {  
     	"shell_cmd": "runJava.bat \"$file\"",  
     	"file_regex": "^(...*?):([0-9]*):?([0-9]*)",  
     	"selector": "source.java",  
     	"encoding":"cp936"  
	}
	~~~
	
还要建立批处理文件，新建runJava.bat文件并保存到jdk安装目录下的bin目录： 

    @echo off
    cd %~dp1
    echo Compiling %~nx1......
    if exist %~n1.class (
     del %~n1.class
    )
    javac %~nx1
    if exist %~n1.class (
     echo ------Output------
     java %~n1
    )
    
在这里也许会碰到无法写入的问题，修改文件夹权限即可。
然后使用Ctrl+B就可以编译啦~

3.Python编译环境设置

Windows下：

 - 下载安装配置好Python3.x
 - sublime新建编译系统
 
    与c/c++相似，新建python3.sublime-build文件：

~~~
{
    "cmd": ["D:/Python/python.exe", "-u", "$file"],  
    "file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",  
   	"selector": "source.python", 
   	"encoding": "cp936",
   	"variants":
   	[{
   	 	"name": "Run",
    	"shell_cmd": "start cmd /c \"python -u \"$file\" & pause\"",
    }]
}
~~~

macOs下：

    {
  		"cmd":["/Library/Frameworks/Python.framework/Versions/3.5/bin/python3", "-u", "$file"],
  		"file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",
  		"selector": "source.python",
  		"env": {"LANG": "en_US.UTF-8"}
	}

将"D:/Python/python.exe" 改为自己的python安装路径。

编码encoding设置为cp936是为了支持中文的输入输出。

同理，‘ctrl+B’ 编译运行，‘ctrl+shift+B’调出cmd窗口编译运行。

4.推荐插件

1)[Highlight Build Errors](https://github.com/bblanchon/SublimeText-HighlightBuildErrors)
高亮运行错误代码行，不用一行一行去找，很方便。

2)[SublimeCodeIntel](https://github.com/Kronuz/SublimeCodeIntel)
一个全功能的 Sublime Text 代码自动完成引擎。

>支持语言：JavaScript, Mason, XBL, XUL, RHTML, SCSS, Python, HTML, Ruby, Python3, XML, Sass, XSLT, Django, HTML5, Perl, CSS, Twig, Less, Smarty, Node.js, Tcl, TemplateToolkit, PHP.

3)[Emmet](https://packagecontrol.io/packages/Emmet)
前身是Zen Coding。[快捷键指南](http://www.w3cplus.com/tools/emmet-cheat-sheet.html)

4)[HighlightBuildErrors](https://github.com/bblanchon/SublimeText-HighlightBuildErrors)

----------

**其他设置**

插件搜索下载地址：
[Sublime package control](https://packagecontrol.io/)

推荐插件：

 1. [BracketHighLighter](https://github.com/facelessuser/BracketHighlighter)
类似于代码匹配，可以匹配括号，引号等符号内的范围。

 2. [TrailingSpaces](https://github.com/SublimeText/TrailingSpaces
)
检测并一键去除代码中多余的空格。

 3. [BufferScroll](https://github.com/titoBouzout/BufferScroll)
---------- 更新于2017.02.28

>It's a simple Sublime Text plug-in which remembers and restores the scroll, cursor positions, also the selections, marks, bookmarks, foldings, selected syntax and optionally the colour scheme.
when you open a file. Will also remember different data depending the
position of the file in the application.

[安装Package Control](https://packagecontrol.io/installation#st3)

[安装汉化包](http://files.cnblogs.com/akwwl/sublime_text_3.zip|0|1|NULL|post|publish|)

将Default.sublime-package放进安装目录Sublime Text3\Packages 下面。

如何安装插件：

1.将插件文件放在Package目录下再重启sublime即可。

2.利用Package Control,ctrl + shift + p 调出console输入install选中install package等待一会即安装成功。

----------

**常用快捷键**

~~~
新建文件：Ctrl+N
关闭当前文档Ctrl+W 
字体大小调整：Ctrl+'+'/'-', Ctrl+鼠标滚动

注释：Ctrl+/
删除一行：Ctrl+Shift+K
跳转到相应的行：Ctrl+G
复制这行文本：Ctrl+Shift+D
合并行(一行或选择需要合并的多行)：Ctrl+J
选择整行(按住-继续选择下行): Ctrl+L
注释已选择内容：Ctrl+Shift+/
上行互换/下行互换：Ctrl+Shift+↑/↓
折叠代码/展开代码 Ctrl+Shift+[/]


选中所有光标所对应词：Ctrl+D
词互换：Ctrl+T 
粘贴过程中保持缩进: Ctrl+Shift+V
Alt+Shift+1（非小键盘）窗口分屏，恢复默认1屏
Alt+Shift+2 左右分屏-2列
Alt+Shift+3 左右分屏-3列
Alt+Shift+4 左右分屏-4列
Alt+Shift+5 等分4屏
Alt+Shift+8 垂直分屏-2屏
Alt+Shift+9 垂直分屏-3屏

命令模式：Ctrl+Shift+P
Ctrl+P 查找当前项目中的文件和快速搜索；
       输入@查找文件主标题/函数(Ctrl+R)；输入:跳转到文件某行。
开启/关闭侧边栏：Ctrl+K+B
标签页切换：Ctrl+Tab
检测语法错误:F6
~~~
