---
layout: original
title: "springboot setting up "
date: 2018-01-12 15:25:00 +0800 
categories: springboot之路
tag: [springboot]
---
* content
{:toc}

这是记录spring boot开发的博客，一为分享，二为自己查询用。 

<!-- more -->
## 你需要
+ ```Java 7``` 及以上  
+ ```IntelliJ IDEA ```
+ ```Maven ```  

本文使用 ```java 1.8.0_151 ```,```IntelliJ IDEA 2017.3.2```,```IDEA自带的maven```,调试通过
<!-- TOC -->

## 使用Maven构建项目 
使用 ```Spring Initializr```构建项目，可以访问http://start.spring.io/下载导入或者使用Idea的工具，这里选择Idea进行示范  
1.打开```Idea```点击```Create New Project```
![](/img_blog/spring-boot/2018-01-12-1.png)  
2.选择```Spring Initializr```,```Project SDK```选择本地SDK（多个版本的话请选择1.7以上），其余默认，点击```Nect```
![](/img_blog/spring-boot/2018-01-12-2.png)  
3.填写各种信息，其中```Packaging```为打包方式，看喜好选择
![](/img_blog/spring-boot/2018-01-12-3.png)  
4.选择依赖，为了便于学习，这里不做选择（后面可以添加），熟悉后可以在这里就选择
![](/img_blog/spring-boot/2018-01-12-4.png)
5.选择存储路径
![](/img_blog/spring-boot/2018-01-12-5.png)
## 项目结构解析
![](/img_blog/spring-boot/2018-01-12-6.png)  
+ ```src/main/java/com.{groupid}.{projectname}``` 项目的java包，代码也主要在这里编写，该目录下的```{projectname}Application```为项目的执行文件  
+ ```src/main/resources``` 项目的配置文件，其中```application.properties```为默认配置，也可以使用```yml```格式的文件，这个目录也可以在后面添加其他的配置文件 