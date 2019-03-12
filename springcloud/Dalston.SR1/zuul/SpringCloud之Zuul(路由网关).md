# SpringCloud之Zuul(路由网关)

# 1.父工程限定版本信息

## 1.1Maven坐标（GAV）

```xml
<dependencyManagement>
	<dependencies>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-dependencies</artifactId>
			<version>Dalston.SR1</version>
			<type>pom</type>
			<scope>import</scope>
		</dependency>
	</dependencies>
</dependencyManagement>
```
## 1.2创建一个子工程（搭建全局路由网关）

### 1.2.1加入Zuul的Maven坐标

```xml
<!-- zuul路由网关 -->
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-zuul</artifactId>
</dependency>
```

### 1.2.2在resources源码文件夹下创建application.yml或application.properties文件并加入如下配置

```yml
zuul: 
  #ignored-services: microservicecloud-dept   某个服务只能通过zuul访问，不能通过服务名访问
  prefix: /hgx                             #全局统一访问前缀
  ignored-services: "*"                    #所有服务只能通过zuul访问，不能通过服务名访问
  routes: 
    mydept.serviceId: microservicecloud-dept     mydept自定义key ，serviceId微服务的名称
    mydept.path: /mydept/**                      对应微服务的访问路径
```

### 1.2.3创建启动类

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableZuulProxy
public class Zuul_9527_StartSpringCloudApp
{
	public static void main(String[] args)
	{
		SpringApplication.run(Zuul_9527_StartSpringCloudApp.class, args);
	}
}
```

