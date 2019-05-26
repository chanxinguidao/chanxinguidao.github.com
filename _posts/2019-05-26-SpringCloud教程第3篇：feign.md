---
layout: post
title:  "SpringCloud教程第3篇：Feign（负载均衡）"
date:   2019-05-26 11:29:35 +0200
categories: SpringCloud
category: SpringCloud
---
[上一篇文章](https://chanxinguidao.github.io/springcloud/2019/05/25/SpringCloud%E6%95%99%E7%A8%8B%E7%AC%AC2%E7%AF%87-Ribbon.html)，讲述了如何通过RestTemplate+Ribbon去消费服务，这篇文章主要讲述如何通过Feign去消费服务。

## 一、Feign简介

Feign是一个声明式的伪Http客户端，它使得写Http客户端变得更简单。使用Feign，只需要创建一个接口并注解。它具有可插拔的注解特性，可使用Feign 注解和JAX-RS注解。Feign支持可插拔的编码器和解码器。Feign默认集成了Ribbon，并和Eureka结合，默认实现了负载均衡的效果。

简而言之：

- Feign 采用的是基于接口的注解
- Feign 整合了ribbon

## 二、准备工作

继续用上一节的工程， 启动eureka-server，端口为8761; 启动service-hi 两次，端口分别为8762 、8773.

## 三、创建一个feign的服务

新建一个spring-boot工程，取名为service-feign，在它的pom文件引入Feign的起步依赖spring-cloud-starter-feign、Eureka的起步依赖spring-cloud-starter-eureka、Web的起步依赖spring-boot-starter-web，代码如下：

```xml
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
    <artifactId>service-feign</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>service-feign</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
        <spring-cloud-services.version>2.1.4.RELEASE</spring-cloud-services.version>
        <spring-cloud.version>Greenwich.SR1</spring-cloud.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
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
            <dependency>
                <groupId>io.pivotal.spring.cloud</groupId>
                <artifactId>spring-cloud-services-dependencies</artifactId>
                <version>${spring-cloud-services.version}</version>
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

在工程的配置文件application.yml文件，指定程序名为service-feign，端口号为8787，服务注册地址为http://localhost:8761/eureka/ ，代码如下：

```yml
server:
  port: 8787
spring:
  application:
    name: service-feign
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka

```

在程序的启动类ServiceFeignApplication ，加上@EnableFeignClients注解开启Feign的功能：

```java
package com.example.servicefeign.controller;
import com.example.servicefeign.service.FeigenService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.openfeign.EnableFeignClients;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@EnableDiscoveryClient
@EnableFeignClients
@RestController
public class FeignController {

    @Autowired
    FeigenService feigenService;

    @GetMapping("/helloFeign")
    public String hello(@RequestParam String name){
        return feigenService.helloWorld(name);
    }
}

```

定义一个feign接口，通过@ FeignClient（“服务名”），来指定调用哪个服务。比如在代码中调用了service-hi服务的“/hi”接口，代码如下：

```java
package com.example.servicefeign.service;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;

@FeignClient("eureka-client")
public interface FeigenService {

    @GetMapping("/hello")
    String helloWorld(@RequestParam String name);
}

```

在Web层的controller层，对外暴露一个”/hi”的API接口，通过上面定义的Feign客户端SchedualServiceHi 来消费服务。代码如下：

```
package com.example.servicefeign.controller;
import com.example.servicefeign.service.FeigenService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.openfeign.EnableFeignClients;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@EnableDiscoveryClient
@EnableFeignClients
@RestController
public class FeignController {

    @Autowired
    FeigenService feigenService;

    @GetMapping("/helloFeign")
    public String hello(@RequestParam String name){
        return feigenService.helloWorld(name);
    }
}

```

启动程序，多次访问[http://localhost:8787/helloFeign?name=springcloud](http://localhost:8787/helloFeign?name=springcloud),浏览器交替显示：

> hi springcloud , welcome to learning SpringCloud ! I am from port 8086
>
> hi springcloud , welcome to learning SpringCloud ! I am from port 8085

本文源码下载：<https://github.com/chanxinguidao/SpringCloudLearning/tree/master/chapter3>
