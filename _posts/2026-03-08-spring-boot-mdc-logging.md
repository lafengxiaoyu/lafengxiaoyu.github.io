---
layout: post
title: "Advanced Logging in Spring Boot Using Custom Filters and MDC"
date: 2026-03-08 10:00:00 +0800
categories: Spring Boot Logging
---

In modern web development, logging is essential. By logging, developers can trace request flows, locate issues, and perform performance tuning. This article introduces how to implement advanced logging functionality in Spring Boot projects using custom filters combined with MDC (Mapped Diagnostic Context) technology.

## What is MDC?

MDC (Mapped Diagnostic Context) is a feature provided by SLF4J and Logback for storing and retrieving diagnostic context information within the same thread. This context information can be dynamic data such as user IDs and request IDs, helping us add useful debugging information to logs.

## Code Example

The following code shows how to configure and use custom filters to implement logging.

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

### Configuring Logback

To make the MDC data appear in logs, we need to configure Logback. Create or modify the `logback.xml` file in the project's resource directory and add the following configuration:

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

## Code Analysis

1. **FilterConfig class**: This is a configuration class marked with the `@Configuration` annotation, indicating it's a Spring configuration class.

2. **loggingFilter method**: This method returns a `FilterRegistrationBean` used to register a custom filter. Here we register `TraceIdLoggingFilter` and specify the filter path as `/users/*`

3. **TraceIdLoggingFilter inner class**: This is the actual filter class extending `GenericFilter`. In the `doFilter` method, we use MDC to add a unique `traceId` (trace ID) to the logging context. After the request processing is complete, MDC automatically cleans up this `traceId`

4. **generateTraceId method**: Generates a unique UUID as the `traceId`

5. **logback.xml file**: Configures console log output with the log format including `traceId`, so each log entry will print the corresponding trace ID.

## Usage

When the Spring Boot application starts and a request arrives at the `/users/*` path, `TraceIdLoggingFilter` will automatically generate a unique `traceId` for each request and place it in MDC. This way, all logs generated during the request processing will include this `traceId`, making it easier to trace and debug.

### Results

You can curl this endpoint locally and receive a response:

```powershell
curl http://localhost:8080/users
[{"id":"4d194d1f-187c-40a0-b0df-466d1561248f","name":"User1","email":"user1@test.com"},{"id":"0ad8c605-84fa-472f-a7c4-d5c58c10495e","name":"User2","email":"user2@test.com"},{"id":"bdb6e551-4e6c-44bb-b665-44f4d943975f","name":"User3","email":"user3@test.com"}]
```

Since this call invokes `users/`, the log output will be as follows:

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

If you call this endpoint multiple times, `val` will be printed multiple times.

I have also uploaded the entire package to GitHub, welcome to check the source code [GitHub source code](https://github.com/lafengxiaoyu/MDC_blog/tree/main)

## References

- [Improved Java Logging with Mapped Diagnostic Context (MDC)](https://www.baeldung.com/mdc-in-log4j-2-logback)
- [logback-spring.xml configuration file tags (detailed)](https://juejin.cn/post/7200549600590282789)
- [How to Define a Spring Boot Filter?](https://www.baeldung.com/spring-boot-add-filter?__cf_chl_tk=zV7PJjjl3sWV2hfVYFaIwDMLSOjtkO930PqvOCKQ-1722198116-0.0.1.1-7465)
