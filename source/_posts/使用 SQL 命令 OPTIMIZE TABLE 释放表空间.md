---
title: 使用 SQL 命令 OPTIMIZE TABLE 释放表空间
date:  2016-05-22 14:24:00
tags: SQL
---
  
## 前提：

1.删除了表的一大部分

2.已经对含有可变长度行的表（含有 VARCHAR, BLOB 或 TEXT 列的表）进行了很多更改
 
## 作用：

利用未使用的空间，释放出来，并整理数据文件的碎片

## 频率：

不需要经常运行，每周一次或每月一次

## 试用范围：

OPTIMIZE TABLE 只对 MyISAM, BDB 和 InnoDB 表起作用

## 注意事项：

1.在 OPTIMIZE TABLE 运行过程中，MySQL 会锁定表。如果涉及海量的数据表，应该考虑使用一些运维手段避免现网的服务受到影响

2.使用 alter table table_name engine=innodb 也可以同样达到释放空洞的效果。这是由于在转换数据引擎（即便没有真正转换）的时候，mysql 也会将表中的数据读取出来，再重新写入，在这个过程中，空洞自然就没有了

---

#### Reference：

[ mysql 中 OPTIMIZE TABLE 的作用](http://blog.sina.com.cn/s/blog_3dbab2840100c13p.html)