---
title: Spring boot 注解
date: 2019-04-28 18:07:07
categories: Java
tags:
- Java
- Spring
- Springboot
---

Springboot 相比于 Spring 的强大之处在于用注解解决了很多 Spring 的设计中需要配置来解决的问题，极大地提高了开发体验。这篇文章主要解释 Springboot 是如何使用注解实现依赖注入的。

## Java 注解

在讲解 Springboot 注解之前先来看看 Java 的注解，每当你创建描述符性质的类或者接口时,一旦其中包含重复性的工作，就可以考虑使用注解来简化与自动化该过程。

Java 注解又称 Java 标注，是 Java 语言 5.0 版本开始支持加入源代码的特殊语法元数据。注解本身并不承载功能，而是绑定在 Java 成员上的一些元数据，可以在特定的时候（编译检查、运行）时被获取和处理。

Java 最开始提供了 4 种元注解，直到 jdk1.8 增加到 7 种，专门负责新注解的创建工作。

**元注解（作用在其他注解的注解）**

- `@Target` - 标记这个注解应该是哪种 Java 成员。
- `@Retention` - 标识这个注解怎么保存，是只在代码中，还是编入class文件中，或者是在运行时可以通过反射访问。
- `@Documented` - 标记这些注解是否包含在用户文档中。
- `@Inherited` - 标记这个注解是继承于哪个注解类(默认 注解并没有继承于任何子类)
- `@SafeVarargs` - Java 7 开始支持，忽略任何使用参数为泛型变量的方法或构造函数调用产生的警告。
- `@FunctionalInterface` - Java 8 开始支持，标识一个匿名函数或函数式接口。
- `@Repeatable` - Java 8 开始支持，标识某注解可以在同一个声明上使用多次。

我们来看一个最常见的注解`@Override`的源码：

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface Before {
}
```

`@interface` 是 Java 注解申明的关键字，它的元注解翻译过来就是“可以用于 Java 方法，并保留至运行时，且不出现在用户文档中”。

更多使用元注解自定义注解可以参考我写的一个小 demo: <https://github.com/ADU-21/java-annotation>，这里就不展开赘述了。

# Spring boot 注解

相比于 Java，Springboot 为我们提供更多的注解，用于依赖注入的有·`@Autowired`、`@Resource`、`@Qualifier`、`@Service`、`@Controller`、`@Repository`、`@Component`。

上面有提到注解本身只是元数据而不实现功能。那 Springboot 从一个类被标记上注解到被注入到 Ioc 容器中都发生了什么？这就要从 Springboot 的启动流程说起。

### Springboot 启动流程

springboot 启动流程大图，可以参考：<https://www.processon.com/view/link/59812124e4b0de2518b32b6e>

**首先**，很熟悉的是，SpringBoot为我们提供了一个注解，@SpringBootApplication, 打开注解的源码我们可以看到，主要由以下几个注解组成：

- **@SpringBootConfiguration**

  `@SpringBootConfiguration`注解是由`@Configuration`来注解的，因此也就表示Application类本身就是一个 Bean。虽然`@SpringBootConfiguration`注释中说该注解一个应用中只能用一次，但是配置多个也不会报错，只是建议在一个应用中只用一次，并且该注解也可以与`@Configuration`互换。

- **@EnableAutoConfiguration**

  `@EnableAutoConfiguration`用于开启自动配置的。通过这个注解我们就能使用很多默认的配置来进行程序的开发，这个注解集成了`@EnableAutoConfiguration`注解，这个注解就是 auto-configuration 的核心，源码如下：

  ![](/images/EnableAutoConfiguration.png)

  **1**.  其中最核心的就是`@Import(EnableAutoConfigurationImportSelector.class)`。 `@Import`注解的作用就是导入其他的配置。

  **2**.  在 Spring 容器启动的时候会利用`ConfigurationClassPostProcessor`进行解析，这个类实现了`BeanDefinitionRegistryPostProcessor`，在容器启动的时候会调用其方法对扫描路径下的`@Configuration`注解进行扫描和解析。

  **3**.  在解析`@Configuration`的时候会去解析`@Import`注解，其中`EnableAutoConfigurationImportSelector`类是`AutoConfigurationImportSelector`的子类，`AutoConfigurationImportSelector`中会进行当前根路径下的 jar 包下的 META-INF/spring.factories 文件的扫描和读取。并且将其中涉及到的 Bean 配合`@Conditional`注解添加到 Spring 容器中。

  整体的调用链如下：

  ![](/images/spring-auto-configuration.png)

- **@ComponentScan**

  这是Springboot 注入的重点，`@ComponentScan`注解的功能与 Spring 在 xml 文件配置的的功能是一样的，而上面也提到 Application 类放在源码的根目录下，其实就是与这个注解有关。`@ComponentScan`在没有指明basePackages 属性的时候，默认会扫描该注解所在的类的包及其子包下的所有`@Component`注解过的类，包括`@Controller`，`@Service`，`@Configuration`这些注解。这也就是为什么我们不用做任何配置，就可以将Springboot 应用中的类扫描为 Bean。

  调用栈可以参考下图：

  ![](/images/component-scan-stack.png)

  具体实现的方法为：`ConfigurationClassParser.doProcessConfigurationClass`:

  ![](/images/ConfigurationClassParser-doProcessConfigurationClass.png)


### Spring Bean 加载

取到所有带 `@Component` 注解的类之后就是创建实例了，再来看调用栈：

![](/images/springboot-newInstance.png)

对应前面大图里工厂模式一块：

![](/images/spring-boot-start-map.png)

事实上 Springboot 源码中在 context refresh 里有包含了绝大多数我们关心的和 bean 有关的处理：

![](/images/spring-context-refresh.png)

## Reference

<http://www.cnblogs.com/ITtangtang/p/3974531.html>

<https://zh.wikipedia.org/wiki/Java%E6%B3%A8%E8%A7%A3>

<https://segmentfault.com/a/1190000012887776>

<http://www.cnblogs.com/xrq730/p/6285358.html>

<https://www.jianshu.com/p/414d3e2f04e9>
