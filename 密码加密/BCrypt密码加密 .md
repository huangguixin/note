# BCrypt密码加密 

任何应用考虑到安全，绝不能明文的方式保存密码。密码应该通过哈希算法进行加密。
有很多标准的算法比如SHA或者MD5，结合salt(盐)是一个不错的选择。 Spring Security
提供了BCryptPasswordEncoder类,实现Spring的PasswordEncoder接口使用BCrypt强
哈希方法来加密密码。 

BCrypt强哈希方法 每次加密的结果都不一样。 

**pom引入依赖** 

```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring‐boot‐starter‐security</artifactId>
</dependency>
```

**添加配置类**  

我们在添加了spring security依赖后，所有的地址都被spring security所控制了，我们目
前只是需要用到BCrypt密码加密的部分，所以我们要添加一个配置类，配置为所有地址
都可以匿名访问。 

```java
/**
* 安全配置类
*/
@Configuration
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter{
@Override
protected void configure(HttpSecurity http) throws Exception {
        http
        .authorizeRequests()
        .antMatchers("/**").permitAll()
        .anyRequest().authenticated()
        .and().csrf().disable();
	}	
}
```

**创建一个密码加密对象**

```java
@Bean
public BCryptPasswordEncoder bcryptPasswordEncoder(){
	return new BCryptPasswordEncoder();
}
```

**加密演示**

```java
@Autowired
BCryptPasswordEncoder encoder;
public void add(Admin admin) {
    admin.setId(idWorker.nextId()+""); //主键值
    //密码加密
    String newpassword = encoder.encode(admin.getPassword());//加密后
    的密码
    admin.setPassword(newpassword);
    adminDao.save(admin);
}
```

**解密演示**

```java
/**
* 根据登陆名和密码查询
* @param loginname
* @param password
* @return
*/
public Admin findByLoginnameAndPassword(String loginname, String
password){
    Admin admin = adminDao.findByLoginname(loginname);
    if( admin!=null && encoder.matches(password,admin.getPassword()))
    {
    return admin;
    }else{
    return null;
    }
}



/**
* 用户登陆
* @param loginname
* @param password
* @return
*/
@RequestMapping(value="/login",method=RequestMethod.POST)
public Result login(@RequestBody Map<String,String> loginMap){
    Admin admin =
    adminService.findByLoginnameAndPassword(loginMap.get("loginname"),
    loginMap.get("password"));
    if(admin!=null){
    return new Result(true,StatusCode.OK,"登陆成功");
    }else{
    return new Result(false,StatusCode.LOGINERROR,"用户名或密码错
    误");
    }s
}
```

