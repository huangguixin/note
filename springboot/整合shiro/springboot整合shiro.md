# springboot整合shiro

## 一、首先锁定版本

```java
<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.0.4.RELEASE</version>
		<relativePath /> <!-- lookup parent from repository -->
</parent>
```

## 二、引入依赖

```java
<properties>
         <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
</properties>


<dependency>
			<groupId>org.apache.shiro</groupId>
			<artifactId>shiro-spring</artifactId>
			<version>1.3.2</version>
</dependency>

<dependency>
			<groupId>org.apache.shiro</groupId>
			<artifactId>shiro-core</artifactId>
			<version>1.3.1</version>
</dependency>

<dependency>
			<groupId>org.apache.shiro</groupId>
			<artifactId>shiro-web</artifactId>
			<version>1.3.1</version>
</dependency>

<dependency>
			<groupId>org.apache.shiro</groupId>
			<artifactId>shiro-ehcache</artifactId>
			<version>1.3.1</version>
</dependency>

<dependency>
			<groupId>org.apache.shiro</groupId>
			<artifactId>shiro-quartz</artifactId>
			<version>1.3.1</version>
</dependency>
```

## 三、编写一个shiro配置类

```java
@Configuration
public class ShiroConfig {

	@Bean
	public EhCacheManager getCacheManager() {// 缓存配置
		EhCacheManager cacheManager = new EhCacheManager();
		cacheManager.setCacheManagerConfigFile("classpath:ehcache.xml");
		return cacheManager;
	}
  
  @Bean
	public MemberRealm getRealm() {
      // 1、获取配置的Realm，之所以没使用注解配置，是因为此处需要考虑到加密处理
		MemberRealm realm = new MemberRealm();
		realm.setCredentialsMatcher(new CustomerCredentialsMatcher());	
		return realm;
	}

  //配置 LifecycleBeanPostProcessor. 可以自定的来调用配置在 Spring IOC 容器中 shiro bean 的生命周期方法.
    @Bean(name = "lifecycleBeanPostProcessor")
	public LifecycleBeanPostProcessor getLifecycleBeanPostProcessor() {
		return new LifecycleBeanPostProcessor();
	}
  
  //启用 IOC 容器中使用 shiro 的注解. 但必须在配置了 LifecycleBeanPostProcessor 之后才可以使用. 
    @Bean
	@DependsOn("lifecycleBeanPostProcessor")
	public DefaultAdvisorAutoProxyCreator getDefaultAdvisorAutoProxyCreator() {
		DefaultAdvisorAutoProxyCreator daap = new DefaultAdvisorAutoProxyCreator();
		daap.setProxyTargetClass(true);
		return daap;
	}
  
    @Bean
	public AuthorizationAttributeSourceAdvisor getAuthorizationAttributeSourceAdvisor(
			DefaultWebSecurityManager securityManager) {
		AuthorizationAttributeSourceAdvisor aasa = new                                                                           AuthorizationAttributeSourceAdvisor();
		aasa.setSecurityManager(securityManager);
		return aasa;
	}
  
  //配置 SecurityManager!
    @Bean
	public DefaultWebSecurityManager getSecurityManager(Realm memberRealm, EhCacheManager cacheManager) {
		DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
		securityManager.setRealm(memberRealm);
		securityManager.setCacheManager(cacheManager);
		return securityManager;
	}

  	@Bean
	public ShiroFilterFactoryBean getShiroFilterFactoryBean(DefaultWebSecurityManager securityManager) {
		ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
		// 必须设置 SecurityManager
		shiroFilterFactoryBean.setSecurityManager(securityManager);
		shiroFilterFactoryBean.setLoginUrl("/loginPage");	// 设置登录页路径
		shiroFilterFactoryBean.setSuccessUrl("/pages/hello");	// 设置跳转成功页
		shiroFilterFactoryBean.setUnauthorizedUrl("/pages/unauthUrl");	// 授权错误页
		Map<String, Filter> filters = new HashMap<String, Filter>();
		filters.put("authc", this.getLoginFilter());
		filters.put("logout", this.getLogoutFilter());
		shiroFilterFactoryBean.setFilters(filters);
		Map<String, String> filterChainDefinitionMap = new HashMap<String, String>();
		filterChainDefinitionMap.put("/logout", "logout");
		filterChainDefinitionMap.put("/loginPage", "authc");	// 定义内置登录处理
		filterChainDefinitionMap.put("/pages/back/**", "authc");
		filterChainDefinitionMap.put("/*", "anon");
		shiroFilterFactoryBean.setFilterChainDefinitionMap(filterChainDefinitionMap);
		return shiroFilterFactoryBean;
	}
  
  public FormAuthenticationFilter getLoginFilter() { // 在ShiroFilterFactoryBean中使用
		FormAuthenticationFilter filter = new FormAuthenticationFilter();
		filter.setUsernameParam("mid");
		filter.setPasswordParam("password");
		filter.setRememberMeParam("rememberMe");
		filter.setLoginUrl("/loginPage");	// 登录提交页面
		filter.setFailureKeyAttribute("error");
		return filter;
	}

	public LogoutFilter getLogoutFilter() { // 在ShiroFilterFactoryBean中使用
		LogoutFilter logoutFilter = new LogoutFilter();
		logoutFilter.setRedirectUrl("/");	// 首页路径，登录注销后回到的页面
		return logoutFilter;
	}
  
}
```





## 三、在类路径下创建application.yml或application.properties配置文件，并配置如下信息

```java

```

