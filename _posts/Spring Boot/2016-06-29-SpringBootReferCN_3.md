---
layout: post
title: 《SpringBoot官方文档中文版翻译》第三章
categories: [SpringBoot]
description: SpringBoot官方文档
keywords: SpringBoot
autotoc: true
comments: true
---

《SpringBoot官方文档中文版翻译》第三章、使用Spring Boot

---

##第三章、使用Spring Boot

###1.继承starter parent

```xml
<!-- Inherit defaults from Spring Boot -->
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>1.4.0.BUILD-SNAPSHOT</version>
</parent>

```

可以自己覆盖默认的version。比如去修改Spring Data的版本：

```xml
<properties>
  <spring-data-releasetrain.version>Fowler-SR2</spring-data-releasetrain.version>
</properties>

```
可以查看[spring-boot-dependencies pom](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-dependencies/pom.xml)各种版本。

###2.不使用parent

当然可以不继承`spring-boot-starter-parent`。而且你仍然可以享受`dependency management `带来的好处，通过使用scope=import：

```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <!-- Import dependency management from Spring Boot -->
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-dependencies</artifactId>
      <version>1.4.0.BUILD-SNAPSHOT</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>

```
这种配置就不允许用户去使用上面提到的方法去修改个体的版本号。如果要修改版本，必须在dependencyManagement下spring-boot-dependencies所在的dependency之前增加`<dependency>`：

```xml
<dependencyManagement>
  <dependencies>
    <!-- Override Spring Data release train provided by Spring Boot -->
    <dependency>
      <groupId>org.springframework.data</groupId>
      <artifactId>spring-data-releasetrain</artifactId>
      <version>Fowler-SR2</version>
      <scope>import</scope>
      <type>pom</type>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-dependencies</artifactId>
      <version>1.4.0.BUILD-SNAPSHOT</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>

```

###3.修改java版本

```xml
<properties>
  <java.version>1.8</java.version>
</properties>

```

###4.使用Spring Boot Maven plugin
Spring Boot包含了Maven plugin，使得项目可以打包成可执行的jar。在<plugins>里面加上配置就行：

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
