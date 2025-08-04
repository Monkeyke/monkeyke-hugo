+++
date = '2025-08-04T05:34:40+08:00'
draft = false
title = 'Spring Cloud 中的 Nacos：服务发现与配置中心详解'

+++
# 

![Nacos封面图](https://picsum.photos/id/237/1200/400)

## 一、Nacos 核心功能概述

Nacos（Dynamic Naming and Configuration Service）是阿里巴巴推出的开源项目，集**服务发现**与**配置管理**于一体，为微服务架构提供了简单易用的解决方案。

其核心优势体现在：



*   同时支持 AP（可用性 + 分区容错性）和 CP（一致性 + 分区容错性）模式

*   动态配置管理，无需重启服务即可更新配置

*   服务健康检查与自动摘除机制

*   丰富的监控与管理控制台

## 二、Nacos 在 Spring Cloud 中的应用

### 2.1 服务注册与发现

在 Spring Cloud 项目中集成 Nacos 服务发现非常简单，只需以下几步：



1.  添加依赖：



```
\<dependency>

&#x20;   \<groupId>com.alibaba.cloud\</groupId>

&#x20;   \<artifactId>spring-cloud-starter-alibaba-nacos-discovery\</artifactId>

\</dependency>
```



1.  配置 Nacos 地址：



```
spring:

&#x20; cloud:

&#x20;   nacos:

&#x20;     discovery:

&#x20;       server-addr: 127.0.0.1:8848
```



1.  启动类添加注解：



```
@SpringBootApplication

@EnableDiscoveryClient

public class ServiceApplication {

&#x20;   public static void main(String\[] args) {

&#x20;       SpringApplication.run(ServiceApplication.class, args);

&#x20;   }

}
```

服务注册成功后，可在 Nacos 控制台查看服务列表：



![Nacos服务列表](https://picsum.photos/id/119/800/400)

### 2.2 配置中心使用

Nacos 作为配置中心可集中管理不同环境的配置：



1.  添加配置依赖：



```
\<dependency>

&#x20;   \<groupId>com.alibaba.cloud\</groupId>

&#x20;   \<artifactId>spring-cloud-starter-alibaba-nacos-config\</artifactId>

\</dependency>
```



1.  创建 bootstrap.yml 配置：



```
spring:

&#x20; cloud:

&#x20;   nacos:

&#x20;     config:

&#x20;       server-addr: 127.0.0.1:8848

&#x20;       file-extension: yaml

&#x20; application:

&#x20;   name: user-service
```



1.  在 Nacos 控制台添加配置：



```
user:

&#x20; name: test

&#x20; age: 18
```



*   Data ID：user-service.yaml

*   配置内容：

1.  在代码中获取配置：



```
@RestController

@RefreshScope // 支持配置动态刷新

public class UserController {

&#x20;   @Value("\${user.name}")

&#x20;   private String userName;

&#x20;  &#x20;

&#x20;   @GetMapping("/user/name")

&#x20;   public String getUserName() {

&#x20;       return userName;

&#x20;   }

}
```

## 三、Nacos 高级特性

### 3.1 配置分组与命名空间



*   **命名空间**：用于区分不同环境（如 dev、test、prod）

*   **配置分组**：同一环境下区分不同业务配置



![Nacos命名空间配置](https://picsum.photos/id/169/800/400)

### 3.2 服务健康检查

Nacos 会定期检查服务健康状态，默认通过发送 HTTP 请求到`/actuator/health`端点，也可自定义健康检查方式。