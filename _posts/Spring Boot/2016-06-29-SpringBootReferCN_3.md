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

###13 外化配置

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

```Java
import org.springframework.stereotype.*;
import org.springframework.beans.factory.annotation.*;
@Component
public class MyBean{
  @Value("${name}")
  private String name;
  //...
}
```

你可以将一个application.properties文件捆绑到jar内，用来提供一个合理的默认name属性值。当运行在生产环境时，可以在jar外提供一个application.properties文件来覆盖name属性。对于一次性的测试，你可以使用特定的命令行开关启动（比如，java-jarapp.jar--name="Spring"）。

####13.1 配置随机值

RandomValuePropertySource在注入随机值（比如，密钥或测试用例）时很有用。它能产生整数，longs或字符串，比如：

```Java
my.secret=${random.value}
my.number=${random.int}
my.bignumber=${random.long}
my.number.less.than.ten=${random.int(10)}
my.number.in.range=${random.int[1024,65536]}
```

random.int*语法是OPENvalue(,max)CLOSE，此处OPEN，CLOSE可以是任何字符，并且value，max是整数。如果提供max，那么value是最小的值，max是最大的值（不包含在内）。

####13.2 访问命令行属性

默认情况下，SpringApplication将任何可选的命令行参数（以'--'开头，比如，--server.port=9000）转化为property，并将其添加到SpringEnvironment中。

如上所述，命令行属性总是优先于其他属性源。如果你不想将命令行属性添加到Environment里，你可以使用SpringApplication.setAddCommandLineProperties(false)来禁止它们。

####13.3 Application属性文件

SpringApplication将从以下位置加载application.properties文件，并把它们添加到SpringEnvironment中：

- 1.当前目录下的一个/config子目录
- 2.当前目录
- 3.一个classpath下的/config包
- 4.classpath根路径（root）

这个列表是按优先级排序的（列表中位置高的将覆盖位置低的）。

> **注：你可以使用YAML（'.yml'）文件替代'.properties'。如果不喜欢将application.properties作为配置文件名，你可以通过指定spring.config.name环境属性来切换其他的名称。你也可以使用spring.config.location环境属性来引用一个明确的路径（目录位置或文件路径列表以逗号分割）。**

```
$java-jarmyproject.jar--spring.config.name=myproject
//or
$java-jarmyproject.jar--spring.config.location=classpath:/default.properties,classpath:/override.properties
```

如果spring.config.location包含目录（相对于文件），那它们应该以/结尾（在加载前，spring.config.name产生的名称将被追加到后面）。不管spring.config.location是什么值，默认的搜索路径classpath:,classpath:/config,file:,file:config/总会被使用。以这种方式，你可以在application.properties中为应用设置默认值，然后在运行的时候使用不同的文件覆盖它，同时保留默认配置。

>**注：如果你使用环境变量而不是系统配置，大多数操作系统不允许以句号分割（period-separated）的key名称，但你可以使用下划线（underscores）代替（比如，使用SPRING_CONFIG_NAME代替spring.config.name）。如果你的应用运行在一个容器中，那么JNDI属性（java:comp/env）或servlet上下文初始化参数可以用来取代环境变量或系统属性，当然也可以使用环境变量或系统属性。**

####13.4 特定的Profile属性

除了application.properties文件，特定配置属性也能通过命令惯例application-{profile}.properties来定义。特定Profile属性从跟标准application.properties相同的路径加载，并且特定profile文件会覆盖默认的配置。

####13.5 属性占位符

当application.properties里的值被使用时，它们会被存在的Environment过滤，所以你能够引用先前定义的值（比如，系统属性）。

```
app.name=MyApp
app.description=${app.name}isaSpringBootapplication

```

> **注：你也能使用相应的技巧为存在的SpringBoot属性创建'短'变量，具体参考Section63.3,“Use‘short’commandlinearguments”。**

####13.6 使用YAML代替Properties

YAML是JSON的一个超集，也是一种方便的定义层次配置数据的格式。无论你何时将SnakeYAML库放到classpath下，SpringApplication类都会自动支持YAML作为properties的替换。

>**注：如果你使用'starterPOMs'，spring-boot-starter会自动提供SnakeYAML。**

#####13.6.1 加载YAML

Spring框架提供两个便利的类用于加载YAML文档，YamlPropertiesFactoryBean会将YAML作为Properties来加载，YamlMapFactoryBean会将YAML作为Map来加载。

示例：

```
environments:
  dev:
    url:http://dev.bar.comname:DeveloperSetup
  prod:
    url:http://foo.bar.comname:MyCoolApp

//上面的YAML文档会被转化到下面的属性中：

environments.dev.url=http://dev.bar.com
environments.dev.name=DeveloperSetup
environments.prod.url=http://foo.bar.comenvironments.prod.name=MyCoolApp
```

YAML列表被表示成使用[index]间接引用作为属性keys的形式，例如下面的YAML：

```
my:
  servers:
    -dev.bar.com
    -foo.bar.com

将会转化到下面的属性中:
my.servers[0]=dev.bar.com
my.servers[1]=foo.bar.com

```

使用SpringDataBinder工具绑定那样的属性（这是@ConfigurationProperties做的事），你需要确定目标bean中有个java.util.List或Set类型的属性，并且需要提供一个setter或使用可变的值初始化它，比如，下面的代码将绑定上面的属性：

```Java
@ConfigurationProperties(prefix="my")
public class Config{
  private List<String> servers = new ArrayList<String>();
  public List<String> getServers(){
    returnthis.servers;
    }
}
```

#####13.6.2 在Spring环境中使用YAML暴露属性:

YamlPropertySourceLoader类能够用于将YAML作为一个PropertySource导出到SprigEnvironment。这允许你使用熟悉的@Value注解和占位符语法访问YAML属性。

#####13.6.3 Multi-profileYAML文档

你可以在单个文件中定义多个特定配置（profile-specific）的YAML文档，并通过一个spring.profileskey标示应用的文档。例如：

```
server:
  address:192.168.1.100
---
spring:
  profiles:development
server:
  address:127.0.0.1
---
spring:
  profiles:production
  server:address:192.168.1.120

```
在上面的例子中，如果development配置被激活，那server.address属性将是127.0.0.1。如果development和production配置（profiles）没有启用，则该属性的值将是192.168.1.100。

**YAML缺点：**

YAML文件不能通过@PropertySource注解加载。所以，在这种情况下，如果需要使用@PropertySource注解的方式加载值，那就要使用properties文件。

####14. Spring Web MVC框架
Spring Web MVC框架（通常简称为"SpringMVC"）是一个富"模型，视图，控制器"的web框架。SpringMVC允许你创建特定的@Controller或@RestControllerbeans来处理传入的HTTP请求。使用@RequestMapping注解可以将控制器中的方法映射到相应的HTTP请求。

示例：

```Java
@RestController
@RequestMapping(value = "/users")
public class MyRestController{

  @RequestMapping(value = "/{user}",method = RequestMethod.GET)
  public User getUser(@PathVariable Long user){
    //...
  }

  @RequestMapping(value = "/{user}/customers",method = RequestMethod.GET)
  List<Customer>getUserCustomers(@PathVariable Long user){
    //...
  }

  @RequestMapping(value = "/{user}",method = RequestMethod.DELETE)
  public User deleteUser(@PathVariable Long user){
    //...
  }
}
```

#####14.1 Spring MVC自动配置

SpringBoot为SpringMVC提供适用于多数应用的自动配置功能。在Spring默认基础上，自动配置添加了以下特性：

- 1.引入ContentNegotiatingViewResolver和BeanNameViewResolverbeans。
- 2.对静态资源的支持，包括对WebJars的支持。
- 3.自动注册Converter，GenericConverter，Formatterbeans。
- 4.对HttpMessageConverters的支持。
- 5.自动注册MessageCodeResolver。
- 6.对静态index.html的支持。
- 7.对自定义Favicon的支持。如果想全面控制SpringMVC，你可以添加自己的@Configuration，并使用@EnableWebMvc对其注解。

如果想保留SpringBootMVC的特性，并只是添加其他的MVC配置(拦截器，formatters，视图控制器等)，你可以添加自己的WebMvcConfigurerAdapter类型的@Bean（不使用@EnableWebMvc注解）。

#####14.2 HttpMessageConverters

Spring MVC使用HttpMessageConverter接口转换HTTP请求和响应。合理的缺省值被包含的恰到好处（out of the box），例如对象可以自动转换为JSON（使用Jackson库）或XML（如果JacksonXML扩展可用则使用它，否则使用JAXB）。字符串默认使用UTF-8编码。

如果需要添加或自定义转换器，你可以使用SpringBoot的HttpMessageConverters类：

```Java
import org.springframework.boot.autoconfigure.web.HttpMessageConverters;
import org.springframework.context.annotation.*;
import org.springframework.http.converter.*;
@Configuration
public class MyConfiguration{

  @Bean
  public HttpMessageConverters customConverters(){
    HttpMessageConverter<?> additional = ...
    HttpMessageConverter<?>another= ...
    return new HttpMessageConverters(additional,another);
  }
}
```

任何在上下文中出现的HttpMessageConverterbean将会添加到converters列表，你可以通过这种方式覆盖默认的转换器（converters）。

#####14.3 MessageCodesResolver

Spring MVC有一个策略，用于从绑定的errors产生用来渲染错误信息的错误码：MessageCodesResolver。如果设置spring.mvc.message-codes-resolver.format属性为PREFIX_ERROR_CODE或POSTFIX_ERROR_CODE（具体查看DefaultMessageCodesResolver.Format枚举值），SpringBoot会为你创建一个MessageCodesResolver。

#####14.4 静态内容

默认情况下，SpringBoot从classpath下一个叫/static（/public，/resources或/META-INF/resources）的文件夹或从ServletContext根目录提供静态内容。这使用了SpringMVC的ResourceHttpRequestHandler，所以你可以通过添加自己的WebMvcConfigurerAdapter并覆写addResourceHandlers方法来改变这个行为（加载静态文件）。

在一个单独的web应用中，容器默认的servlet是开启的，如果Spring决定不处理某些请求，默认的servlet作为一个回退（降级）将从ServletContext根目录加载内容。大多数时候，这不会发生（除非你修改默认的MVC配置），因为Spring总能够通过DispatcherServlet处理请求。

此外，上述标准的静态资源位置有个例外情况是Webjars内容。任何在/webjars/** 路径下的资源都将从jar文件中提供，只要它们以Webjars的格式打包。

> **注：如果你的应用将被打包成jar，那就不要使用src/main/webapp文件夹。尽管该文件夹是一个共同的标准，但它仅在打包成war的情况下起作用，并且如果产生一个jar，多数构建工具都会静悄悄的忽略它。**

#####14.5 模板引擎

正如REST web服务，你也可以使用Spring MVC提供动态HTML内容。SpringMVC支持各种各样的模板技术，包括Velocity,FreeMarker和JSPs。很多其他的模板引擎也提供它们自己的SpringMVC集成。

SpringBoot为以下的模板引擎提供自动配置支持：
- 1.FreeMarker
- 2.Groovy
- 3.Thymeleaf
- 4.Velocity

**注：如果可能的话，应该忽略JSPs，因为在内嵌的servlet容器使用它们时存在一些已知的限制。**

当你使用这些引擎的任何一种，并采用默认的配置，你的模板将会从src/main/resources/templates目录下自动加载。

>**注：IntelliJIDEA根据你运行应用的方式会对classpath进行不同的整理。在IDE里通过main方法运行你的应用跟从Maven或Gradle或打包好的jar中运行相比会导致不同的顺序。这可能导致SpringBoot不能从classpath下成功地找到模板。如果遇到这个问题，你可以在IDE里重新对classpath进行排序，将模块的类和资源放到第一位。或者，你可以配置模块的前缀为classpath*:/templates/，这样会查找classpath下的所有模板目录。**

#####14.6 错误处理

SpringBoot默认提供一个/error映射用来以合适的方式处理所有的错误，并且它在servlet容器中注册了一个全局的错误页面。对于机器客户端（相对于浏览器而言，浏览器偏重于人的行为），它会产生一个具有详细错误，HTTP状态，异常信息的JSON响应。对于浏览器客户端，它会产生一个白色标签样式（whitelabel）的错误视图，该视图将以HTML格式显示同样的数据（可以添加一个解析为erro的View来自定义它）。为了完全替换默认的行为，你可以实现ErrorController，并注册一个该类型的bean定义，或简单地添加一个ErrorAttributes类型的bean以使用现存的机制，只是替换显示的内容。

如果在某些条件下需要比较多的错误页面，内嵌的servlet容器提供了一个统一的JavaDSL（领域特定语言）来自定义错误处理。

示例：

```java
@Bean
public EmbeddedServletContainerCustomizer containerCustomizer(){
  return new MyCustomizer();
}
//...
private static class MyCustomizer implements EmbeddedServletContainerCustomizer{

  @Override
  public void customize(ConfigurableEmbeddedServletContainercontainer){
    container.addErrorPages(newErrorPage(HttpStatus.BAD_REQUEST,"/400"));
  }
}
```

你也可以使用常规的SpringMVC特性来处理错误，比如@ExceptionHandler方法和@ControllerAdvice。ErrorController将会捡起任何没有处理的异常。

N.B.如果你为一个路径注册一个ErrorPage，最终被一个过滤器（Filter）处理（对于一些非Springweb框架，像Jersey和Wicket这很常见），然后过滤器需要显式注册为一个ERROR分发器（dispatcher）。

```Java
@Bean
public FilterRegistrationBean myFilter(){
  FilterRegistrationBean registration = new FilterRegistrationBean();
  registration.setFilter(newMyFilter());
  ...
  registration.setDispatcherTypes(EnumSet.allOf(DispatcherType.class));
  returnregistration;
}
```

注：默认的FilterRegistrationBean没有包含ERROR分发器类型。
