---
layout: post
title:  "SpringBoot教程第1篇：构建springboot工程"
date:   2019-05-25 10:32:26 +0200
categories: SpringBoot
category: SpringBoot
---
## 简介

spring boot 它的设计目的就是为例简化开发，开启了各种自动装配，你不想写各种配置文件，引入相关的依赖就能迅速搭建起一个web工程。它采用的是建立生产就绪的应用程序观点，优先于配置的惯例。

虽然{

​		可能你有很多理由不放弃SSM,SSH，但是当你一旦使用了springboot ,你会觉得一切变得简单了，配置变的简单了、编码变的简单了，部署变的简单了，感觉自己健步如飞，开发速度大大提高了。就好比，当你用了IDEA，你会觉得再也回不到Eclipse时代一样。另，本系列教程全部用的IDEA作为开发工具。

}

但是{

​		技术是服务于业务的，国内一线城市技术的不断迭代，架构不断升级，这没有问题，因为业务需要这样的架构。然而，在非一线城市的同样也有软件公司，尤其是一些体量庞大的体系类的，例如：银行、运营商、电力等他们追求的是稳定，一个项目往
往都承载了非常多的业务和生产数据。并非轻易可改变，技术最终还是要能创造价值而非一味求新。毕竟从编程的发展来看新技术学习成本是越来越低，门槛也低。

​		以上代表个人观点，毕竟去年还在一个成熟的EKP平台见证了struts1，历经了struts1，struts2，ssm，到目前的springboot。一路走来，技术越来越简单了，思维越来越重要。

}finally{

​		后面还有docker的持续集成等，更更更加简单。

}

## 建构工程

你需要：

- 15分钟
- jdk 1.8或以上
- maven 3.0+
- Idea

打开Idea-> new Project ->Spring Initializr ->填写group、artifact ->钩上web(开启web功能）->点下一步就行了。

## 工程目录

创建完工程，工程的目录结构如下：

```
- src
    -main
        -java
            -package
                #主函数，启动类，运行它如果运行了 Tomcat、Jetty、Undertow 等容器
                -SpringbootApplication	
        -resouces
            #存放静态资源 js/css/images 等
            - statics
            #存放 html 模板文件
            - templates
            #主要的配置文件，SpringBoot启动时候会自动加载application.yml/application.properties		
            - application.yml
    #测试文件存放目录		
    -test
 # pom.xml 文件是Maven构建的基础，里面包含了我们所依赖JAR和Plugin的信息
- pom
```

- pom文件为基本的依赖管理文件
- resouces 资源文件
  - statics 静态资源
  - templates 模板资源
  - application.yml 配置文件
- SpringbootApplication程序的入口。

pom.xml的依赖：

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
    <artifactId>springboot-first-application</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>springboot-first-application</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

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

其中spring-boot-starter-web不仅包含spring-boot-starter,还自动开启了web功能。

## 功能演示

闭着眼界创建一个行业界例行惯例：HelloWorld

建个controller：

```java
package com.example.springbootfirstapplication.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController   //等价于 @Controller + @ResponseBody
public class HelloWorldController {

    @GetMapping("/helloSpringBoot")   //等价于 @RequestMapping(method = {RequestMethod.GET})
    public String helloWorld(){
        return "helloWorld";
    }
}

```

启动SpringbootFirstApplication的main方法，打开浏览器[http://localhost:8080](http://localhost:8080),浏览器显示：

> helloWorld

### 神奇之处：

- 疯了吧
- 我干了什么
- web.xml呢？
- 说好的springmvc配置文件呢？
- 还有那个tomcat呢？
- 这一切都有的，只不过springboot帮我们都配置默认了！！

### 启动springboot 方式

最简单的方式：

![启动](https://chanxinguidao.github.io/assets/images/springboot/chapter1/2.png)

其他命（ZHUANG）令(BI)方式：

打开idea的Terminal（默认在项目文件夹中）:

可以输入：

```
mvn spring-boot: run
```

或者可以 

```
mvn clean  
mvn package  -f pom.xml
cd target
java -jar springboot-first-application-0.0.1-SNAPSHOT.jar
```

源码下载：<https://github.com/chanxinguidao/SpringBootLearning/tree/master/chapter1/>

## 结语

技术学习远不是十年前资源那么少了，现在网络上有大量非常优秀的资源，也因为是互联网的资源，也良莠不齐，并不能适应多数人。自己也没有那么多经历去一一甄别，毕竟还是要工作的。整理这内容，主要目的是自己的记录与学习，也同时让一些小伙伴有一个学习的渠道，本内容不作为任何的商业手段。独乐乐不如众乐乐，自己学习提升固然重要，也希望能分享出来，让其他人一起成长。
