# spring-boot-starter-freemarker

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
			<artifactId>spring-boot-starter-freemarker</artifactId>
</dependency>
```

## 三、在类路径下创建application.yml或application.properties配置文件，并配置如下信息

```java
spring.freemarker.allow-request-override=false
spring.freemarker.cache=true
spring.freemarker.check-template-location=true
spring.freemarker.charset=UTF-8
spring.freemarker.content-type=text/html
spring.freemarker.expose-request-attributes=false
spring.freemarker.expose-session-attributes=false
spring.freemarker.expose-spring-macro-helpers=false
spring.freemarker.prefix=
#spring.freemarker.request-context-attribute=
#spring.freemarker.settings.
spring.freemarker.suffix=.ftl
spring.freemarker.template-loader-path=classpath:/templates/
#spring.freemarker.view-names= # whitelist of view names that can be resolved
template.number_format=0.######
template.boolean_format=true,false
template.datetime_format=yyyy-MM-dd
template.date_format=yyyy-MM-dd
template.time_format=HH:mm:ss
template.loader_path=classpath:/templates/
template.update_delay=0
template.encoding=UTF-8
```

