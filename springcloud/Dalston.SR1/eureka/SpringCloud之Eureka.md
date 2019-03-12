# SpringCloud之Eureka（服务的注册与发现）

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

## 1.2创建一个子工程（搭建Eureka注册中心）

### 1.2.1加入Eureka（服务端）的Maven坐标

```xml
<!--eureka-server服务端 -->
<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-eureka-server</artifactId>
</dependency>
```

### 1.2.2在resources源码文件夹下创建application.yml或application.properties文件并加入如下配置

```yml
eureka: 
  instance:
    hostname: eureka7001.com   #eureka服务端的实例名称  Ip地址或域名都行
  client: 
    register-with-eureka: false     #false表示不向注册中心注册自己。
    fetch-registry: false     #false表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    service-url: 
      #单机 defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/       #设置 与Eureka Server交互的地址，查询服务和注册服务都需要依赖这个地址（单机）。
      defaultZone: http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/ #Eureka集群设置
      
```

### 1.2.3创建一个启动类

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@SpringBootApplication
@EnableEurekaServer // EurekaServer服务器端启动类,接受其它微服务注册进来
public class EurekaServerApp
{
	public static void main(String[] args)
	{
		SpringApplication.run(EurekaServerApp.class, args);
	}
}

```

## 1.3创建另一个子工程（搭建Eureka客户端）

### 1.3.1加入Eureka（客户端）的Maven坐标

```xml
<!-- 将微服务provider侧注册进eureka -->
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-eureka</artifactId>
</dependency>
```

### 1.3.2在resources源码文件夹下创建application.yml或application.properties文件并加入如下配置

```
spring:
   application:
    name: microservicecloud  #微服务的名称，服务之间调用所需
    
eureka:
  client: #客户端注册进eureka服务列表内
    service-url: 
      #defaultZone: http://localhost:7001/eureka
       defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/      
  instance:
    instance-id: microservicecloud-dept8001  #在Eureka注册列表显示的名字
    prefer-ip-address: true     #访问路径可以显示IP地址     
```

### 1.3.3创建一个启动类

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableEurekaClient //本服务启动后会自动注册进eureka服务中
public class DeptProviderApp
{
	public static void main(String[] args)
	{
		SpringApplication.run(DeptProviderApp.class, args);
	}
}
```

## 1.4Eureka之服务的发现

### 1.4.1在Eureka的客户端的Controller中注入DiscoveryClient这个类，并写个接口供调用就行

```java
import java.util.List;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cloud.client.ServiceInstance;
import org.springframework.cloud.client.discovery.DiscoveryClient;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class DeptController
{
	@Autowired
	private DiscoveryClient client;

	@RequestMapping(value = "/dept/discovery", method = RequestMethod.GET)
	public Object discovery()
	{
		List<String> list = client.getServices();
		System.out.println("**********" + list);

		List<ServiceInstance> srvList = client.getInstances("MICROSERVICECLOUD-DEPT");
		for (ServiceInstance element : srvList) {
			System.out.println(element.getServiceId() + "\t" + element.getHost() + "\t" + element.getPort() + "\t"
					+ element.getUri());
		}
		return this.client;
	}

}

```

### 1.4.2在启动类上加个@EnableDiscoveryClient 

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableEurekaClient //本服务启动后会自动注册进eureka服务中
@EnableDiscoveryClient //服务发现
public class DeptProviderApp
{
	public static void main(String[] args)
	{
		SpringApplication.run(DeptProviderApp.class, args);
	}
}

```

### 1.4.3微服务info信息的完善

#### 1.4.3.1父工程POM文件中加入如下配置

```xml
<build>
		<finalName>父工程名称</finalName>
		<resources>
			<resource>
				<directory>src/main/resources</directory>
				<filtering>true</filtering>
			</resource>
		</resources>
		<plugins>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-resources-plugin</artifactId>
				<configuration>
					<delimiters>
						<delimit>$</delimit>
					</delimiters>
				</configuration>
			</plugin>
		</plugins>
	</build>
```

#### 1.4.3.2每一个微服务加入如下依赖

```xml
<!-- actuator监控信息完善 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

#### 1.4.3.3配置文件加入如下信息即可

```yml
info: 
  app.name: microservicecloud
  company.name: www.u.com
  build.artifactId: $project.artifactId$
  build.version: $project.version$
```



