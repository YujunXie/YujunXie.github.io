---
title: Wireshark之NPF拒绝访问
key: 20170524
tags: WireShark
---

重新安装的WireShark好好的，打开后显示 

***“interface not found”.***

这是Wireshark拒绝访问NPF，NPF是什么？

> **NPF**即网络数据包过滤器（Netgroup Packet Filter，NPF）是Winpcap的核心部分，它是Winpcap完成困难工作的组件。它处理网络上传输的数据包，并且对用户级提供可捕获（capture）、发送（injection）和分析性能（analysis capabilities）。它不仅提供了基本的特性（例如抓包），还有更高级的特性（例如可编程的过滤器系统）。前者可以被用来约束一个抓包会话只针对网络通信中的一个子集，后者提供了一个强大而简单的统计网络通信量的机制。

NPF服务未打开，抓包也就无法进行。

1. 首先确定是否安装**WinPcap**.
	
	在一键安装时若没有勾选这一项。手动安装[WinPcap](https://www.winpcap.org/)
	
2. 打开NPF服务
	
	以管理员身份打开命令行cmd，输入`net start npf`即可。

这时候就可以愉快地抓包啦~
