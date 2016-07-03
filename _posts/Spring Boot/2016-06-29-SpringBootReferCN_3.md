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

可以自己去覆盖默认的version。比如去修改Spring Data的版本：

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

###5.不推荐使用的default package
Spring Boot不推荐使用`default package`，因为这会导致使用注解出现问题，比如在使用**@ComponentScan**、**@EntityScan**或者 **@SpringBootApplication**。

###6.创建main application类

建议将main application类放到程序的**根目录**下。一般main application类都会需要**@EnableAutoConfiguration**注解。

典型的main application结构：

```
com
 +- example
     +- myproject
         +- Application.java
         |
         +- domain
         |   +- Customer.java
         |   +- CustomerRepository.java
         |
         +- service
         |   +- CustomerService.java
         |
         +- web
             +- CustomerController.java
```

Application.java类如下：

```java
@Configuration
@EnableAutoConfiguration
@ComponentScan
public class Application {


    public static void main(String[] args){
        SpringApplication.run(Application.class,args);
    }
}

```

###7.Auto-configuration自动配置
Auto-configuration会尝试在依赖的jar包的基础上自动去配置应用。比如你的classpath上有HSQLDB，那么你不需要手工去配置任何的数据库连接bean，springboot会自动配置一个内存数据库。

你可以在拥有**@Configuration**注解的类上面使用**@EnableAutoConfiguration**或者**@SpringBootApplication**注解去完成自动配置。

> 你只需要配置一个**@EnableAutoConfiguration**注解就可以。一般推荐你加在第一个拥有**@Configuration**注解的类上面。

####7.1 逐渐替代Auto-configuration
Auto-configuration是非侵入式的，你可以在任何地方定义你自己的configuration去替换Auto-configuration里某些具体的配置。

如果你想看目前正在用到哪些配置，你可以使用`--debug`来查看。

####7.2 禁用某个Auto-configuration

如果想禁用某个配置，可以使用`exclude`属性去禁用：

```java
@Configuration
@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
public class MyConfiguration {

}

```

如果该类不在classpath上，可以使用`excludeName`
