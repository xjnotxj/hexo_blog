---
title: mysql按汉语拼音首字母排序
date: 2016-03-21 21:52:00
tags: SQL
categories: 技术
---
  
如果数据表table的某字段name的字符编码是utf8_general_ci :

> SELECT * FROM table <bold>ORDER BY convert(name USING gbk) COLLATE gbk_chinese_ci asc</bold> ;

 
