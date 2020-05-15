---
layout: original
title: "简单了解redis和redis应用"
date: 2020-05-14 18:31:27 +0800 
categories: 分类
tag: [redis,springboot]
---
* content
{:toc}

简单讲讲redis以及redis在springboot里的使用。

<!-- more -->
<!-- TOC -->
## 什么是redis 
一句话概括，redis就是跑在内存里的高性能key-value数据库。  
当然不只这一点：
* 支持数据持久，数据存到磁盘中，重启重新加载使用； 
- 不仅仅是key-value，同时还提供list，set，sorted set，hash等数据结构的存储； 
* Redis支持数据的备份，即[master-slave](##master-slave)模式的数据备份。 
- 性能极高 – Redis能读的速度是110000次/s,写的速度是81000次/s.  
* 原子 – Redis的所有操作都是[原子性](##原子性)的，同时Redis还支持对几个操作全并后的原子性执行。 
- 丰富的特性 – Redis还支持 publish/subscribe, 通知, key 过期等等特性


## 数据类型 
### key-string 
在redis里，其实key和string是两种概念，但使用上其实是要一起使用的  
进入docker中的redis    
``` shell
[root@localhost ~]# docker exec -it redis  /bin/bash
root@a97e5497bcb4:/data# redis-cli
127.0.0.1:6379> auth 123456
OK
127.0.0.1:6379> 
```  
创建key-string,查询  
``` shell
127.0.0.1:6379> set test-key test-string
OK
127.0.0.1:6379> get test-key 
"test-string"
127.0.0.1:6379>  
```  

### hash
简单来讲，就是key-key-value,也就是hash里存储着键值对   
``` shell
127.0.0.1:6379> hmset hkey key1 value1 key2 value2
OK
127.0.0.1:6379> hget hkey key1
"value1"
127.0.0.1:6379> 
```  

### List 
Redis列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边） 
``` shell
127.0.0.1:6379> lpush lkey value1
(integer) 1
127.0.0.1:6379> lpush lkey value2
(integer) 2
127.0.0.1:6379> lpush lkey value3
(integer) 3
127.0.0.1:6379> lrange lkey 0 2
1) "value3"
2) "value2"
3) "value1"
``` 
当然也可以通过序列设置值
```shell
lset lkey 0 newvalue
OK
127.0.0.1:6379> lrange lkey 0 2
1) "newvalue"
2) "value2"
3) "value1"
``` 

### 小应用
由于redis操作的原子性，利用redis和hash就可以做很多事情，比如[红包](../../../../2018/07/30/redis-red-packet/)







## master-slave  
主从设备模式也叫做主仆模式英文简称为Master-Slave,核心思想是基于分而治之的思想,将一个原始任务分解为若干个语义等同的子任务,并由专门的工作者线程来并行执行这些任务,原始任务的结果是通过整合各个子任务的处理结果形成的.主要的使用场景有  
* 并行计算,以提升计算性能  
- 容错处理,以提升计算的可靠性  
* 计算精度,以提高计算的精确程度  

[返回](##什么是redis)

## 原子性 
即一个操作或者多个操作，要么全部执行并且执行的过程不会被任何因素打断，要么就都不执行。原子性就像数据库里面的事务一样，他们是一个团队，同生共死。  
[返回](##什么是redis)
