---
title: 常用 mysql 数据库引擎——MyISAM 和 InnoDB 区别
date: 2016-05-22 14:20:00
tags: SQL
categories: 技术
---
  
## 背景：
昨天做项目时，发现使用事务后回滚不了，后来把数据库引擎从 MyISAM 换成 InnoDB 后果断好了，如下图：
 
![](http://images2015.cnblogs.com/blog/896608/201605/896608-20160522141935919-1781780255.png)

## 正文：

**MyISAM** 和 **InnoDB**是 mysql 常用的数据库引擎，他们的区别如下：

|数据库引擎|适用范围|性能|事务|外键|数据受损恢复|表的大小|
|--------|:----:|:----:|:----:|:----:|:----:|:----:|
|MyISAM|适合频繁查询|高|×|×|×|小|
|InnoDB|适合频繁修改|中|√|√|√|大|

> MyISAM 是 MySQL 的 ISAM 扩展格式和缺省的数据库引擎。

---

#### Reference：

[ MySQL 数据库引擎介绍、区别、创建和性能测试的深入分析](http://www.jb51.net/article/38004.htm)