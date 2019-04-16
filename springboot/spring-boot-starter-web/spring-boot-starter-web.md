# spring-boot-starter-web

## 一、首先锁定版本

```java
<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.0.4.RELEASE</version>
		<relativePath /> <!-- lookup parent from repository -->
</parent>
```

## 二、引入web依赖

```java
<properties>
         <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
</properties>


<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

## 三、在类路径下创建application.yml或application.properties配置文件，并配置如下信息

```java
server:
  port: 8081
  servlet:
    context-path: /dzhyk
  tomcat:
    uri-encoding: UTF-8
```

