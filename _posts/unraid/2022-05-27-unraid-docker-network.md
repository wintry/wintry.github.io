---
layout: post
title: "docker中创建网关非宿主机网关的网络"
date: 2022-05-27 10:22:00 +0800 
categories: 分类
tags: [unraid, docker, docker network]
---
* content
{:toc}

unraid的网关指向旁路由，容器指向软路由

<!-- more -->
<!-- TOC -->
删除原来的macvlan，
```shell
docker network rm br0
```
创建新的macvlan网络
```shell
 docker network create --driver macvlan --gateway 192.168.1.1 --subnet 192.168.1.0/24 -o parent=br0  brx
 ```
 容器加dns
```shell
 --dns=192.168.1.1
```