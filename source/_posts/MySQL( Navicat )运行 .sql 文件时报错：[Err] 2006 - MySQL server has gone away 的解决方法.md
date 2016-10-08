---
title: MySQL( Navicat )运行 .sql 文件时报错：[Err] 2006 - MySQL server has gone away 的解决方法
date: 2016-06-01 22:12:00
tags: SQL
---
  
## 背景：

今天导入一个数据量很大的 .sql 文件时，报错：

![](http://img.blog.csdn.net/20150419155518477?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGVmb25kcw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
![](http://images2015.cnblogs.com/blog/896608/201606/896608-20160601220554274-328464488.png)
 
## 原因：

可能是 sql 语句过长，超过 mysql 通信缓存区最大长度。

## 解决：

1. 编辑 MySQL 安装目录下的 my.ini，在最后添加以下内容：
```
max_allowed_packet=16M
```

2. 重启 MySQL 服务

---

#### Reference：

 - [ MySQL 数据批量恢复时 [Err] 2006 - MySQL server has gone away 错误的解决](http://www.th7.cn/db/mysql/201504/101946.shtml)
 - [#2006-MySQLserverhasgoneaway](http://www.2cto.com/database/201409/338500.html)