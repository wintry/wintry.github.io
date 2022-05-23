---
layout: post
title: "springboot打包jar时复制配置文件，打包docker复制配置文件"
date: 2022-05-23 18:38:00 +0800 
categories: springboot之路
tag: [build,docker]
---
* content
{:toc}

## springboot打包jar时复制配置文件

<!-- more -->
<!-- TOC -->
### pom.xml配置
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-resources-plugin</artifactId>
    <version>2.5</version>
    <executions>
        <execution>
            <id>copy-resources</id>
            <phase>package</phase>
            <goals>
                <goal>copy-resources</goal>
            </goals>
            <configuration>
            <encoding>UTF-8</encoding>
            <outputDirectory>${project.build.directory}</outputDirectory>
            <resources>
                <resource>
                    <directory>./src/main/resources</directory>
                    <includes>
                        <include>application.properties</include>
                    </includes>
                </resource>
            </resources>
        </configuration>
    </execution>
    </executions>
</plugin>
```

## 打包docker复制配置文件
### pom.xml配置  
```xml
<plugin>
    <groupId>com.spotify</groupId>
    <artifactId>docker-maven-plugin</artifactId>
    <version>0.4.13</version>
    <configuration>
        <imageName>${imageName}:${imageVersion}</imageName>
        <dockerDirectory>src/main/docker</dockerDirectory>
        <resources>
            <resource>
                <targetPath>/</targetPath>
                <directory>${project.build.directory}</directory>
                <include>${project.build.finalName}.jar</include>
            </resource>
            <resource>
                <targetPath>/</targetPath>
                <directory>${project.build.directory}</directory>
                <include>application.properties</include>
            </resource>
        </resources>
    </configuration>
</plugin>
```


