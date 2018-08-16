---
title: PHP的Session会话机制
key: 20160914
tags: PHP
---

因为最近才学习php与mysql，为了写留言板的注册，登录与注销功能，接触到php的session会话机制，想要记录下来。

Session: 代表服务器与浏览器的一次会话过程（其本来的含义是指有始有终的一系列动作/消息），这个过程是连续的，也可以时断时续的。

我们在这里讨论的session机制实质是客户端与服务器端之间状态保持的解决方案。

> Session 的工作机制是：为每个访问者创建一个唯一的 id (UID)，并基于这个 UID 来存储变量。UID 存储在 cookie 中，亦或通过 URL 进行传导。

session和cookie一直被放在一起比较，我们这里不讨论它们俩，只需知道cookie机制采用的是在客户端保持状态的方案，而session机制采用的是在服务器端保持状态的方案，且大部分session机制都使用会话cookie来保存session id（标识session是否存在）。

接下来我们讲如何用php保持登录状态且注销登录。

首先，建立session会话：

```
<?php
	session_start();
?>
```

生成session文件存放在tmp目录下。同时会设置一个客户端的cookie，用于保存session_id（发送客户端请求，若存在则使用，不存在建立）。

然后，存储session数据：

```
<?php
	$_SESSION['username'] = 'xyj';
?>
```

存储在超级全局变量数组$_SESSION中。这样，设置好Session后就能通过session中保存的数据用于在网页显示用户信息与状态了。

最后，注销用户登录：

```
<?php
	unset('$_SESSION['username']);//释放指定的session变量
	session_destroy(); //失去所有已存储的 session 数据。
?>
```

当然你也可以直接关闭浏览器，重新打开网页来注销session（不过这样做很傻）。


总结下来不多，因为并没有往更深处探索，像cookie机制，http无状态协议等，用来写个简单的网页登录功能也够了。
如果能够深刻理解了session机制，可以不用php的session机制，自定义一个，让你的网站更加安全。
