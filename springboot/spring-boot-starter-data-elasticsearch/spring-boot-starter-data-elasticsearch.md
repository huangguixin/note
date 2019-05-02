# spring-boot-starter-data-elasticsearch

## 参考博客

[[SpringBoot 2.0集成spring-data-elasticsearch](https://segmentfault.com/a/1190000015568618)](https://segmentfault.com/a/1190000015568618?utm_source=tag-newest)



## pom.xml引入依赖 

```springboot
<dependencies>
    <dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
    </dependency>
</dependencies>
```

## application.yml 

```yml
server:
	port: 9007
spring:
	application:
		name: search #指定服务名
	data:
		elasticsearch:
			cluster‐nodes: 127.0.0.1:9300
```

## 创建映射实体

```java
/
**
* 实体类
*/
@Document(indexName="hgx",type="article")
public class Article implements Serializable{
    @Id
    private String id;//ID
  
    @Field(index= true,analyzer="ik_max_word",searchAnalyzer="ik_max_word")
    private String title;//标题
  
    @Field(index= true ,analyzer="ik_max_word",searchAnalyzer="ik_max_word")
    private String content;//文章正文
  
    private String state;//审核状态
    //getter and setter ......
}
```

## 接口访问层

```java
/**
* 数据访问层接口
*/
public interface ArticleSearchDao extends
	ElasticsearchRepository<Article,String> {
}
```

接下来的操作更springboot-jpa一致