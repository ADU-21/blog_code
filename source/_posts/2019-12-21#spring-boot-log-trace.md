---
title: Spring boot 全局参数传递和追踪
date: 2019-12-21 10:25:01
categories: Java
tags:
- DMC
- Springboot
- 全链路追踪
- 微服务
---

本文介绍如何基于 spring boot 获取、传递、使用一次会话的全局参数，并在所有链路日志中打印。

<!-- more -->

# 支持场景

- 微服务中使用 traceId 实现跨应用日志追踪 。
- 用户认证信息传递。
- 和全局参数有关的日志追踪。

# 具体实现

## TraceId 获取

在决定在什么位置获取 traceId 之前先看一下 Tomcat 容器中 Servlet、Filter 、 Listener 和 Interceptor 的关系：

![](/images/tomcat-container.png)

<center>**图片来源：<https://blog.csdn.net/xuemengrui12/article/details/98783715>**</center>

在选择获取 traceId 的时候我希望他是一个全局的，对所有请求生效的配置，因此放在 Filter 层，下面尝试在 Filter 中打印出 traceId:

**ContextFilter.java**

```java
@Component
@Order(Ordered.HIGHEST_PRECEDENCE)
@Slf4j
public class ContextFilter implements Filter {
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain)
        throws IOException, ServletException {
        HttpServletRequest request = (HttpServletRequest) servletRequest;

        Enumeration<String> headerNames = request.getHeaderNames();
        String traceId = request.getHeader("Trace-Id");

        if (StringUtils.isNotBlank(traceId)) {
            log.info(traceId);
        }

        filterChain.doFilter(servletRequest, servletResponse);
    }

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void destroy() {

    }
}

```

## 全局参数传递

验证完获取 traceId 之后，我们需要把 traceId 在一个次会话中传递，先使用 Thread local 实现：

**AppContext.java**

```java
public class AppContext implements Serializable {
    public static final String TRACE_ID_HEADER = "Trace-Id";
    private static final long serialVersionUID = -979220111440953115L;

    private String traceId;

    private static final ThreadLocal<AppContext> LOCAL = ThreadLocal.withInitial(AppContext::new);

    public static AppContext getContext() {
        return LOCAL.get();
    }

    public static void setContext(AppContext context) {
        LOCAL.set(context);
    }

    public static void removeContext() {
        LOCAL.remove();
    }

    public String getTraceId() {
        return traceId;
    }

    public void setTraceId(String traceId) {
        this.traceId = traceId;
    }
}
```

有了 Thread local 对象，就可以实现随用随取了，类似的，用户信息也可以用这种方式隐式传递。

```java
AppContext.getContext().setTraceId(traceId); # 在 Filter 中将 traceId 存入 thread local
AppContext.getContext().getTraceId();        # 在 controller 中取出 traceId
```

## 多线程全局参数传递

Thread local 可以优雅地实现 Session 管理，但仅仅局限在一个封闭线程内，在使用线程池等会池化复用线程的执行组件情况下 Thread local 的信息就会丢失，例如我们在获取用户信息的地方加入一个线程池：

**UserService.java**

```java
private static final ExecutorService SAVE_USER_THREAD_POOL = Executors.newFixedThreadPool(5);

public User getUser(Long userId) throws InterruptedException, ExecutionException {
        log.info("[{}]Get user by id={}", AppContext.getContext().getTraceId(), userId);
        try {
            return GET_USER_THREAD_POOL.submit(() -> userRepository.getUserById(userId)).get();
        } catch (ExecutionException e) {
            Throwable t = e.getCause();
            if (t instanceof CommonException) {
                throw (CommonException)t;
            }
            throw e;
        }
    }
```

这时候再来测试一下：

```bash
curl -H 'Trace-Id:123' localhost:8080/user?id=3
```

![](/images/fail-transter-context.png)

可以看到，进入线程池之前的 UserService 中是可以打印出 traceId 的，但是在线程池中的 UserRepository 渠道的 traceId 则为 null，正是由于 Thread local 无法实现跨线程传递。[TTL](<https://github.com/alibaba/transmittable-thread-local>) 帮我们解决了这个问题，可以把把任务提交给线程池时的 Thread local 值传递到 任务执行时：

**AppContext.java**

```java
private static final TransmittableThreadLocal<AppContext> LOCAL = new TransmittableThreadLocal<AppContext>() {
        @Override
        protected AppContext initialValue() {
            return new AppContext();
        }
    };
```

再运行一次，可以看到线程池中也可以获取到全局变量 traceId 了：

![](/images/success-transfer-context.png)

## 单元测试

TTL 好用，可是当隐式传递的 Session 信息无孔不入地被应用到业务逻辑的时候，如果对 Thread local 对象添加单元测试成了不得不面对的问题，下面给出一种方式：Mock Context 对象的静态方法：

**pom.xml**

```xml
<dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.powermock</groupId>
            <artifactId>powermock-module-junit4</artifactId>
            <version>2.0.4</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.powermock</groupId>
            <artifactId>powermock-api-mockito2</artifactId>
            <version>2.0.4</version>
            <scope>test</scope>
        </dependency>
```

**UserServiceTest.java**

```java
@RunWith(PowerMockRunner.class)
@PrepareForTest(AppContext.class)
public class UserServiceTest {

    private static final long CONTEXT_USER_ID = 123L;
    private static final String CONTEXT_TRACE_ID = "trace-id";
    @InjectMocks
    private UserService userService;
    @Mock
    private UserRepository userRepository;

    @Before
    public void setUp() throws Exception {
        AppContext context = new AppContext();
        context.setTraceId(CONTEXT_TRACE_ID);
        context.setUserId(CONTEXT_USER_ID);
        PowerMockito.mockStatic(AppContext.class);
        BDDMockito.given(AppContext.getContext()).willReturn(context);
    }

    @Test
    public void shouldReturnUserWhenUserIdInContext() throws ExecutionException, InterruptedException {
        // given
        when(userRepository.getUserById(eq(CONTEXT_USER_ID))).thenReturn(new User(CONTEXT_USER_ID, "userName"));

        // when
        User user = userService.getCurrentUser();

        // then
        Assert.assertEquals(Long.valueOf(123), user.getId());
        Assert.assertEquals("userName", user.getName());
    }
}
```

## 日志中打印全局参数

在上文的日志中我使用 ```log.("[{}]", traceId)```的方式在日志中加入全局参数，事实上很多情况下我们是想随时知道全局参数的变量值的，怎么避免每次重复地写打印全局变量日志的方式呢？log4j 和 logback 提供的一种方便在多线程条件下记录日志的功能：MDC（Mapped Diagnostic Context，映射调试上下文） 。

只需要在日志模板中加入 ```%X{traceId}```，再在适当的位置把 traceId 放入 ```org.slf4j.MDC``` 中即可：

**logback-spring.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <!-- https://github.com/spring-projects/spring-boot/blob/v1.5.13.RELEASE/spring-boot/src/main/resources/org/springframework/boot/logging/logback/defaults.xml -->
    <include resource="org/springframework/boot/logging/logback/defaults.xml" />

    <property name="FILE_LOG_PATTERN" value="%d{yyyy-MM-dd HH:mm:ss.SSS} ${LOG_LEVEL_PATTERN:-%5p} [%t] [traceId:%X{traceId}] %-40.40logger{39} : %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}"/>
    <property name="CONSOLE_LOG_PATTERN" value="%d{yyyy-MM-dd HH:mm:ss.SSS} ${LOG_LEVEL_PATTERN:-%5p} [%t] [traceId:%X{traceId}] %-40.40logger{39} : %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}"/>

    <property name="APP_NAME" value="global-param-trace" />
    <property name="LOG_PATH" value="${user.home}/${APP_NAME}/logs" />
    <property name="LOG_FILE" value="${LOG_PATH}/application.log" />

    <appender name="APPLICATION"
              class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${LOG_FILE}</file>
        <encoder>
            <pattern>${FILE_LOG_PATTERN}</pattern>
        </encoder>
        <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
            <fileNamePattern>${LOG_FILE}.%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <maxHistory>7</maxHistory>
            <maxFileSize>50MB</maxFileSize>
            <totalSizeCap>20GB</totalSizeCap>
        </rollingPolicy>
    </appender>

    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>${CONSOLE_LOG_PATTERN}</pattern>
            <charset>utf8</charset>
        </encoder>
    </appender>

    <root level="INFO">
        <appender-ref ref="CONSOLE" />
        <appender-ref ref="APPLICATION" />
    </root>
</configuration>
```

这里我选择使用 Interceptor 来放入MDC，给 Session 信息补偿和权限判定预留空间：

**MdcInterceptor.java**

```java
@Component
public class MdcInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
        throws Exception {
        MDC.put(AppContext.KEY_TRACE_ID, AppContext.getContext().getTraceId());
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
        ModelAndView modelAndView) throws Exception {

    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)
        throws Exception {

    }
}

```

在 Configuration 中注册 Interceptor:

**AppConfiguration.java**

```java
@Configuration
public class AppConfiguration extends WebMvcConfigurerAdapter {

    @Autowired
    private MdcInterceptor mdcInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(mdcInterceptor);
    }
}
```

把日志中的 ```[{}]``` 移除，尝试请求 API，可以看到日志中已经可以出现 traceId了：

![](/images/trace-id-fail-with-thread-pool.png)

## 使用 AOP 在多线程日志中打印全局参数

上一步中打印出来的 traceId 在线程池中再次丢失了，这是因为与第一个实现 Context 的方式类似，MDC 也是使用 thread local，无法跨线程传递，要解决这个问题可以使用一个切面，讲 TTL 中的全局参数补偿到 DMC 中：

定义一个注解：

**MdcCompensation.java**

```java
@Documented
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface MdcCompensation {
}
```

将注解和包的位置添加到切面的切点：

**MdcAspect.java**

```java
@Aspect
@Component
public class MdcAspect {

    @Pointcut("execution(* com.adu21.spring.boot.global.param.trace.repository..*.*(..))")
    public void service() {}

    @Before(value = "service() && @annotation(mdcCompensation)")
    public void doBefore(MdcCompensation mdcCompensation) {
        MDC.put(AppContext.KEY_TRACE_ID, AppContext.getContext().getTraceId());
    }
}
```

使用切面，注意这个地方由于 Spring 基于动态代理生成代理类的方式让客户端调用代理类而达到织入的效果，因此使用注解的方法只有在被外部类调用的时候才会生效：

**UserRepository.java**

```java
@MdcCompensation
public User getUserById(Long userId) {
    ...
}
```

再次运行，可以看到所有链路都能打印出 traceId 了：

![](/images/trace-id-success-with-thread-pool.png)

# 代码示例

文中使用的代码，完整版在这里：<https://github.com/ADU-21/global-param-trace>

# Reference

> * <https://blog.csdn.net/xuemengrui12/article/details/98783715>
> * <https://github.com/alibaba/transmittable-thread-local>
> * <https://blog.csdn.net/sunzhenhua0608/article/details/29175283>
> * <https://www.jianshu.com/p/6534945eb3b5>