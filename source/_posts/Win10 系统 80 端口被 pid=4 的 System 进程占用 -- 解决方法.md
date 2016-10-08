---
title: Win10 系统 80 端口被 pid=4 的 System 进程占用 -- 解决方法
date: 2016-05-01 18:44:00
tags: 服务器-Windows
categories: 技术
---
  
## 背景： 
今天在家里电脑下载 phpstudy 2016 后，准备启动服务器，结果提示 80 端口被占用。

## 过程： 
百度后有这几种解决方法：

#### 1.IIS 占用
![](http://images2015.cnblogs.com/blog/896608/201605/896608-20160501184349363-1754223170.png)
![](http://images2015.cnblogs.com/blog/896608/201605/896608-20160501184350363-371181336.png)
 
#### 2.SQL server 2008 的报表服务占用
![](http://images2015.cnblogs.com/blog/896608/201605/896608-20160501184352347-1899626256.jpg)

可惜这两种方法试了都不行。

## 解决：

1.打开注册表：regedit

2.找到：HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\HTTP

3.在右边找到 Start 这一项，将其改为 0

4.重启系统

---

#### Reference：

[Win10系统80端口被System占用 造成Apache不能启动的解](http://www.chinaz.com/web/2015/0730/428496.shtml)