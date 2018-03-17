---
title: Spring boot 2.0 新特性
date: 2018-03-01 17:29:51
categories: Java
tags:
- Java
- 代码
- 后端开发
- 框架
---

2018 年 3 月 1 日，经过漫长的等待之后，Spring Boot 2.0 正式发布。

2014 年 4 月 1 日，Spring Boot 发布了第一个正式版本。该项目旨在帮助开发者更容易地创建基于 Spring 的应用程序和服务。一直到今天发布 2.0 版本，在四年时间，Spring Boot 凭借其优雅的配置，灵活的部署，以及众多企业级项目的支持，成长为了一个拥有了 21000 多 Star，贡献者超过 400 名的热门开源项目。

# 从 Spring boot 1.5 升级到 Spring boot 2.0

强烈建议您在迁移到Spring Boot 2.0之前[升级到Spring Boot 1.5](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-1.5-Release-Notes)，并且使用 [Java 8](https://www.duyidong.com/2017/08/30/what-new-in-java8/)，再参考[官方文档](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Migration-Guide)升级到 Springboot2.0。

# Spring boot 2.0 新特性

## 以 Java 8 为基准

pring Boot 2.0 要求Java 版本必须8以上， Java 6 和 7 不再支持。一些 API 已被更新使用 Java 8的特性，例如：[接口默认方法](https://www.duyidong.com/2017/08/30/what-new-in-java8/#%E9%BB%98%E8%AE%A4%E6%96%B9%E6%B3%95%EF%BC%88Default-Methods%EF%BC%89)，基于 Lambda 的函数回调以及基于`java.time`的新 API 如`javax.time`。

## 第三方库升级

Spring Boot 2.0 是基于 [Spring Framework 5](https://github.com/spring-projects/spring-framework/wiki/What%27s-New-in-Spring-Framework-5.x) 的，Spring 的新特性均可以在 Spring Boot 2.0 中使用。其他第三方组件依赖升级版本如下：

- Tomcat 8.5
- Flyway 5
- Hibernate 5.2
- Thymeleaf 3

## 活跃的春天

Spring系列的产品为[响应式应用](https://github.com/reactive-streams/reactive-streams-jvm#reactive-streams)提供一流的支持。反应性应用程序是完全异步和非阻塞的。它们旨在用于事件循环执行模型（而不是更传统的每个请求线程执行模型）。Spring框架参考文档中的[“Web反应堆栈”](https://docs.spring.io/spring/docs/current/spring-framework-reference/web-reactive.html)部分为这个主题提供了一个很好的入门。

Spring Boot 2.0通过自动配置和启动器POM完全支持反应式应用。Spring Boot的内部本身也在必要时进行了更新，以提供反应性的反应（最明显的是我们的嵌入式服务器支持）。

### Spring WebFlux＆WebFlux.fn

Spring WebFlux是Spring MVC的完全非阻塞反应式替代方案。Spring Boot为基于注释的Spring WebFlux应用程序以及WebFlux.fn提供了自动配置，WebFlux.fn提供了更实用的样式API。

要开始，请使用`spring-boot-starter-webflux`初学者POM，它将提供由嵌入式Netty服务器支持的Spring WebFlux。有关详细信息，请参阅[Spring Boot参考文档](http://docs.spring.io/spring-boot/docs/2.0.x-SNAPSHOT/reference/htmlsingle/#boot-features-developing-web-applications)。

### 反应性弹簧数据

在底层技术支持的情况下，Spring Data还为反应式应用程序提供支持。目前Cassandra，MongoDB，Couchbase和Redis都有反应式API支持。

Spring Boot包含针对这些技术的特殊入门者POM，可为您提供入门所需的一切。例如，`spring-boot-starter-data-mongodb-reactive`包括对反应性mongo驱动程序和项目反应堆的依赖性。

### 反应式弹簧安全

Spring Boot 2.0可以利用Spring Security 5.0来保护您的反应式应用程序。当Spring Security位于类路径中时，为WebFlux应用程序提供自动配置。

使用WebFlux的Spring Security访问规则可以通过`SecurityWebFilterChain`。如果你之前使用过Spring MVC，Spring应该感到非常熟悉。有关更多详细信息，请参阅[Spring Boot参考文档](http://docs.spring.io/spring-boot/docs/2.0.x-SNAPSHOT/reference/htmlsingle/#boot-features-security-webflux)和[Spring Security文档](https://docs.spring.io/spring-security/site/docs/5.0.0.RELEASE/reference/htmlsingle/#jc-webflux)。

### 嵌入式Netty服务器

由于WebFlux不依赖于Servlet API，我们现在可以首次为Netty作为嵌入式服务器提供支持。该`spring-boot-starter-webflux`起动POM将拉Netty中4.1和[Ractor Netty的](https://github.com/reactor/reactor-netty)。

| 注意 | 您只能将Netty用作反应式服务器。不提供阻止servlet API支持。 |
| ---- | ---------------------------------------------------------- |
|      |                                                            |

## HTTP / 2支持

为Tomcat，Undertow和Jetty提供HTTP / 2支持。支持取决于所选的Web服务器和应用程序环境（因为JDK 8不支持该协议）。

有关详细信息，请参阅[此“如何”部分](http://docs.spring.io/spring-boot/docs/2.0.x-SNAPSHOT/reference/htmlsingle/#howto-configure-http2)。

## 配置属性绑定

在Spring Boot 2.0中，用于绑定`Environment`属性的机制`@ConfigurationProperties`已经完全彻底修改。我们借此机会收紧管理宽松绑定的规则，并修复了Spring Boot 1.x中的许多不一致之处。

新的`Binder`API也可以`@ConfigurationProperties`在您自己的代码中直接使用。例如，下面将结合到`List`的`PersonName`对象：

```
列表< PersonName > people =  Binder 。得到（环境）
    .bind（ “ my.property ”，可绑定。 listOf（PERSONNAME 。类））
    .orElseThrow（IllegalStateException异常:: 新）;
```

配置源可以像这样在YAML中表示：

```
my ： 
  property ：
  - first - name ： Jane 
    last - name ： Doe 
  - first - name ： John 
    last - name ： Doe
```

有关更新绑定规则的更多信息，[请参阅此Wiki页面](https://github.com/spring-projects/spring-boot/wiki/Relaxed-Binding-2.0)。

### 物产起源

YAML文件和Spring Boot加载的属性文件现在包含`Origin`可帮助您跟踪项目从何处加载的信息。几个Spring Boot功能利用这些信息并在适当时显示。

例如，`BindException`绑定失败时抛出的类是一个`OriginProvider`。这意味着起源信息可以很好地从故障分析器中显示出来。

另一个例子是`env`执行器端点，当它有可用时包括原始信息。下面的代码片断显示该`spring.security.user.name`属性来自jar中打包的文件的行`1`，列：`27``application.properties`

```
{
   “ name ”：“ applicationConfig：[classpath：/application.properties] ”，
   “ properties ”：{
     “ spring.security.user.name ”：{
       “ value ”：“ user ”，
       “ origin ”：“ class path resource [application.properties]：1：27 “ 
    } 
  } 
}
```

### 转换器支持

绑定使用了一个新`ApplicationConversionService`类，它提供了一些对属性绑定特别有用的额外转换器。最引人注目的是转换器的`Duration`类型和分隔字符串。

该`Duration`转换器允许在任一ISO-8601格式中指定的持续时间，或作为一个简单的字符串（例如`10m`，10分钟）。现有的属性已更改为始终使用`Duration`。该`@DurationUnit`注释通过设置如果没有指定所使用的单元确保向后兼容性。例如，Spring Boot 1.5中需要秒数的属性现在必须`@DurationUnit(ChronoUnit.SECONDS)`确保一个简单的值，例如`10`实际使用的值`10s`。

分隔字符串转换允许您将简单绑定`String`到`Collection`或`Array`不必分割逗号。例如，`base-dn`使用LDAP 属性时`@Delimiter(Delimiter.NONE)`，LDAP DN（通常包含逗号）不会被错误解释。

## Gradle插件

Spring Boot的Gradle插件在很大程度上已被重写，以实现[许多重大改进](https://github.com/spring-projects/spring-boot/issues?utf8=%E2%9C%93&q=label%3A%22theme%3A%20gradle-plugin%22%20milestone%3A2.0.0.M1%20)。您可以在其[参考文献](http://docs.spring.io/spring-boot/docs/2.0.x-SNAPSHOT/gradle-plugin/reference)和[API](https://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/gradle-plugin/api)文档中阅读关于插件功能的更多信息。

Spring Boot现在需要Gradle 4.x. 如果您要升级使用Gradle的项目，请查看[迁移指南](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Migration-Guide)。

## 科特林

Spring Boot 2.0现在包含对Kotlin 1.2.x的支持，并提供了`runApplication`一种使用惯用Kotlin运行Spring Boot应用程序的功能。我们还公开和利用了Kotlin对其他Spring项目（如Spring Framework，Spring Data和Reactor）已添加到其最近版本中的支持。

有关更多信息，请参阅[参考文档](http://docs.spring.io/spring-boot/docs/2.0.x-SNAPSHOT/reference/htmlsingle/#boot-features-kotlin)的[Kotlin支持部分](http://docs.spring.io/spring-boot/docs/2.0.x-SNAPSHOT/reference/htmlsingle/#boot-features-kotlin)。

## 执行器改进

使用Spring Boot 2.0对执行器端点进行了许多改进和改进。所有HTTP执行器端点现在都在该`/actuator`路径下公开，并且生成的JSON有效载荷得到了改进。

我们现在也不会在默认情况下暴露很多端点。如果您要升级现有的Spring Boot 1.5应用程序，请务必查看[迁移指南](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Migration-Guide)并特别注意该`management.endpoints.web.exposure.include`属性。

### 执行器JSON

Spring Boot 2.0改进了从许多端点返回的JSON有效负载。

现在许多端点都具有更精确地反映底层数据的JSON。例如，`/actuator/conditions`终端（`/autoconfig`在Spring Boot 1.5中）现在有一个顶级`contexts`密钥来将结果分组`ApplicationContext`。

现在还使用[Spring REST Docs](https://projects.spring.io/spring-restdocs/)生成了广泛的[REST API ](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/actuator-api/html/)[文档，](https://projects.spring.io/spring-restdocs/)并随每个版本发布。

### 泽西岛和WebFlux支持

除了支持Spring MVC和JMX，您现在可以在开发纯泽西或WebFlux应用程序时访问执行器端点。Jersey支持通过自定义Jersey提供`Resource`，WebFlux使用自定义`HandlerMapping`。

### 超媒体链接

该`/actuator`端点现在提供了一个HAL格式的响应提供链接到所有活动端点（即使你没有春天HATEOAS在classpath）。

### 执行器@Endpoints

为了支持Spring MVC，JMX，WebFlux和Jersey，我们为执行器端点开发了一种新的编程模型。该`@Endpoint`批注可以与组合使用`@ReadOperation`，`@WriteOperation`并`@DeleteOperation`制定一个技术无关的方式端点。

您还可以使用`@EndpointWebExtension`或`@EndpointJmxExtension`编写技术特定的增强功能到端点。详细信息请参阅[更新的参考文档](http://docs.spring.io/spring-boot/docs/2.0.x-SNAPSHOT/reference/htmlsingle/#production-ready-endpoints-custom)。

### 千分尺

Spring Boot 2.0不再提供自己的指标API。相反，我们依靠[micrometer.io](https://micrometer.io/)来满足所有应用程序监视需求。

千分尺包括尺寸指标的支持，当与尺寸监测系统配对时，尺寸指标可以有效访问特定的指定度量标准，并且可以在其尺寸范围内向下钻取。

指标可以输出到各种系统和开箱即用的Spring Boot 2.0，为Atlas，Datadog，Ganglia，Graphite，Influx，JMX，New Relic，Prometheus，SignalFx，StatsD和Wavefront提供支持。另外还可以使用简单的内存中度量标准。

集成随JVM指标（包括CPU，内存，线程和GC），Logback，Tomcat，Spring MVC＆提供`RestTemplate`。

有关更多详细信息，请参阅参考文档的更新[“指标”部分](http://docs.spring.io/spring-boot/docs/2.0.x-SNAPSHOT/reference/htmlsingle/#production-ready-metrics)。

## 数据支持

除了上面提到的“反应性弹簧数据”支持外，在数据领域还进行了其他一些更新和改进。

### HikariCP

Spring Boot 2.0中的默认数据库池技术已从Tomcat池切换到HikariCP。我们发现Hakari提供了卓越的性能，我们的许多用户更喜欢Tomcat Pool。

### 初始化

数据库初始化逻辑在Spring Boot 2.0中已经合理化。Spring Batch，Spring Integration，Spring Session和Quartz的初始化现在默认情况下仅在使用嵌入式数据库时才会发生。该`enabled`属性已被更具表现力的枚举所取代。例如，如果您想要始终执行Spring Batch初始化，您可以设置`spring.batch.initialize-schema=always`。

如果Flyway或Liquibase正在管理DataSource的模式，并且您正在使用嵌入式数据库，Spring Boot现在会自动关闭Hibernate的自动DDL功能。

### JOOQ

Spring Boot 2.0现在基于DataSource自动检测jOOQ方言（类似于JPA方言）。`@JooqTest`还引入了一个新的注释来简化只有jOOQ必须使用的测试。

### 的JdbcTemplate

在`JdbcTemplate`那个春天开机自动配置就现在可以通过定制化的`spring.jdbc.template`特性。此外，`NamedParameterJdbcTemplate`自动配置的内容会重用`JdbcTemplate`幕后。

### Spring Data Web配置

Spring Boot公开了一个新的`spring.data.web`配置名称空间，可以轻松配置分页和排序。

### Influx DB

Spring Boot现在自动配置开源时间序列数据库InfluxDB。要启用InfluxDB支持，您需要设置一个`spring.influx.url`属性，并将其包含`influxdb-java`在您的类路径中。

### 飞行路线/ Liquibase灵活配置

如果仅提供自定义`url`或`user`属性，则Flyway和Liquibase的自动配置现在将重用标准数据源属性，而不是忽略它们。这使您可以`DataSource`仅使用所需的信息为迁移目的创建定制。

### 过冬

现在支持自定义Hibernate命名策略。对于高级场景，现在可以在上下文中定义`ImplicitNamingStrategy`或`PhysicalNamingStrategy`使用常规bean。

现在也可以通过公开`HibernatePropertiesCustomizer`bean 来更加细致地定制Hibernate使用的属性。

### MongoDB客户端自定义

现在可以通过定义一个类型的bean来为Spring Boot自动配置的Mongo客户端应用高级定制`MongoClientSettingsBuilderCustomizer`。

### Redis的

现在可以使用`spring.cache.redis.*`属性配置Redis的缓存默认值。

## 卷筒纸

除了上面提到的WebFlux和WebFlux.fn支持之外，还在开发Web应用程序时进行了以下改进。

### 上下文路径记录

当使用嵌入式容器时，当您的应用程序启动时，上下文路径将与HTTP端口一起记录。例如，嵌入式Tomcat现在看起来像这样：

```
Tomcat在端口上启动：8080（http），其上下文路径为'/ foo'
```

### Web过滤器初始化

Web过滤器现在在所有支持的容器上急切地初始化。

### Thymeleaf

Thymeleaf初学者现在包括`thymeleaf-extras-java8time`提供`javax.time`类型支持。

### JSON支持

新的`spring-boot-starter-json`起始者收集必要的位以读取和写入JSON。它不仅提供了`jackson-databind`与Java8工作时，也是有用的模块：`jackson-datatype-jdk8`，`jackson-datatype-jsr310`和`jackson-module-parameter-names`。这个新的起动器现在被用于`jackson-databind`之前定义的地方。

如果您更喜欢杰克逊以外的其他产品，我们对Spring Boot 2.0的支持已经大大提高。我们还引入了对JSON-B的支持（包括JSON-B测试支持）。

## 石英

自动配置支持现在包含在[Quartz Scheduler中](http://www.quartz-scheduler.org/)。我们还添加了新的`spring-boot-starter-quartz`起始者POM。

您可以使用内存`JobStores`中或完整的基于JDBC的存储。所有`JobDetail`，`Calendar`并`Trigger`从你的Spring应用程序上下文豆将自动注册`Scheduler`。

有关更多详细信息，请阅读参考文档的新[“Quartz Scheduler”部分](http://docs.spring.io/spring-boot/docs/2.0.x-SNAPSHOT/reference/htmlsingle/#boot-features-quartz)。

## 测试

对Spring Boot 2.0中提供的测试支持进行了一些补充和调整：

- `@WebFluxTest`已添加新注释以支持WebFlux应用程序的“切片”测试。
- `Converter`和`GenericConverter`豆类现在自动扫描`@WebMvcTest`和`@WebFluxTest`。
- `@AutoConfigureWebTestClient`已经添加了一个注释来提供一个`WebTestClient`bean供测试使用。注释会自动应用于`@WebFluxTest`测试。
- 增加了一个新的`ApplicationContextRunner`测试实用程序，可以很容易地测试您的自动配置。我们已将大部分内部测试套件移至此新模型。详细信息请参阅[更新的文档](http://docs.spring.io/spring-boot/docs/2.0.x-SNAPSHOT/reference/htmlsingle/#boot-features-test-autoconfig)。

## 杂

除了上面列出的变化之外，还有很多小的调整和改进，包括：

- `@ConditionalOnBean`现在在确定条件是否被满足时使用逻辑`AND`而不是逻辑`OR`。
- 无条件类现在包含在自动配置报告中。
- 该`spring`CLI应用程序现在包括`encodepassword`可用于创建Spring Security的兼容散列密码命令。
- 计划任务（即``@EnableScheduling`）可以使用`scheduledtasks`执行器端点进行审查。
- 该`loggers`驱动器终端现在允许你重新设置一个记录器级别为它的默认。
- Spring Session用户现在可以通过`sessions`执行器端点查找和删除会话。
- 使用`spring-boot-starter-parent`现在基于Maven的应用程序`-parameters`默认使用标志。
- 我们的构建现在使用[大堂](http://concourse.ci/)的CI和我们的项目POM文件已被重构，使它们更简单的（这些变化应该是透明的大多数用户，但如果你发现与公布的POM任何问题，请[报告这些](https://github.com/spring-projects/spring-boot/issues)）。

## 动画ASCII艺术

最后，为了好玩，Spring Boot 2.0现在支持动画GIF横幅。见[这个项目](https://github.com/snicoll-demos/demo-animated-banner)的一个例子。