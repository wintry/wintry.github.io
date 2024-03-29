---
layout: post
title: "利用redis实现红包"
date: 2018-07-30 11:00:00 +0800 
categories: redis
tags: [redis]
---
* content
{:toc}

这篇博客主要是对之前工作中对红包功能实现的总结，记录学习过程，也供各位网友参考，如有设计以及实现上的缺陷，或是存在漏洞，请大家批评指正！

<!-- more -->
<!-- TOC -->
## 需求
    1 红包发送    
2 红包接收    
3 过期红包回收    
4 红包记录查询    

目前的设计是在红包发出去就分配好

## 数据库
    +redis：list存分开的小红包，hash存领取情况  
        -list：key为红包id，各个值为小红包金额  
        -hash:key为红包id，field为用户id，value为用户抢到的金额  
    +mysql或postgresql等数据库
        -红包发送表：红包id，发送者id，金额，是否抢完，是否到期等
        -红包分配表：红包id，抢到的用户id，金额

## 红包发送
![红包发送流程图](/img_blog/2017-09-01-1.jpg)  
    
    1用户发红包后，在红包发送表中插入一条记录
    2用户钱包减去发送金额（与1做事务处理）
    3将红包随机分为5份小红包（分配算法此处不做介绍，过段时间可能会写一下）
    4将5个小红包存入redis的list

![抢红包示意图](/img_blog/2017-09-01-2.jpg)

    1将红包id发送给服务器，判断是否有权限抢红包
    2查询红包发送表，红包是否超时
    3查询红包发送表是否抢完
    4尝试获取并删除list的一个元素，获取成功则就进入下一步，失败则表示已抢完并跟新红包发送表为已抢完
    5hash记录用户id和金额（与4做事务处理）
    6红包分配表增加一条记录
    7接收者账户+抢到的金额（与6做事务处理）

![回收红包示意图](/img_blog/2017-09-01-3.jpg)
     
    
    1查询所以符合回收条件的红包
    2删除所以redis相关记录
    3根据红包分配表将未抢完的红包回收
    4更新红包为超时，发送者余额增加