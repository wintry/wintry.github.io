---
layout: post
title: "springboor in docker"
date: 2020-01-14 09:00:00 +0800 
categories: springboot之路
tag: [springboot,docker]
---
* content
{:toc}

本文主要写将springboot的jar简单地在docker中作为容器进行运行。

<!-- more -->
<!-- TOC -->
## 打包镜像
创建一个Dockerfile与jar放在同一路径下，内容如下 
```java
#FROM命令定义构建镜像的基础镜像，该条必须是dockerfile的首个命令,这里就是另外一个jdk的镜像
FROM openjdk:8u151-jdk
#将文件添加到镜像中
ADD api.jar /api.jar
#端口
EXPOSE 8080
#这条命令就是镜像运行时执行的命令
ENTRYPOINT [ "java","-jar","api.jar" ]
``` 
打包 
```cmd
docker build -t api:1.0 .
```


## 运行
```cmd
docker run --name api  -d -p 8080:8889  api:1.0
```
