---
title: 在 Filter 中给 HttpServletRequest 添加 Header
date: 2019-08-18 17:55:54
categories: Java
tags: 
- Springboot
- 微服务
- Servelet
---

本文介绍如何在 Spring 的 Filter 中修改向下传递的 Request 中的 Header。

<!-- more -->

# 业务场景

系统已经有了一套基于 HTTP Header 的鉴权逻辑，现在我们希望在这套逻辑之上做一层扩展用于支持更多的鉴权类型，因此需要在 Interceptor 只上添加一个 Filter 把不同鉴权逻辑的信息转换为原有鉴权逻辑所需要的 Header。

# 遇到问题

Filter 中能够获取到的 [javax.servlet.http.HttpServletRequest](<https://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletRequest.html>) 暴露的接口，除了从其父接口 [javax.servlet.ServletRequest ](<https://docs.oracle.com/javaee/6/api/javax/servlet/ServletRequest.html>) 接口继承的`setCharacterEncoding`方法和`setAttribute`方法之外，没有`setter`方法。因此 HttpServletRequest类型对象是只读的。

我们只能使用装饰器模式，扩展 HttpServletRequest 的一个实现类 [javax.servlet.http.HttpServletRequestWrapper](<https://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletRequestWrapper.html>)，并使用装饰的对象`MutableHttpServletRequest`代替原始对象，以下为示例代码：

```java
import java.util.Collections;
import java.util.Enumeration;
import java.util.HashMap;
import java.util.HashSet;
import java.util.Map;
import java.util.Set;
 
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletRequestWrapper;
 
final class MutableHttpServletRequest extends HttpServletRequestWrapper {
    // holds custom header and value mapping
    private final Map<String, String> customHeaders;
 
    public MutableHttpServletRequest(HttpServletRequest request){
        super(request);
        this.customHeaders = new HashMap<String, String>();
    }
    
    public void putHeader(String name, String value){
        this.customHeaders.put(name, value);
    }
 
    public String getHeader(String name) {
        // check the custom headers first
        String headerValue = customHeaders.get(name);
        
        if (headerValue != null){
            return headerValue;
        }
        // else return from into the original wrapped object
        return ((HttpServletRequest) getRequest()).getHeader(name);
    }
 
    public Enumeration<String> getHeaderNames() {
        // create a set of the custom header names
        Set<String> set = new HashSet<String>(customHeaders.keySet());
        
        // now add the headers from the wrapped request object
        @SuppressWarnings("unchecked")
        Enumeration<String> e = ((HttpServletRequest) getRequest()).getHeaderNames();
        while (e.hasMoreElements()) {
            // add the names of the request headers into the list
            String n = e.nextElement();
            set.add(n);
        }
 
        // create an enumeration from the set and return
        return Collections.enumeration(set);
    }
}
```

有了这个装饰对象，就可以在 Filter 中使用 MutableHttpServletRequest 类来拦截传入的HTTP 请求以添加一些自定义 Header，代码如下：

```java
import java.io.IOException;
 
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
 
public class SecurityFilter implements javax.servlet.Filter {
 
    @Override
    public void destroy() {
        
    }
 
    @Override
    public void doFilter(ServletRequest request, ServletResponse response,
            FilterChain chain) throws IOException, ServletException {
        HttpServletRequest req = (HttpServletRequest) request;
        MutableHttpServletRequest mutableRequest = new MutableHttpServletRequest(req);
        ...
        mutableRequest.putHeader("x-custom-header", "custom value");
        chain.doFilter(mutableRequest, response);
    }
 
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        
    }
}
```

# Reference

> * <https://wilddiary.com/adding-custom-headers-java-httpservletrequest/>