---
layout: post
title: "在Spring Boot中使用自定义过滤器和MDC实现高级日志记录"
date: 2026-03-08 10:00:00 +0800
categories: Spring Boot 日志记录
---

在现代Web开发中，日志记录是必不可少的。通过记录日志，开发者可以追踪请求的流程、定位问题并进行性能调优。本文将介绍如何在Spring Boot项目中使用自定义过滤器结合MDC（Mapped Diagnostic Context）技术，实现高级日志记录功能。

## 什么是MDC？

MDC（Mapped Diagnostic Context）是SLF4J和Logback提供的功能，用于在同一线程中存储和获取诊断上下文信息。这些上下文信息可以是用户ID、请求ID等动态数据，能够帮助我们在日志中添加有用的调试信息。

## 代码示例

下面的代码展示了如何配置和使用自定义过滤器来实现日志记录。

```java
package com.xiaoyu.bootcustomfilters;

import jakarta.servlet.Filter;
import jakarta.servlet.FilterChain;
import jakarta.servlet.GenericFilter;
import jakarta.servlet.ServletException;
import jakarta.servlet.ServletRequest;
import jakarta.servlet.ServletResponse;
import org.slf4j.MDC;
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.io.IOException;

@Configuration
public class FilterConfig {

    @Bean
    public FilterRegistrationBean<Filter> loggingFilter() {
        FilterRegistrationBean<Filter> registrationBean = new FilterRegistrationBean<>();

        registrationBean.setFilter(new TraceIdLoggingFilter());
        registrationBean.addUrlPatterns("/users/*");
        registrationBean.setOrder(2);

        return registrationBean;
    }

    private static class TraceIdLoggingFilter extends GenericFilter {
        @Override
        public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
                throws ServletException, IOException {
            try (MDC.MDCCloseable ignored = MDC.putCloseable("traceId", generateTraceId())) {
                chain.doFilter(request, response);
            }
        }

        private String generateTraceId() {
            return java.util.UUID.randomUUID().toString();
        }
    }
}
```

### 配置Logback

要使上述MDC数据在日志中显示，我们需要配置Logback。在项目的资源目录中创建或修改`logback.xml`文件，添加如下配置：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%X{traceId} - %msg%n</pattern>
        </encoder>
    </appender>

    <root level="INFO">
        <appender-ref ref="STDOUT" />
    </root>
</configuration>
```

## 代码解析

1. **FilterConfig类**：这是一个配置类，使用`@Configuration`注解标记，表示这是一个Spring配置类。

2. **loggingFilter方法**：该方法返回一个`FilterRegistrationBean`，用来注册自定义过滤器。这里注册的是`TraceIdLoggingFilter`，并指定了过滤路径为`/users/*`

3. **TraceIdLoggingFilter内部类**：这是实际的过滤器类，继承自`GenericFilter`。在`doFilter`方法中，我们使用MDC将一个唯一的`traceId`（追踪ID）添加到日志上下文中。在请求处理完毕后，MDC会自动清理这个`traceId`

4. **generateTraceId方法**：生成一个唯一的UUID作为`traceId`

5. **logback.xml文件**：配置了一个控制台日志输出，并且日志格式中包含了`traceId`，从而每条日志都会打印出对应的追踪ID。

## 使用说明

当Spring Boot应用启动并且有请求到达`/users/*`路径时，`TraceIdLoggingFilter`会自动为每个请求生成一个唯一的`traceId`，并将其放入MDC。这样，所有在该请求处理过程中生成的日志都会包含这个`traceId`，便于追踪和调试。

### 结果展示

在本地curl这个endpoint的时候可以收到回复

```powershell
curl http://localhost:8080/users
[{"id":"4d194d1f-187c-40a0-b0df-466d1561248f","name":"User1","email":"user1@test.com"},{"id":"0ad8c605-84fa-472f-a7c4-d5c58c10495e","name":"User2","email":"user2@test.com"},{"id":"bdb6e551-4e6c-44bb-b665-44f4d943975f","name":"User3","email":"user3@test.com"}]
```

因为这个call里调用了`users/`，所以会在log里输出如下

```powershell

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::                (v3.1.5)

val
```

如果多次call这个endpoint会多次打印`val`

另外我已经把整个package上传到了github，欢迎查看源码 [github源码](https://github.com/lafengxiaoyu/MDC_blog/tree/main)

## 参考文献

- [Improved Java Logging with Mapped Diagnostic Context (MDC)](https://www.baeldung.com/mdc-in-log4j-2-logback)
- [logback-spring.xml配置文件标签（超详解）](https://juejin.cn/post/7200549600590282789)
- [How to Define a Spring Boot Filter?](https://www.baeldung.com/spring-boot-add-filter?__cf_chl_tk=zV7PJjjl3sWV2hfVYFaIwDMLSOjtkO930PqvOCKQ-1722198116-0.0.1.1-7465)
