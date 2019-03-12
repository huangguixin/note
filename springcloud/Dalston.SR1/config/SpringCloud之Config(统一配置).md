# SpringCloud之Config(统一配置)

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

## 1.2创建一个Config服务端工程

```xml
	<!-- springCloud Config -->
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-config-server</artifactId>
		</dependency>
		<!-- 避免Config的Git插件报错：org/eclipse/jgit/api/TransportConfigCallback -->
		<dependency>
			<groupId>org.eclipse.jgit</groupId>
			<artifactId>org.eclipse.jgit</artifactId>
			<version>4.10.0.201712302008-r</version>
		</dependency>
```

### 1.2.1配置文件内容

```yml
server: 
  port: 3344 
  
spring:
  application:
    name:  microservicecloud-config
  cloud:
    config:
      server:
        git:
          uri: git@github.com:icroservicecloud-config.git #GitHub上面的git仓库名字
 


```

### 1.2.2主启动类

```java
@SpringBootApplication
@EnableConfigServer
public class Config_3344_StartSpringCloudApp
{
	public static void main(String[] args)
	{
		SpringApplication.run(Config_3344_StartSpringCloudApp.class, args);
	}
}

```

## 1.3创建一个Config客户端

```xml
<!-- SpringCloudConfig配置 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
</dependency>
```

### 1.3.1在每一个客户端创建bootstrap.yml

```yml
spring:
  cloud:
    config:
      name: microservicecloud-config-dept-client #需要从github上读取的资源名称，注意没有yml后缀名
      #profile配置是什么就取什么配置dev or test
      profile: dev
      #profile: test
      label: master
      uri: http://config-3344.com:3344  #SpringCloudConfig获取的服务地址


```

