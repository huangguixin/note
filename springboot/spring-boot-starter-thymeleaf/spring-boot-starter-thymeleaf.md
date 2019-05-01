# spring-boot-starter-thymeleaf

## 1.引入依赖

```java
<properties>
  <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding> 
  <thymeleaf.version>3.0.0.RELEASE</thymeleaf.version> 
  <thymeleaf-layout-dialect.version>2.0.0</thymeleaf-layout-dialect.version>      			    <java.version>1.8</java.version> 
 </properties>

<dependency> 
   <groupId>org.springframework.boot</groupId> 
   <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

## 2.在application.properties中配置thymeleaf模板解析器属性

```java
spring.thymeleaf.mode=HTML5 
spring.thymeleaf.encoding=UTF-8 
spring.thymeleaf.content-type=text/html
spring.thymeleaf.cache=false   #开发时关闭缓存,不然没法看到实时页面 
spring.thymeleaf.check-template-location=true  #检查模板位置是否存在。
spring.thymeleaf.enabled=true  #启用MVC Thymeleaf视图解析。
spring.thymeleaf.prefix=classpath:/templates/  #前缀，在构建URL时作为查看名称的前缀。
spring.thymeleaf.suffix=.html #在构建URL时附加到视图名称的后缀。
spring.thymeleaf.excluded-view-names=    #以逗号分隔的视图名称列表，应排除在解析之外。
spring.thymeleaf.template-resolver-order=        #链中模板解析器的顺序。
spring.thymeleaf.view-names=    #可以解析的以逗号分隔的视图名称列表。
```

具体可以配置的参数可以查看 

`org.springframework.boot.autoconfigure.thymeleaf.ThymeleafProperties`这个类,上面的配置实际上就是注入到该类中的属性值.