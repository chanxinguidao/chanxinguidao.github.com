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

​		技术是服务于业务的，国内一线城市技术的不断迭代，架构不断升级，这没有问题，因为业务需要这样的架构。然而，在非一线城市的同样也有软件公司，尤其是一些体量庞大的体系类的，例如：银行、运营商、电力等他们追求的是稳定，一个项目往往都承载了非常多的业务和生产数据。并非轻易可改变，技术最终还是要能创造价值而非一味求新。毕竟从编程的发展来看新技术学习成本是越来越低，门槛也低。

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
				-SpringbootApplication
		-resouces
			- statics
			- templates
			- application.yml
	-test
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

最简单的方式 点击：

![启动]()

cd到项目主目录:

```
mvn clean  
mvn package  编译项目的jar
```

- mvn spring-boot: run 启动
- cd 到target目录，java -jar 项目.jar

## 来看看springboot在启动的时候为我们注入了哪些bean

在程序入口加入：

```
@SpringBootApplication
public class SpringbootFirstApplication {

	public static void main(String[] args) {
		SpringApplication.run(SpringbootFirstApplication.class, args);
	}

	@Bean
	public CommandLineRunner commandLineRunner(ApplicationContext ctx) {
		return args -> {

			System.out.println("Let's inspect the beans provided by Spring Boot:");

			String[] beanNames = ctx.getBeanDefinitionNames();
			Arrays.sort(beanNames);
			for (String beanName : beanNames) {
				System.out.println(beanName);
			}

		};
	}

}
```

程序输出：

> Let’s inspect the beans provided by Spring Boot: basicErrorController beanNameHandlerMapping beanNameViewResolver characterEncodingFilter commandLineRunner conventionErrorViewResolver defaultServletHandlerMapping defaultViewResolver dispatcherServlet dispatcherServletRegistration duplicateServerPropertiesDetector embeddedServletContainerCustomizerBeanPostProcessor error errorAttributes errorPageCustomizer errorPageRegistrarBeanPostProcessor

> …. ….

在程序启动的时候，springboot自动诸如注入了40-50个bean.

## 单元测试

通过@RunWith() @SpringBootTest开启注解：

```
@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class HelloControllerIT {

    @LocalServerPort
    private int port;

    private URL base;

    @Autowired
    private TestRestTemplate template;

    @Before
    public void setUp() throws Exception {
        this.base = new URL("http://localhost:" + port + "/");
    }

    @Test
    public void getHello() throws Exception {
        ResponseEntity<String> response = template.getForEntity(base.toString(),
                String.class);
        assertThat(response.getBody(), equalTo("Greetings from Spring Boot!"));
    }
}
```

运行它会先开启sprigboot工程，然后再测试，测试通过 ^.^

源码下载：<https://github.com/forezp/SpringBootLearning>

## 结语

市面上有很多springboot的书，有很多springboot的博客，为什么我还要写这样一个系列？到目前为止，我没有看过一本springboot的书，因为还没来得及看，看的都是官方指南，当然也参考了很多的博客，他们都写的非常的棒！在看官方指南和博客的时候，发现他们有很多不同之处，所以我打算写一个来源于官方，通过自己理解加整合写一个系列，所以取名叫《springboot 非官方教程》。我相信我写的可能跟其他人的写的会不太一样。另外，最主要的原因还是提高自己，怀着一个乐于分享的心，将自己的理解分享给更多需要的人。
