---
layout: post
title: "mybatis多数据源配置"
date: 2022-04-15 16:29:00 +0800 
categories: springboot之路
tags: [mybatis]
---
* content
{:toc}

Spring Boot中mybatis多数据源

<!-- more -->
<!-- TOC -->
## application.properties配置
```
spring.datasource.source.jdbc-url=jdbc:mysql://10.1.49.223:3307/case?allowMultiQueries=true&useUnicode=true&characterEncoding=UTF-8&serverTimezone=GMT%2B8
spring.datasource.source.username=root
spring.datasource.source.password=Cetc52@pwd
spring.datasource.source.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.slave.jdbc-url=jdbc:oracle:thin:@26.84.1.47:1521:hlbejzdb
spring.datasource.slave.username=gx_zfjd
spring.datasource.slave.password=gx_zfjd
spring.datasource.slave.driver-class-name=oracle.jdbc.OracleDriver
```
注意，配置中的数据源连接 url 末尾使用的是 jdbc-url

## 配置  
```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.jdbc.DataSourceBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;

import javax.sql.DataSource;

@Configuration
public class DataSourceConfiguration {

    @Primary
    @Bean
    @ConfigurationProperties(prefix = "spring.datasource.source")
    public DataSource sourceDataSource() {
        return DataSourceBuilder.create().build();
    }


    @Bean
    @ConfigurationProperties(prefix = "spring.datasource.slave")
    public DataSource slaveDataSource() {
        return DataSourceBuilder.create().build();
    }
}
```   
主数据库配置
```java
import org.apache.ibatis.session.SqlSessionFactory;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.SqlSessionTemplate;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.sql.DataSource;

@Configuration
@MapperScan(
        basePackages = "com.xxx.mapper",
        sqlSessionFactoryRef = "sqlSessionFactoryPrimary",
        sqlSessionTemplateRef = "sqlSessionTemplatePrimary")
public class SourceConfig {

    private DataSource sourceDataSource;

    public SourceConfig(@Qualifier("sourceDataSource") DataSource primaryDataSource) {
        this.sourceDataSource = primaryDataSource;
    }

    @Bean
    public SqlSessionFactory sqlSessionFactoryPrimary() throws Exception {
        SqlSessionFactoryBean bean = new SqlSessionFactoryBean();
        bean.setDataSource(sourceDataSource);
        return bean.getObject();
    }

    @Bean
    public SqlSessionTemplate sqlSessionTemplatePrimary() throws Exception {
        return new SqlSessionTemplate(sqlSessionFactoryPrimary());
    }

}
```  
从数据库配置
```java
import org.apache.ibatis.session.SqlSessionFactory;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.SqlSessionTemplate;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.sql.DataSource;

@Configuration
@MapperScan(
        basePackages = "com.xxxxxx.xxxx.mapper",
        sqlSessionFactoryRef = "slaveSqlSessionFactorySecondary",
        sqlSessionTemplateRef = "SlaveSessionTemplateSecondary")
public class SlaveConfig {

    private DataSource slaveDataSource;

    public SlaveConfig(@Qualifier("slaveDataSource") DataSource secondaryDataSource) {
        this.slaveDataSource = secondaryDataSource;
    }

    @Bean
    public SqlSessionFactory slaveSqlSessionFactorySecondary() throws Exception {
        SqlSessionFactoryBean bean = new SqlSessionFactoryBean();
        bean.setDataSource(slaveDataSource);
        return bean.getObject();
    }

    @Bean
    public SqlSessionTemplate SlaveSessionTemplateSecondary() throws Exception {
        return new SqlSessionTemplate(slaveSqlSessionFactorySecondary());
    }

}
```


