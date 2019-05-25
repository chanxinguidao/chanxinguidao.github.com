---
layout: post
title:  "SpringCloud教程第2篇：Ribbon（负载均衡）"
date:   2019-05-25 13:25:35 +0200
categories: SpringCloud
category: SpringCloud
---
[在上一篇文章](https://chanxinguidao.github.io/springcloud/2019/05/20/SpringCloud%E6%95%99%E7%A8%8B%E7%AC%AC1%E7%AF%87-Eureka.html)，讲了服务的注册和发现。在微服务架构中，业务都会被拆分成一个独立的服务，服务与服务的通讯是基于http restful的。Spring cloud有两种服务调用方式，一种是ribbon+restTemplate，另一种是feign。在这一篇文章首先讲解下基于ribbon+rest。

## 一、ribbon简介

> Ribbon is a client side load balancer which gives you a lot of control over the behaviour of HTTP and TCP clients. Feign already uses Ribbon, so if you are using @FeignClient then this section also applies.
>
> —–摘自官网

ribbon是一个负载均衡客户端，可以很好的控制http和tcp的一些行为。Feign默认集成了ribbon。

ribbon 已经默认实现了这些配置bean：

- IClientConfig ribbonClientConfig: DefaultClientConfigImpl
- IRule ribbonRule: ZoneAvoidanceRule
- IPing ribbonPing: NoOpPing
- ServerList ribbonServerList: ConfigurationBasedServerList
- ServerListFilter ribbonServerListFilter: ZonePreferenceServerListFilter
- ILoadBalancer ribbonLoadBalancer: ZoneAwareLoadBalancer

## 二、准备工作

这一篇文章基于上一篇文章的工程(我这边直接复制，并且发在chapter2中,并且做了如下一些修改)

eureka-server

```xml
server.port=8761
eureka.client.register-with-eureka=false
eureka.client.fetch-registry=false
#服务端用second表示区分
server.servlet.context-path=/second

```



eureka-client

仅仅修改first为second（没有强迫症的，你们可以直接在上个章节的代码继续深入）

```yml
server:
  port: 8085
spring:
  application:
    name: eureka-client
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/second/eureka/

```

新建一个controller

```java
package com.example.eurekaclient.controller;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloCloudController {

    /**
     * 加个读取配置端口的参数，用于查看是否实现负载均衡
     */
    @Value("${server.port}")
    String port;

    @GetMapping("/hello")
    public String helloWorld(@RequestParam String name){
        return "hi "+ name + " , welcome to learning SpringCloud ! I am from port "+ port;
    }
}
```







1. 启动eureka-server 工程；
2. 启动eureka-client工程，它的端口为8086；
3. 利用 maven打个package，然后 使用 命令可以随意运行多个端口（springboot基础）

![](https://chanxinguidao.github.io/assets/images/springcloud/chapter2/1.png)

![](https://chanxinguidao.github.io/assets/images/springcloud/chapter2/2.png)



，这时你会发现：eureka-client在eureka-server注册了2个实例，这就相当于一个小的集群。访问[http://localhost:8761/second](http://localhost:8761/second)如图所示：

![](https://chanxinguidao.github.io/assets/images/springcloud/chapter2/3.png)

## 三、建一个服务消费者

重新新建一个spring-boot工程，取名为：service-ribbon; 代码如下：

操作如下图：

![](https://chanxinguidao.github.io/assets/images/springcloud/chapter2/4.png)



```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.5.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.example</groupId>
    <artifactId>service-ribbon</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>service-ribbon</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
        <spring-cloud.version>Greenwich.SR1</spring-cloud.version>
    </properties>

    <dependencies>
    	<!-- 再单独加这个，idea里面好像没这个选项 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>

```

在工程的配置文件指定服务的注册中心地址为http://localhost:8761/second/eureka/，程序名称为 eureka-ribbon，程序端口为8777。配置文件application.yml如下：

```yml
server:
  port: 8777
spring:
  application:
    name: eureka-ribbon
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/second/eureka
```

在工程的启动类中,通过@EnableDiscoveryClient向服务中心注册；并且向程序的ioc注入一个bean: restTemplate;并通过@LoadBalanced注解表明这个restRemplate开启负载均衡的功能。

```java
package com.example.serviceribbon;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.context.annotation.Bean;
import org.springframework.web.client.RestTemplate;

@EnableDiscoveryClient
@SpringBootApplication
public class ServiceRibbonApplication {

    public static void main(String[] args) {
        SpringApplication.run(ServiceRibbonApplication.class, args);
    }

    /**
     * @Bean ：初始化，可以被HelloService  @Autowired
     * @LoadBalanced ：表示启动负载均衡
     * @return
     */
    @Bean
    @LoadBalanced
    RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```

写一个测试类HelloService，通过之前注入ioc容器的restTemplate来消费eureka-client服务的“/hello”接口，在这里我们直接用的程序名替代了具体的url地址，在ribbon中它会根据服务名来选择具体的服务实例，根据服务实例在请求的时候会用具体的url替换掉服务名，代码如下：

```java
package com.example.serviceribbon.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;

@Service
public class HelloService {

    @Autowired
    RestTemplate restTemplate;

    public String hello(String name){
        return restTemplate.getForObject("http://eureka-client/hello?name="+name,String.class);    }
}

```

写一个controller，在controller中用调用HelloService 的方法，代码如下：

```
package com.example.serviceribbon.controller;

import com.example.serviceribbon.service.HelloService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @Autowired
    HelloService helloService;

    @GetMapping
    public String hello(@RequestParam String name){
        return helloService.hello(name);
    }
}

```

启动service-ribbon，刷新eureka-service可以看到：

![](https://chanxinguidao.github.io/assets/images/springcloud/chapter2/6.png)

在浏览器上多次访问[http://localhost:8777/hello?name=chanxinguidao](http://localhost:8777/hello?name=chanxinguidao)，浏览器交替显示：

> hi chanxinguidao , welcome to learning SpringCloud ! I am from port 8085
>
> hi chanxinguidao , welcome to learning SpringCloud ! I am from port 8088

这说明当我们通过调用restTemplate.getForObject(“http://eureka-client/hi?name=”+name,String.class)方法时，已经做了负载均衡，访问了不同的端口的服务实例。

## 四、此时的架构

![此时架构图.png](https://chanxinguidao.github.io/assets/images/springcloud/chapter2/7.png)

- 一个服务注册中心，eureka server,端口为8761
- eureka-client工程跑了两个实例，端口分别为8085 和 8088，分别向服务注册中心注册
- sercvice-ribbon端口为8777,向服务注册中心注册
- 当service-ribbon通过restTemplate调用eureka-client的hi接口时，因为用ribbon进行了负载均衡，会轮流的调用eureka-client：8085 和8088两个端口的hi接口；

源码下载：<https://github.com/chanxinguidao/SpringCloudLearning/tree/master/chapter2>
