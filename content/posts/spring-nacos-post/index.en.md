+++
date = '2025-08-01T05:05:50+08:00' 
draft = false 
title = 'Spring Cloud Nacos Explained Simply: One-stop Solution for Service Discovery and  Configuration Management'
categories = ["Technical Sharing"] 
tags = ["Nacos", "Spring", "Spring Cloud", "Backend"] 
+++

![img](1.png)

In the wave of microservices architecture, dynamic management of services and centralized configuration have become core challenges that developers must face. At this point, Nacos acts like a Swiss Army knife, offering us an elegant and powerful one-stop solution. This article will guide you from scratch to deeply understand Nacos' core features, and demonstrate in practice how to seamlessly integrate it into a Spring Cloud project.

## **I\. What is Nacos? Why choose it?**

Nacos (Dynamic Naming and Configuration Service) is an open-source dynamic service discovery, configuration management, and service management platform developed by Alibaba, which makes it easier to build cloud-native applications. Simply put, it plays two key roles within a project: service registry and configuration center.

The core advantages of choosing Nacos are:

*   Integrated functions: No need to maintain two separate systems, such as Eureka/Consul and Spring Cloud Config. Nacos handles everything in one place.
*   Flexible modes: It supports both AP (High Availability) and CP (High Consistency) modes, allowing you to switch flexibly between them based on your business scenario for service discovery, meeting different requirements.
*   Dynamic configuration with real-time effect: After modifying the configuration, the application can perceive the changes without needing to restart, greatly improving operation and maintenance efficiency.
*   Powerful Console: Provides an elegant and user-friendly visual interface, making it easy for us to monitor service status and manage configurations.
*   Service Health Check: It can automatically detect the health status of service instances and intelligently remove unhealthy instances, ensuring the overall stability of the system.

## **II. Practical Application of Nacos: Integrating with Spring Cloud**

Next, we will see how simple it is to integrate Nacos through a typical Spring Cloud project.

### **2.1 As a Service Registration and Discovery Center**

Objective: Let our microservices automatically register with Nacos, and be able to discover each other.

#### **Step 1: Add Maven Dependency**

In your `pom.xml` file, introduce the `nacos-discovery` starter.

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

#### **Step 2: Configure Nacos server address**

In `application.yml` , specify the Nacos address.

```yaml
spring:
  application:
    # 为你的服务起一个名字
    name: order-service
  cloud:
    nacos:
      discovery:
        # Nacos Server 的地址
        server-addr: 127.0.0.1:8848
```

#### **Step 3: Enable service discovery on the startup class**

Add the `@EnableDiscoveryClient` annotation to the main startup class of Spring Boot.

```java
@SpringBootApplication
@EnableDiscoveryClient // 声明这是一个Nacos客户端
public class ServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(ServiceApplication.class, args);
    }
}
```

After starting the application, you can see that the service named `order-service` has been successfully registered in the "Service Management" -> "Service List" section of the Nacos console!

### **2.2 As a unified configuration center**

Objective: Extract the application's configuration information from the code and entrust it to Nacos for unified management.

#### **Step 1: Add Maven dependency**

Similarly, introduce the `nacos-config` starter in `pom.xml` .

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
```

#### **Step 2: Create `bootstrap.yml`**

Note: The configuration from the configuration center needs to be loaded before `application.yml` , so it must be written in the `bootstrap.yml` file.

```yaml
spring:
  application:
    # 同样需要指定服务名，Nacos会根据它来查找配置
    name: user-service
  cloud:
    nacos:
      config:
        server-addr: 127.0.0.1:8848
        # 指定配置文件格式
        file-extension: yaml
```

#### **Step 3: Create a configuration in the Nacos console**

1.  Log in to the Nacos console, and go to "Configuration Management" -> "Configuration List".
2.  Click the "+" button to create a new configuration.
3.  Fill in the Data ID: `user-service.yaml` (the format is usually `${spring.application.name}.${file-extension}` )
4.  Select the configuration format as `YAML` .
5.  Enter in "Configuration Content":
    
    ```yaml
    user:
      name: CoderWang
      age: 25
    ```
    
6.  Click "Publish".

#### **Step 4: Dynamically retrieve the configuration in the code**

Use `@Value` annotation to read the configuration, and add `@RefreshScope` annotation to support dynamic configuration refresh.

```java
@RestController
@RefreshScope // 这个注解是实现配置动态刷新的关键！
public class UserController {

    @Value("${user.name}")
    private String userName;

    @GetMapping("/user/name")
    public String getUserName() {
        // 返回从Nacos获取的用户名
        return "Current user name is: " + userName;
    }
}
```

Now, start the application and access `/user/name` . Then try modifying the value of `user.name` in the Nacos console and publish it. Access the interface again, and you will find that the updated value is returned without needing to restart the application!

## **III. Advanced Features of Nacos**

*   Namespace: This is the core of environment isolation. You can create different namespaces for different environments such as `dev` , `test` , `prod` , achieving natural isolation of configurations and services, which is very secure.
*   Configuration Group: When a project has multiple different business configurations, you can use Groups to logically categorize them, making configuration management clearer.
*   Service Health Check: The Nacos Client regularly sends heartbeats to the Server to indicate that it is "alive." If the Server does not receive heartbeats for a long time, it will mark the instance as unhealthy and temporarily remove it from the service list, thus avoiding traffic being directed to failed services and achieving high availability for service calls.

## **IV. Conclusion**

Nacos, with its powerful capabilities that combine service discovery and configuration management, as well as its seamless integration with the Spring Cloud ecosystem, has become one of the preferred components for building modern microservices architectures. It greatly simplifies service governance and configuration maintenance, allowing developers to focus more on implementing business logic.

* * *