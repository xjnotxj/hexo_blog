---
title: SQL 查询语句去除重复行
date: 2016-03-11 16:44:00
tags: SQL
---

## 1.存在两条完全相同的纪录

这是最简单的一种情况，用关键字 **distinct** 就可以去掉。

> select distinct * from table(表名) where (条件)


## 2.存在部分字段相同的纪录（有主键 id 即唯一键）

如果是这种情况的话用 distinct 是过滤不了的，这就要用到主键 id 的唯一性特点及 group by 分组。

> select * from table where id in (select <bold>min</bold> (id) from table group by [去除重复的字段名列表,....])

> [取重复的第一行]

或者

> select * from table where id in (select <bold>max</bold> (id) from table group by [去除重复的字段名列表,....])

> [取重复的最后一行]

## 3.没有唯一键ID

> **select identity(int,1,1)** as id, **into** newtable(临时表) from table

> select * from newtable where id in (select min(id) from newtable group by [去除重复的字段名列表,....])

> drop table newtable

---

#### 扩展:

 - identity(int,1,1) —— 从 1 开始，每次递增 1
 - SELECT INTO 语句 —— **SELECT** &#42; **INTO** new_table_name [IN externaldatabase] FROM old_tablename

---

#### Reference：

[sql 查询语句去除重复列（行）](http://blog.knowsky.com/234240.htm)
