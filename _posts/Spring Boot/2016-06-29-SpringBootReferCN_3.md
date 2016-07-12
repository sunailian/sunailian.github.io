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
SpringBoot自动配置（auto-configuration）尝试根据你添加的jar依赖自动配置你的Spring应用。例如，如果你的classpath下存在HSQLDB，并且你没有手动配置任何数据库连接beans，那么我们将自动配置一个内存型（in-memory）数据库。

你可以通过将@EnableAutoConfiguration或@SpringBootApplication注解添加到一个@Configuration类上来选择自动配置。

>注：你只需要添加一个**@EnableAutoConfiguration**注解。我们建议你将它添加到**主@Configuration**类上。


####7.1 逐渐替代Auto-configuration
自动配置是非侵占性的，任何时候你都可以定义自己的配置类来替换自动配置的特定部分。例如，如果你添加自己的DataSourcebean，默认的内嵌数据库支持将不被考虑。

如果需要找出当前应用了哪些自动配置及应用的原因，你可以使用`--debug`开关启动应用。这将会记录一个自动配置的报告并输出到控制台。

####7.2 禁用某个Auto-configuration

如果发现应用了你不想要的特定自动配置类，你可以使用`@EnableAutoConfiguration`注解的**排除属性**来禁用它们。

```java
@Configuration
@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
public class MyConfiguration {

}

```

###8.SpringBeans和依赖注入

你可以自由地使用任何标准的Spring框架技术去定义beans和它们注入的依赖。简单起见，我们经常使用**@ComponentScan**注解搜索beans，并结合@Autowired构造器注入。

如果使用上面建议的结构组织代码（将应用类放到根包下），你可以添加@ComponentScan注解而不需要任何参数。你的所有应用程序组件（**@Component,@Service,@Repository,@Controller**等）将被自动注册为SpringBeans。

下面是一个**@ServiceBean**的示例，它使用构建器注入获取一个需要的RiskAssessorbean。

```java
packagecom.example.service;
importorg.springframework.beans.factory.annotation.Autowired;
importorg.springframework.stereotype.Service;
@Service
public class DatabaseAccountService implements AccountService{

  private final RiskAssessor riskAssessor;

  @Autowired
  public DatabaseAccountService(RiskAssessor riskAssessor){
    this.riskAssessor = riskAssessor;
  }
  //...
}

```

> **注：注意如何使用构建器注入来允许riskAssessor字段被标记为final，这意味着riskAssessor后续是不能改变的。**

###9.使用@SpringBootApplication注解

很多SpringBoot开发者总是使用`@Configuration，@EnableAutoConfiguration`和`@ComponentScan`注解他们的main类。由于这些注解被如此频繁地一块使用（特别是你遵循以上最佳实践时），SpringBoot提供一个方便的`@SpringBootApplication`选择。

该`@SpringBootApplication`注解等价于以默认属性使用`@Configuration，@EnableAutoConfiguration和@ComponentScan`。

```java
packagecom.example.myproject;
importorg.springframework.boot.SpringApplication;
importorg.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication//same as @Configuration @EnableAutoConfiguration @ComponentScan
public class Application{

  public static void main(String[]args){
    SpringApplication.run(Application.class,args);
  }
}

```

###10.运行应用程序

将应用打包成jar并使用一个内嵌HTTP服务器的一个最大好处是，你可以像其他方式那样运行你的应用程序。调试SpringBoot应用也很简单；你不需要任何特殊IDE或扩展。

>**注：本章节只覆盖基于jar的打包，如果选择将应用打包成war文件，你最好参考一下服务器和IDE文档。**

####10.1 作为一个打包后的应用运行

如果使用SpringBootMaven或Gradle插件创建一个可执行jar，你可以使用java-jar运行你的应用。例如：

```
$java -jar target/myproject-0.0.1-SNAPSHOT.jar
```

运行一个打包的程序并开启**远程调试支持**是可能的，这允许你将调试器附加到打包的应用程序上：

```
$java -Xdebug -Xrunjdwp:server=y,transport=dt_socket,address=8000,suspend=n\-jartarget/myproject-0.0.1-SNAPSHOT.jar
```

###10.2 使用Maven插件运行

SpringBootMaven插件包含一个run目标，它可以用来快速编译和运行应用程序。应用程序以一种暴露的方式运行，由于即时"热"加载，你可以编辑资源。

```
$mvn spring-boot:run
```
你可能想使用有用的操作系统环境变量：

```
$export MAVEN_OPTS=-Xmx1024m -XX:MaxPermSize=128M -Djava.security.egd=file:/dev/./urandom
```
("egd"设置是通过为Tomcat提供一个更快的会话keys熵源来加速Tomcat的。)


###11.打包用于生产的应用程序

可执行jars可用于生产部署。由于它们是自包含的，非常适合基于云的部署。关于其他“生产准备”的特性，比如健康监控，审计和指标REST，或JMX端点，可以考虑添加`spring-boot-actuator`。具体参考PartV,“SpringBootActuator:Production-readyfeatures”。

###12.SpringApplication

SpringApplication类提供了一种从main()方法启动Spring应用的便捷方式。在很多情况下，你只需委托给SpringApplication.run这个静态方法：

```java
public static void main(String[]args){
  SpringApplication.run(MySpringConfiguration.class,args);
}

```

当应用启动时，默认情况下会显示INFO级别的日志信息，包括一些相关的启动详情，比如启动应用的用户等。

####12.1 自定义Banner

通过在classpath下添加一个banner.txt或设置banner.location来指定相应的文件可以改变启动过程中打印的banner。如果这个文件有特殊的编码，你可以使用banner.encoding设置它（默认为UTF-8）。

在banner.txt中可以使用如下的变量：

变量    |    描述
--------| -----:|
${application.version}  |MANIFEST.MF中声明的应用版本号，例如1.0
${application.formatted-version} |MANIFEST.MF中声明的被格式化后的应用版本号（被括号包裹且以v作为前缀），用于显示，例如(v1.0)
${spring-boot.version}     |正在使用的SpringBoot版本号，例如1.2.2.BUILD-SNAPSHOT
${spring-boot.formatted-version}  |正在使用的SpringBoot被格式化后的版本号（被括号包裹且以v作为前缀）,用于显示，例如(v1.2.2.BUILD-SNAPSHOT)

>注：如果想以编程的方式产生一个banner，可以使用 **SpringBootApplication.setBanner(...)** 方法。使用`org.springframework.boot.Banner`接口，实现你自己的printBanner()方法

####12.2 自定义SpringApplication

如果默认的SpringApplication不符合你的口味，你可以创建一个本地的实例并自定义它。例如，关闭banner你可以这样写：

```java
public static void main(String[]args){
  SpringApplication app= new SpringApplication(MySpringConfiguration.class);
  app.setShowBanner(false);
  app.run(args);
}
```

>注：传递给SpringApplication的构造器参数是springbeans的配置源。在大多数情况下，这些将是@Configuration类的引用，但它们也可能是XML配置或要扫描包的引用。
你也可以使用application.properties文件来配置SpringApplication。具体参考Externalized配置。查看配置选项的完整列表，可参考SpringApplicationJavadoc.

####12.3 流畅的构建API

如果你需要创建一个分层的ApplicationContext（多个具有父子关系的上下文），或你只是喜欢使用流畅的构建API，你可以使用SpringApplicationBuilder。SpringApplicationBuilder允许你以链式方式调用多个方法，包括可以创建层次结构的parent和child方法。

```java
new SpringApplicationBuilder().showBanner(false)
                              .sources(Parent.class)
                              .child(Application.class)
                              .run(args);
```

>注：创建ApplicationContext层次时有些限制，比如，Web组件(components)必须包含在子上下文(childcontext)中，且相同的Environment即用于父上下文也用于子上下文中。具体参考SpringApplicationBuilderjavadoc22.3.流畅的构建APISpringBoot参考指南8122.3.流畅的构建API

####12.4 Application事件和监听器

除了常见的Spring框架事件，比如`ContextRefreshedEvent`，一个SpringApplication也发送一些额外的应用事件。一些事件实际上是在ApplicationContext被创建前触发的。

你可以使用多种方式注册事件监听器，最普通的是使用`SpringApplication.addListeners(...)`方法。

在你的应用运行时，应用事件会以下面的次序发送：

- 1.在运行开始，但除了监听器注册和初始化以外的任何处理之前，会发送一个ApplicationStartedEvent。
- 2.在Environment将被用于已知的上下文，但在上下文被创建前，会发送一个ApplicationEnvironmentPreparedEvent。
- 3.在refresh开始前，但在bean定义已被加载后，会发送一个ApplicationPreparedEvent。
- 4.启动过程中如果出现异常，会发送一个ApplicationFailedEvent。

>注：你通常不需要使用应用程序事件，但知道它们的存在会很方便（在某些场合可能会使用到）。在Spring内部，SpringBoot使用事件处理各种各样的任务。

####12.5 Web环境

一个SpringApplication将尝试为你创建正确类型的ApplicationContext。在默认情况下，使用AnnotationConfigApplicationContext或AnnotationConfigEmbeddedWebApplicationContext取决于你正在开发的是否是web应用。

用于确定一个web环境的算法相当简单（基于是否存在某些类）。如果需要覆盖默认行为，你可以使用setWebEnvironment(booleanwebEnvironment)。通过调用setApplicationContextClass(...)，你可以完全控制ApplicationContext的类型。

>注：当JUnit测试里使用SpringApplication时，调用setWebEnvironment(false)是可取的。

####12.5 命令行启动器

如果你想获取原始的命令行参数，或一旦SpringApplication启动，你需要运行一些特定的代码，你可以实现CommandLineRunner接口。在所有实现该接口的Springbeans上将调用run(String...args)方法。

```java
import org.springframework.boot.*;
import org.springframework.stereotype.*;

@Component
public class MyBean implements CommandLineRunner{
  public void run(String...args){
    //Dosomething...
  }
}
```

如果一些CommandLineRunnerbeans被定义必须以特定的次序调用，你可以额外实现org.springframework.core.Ordered接口或使用org.springframework.core.annotation.Order注解。

####12.6 Application退出

每个SpringApplication在退出时为了确保ApplicationContext被优雅的关闭，将会注册一个JVM的shutdown钩子。所有标准的Spring生命周期回调（比如，DisposableBean接口或@PreDestroy注解）都能使用。

此外，如果beans想在应用结束时返回一个特定的退出码（exitcode），可以实现org.springframework.boot.ExitCodeGenerator接口。

####12.7 外化配置

SpringBoot允许外化（externalize）你的配置，这样你能够在不同的环境下使用相同的代码。你可以使用properties文件，YAML文件，环境变量和命令行参数来外化配置。使用`@Value`注解，可以直接将属性值注入到你的beans中，并通过Spring的Environment抽象或绑定到结构化对象来访问。

SpringBoot使用一个非常特别的PropertySource次序来允许对值进行合理的覆盖，需要以下面的次序考虑属性：

- 1.命令行参数
- 2.来自于java:comp/env的JNDI属性
- 3.Java系统属性（System.getProperties()）
- 4.操作系统环境变量
- 5.只有在random.* 里包含的属性会产生一个RandomValuePropertySource
- 6.在打包的jar外的应用程序配置文件（application.properties，包含YAML和profile变量）
- 7.在打包的jar内的应用程序配置文件（application.properties，包含YAML和profile变量）
- 8.在@Configuration类上的@PropertySource注解
- 9.默认属性（使用SpringApplication.setDefaultProperties指定）

下面是一个具体的示例（假设你开发一个使用name属性的@Component）：
