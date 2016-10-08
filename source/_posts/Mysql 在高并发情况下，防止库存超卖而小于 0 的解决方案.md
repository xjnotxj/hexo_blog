---
title: Mysql 在高并发情况下，防止库存超卖而小于 0 的解决方案
date: 2016-05-22 13:12:00
tags: SQL
---
  
## 背景：
本人上次做申领 campaign 的PHP后台时，因为项目上线后某些时段同时申领的人过多，导致一些**专柜的存货为负数（< 0）**，还好并发量不是特别大，只存在于小部分专柜而且一般都是 -1 的状况，没有造成特别特别严重的后果，但还是要反思了自己的过错。

这次又有新的申领 campaign，我翻看了上次的代码逻辑：

## 正文：

#### 【先 select 后 update】

```
beginTranse(开启事务)
try{
    $result = $dbca->query('select amount from s_store where postID = 12345');
    if(result->amount > 0){
        $dbca->query('update s_store set amount = amount - 1 where postID = 12345');
    }
}catch($e Exception){
    rollBack(回滚)
}
commit(提交事务)
```

以上代码就是我第一次的写法，看似问题不大，其实隐藏着巨大的漏洞。数据库的访问其实就是对磁盘文件的访问，数据库中的表其实就是保存在磁盘上的一个个文件，甚至一个文件包含了多张表。例如由于高并发，当前有三个用户 a、b、c 三个用户进入到了这个事务中，这个时候会产生一个**共享锁**，所以在 select 的时候，这三个用户查到的库存数量都是>=0的。

然后是 update，假如这三个用户同时到达 update 这里，这个时候 update 更新语句会把并发串行化，也就是给同时到达这里的是三个用户排个序，一个一个执行，并生成**排他锁**，在当前这个 update 语句 commit 之前，其他用户等待执行，commit 后，生成新的版本；这样执行完后，库存肯定为负数了。但是根据以上描述，我们修改一下代码就不会出现超买现象了，代码如下：

#### 【先 update 后 select】

```
beginTranse(开启事务)
try{
    $dbca->query('update s_store set amount = amount - 1 where postID = 12345');
    $result = $dbca->query('select amount from s_store where postID = 12345');
    if(result->amount < 0){
       throw new Exception('库存不足');
    }
}catch($e Exception){
    rollBack(回滚)
}
commit(提交事务)
```


　另外，更简洁的方法：

#### 【update & select 合并】
```
beginTranse(开启事务)
try{
    $dbca->query('update s_store set amount = amount - 1 where amount>=1 and postID = 12345');
}catch($e Exception){
    rollBack(回滚)
}
commit(提交事务)
```

---

#### 补充

1.这个肯定不能直接操作数据库的，会挂的。直接读库写库对数据库压力太大，要用**缓存**

把你要卖出的商品比如 10 个商品放到缓存中；然后在 memcache 里设置一个计数器来记录请求数，这个请求书你可以以你要秒杀卖出的商品数为基数，比如你想卖出 10 个商品，只允许 100 个请求进来。那当计数器达到 100 的时候，后面进来的就显示秒杀结束，这样可以减轻你的服务器的压力。然后根据这 100 个请求，先付款的先得后付款的提示商品以秒杀完。



2.首先，多用户并发修改同一条记录时，肯定是后提交的用户将覆盖掉前者提交的结果了。这个直接可以使用**加锁**机制去解决，乐观锁或者悲观锁

**悲观锁( Pessimistic Lock )**, 顾名思义，就是很悲观，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会 block 直到它拿到锁。传统的关系型数据库里边就用到了很多这种锁机制，比如行锁，表锁等，读锁，写锁等，都是在做操作之前先上锁。

**乐观锁( Optimistic Lock )**, 顾名思义，就是很乐观，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号等机制。乐观锁适用于多读的应用类型，这样可以提高吞吐量，像数据库如果提供类似于 write_condition 机制的其实都是提供的乐观锁。

两种锁各有优缺点，不能单纯的定义哪个好于哪个。**乐观锁比较适合数据修改比较少，读取比较频繁的场景**，即使出现了少量的冲突，这样也省去了大量的锁的开销，故而提高了系统的吞吐量。但是如果经常发生冲突（写数据比较多的情况下），上层应用不不断的 retry，这样反而降低了性能，对于这种情况使用悲观锁就更合适。



3.**不建议在数据库层面加锁，建议通过服务端的内存锁（锁主键）**

当某个用户要修改某个 id 的数据时，把要修改的 id 存入 memcache，若其他用户触发修改此 id 的数据时，读到 memcache 有这个 id 的值时，就阻止那个用户修改。

---

#### Reference：

[mysql处理高并发，防止库存超卖](http://blog.csdn.net/caomiao2006/article/details/38568825)