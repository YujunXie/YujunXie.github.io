---
title: 使用virtualbox安装Fedora
key: 20170423
tags: Linux
---

[virtualbox下载地址](https://www.virtualbox.org/)
一键安装，非常简单。

[Fedora22 64-bit 镜像下载地址](http://ftp.cuhk.edu.hk/pub/linux/fedora/releases/22/Workstation/x86_64/iso/)

进入virtualbox，点击新建，简单选择就建立成功了。

**加载iso文件**

点击设置->存储-》控制器：IDE->没有盘片->属性（右部）->选择一个虚拟光盘文件
把镜像加进去。

**网络设置**

点击设置->网络->网卡1->连接方式
选择网络地址转换NAT

**其他**

点击设置->系统->主板->启动顺序去掉软驱

然后启动虚拟机。参见[初始化Fedora live](http://www.linuxdiyf.com/linux/13349.html)

重点注意配置完成后手动卸载盘片，也就是之前引入的ios镜像文件，否则再次进入`live CD`环境，又让你配置一遍。

差不多就这些，如果觉得虚拟机很*慢*，可以这样做：
`点击设置->显示->屏幕`,
将显存大小调整至32MB，启用3D加速。
