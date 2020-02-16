---
layout: post
title: "Spring Boot Actuator"
date: 2020-02-16 11:00:00 +0800 
categories: springboot之路
tag: [actuator]
---
* content
{:toc}

Spring Boot Actuator监控端点小结

<!-- more -->
<!-- TOC -->
## 初步认识Actuator
在现有的Spring Boot应用中引入该模块非常简单，只需要在pom.xml的dependencies节点中，新增spring-boot-starter-actuator的依赖即可，具体如下：
```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```
重新启动后，在控制台看到如下输出： 
```
INFO 1264 --- [nio-8080-exec-3] s.b.a.e.m.MvcEndpointSecurityInterceptor : Full authentication is required to access actuator endpoints. Consider adding Spring Security or set 'management.security.enabled' to false.
```
根据提示，在配置文件中添加management.security.enabled=false
重新启动后，如下  
![](/img_blog/spring-boot/2020-02-16-1.png)  

上图显示了一批端点定义，这些端点并非我们自己在程序中创建，而是由spring-boot-starter-actuator模块根据应用依赖和配置自动创建出来的监控和管理端点。通过这些端点，我们可以实时的获取应用的各项监控指标，比如：访问/health端点，我们可以获得如下返回的应用健康信息：
![](/img_blog/spring-boot/2020-02-16-2.png)  

## 原生端点 
原生端点分为三大类：
* 应用配置类：获取应用程序中加载的应用配置、环境变量、自动化配置报告等与Spring Boot应用密切相关的配置类信息。
- 度量指标类：获取应用程序运行过程中用于监控的度量指标，比如：内存信息、线程池信息、HTTP请求统计等。
* 操作控制类：提供了对应用的关闭等操作类功能。 

## 应用配置类  
应用配置类端点可以帮助我们轻松的获取一系列关于Spring 应用配置内容的详细报告，比如：自动化配置的报告、Bean创建的报告、环境属性的报告等。  
* /autoconfig：该端点用来获取应用的自动化配置报告，其中包括所有自动化配置的候选项。同时还列出了每个候选项自动化配置的各个先决条件是否满足。所以，该端点可以帮助我们方便的找到一些自动化配置为什么没有生效的具体原因。该报告内容将自动化配置内容分为两部分：
    * positiveMatches中返回的是条件匹配成功的自动化配置
    - negativeMatches中返回的是条件匹配不成功的自动化配置
``` json
{
    "positiveMatches": { // 条件匹配成功的
        "EndpointWebMvcAutoConfiguration": [
            {
                "condition": "OnClassCondition",
                "message": "@ConditionalOnClass classes found: javax.servlet.Servlet,org.springframework.web.servlet.DispatcherServlet"
            },
            {
                "condition": "OnWebApplicationCondition",
                "message": "found web application StandardServletEnvironment"
            }
        ],
        ...
    },
    "negativeMatches": {  // 条件不匹配成功的
        "HealthIndicatorAutoConfiguration.DataSourcesHealthIndicatorConfiguration": [
            {
                "condition": "OnClassCondition",
                "message": "required @ConditionalOnClass classes not found: org.springframework.jdbc.core.JdbcTemplate"
            }
        ],
        ...
    }
}
```

* /beans：该端点用来获取应用上下文中创建的所有Bean。
```json
[
    {
        "context": "application",
        "parent": null,
        "beans": [
            {
                "bean": "springbootscaffoldApplication",
                "aliases": [],
                "scope": "singleton",
                "type": "com.wkzd.springbootscaffold.SpringbootscaffoldApplication$$EnhancerBySpringCGLIB$$d9719d42",
                "resource": "null",
                "dependencies": []
            },
            ...
        ]
    }
]
```

* /configprops：该端点用来获取应用中配置的属性信息报告。从下面该端点返回示例的片段中，我们看到返回了关于该短信的配置信息，prefix属性代表了属性的配置前缀，properties代表了各个属性的名称和值。所以，我们可以通过该报告来看到各个属性的配置路径，比如我们要关闭该端点，就可以通过使用endpoints.configprops.enabled=false来完成设置。  
```json
{
 "diskSpaceHealthIndicatorProperties": {
        "prefix": "management.health.diskspace",
        "properties": {
            "path": "D:\\+work\\SpringBootScaffold\\.",
            "threshold": 10485760
        }
    },
    ...
}
``` 

* /env：该端点与/configprops不同，它用来获取应用所有可用的环境属性报告。包括：环境变量、JVM属性、应用的配置配置、命令行中的参数。从下面该端点返回的示例片段中，我们可以看到它不仅返回了应用的配置属性，还返回了系统属性、环境变量等丰富的配置信息，其中也包括了应用还没有没有使用的配置。所以它可以帮助我们方便地看到当前应用可以加载的配置信息，并配合@ConfigurationProperties注解将它们引入到我们的应用程序中来进行使用。另外，为了配置属性的安全，对于一些类似密码等敏感信息，该端点都会进行隐私保护，但是我们需要让属性名中包含：password、secret、key这些关键词，这样该端点在返回它们的时候会使用*来替代实际的属性值。  
```json
{
    "profiles": [],
    "server.ports": {
        "local.server.port": 8080
    },
    "servletContextInitParams": {},
    "systemProperties": {
        "java.runtime.name": "Java(TM) SE Runtime Environment",
        "spring.output.ansi.enabled": "always",
        "sun.boot.library.path": "C:\\Program Files\\Java\\jdk1.8.0_241\\jre\\bin",
        "java.vm.version": "25.241-b07",
        "java.vm.vendor": "Oracle Corporation",
        "java.vendor.url": "http://java.oracle.com/",
        "java.rmi.server.randomIDs": "true",
        "path.separator": ";",
        "java.vm.name": "Java HotSpot(TM) 64-Bit Server VM",
        "file.encoding.pkg": "sun.io",
        "user.country": "CN",
        "user.script": "",
        "sun.java.launcher": "SUN_STANDARD",
        ...}
}
```
* /mappings：该端点用来返回所有Spring MVC的控制器映射关系报告。从下面的示例片段中，我们可以看该报告的信息与我们在启用Spring MVC的Web应用时输出的日志信息类似，其中bean属性标识了该映射关系的请求处理器，method属性标识了该映射关系的具体处理类和处理函数。  

```json
{
    "/webjars/**": {
        "bean": "resourceHandlerMapping"
    },
    "/**": {
        "bean": "resourceHandlerMapping"
    },
    "/**/favicon.ico": {
        "bean": "faviconHandlerMapping"
    },
    "{[/hello]}": {
        "bean": "requestMappingHandlerMapping",
        "method": "public java.lang.String com.wkzd.springbootscaffold.controller.HelloController.index()"
    },
    ...
}
```
* /info：该端点用来返回一些应用自定义的信息。默认情况下，该端点只会返回一个空的json内容。我们可以在application.properties配置文件中通过info前缀来设置一些属性，比如下面这样：
``` 
info.app.name=谋杀复读机的测试
```
再访问/info端点，我们可以得到下面的返回报告，其中就包含了上面我们在应用自定义的两个参数。 

![](/img_blog/spring-boot/2020-02-16-3.png)


## 度量指标类
上面我们所介绍的应用配置类端点所提供的信息报告在应用启动的时候都已经基本确定了其返回内容，可以说是一个静态报告。而度量指标类端点提供的报告内容则是动态变化的，这些端点提供了应用程序在运行过程中的一些快照信息，比如：内存使用情况、HTTP请求统计、外部资源指标等。这些端点对于我们构建微服务架构中的监控系统非常有帮助，由于Spring Boot应用自身实现了这些端点，所以我们可以很方便地利用它们来收集我们想要的信息，以制定出各种自动化策略。下面，我们就来分别看看这些强大的端点功能。  
* /metrics：该端点用来返回当前应用的各类重要度量指标，比如：内存信息、线程信息、垃圾回收信息等。
 
```json
{
    "mem": 358753,
    "mem.free": 162466,
    "processors": 6,
    "instance.uptime": 257280,
    "uptime": 259472,
    "systemload.average": -1,
    "heap.committed": 316416,
    "heap.init": 262144,
    "heap.used": 153949,
    "heap": 3714048,
    "nonheap.committed": 43328,
    "nonheap.init": 2496,
    "nonheap.used": 42338,
    "nonheap": 0,
    "threads.peak": 31,
    "threads.daemon": 23,
    "threads.totalStarted": 43,
    "threads": 25,
    "classes": 5996,
    "classes.loaded": 5996,
    "classes.unloaded": 0,
    "gc.ps_scavenge.count": 4,
    "gc.ps_scavenge.time": 30,
    "gc.ps_marksweep.count": 1,
    "gc.ps_marksweep.time": 23,
    "httpsessions.max": -1,
    "httpsessions.active": 0,
    "gauge.response.info": 3,
    "counter.status.200.info": 2
}
```

* 系统信息：包括处理器数量processors、运行时间uptime和instance.uptime、系统平均负载systemload.average。
- mem.*：内存概要信息，包括分配给应用的总内存数量以及当前空闲的内存数量。这些信息来自java.lang.Runtime。
* heap.*：堆内存使用情况。这些信息来自java.lang.management.MemoryMXBean接口中getHeapMemoryUsage方法获取的java.lang.management.MemoryUsage。
- nonheap.*：非堆内存使用情况。这些信息来自java.lang.management.MemoryMXBean接口中getNonHeapMemoryUsage方法获取的java.lang.management.MemoryUsage。
* threads.*：线程使用情况，包括线程数、守护线程数（daemon）、线程峰值（peak）等，这些数据均来自java.lang.management.ThreadMXBean。
- classes.*：应用加载和卸载的类统计。这些数据均来自java.lang.management.ClassLoadingMXBean。
* gc.*：垃圾收集器的详细信息，包括垃圾回收次数gc.ps_scavenge.count、垃圾回收消耗时间gc.ps_scavenge.time、标记-清除算法的次数gc.ps_marksweep.count、标记-清除算法的消耗时间gc.ps_marksweep.time。这些数据均来自java.lang.management.GarbageCollectorMXBean。
- httpsessions.*：Tomcat容器的会话使用情况。包括最大会话数httpsessions.max和活跃会话数httpsessions.active。该度量指标信息仅在引入了嵌入式Tomcat作为应用容器的时候才会提供。
* gauge.*：HTTP请求的性能指标之一，它主要用来反映一个绝对数值。比如上面示例中的gauge.response.info: 3，它表示上一次info请求的延迟时间为3毫秒。
- counter.*：HTTP请求的性能指标之一，它主要作为计数器来使用，记录了增加量和减少量。如上示例中counter.status.200.info: 2，它代表了info请求返回200状态的次数为2。  
对于gauge.*和counter.*的统计，这里还有有一个特殊的内容请求star-star没有出现，它代表了对静态资源的访问。这两类度量指标非常有用，我们可以使用它默认的统计指标。  
/metrics端点可以提供应用运行状态的完整度量指标报告，这项功能非常的实用，但是对于监控系统中的各项监控功能，它们的监控内容、数据收集频率都有所不同，如果我们每次都通过全量获取报告的方式来收集，略显粗暴。所以，我们还可以通过/metrics/{name}接口来更细粒度的获取度量信息，比如我们可以通过访问/metrics/mem.free来获取当前可用内存数量。  

* /health：该端点在一开始的示例中我们已经使用过了，它用来获取应用的各类健康指标信息。在spring-boot-starter-actuator模块中自带实现了一些常用资源的健康指标检测器。  

* /dump：该端点用来暴露程序运行中的线程信息。它使用java.lang.management.ThreadMXBean的dumpAllThreads方法来返回所有含有同步信息的活动线程详情。

* /trace：该端点用来返回基本的HTTP跟踪信息。默认情况下，跟踪信息的存储采用org.springframework.boot.actuate.trace.InMemoryTraceRepository实现的内存方式，始终保留最近的100条请求记录。它记录的内容格式如下：  
```json
[
    {
        "timestamp": 1581841909326,
        "info": {
            "method": "GET",
            "path": "/dump",
            "headers": {
                "request": {
                    "host": "127.0.0.1:8080",
                    "connection": "keep-alive",
                    "upgrade-insecure-requests": "1",
                    "user-agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/79.0.3945.130 Safari/537.36",
                    "sec-fetch-user": "?1",
                    "accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9",
                    "sec-fetch-site": "none",
                    "sec-fetch-mode": "navigate",
                    "accept-encoding": "gzip, deflate, br",
                    "accept-language": "zh-CN,zh;q=0.9,en;q=0.8,zh-TW;q=0.7,ja;q=0.6"
                },
                "response": {
                    "X-Application-Context": "application",
                    "Content-Type": "application/vnd.spring-boot.actuator.v1+json;charset=UTF-8",
                    "Transfer-Encoding": "chunked",
                    "Date": "Sun, 16 Feb 2020 08:31:49 GMT",
                    "status": "200"
                }
            },
            "timeTaken": "21"
        }
    },
    ...
]
```

## 操作控制类
仔细的读者可能会发现，我们在“初步认识Actuator”时运行示例的控制台中输出的所有监控端点，已经在介绍应用配置类端点和度量指标类端点时都讲解完了。那么还有哪些是操作控制类端点呢？实际上，由于之前介绍的所有端点都是用来反映应用自身的属性或是运行中的状态，相对于操作控制类端点没有那么敏感，所以他们默认都是启用的。而操作控制类端点拥有更强大的控制能力，如果要使用它们的话，需要通过属性来配置开启。

在原生端点中，只提供了一个用来关闭应用的端点：/shutdown。我们可以通过如下配置开启它：
```
endpoints.shutdown.enabled=true
```
在配置了上述属性之后，只需要访问该应用的/shutdown端点就能实现关闭该应用的远程操作。