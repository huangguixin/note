# 自动装配-@Autowired-@Resource-@Inject

## 自动装配

 * 		**Spring利用依赖注入（DI），完成对IOC容器中中各个组件的依赖关系赋值；**

### @Autowired-自动注入

- **默认优先按照类型去容器中找对应的组件:applicationContext.getBean(BookDao.class);找到就赋值**

- **如果找到多个相同类型的组件，再将属性的名称作为组件的id去容器中查找applicationContext.getBean("bookDao")**

- **@Qualifier("bookDao")：使用@Qualifier指定需要装配的组件的id，而不是使用属性名**

- **自动装配默认一定要将属性赋值好，没有就会报错；可以使用@Autowired(required=false);**

- **@Primary：让Spring进行自动装配的时候，默认使用首选的bean；**

  **也可以继续使用@Qualifier指定需要装配的bean的名字**



**Spring还支持使用@Resource(JSR250)和@Inject(JSR330)[java规范的注解]**



### @Resource(JSR250)-自动注入

- **可以和@Autowired一样实现自动装配功能；默认是按照组件名称进行装配的；**
- **没有能支持@Primary功能没有支持@Autowired（reqiured=false）;**



### @Inject-自动注入

- **需要导入javax.inject的包，和Autowired的功能一样。没有required=false的功能；**

  ```pom
  <!-- https://mvnrepository.com/artifact/javax.inject/javax.inject -->
  <dependency>
    <groupId>javax.inject</groupId>
    <artifactId>javax.inject</artifactId>
    <version>1</version>
  </dependency>
  ```

  ​

### 三者的区别

- **@Autowired:Spring定义的； @Resource、@Inject都是java规范**



### 原理

- **AutowiredAnnotationBeanPostProcessor:解析完成自动装配功能；**



### 例子：

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
@Controller
public class BookController {
	
	@Autowired
	private BookService bookService;

}


import javax.annotation.Resource;
import javax.inject.Inject;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Service;
@Service
public class BookService {
	
	@Qualifier("bookDao")
	@Autowired(required=false)
	@Resource(name="bookDao2")
	@Inject
	private BookDao bookDao;
	
	public void print(){
		System.out.println(bookDao);
	}

	@Override
	public String toString() {
		return "BookService [bookDao=" + bookDao + "]";
	}
	
}


import org.springframework.stereotype.Repository;
//名字默认是类名首字母小写
@Repository
public class BookDao {
	
	private String lable = "1";

	public String getLable() {
		return lable;
	}

	public void setLable(String lable) {
		this.lable = lable;
	}

	@Override
	public String toString() {
		return "BookDao [lable=" + lable + "]";
	}
}


import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
@Configuration
@ComponentScan({"com.hgx.service","com.hgx.dao",
	"com.hgx.controller","com.hgx.bean"})
public class MainConifgOfAutowired {
	
	@Primary
	@Bean("bookDao2")
	public BookDao bookDao(){
		BookDao bookDao = new BookDao();
		bookDao.setLable("2");
		return bookDao;
	}
}


import org.junit.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
@Test
	public void test01(){
		AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(MainConifgOfAutowired.class);
		
		BookService bookService = applicationContext.getBean(BookService.class);
		System.out.println(bookService);
		
		BookDao bean = applicationContext.getBean(BookDao.class);
		System.out.println(bean);
		
		applicationContext.close();
	}
```

