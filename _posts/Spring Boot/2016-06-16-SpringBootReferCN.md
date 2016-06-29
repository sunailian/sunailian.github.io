---
layout: post
title: 《SpringBoot官方文档中文版翻译》第二章
categories: [SpringBoot]
description: SpringBoot官方文档
keywords: SpringBoot
autotoc: true
comments: true
---

《SpringBoot官方文档中文版翻译》第二章

---

##第二章 开始第一个SpringBoot工程
  这一章非常适合正在开始学习Spring Boot或者Spring的用户。这一章里会解答一些基础的“是什么？”、“怎么做？”以及“为什么？”之类的问题。你可以在安装的过程中对SpringBoot有一个总体的了解。然后我们会创建我们第一个Spring Boot项目，同时随着慢慢的深入，我们也会去讨论一些核心的机制和准则。

###1.Spring Boot介绍
  SpringBoot指导手册提供html、pdf和epub版本。用户可在<docs.spring.io/spring-boot/docs/current/reference>上下载最新的版本。

###2.Maven支持

以下是spring boot的一个典型的pom.xml配置：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.example</groupId>
  <artifactId>myproject</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <!-- Inherit defaults from Spring Boot -->
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.4.0.BUILD-SNAPSHOT</version>
  </parent>
  <!-- Add typical dependencies for a web application -->
  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
  </dependencies>
  <!-- Package as an executable jar -->
  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
  <!-- Add Spring repositories -->
  <!-- (you don't need this if you are using a .RELEASE version) -->
  <repositories>
    <repository>
      <id>spring-snapshots</id>
      <url>http://repo.spring.io/snapshot</url>
      <snapshots>
        <enabled>true</enabled>
      </snapshots>
    </repository>
    <repository>
      <id>spring-milestones</id>
      <url>http://repo.spring.io/milestone</url>
    </repository>
  </repositories>
  <pluginRepositories>
    <pluginRepository>
      <id>spring-snapshots</id>
      <url>http://repo.spring.io/snapshot</url>
    </pluginRepository>
    <pluginRepository>
      <id>spring-milestones</id>
      <url>http://repo.spring.io/milestone</url>
    </pluginRepository>
  </pluginRepositories>
</project>

```

###3.安装Spring Boot CLI
Spring Boot CLI是一个命令行工具。

**Mac下安装：**

```
$ brew tap pivotal/tap
$ brew install springboot
```

###4.开始第一个Spirng Boot工程

- 1. 创建pom文件：参看上面；
- 2. 添加依赖：

```xml
<dependencies>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
</dependencies>

```

- 3. 创建代码：

```Java

import org.springframework.boot.*;
import org.springframework.boot.autoconfigure.*;
import org.springframework.stereotype.*;
import org.springframework.web.bind.annotation.*;

@RestController
@EnableAutoConfiguration
public class Example {

  @RequestMapping("/")
  String home() {
    return "Hello World!";
  }

  public static void main(String[] args) throws Exception {
    SpringApplication.run(Example.class, args);
  }

}

```

**EnableAutoConfiguration**：开启springboot自动配置。

**SpringApplication**:main方法中的SpringApplication会启动整个应用；

- 4. 生成可执行的jar

可执行的jar包（也叫“胖jar”）需要在pom文件中添加`spring-boot-maven-plugin` 。

```xml
<build>
  <plugins>
    <plugin>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
    </plugin>
  </plugins>
</build>

```

执行`mvn package`可以打包jar。使用命令运行jar包：


```
$ jar tvf target/myproject-0.0.1-SNAPSHOT.jar
```
