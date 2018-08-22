---
title: Android开发（七）：Mob短信验证SDK
key: 20180120
tags: Android
---

一般的app都需要注册登录后再使用，这里我选择了免费的[Mob移动开发者服务平台](http://www.mob.com/)。

这个服务平台上有`ShareSDK`，用于一键分享其他社交平台，有`MobLink`，实现Web和App间的无缝连接，有`ShareREC`，解决手游录制，有`MobAPI`，用于接入数据服务。对于我们而言最重要的就是[短信验证码SDK](http://sms.mob.com/)，用于短信登录注册。

基本流程就是输入手机号->获取验证码->提交验证。还有语音验证，自定义签名，寻找通讯录好友，数据统计等功能。

[下载短信验证码SDK](http://www.mob.com/downloads/)

[SMSS-Android文档](http://wiki.mob.com/sdk-sms-android-3-0-0/)

注意要先注册登录，添加应用后获取该SDK的`App Key`和`App Secret`。

官方文档写的比较简单，所以当你在使用自定义`GUI`时参考这一篇：[Android Studio Mob快速集成短信验证(图文教程)](http://blog.csdn.net/qq_31927865/article/details/54378426)

*tips*：12小时内每个手机号码只能使用验证码5次。

![mob.png](https://i.loli.net/2018/08/22/5b7cfb520b583.png)
