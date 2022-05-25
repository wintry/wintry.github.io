---
layout: original
title: "springboot setting up "
date: 2018-01-12 15:25:00 +0800 
categories: springboot之路
tags: [springboot]
---
* content
{:toc}

这是记录spring boot开发的博客，一为分享，二为自己查询用。   
本文源码在[github](https://github.com/wintry/LearnSpringBoot/tree/master/springboot-1)，欢迎star、fork

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
4.选择依赖，为了便于学习，这里不做选择（后面可以添加），熟悉后可以在这里就选择，SpringBoot版本也在这里选择，本文选择1.5.9
![](/img_blog/spring-boot/2018-01-12-4.png)
5.选择存储路径
![](/img_blog/spring-boot/2018-01-12-5.png)
## 项目结构解析
![](/img_blog/spring-boot/2018-01-12-6.png)  
+ ```src/main/java/com.{groupid}.{projectname}``` 项目的java包，代码也主要在这里编写，该目录下的```{projectname}Application```为项目的执行文件  
+ ```src/main/resources``` 项目的配置文件，其中```application.properties```为默认配置，也可以使用```yml```格式的文件，这个目录也可以在后面添加其他的配置文件  

## 引入web模块依赖  
当前项目只有2个模块,```pom.xml```内容如下：  
+ ```spring-boot-starter```  核心模块，各种自动配置
+ ```spring-boot-starter-test``` 测试模块  

```xml
<dependencies>
	<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
	</dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```
2个模块的版本记录在：
```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.9.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```
在这里修改可以调整SpringBoot的版本  

引入Web模块，需添加```spring-boot-starter-web```依赖：
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```
这里Idea会提示导入依赖  
![](/img_blog/spring-boot/2018-01-12-7.png)  
选择导入或者自动导入（以后```pom.xml```有修改就会自动导入）都没有问题  

## 编写HelloWorld  
2. 创建```package```命名为```com.{groupid}.{projectname}.controller``` 
2. 创建```HelloController```类，内容如下  

```java
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @RequestMapping("/hello")
    public String index() {
        return "Hello World";
    }

}
```  

启动```{projectname}Application```,打开浏览器访问```http://localhost:8080/hello```，可以看到页面输出```Hello World```