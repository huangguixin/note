# 分布式搜索引擎ElasticSearch 

## ElasticSearch简介 

### 什么是ElasticSearch 

 Elasticsearch是一个实时的分布式搜索和分析引擎。它可以帮助你用前所未有的速度去处理大规模数据。ElasticSearch是一个基于Lucene的搜索服务器。它提供了一个分布式多用户能力的全文搜索引擎，基于RESTful web接口。Elasticsearch是用Java开发的，并作为Apache许可条款下的开放源码发布，是当前流行的企业级搜索引擎。设计用于云计算中，能够达到实时搜索，稳定，可靠，快速，安装使用方便。 

### ElasticSearch特点 

（1）可以作为一个大型分布式集群（数百台服务器）技术，处理PB级数据，服务大公司；也可以运行在单机上 

（2）将全文检索、数据分析以及分布式技术，合并在了一起，才形成了独一无二的ES； 

（3）开箱即用的，部署简单 

（4）全文检索，同义词处理，相关度排名，复杂数据分析，海量数据的近实时处理 

### ElasticSearch体系结构 

![](img/01.png)

## 走进ElasticSearch 

### ElasticSearch部署与启动 

下载ElasticSearch 5.6.8版本
https://www.elastic.co/downloads/past-releases/elasticsearch-5-6-8 

#### windows下安装

无需安装，解压安装包后即可使用
在命令提示符下，进入ElasticSearch安装目录下的bin目录,执行命令 

```elasticsearch
elasticsearch
```

即可启动。 

我们打开浏览器，在地址栏输入http://127.0.0.1:9200/ 即可看到输出结果 

```elasticsearch
{
"name" : "uV2glMR",
"cluster_name" : "elasticsearch",
"cluster_uuid" : "RdV7UTQZT1‐Jnka9dDPsFg",
"version" : {
"number" : "5.6.8",
"build_hash" : "688ecce",
"build_date" : "2018‐02‐16T16:46:30.010Z",
"build_snapshot" : false,
"lucene_version" : "6.6.1"
},
"tagline" : "You Know, for Search"
}
```

### Postman调用RestAPI 

#### 新建索引 

```elasticsearch
例如我们要创建一个叫articleindex的索引 ,就以put方式提交

http://127.0.0.1:9200/articleindex/
```

#### 新建文档 

```elasticsearch
新建文档：

以post方式提交 http://127.0.0.1:9200/articleindex/article


body:
{
    "title":"SpringBoot2.0",
    "content":"发布啦"
}

返回结果如下：
{
    "_index": "articleindex",
    "_type": "article",
    "_id": "AWPKsdh0FdLZnId5S_F9",
    "_version": 1,
    "result": "created",
    "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
    },
    "created": true
}

_id是由系统自动生成的。
```

#### 查询全部文档 

```elasticsearch
查询某索引某类型的全部数据，以get方式请求

http://127.0.0.1:9200/articleindex/article/_search 

返回结果如下：

{
    "took": 5,
    "timed_out": false,
    "_shards": {
    "total": 5,
    "successful": 5,
    "skipped": 0,
    "failed": 0
    },
    "hits": {
    "total": 2,
    "max_score": 1,
    "hits": [
    {
    "_index": "articleindex",
    "_type": "article",
    "_id": "AWPKrI4pFdLZnId5S_F7",
    "_score": 1,
    "_source": {
    "title": "SpringBoot2.0",
    "content": "发布啦"
    }
    },
    {
    "_index": "articleindex",
    "_type": "article",
    "_id": "AWPKsdh0FdLZnId5S_F9",
    "_score": 1,
    "_source": {
    "title": "elasticsearch入门",
    "content": "零基础入门"
    }
    }
    ]
    }
}
```

#### 修改文档 

```java
以put形式提交以下地址：

http://127.0.0.1:9200/articleindex/article/AWPKrI4pFdLZnId5S_F7

body:
{
  "title":"SpringBoot2.0正式版",
  "content":"发布了吗"
}

返回结果：
{
    "_index": "articleindex",
    "_type": "article",
    "_id": "AWPKsdh0FdLZnId5S_F9",
    "_version": 2,
    "result": "updated",
    "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
    },
    "created": false
}

如果我们在地址中的ID不存在，则会创建新文档

以put形式提交以下地址：

http://127.0.0.1:9200/articleindex/article/1

body:
{
  "title":"课程好给力",
  "content":"知识点很多"
}

返回信息：
{
  "_index": "articleindex",
  "_type": "article",
  "_id": "1",
  "_version": 1,
  "result": "created",
  "_shards": {
  "total": 2,
  "successful": 1,
  "failed": 0
  },
  "created": true
}

再次查询，看是否有新增的这条文档
```

#### 按ID查询文档 

```elasticsearch
GET方式请求

http://127.0.0.1:9200/articleindex/article/1
```

#### 基本匹配查询 

```elasticsearch
根据某列进行查询 get方式提交下列地址：

http://192.168.184.134:9200/articleindex/article/_search?q=title:课程好给力

以上为按标题查询，返回结果如下：
{
    "took": 10,
    "timed_out": false,
    "_shards": {
    "total": 5,
    "successful": 5,
    "skipped": 0,
    "failed": 0
    },
    "hits": {
    "total": 1,
    "max_score": 2.0649285,
    "hits": [
    {
    "_index": "articleindex",
    "_type": "article",
    "_id": "1",
    "_score": 2.0649285,
    "_source": {
    "title": "课程好给力",
    "content": "知识点很多"
    }
    }
    ]
    }
}
```

#### 模糊查询 

```elasticsearh
我们可以用*代表任意字符

http://127.0.0.1:9200/articleindex/article/_search?q=title:*s*
```

#### 删除文档 

```elasticsearch
根据ID删除文档,删除ID为1的文档 DELETE方式提交

http://127.0.0.1:9200/articleindex/article/1

返回结果如下：

{
    "found": true,
    "_index": "articleindex",
    "_type": "article",
    "_id": "1",
    "_version": 2,
    "result": "deleted",
    "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
    }
}

再次查看全部是否还存在此记录
```



## Head插件的安装与使用 

### Head插件安装 

如果都是通过rest请求的方式使用Elasticsearch，未免太过麻烦，而且也不够人性化。我
们一般都会使用图形化界面来实现Elasticsearch的日常管理，最常用的就是Head插件 



下载head插件：https://github.com/mobz/elasticsearch-head 

也即：elasticsearch-head-master.zip 



解压到任意目录，但是要和elasticsearch的安装目录区别开 



安装node js ,安装cnpm 

```node
npm install ‐g cnpm ‐‐registry=https://registry.npm.taobao.org
```

将grunt安装为全局命令 。Grunt是基于Node.js的项目构建工具。它可以自动运行你所设定的任务 

```node
npm install ‐g grunt‐cli
```

安装依赖 

```node
cnpm install
```

进入head目录启动head，在命令提示符下输入命令 

```node
grunt server
```

打开浏览器，输入 http://localhost:9100 



点击连接按钮没有任何相应，按F12发现有如下错误
No 'Access-Control-Allow-Origin' header is present on the requested resource
这个错误是由于elasticsearch默认不允许跨域调用，而elasticsearch-head是属于前端工
程，所以报错。
我们这时需要修改elasticsearch的配置，让其允许跨域访问。 



修改elasticsearch配置文件：elasticsearch.yml，增加以下两句命令： 

```elasticsearch
http.cors.enabled: true
http.cors.allow‐origin: "*"
```

此步为允许elasticsearch跨越访问 点击连接即可看到相关信息 

![](img/02.png)

### Head插件操作 

#### 新建索引 

选择“索引”选项卡，点击“新建索引”按钮 

![](img/03.png)

输入索引名称点击OK 

#### 新建或修改文档 

在复合查询中提交地址，输入内容，提交方式为PUT 

![](img/04.png)

点击数据浏览 ,点击要查询的索引名称，右侧窗格中显示文档信息 

![](img/05.png)

点击文档信息 

![](img/06.png)

修改数据后重新提交请求 , 此时因为ID已经存在，所以执行的是修改操作。
重新查询此记录，发现版本为2 。也就是说每次修改后版本都会增加1 

![](img/07.png)

#### 搜索文档 

![](img/08.png)

#### 删除文档 

![](img/09.png)

## IK分词器 

### 什么是IK分词器 

```elasticsearch
我们在浏览器地址栏输入http://127.0.0.1:9200/_analyze?
analyzer=chinese&pretty=true&text=我是程序员，浏览器显示效果如下
```

```elasticsearch
{
    "tokens" : [
    {
    "token" : "我",
    "start_offset" : 0,
    "end_offset" : 1,
    "type" : "<IDEOGRAPHIC>",
    "position" : 0
    },
    {
    "token" : "是",
    "start_offset" : 1,
    "end_offset" : 2,
    "type" : "<IDEOGRAPHIC>",
    "position" : 1
    },
    {
    "token" : "程",
    "start_offset" : 2,
    "end_offset" : 3,
    "type" : "<IDEOGRAPHIC>",
    "position" : 2
    },
    {
    "token" : "序",
    "start_offset" : 3,
    "end_offset" : 4,
    "type" : "<IDEOGRAPHIC>",
    "position" : 3
    },
    {
    "token" : "员",
    "start_offset" : 4,
    "end_offset" : 5,
    "type" : "<IDEOGRAPHIC>",
    "position" : 4
    }
    ]
}
```

默认的中文分词是将每个字看成一个词，这显然是不符合要求的，所以我们需要安装中文分词器来解决这个问题。
IK分词是一款国人开发的相对简单的中文分词器。虽然开发者自2012年之后就不在维护了，但在工程应用中IK算是比较流行的一款！我们今天就介绍一下IK中文分词器的使用。 

### IK分词器安装 

下载地址：https://github.com/medcl/elasticsearch-analysis-ik/releases 下载5.6.8版 

（1）先将其解压，将解压后的elasticsearch文件夹重命名文件夹为ik
（2）将ik文件夹拷贝到elasticsearch/plugins 目录下。
（3）重新启动，即可加载IK分词器 

### IK分词器测试 

IK提供了两个分词算法ik_smart 和 ik_max_word
其中 ik_smart 为最少切分，ik_max_word为最细粒度划分
我们分别来试一下 

（1）最小切分：在浏览器地址栏输入地址 

http://127.0.0.1:9200/_analyze?analyzer=ik_smart&pretty=true&text=我是程序员
输出的结果为： 

```elasticsearch
{
"tokens" : [
{
"token" : "我",
"start_offset" : 0,
"end_offset" : 1,
"type" : "CN_CHAR",
"position" : 0
},
{
"token" : "是",
"start_offset" : 1,
"end_offset" : 2,
"type" : "CN_CHAR",
"position" : 1
},
{
"token" : "程序员",
"start_offset" : 2,
"end_offset" : 5,
"type" : "CN_WORD",
"position" : 2
}
]
}
```

（2）最细切分：在浏览器地址栏输入地址
http://127.0.0.1:9200/_analyze?analyzer=ik_max_word&pretty=true&text=我是程序员 

输出的结果为： 

```elasticsearch
{
"tokens" : [
{
"token" : "我",
"start_offset" : 0,
"end_offset" : 1,
"type" : "CN_CHAR",
"position" : 0
},
{
"token" : "是",
"start_offset" : 1,
"end_offset" : 2,
"type" : "CN_CHAR",
"position" : 1
},
{
"token" : "程序员",
"start_offset" : 2,
"end_offset" : 5,
"type" : "CN_WORD",
"position" : 2
},
{
"token" : "程序",
"start_offset" : 2,
"end_offset" : 4,
"type" : "CN_WORD",
"position" : 3
},
{
"token" : "员",
"start_offset" : 4,
"end_offset" : 5,
"type" : "CN_CHAR",
"position" : 4
}
]
}
```

### 自定义词库 

我们现在测试"广东东莞银行"，浏览器的测试效果如下：

```elasticserach
http://127.0.0.1:9200/_analyze?analyzer=ik_smart&pretty=true&text=广东东莞银行
```

默认的分词并没有识别“广东东莞银行”是一个词。如果我们想让系统识别“广东东莞银行”是一个词，需要编辑自定义词库。 

（1）进入elasticsearch/plugins/ik/config目录
（2）新建一个my.dic文件，编辑内容： 

```elasticsearch
广东东莞银行
```

修改IKAnalyzer.cfg.xml（在ik/config目录下） 

```elasticsearch
<properties>
<comment>IK Analyzer 扩展配置</comment>
<!‐‐用户可以在这里配置自己的扩展字典 ‐‐>
<entry key="ext_dict">my.dic</entry>
<!‐‐用户可以在这里配置自己的扩展停止词字典‐‐>
<entry key="ext_stopwords"></entry>
</properties>
```

重新启动elasticsearch,通过浏览器测试分词效果 

```e
{
"tokens" : [
{
"token" : "广东东莞银行",
"start_offset" : 0,
"end_offset" : 4,
"type" : "CN_WORD",
"position" : 0
}
]
}
```



## elasticsearch与MySQL数据同步 

### Logstash 

#### 什么是Logstash 

Logstash是一款轻量级的日志搜集处理框架，可以方便的把分散的、多样化的日志搜集
起来，并进行自定义的处理，然后传输到指定的位置，比如某个服务器或者文件 

#### Logstash安装与测试 

解压，进入bin目录 

```elasticsearch
logstash ‐e 'input { stdin { } } output { stdout {} }'
```

控制台输入字符，随后就有日志输出 控制台输入字符，随后就有日志输出 

![](img/10.png)

stdin，表示输入流，指从键盘输入
stdout，表示输出流，指从显示器输出
命令行参数:
-e 执行
--config 或 -f 配置文件，后跟参数类型可以是一个字符串的配置或全路径文件名或全路径
路径(如：/etc/logstash.d/，logstash会自动读取/etc/logstash.d/目录下所有*.conf 的文
本文件，然后在自己内存里拼接成一个完整的大配置文件再去执行) 

### MySQL数据导入Elasticsearch 

（1）在logstash-5.6.8安装目录下创建文件夹mysqletc （名称随意）
（2）文件夹下创建mysql.conf （名称随意） ，内容如下： 

```elasticsearch
input {
    jdbc {
    # mysql jdbc connection string to our backup databse 后面的test
    对应mysql中的test数据库
    jdbc_connection_string =>
    "jdbc:mysql://127.0.0.1:3306/tensquare_article?characterEncoding=UTF8"
    # the user we wish to excute our statement as
    jdbc_user => "root"
    jdbc_password => "123456"
    # the path to our downloaded jdbc driver
    jdbc_driver_library => "D:/logstash‐5.6.8/mysqletc/mysql‐
    connector‐java‐5.1.46.jar"
    # the name of the driver class for mysql
    jdbc_driver_class => "com.mysql.jdbc.Driver"
    jdbc_paging_enabled => "true"
    jdbc_page_size => "50000"
    #以下对应着要执行的sql的绝对路径。
    statement => "select id,title,content from tb_article"
    #定时字段 各字段含义（由左至右）分、时、天、月、年，全部为*默认含义为
    每分钟都更新
    schedule => "* * * * *"
    }
    }
    output {
    elasticsearch {
    #ESIP地址与端口
    hosts => "localhost:9200"
    #ES索引名称（自己定义的）
    index => "tensquare"
    #自增ID编号
    document_id => "%{id}"
    document_type => "article"
    } s
    tdout {
    #以JSON格式输出
    codec => json_lines
    }
}
```

（3）将mysql驱动包mysql-connector-java-5.1.46.jar拷贝至D:/logstash-5.6.8/mysqletc/ 下 。D:/logstash-5.6.8是你的安装目录 

（4）命令行下执行 

```elasticsearch
logstash ‐f ../mysqletc/mysql.conf
```

观察控制台输出，每间隔1分钟就执行一次sql查询 

![](img/11.png)

再次刷新elasticsearch-head的数据显示，看是否也更新了数据。 





## 参考博客

[[深入浅出 spring-data-elasticsearch 之 ElasticSearch 架构初探](https://www.bysocket.com/archives/1889/%e6%b7%b1%e5%85%a5%e6%b5%85%e5%87%ba-spring-data-elasticsearch-%e4%b9%8b-elasticsearch-%e6%9e%b6%e6%9e%84%e5%88%9d%e6%8e%a2%ef%bc%88%e4%b8%80%ef%bc%89)](https://www.bysocket.com/archives/1889/%e6%b7%b1%e5%85%a5%e6%b5%85%e5%87%ba-spring-data-elasticsearch-%e4%b9%8b-elasticsearch-%e6%9e%b6%e6%9e%84%e5%88%9d%e6%8e%a2%ef%bc%88%e4%b8%80%ef%bc%89)



## Solr 和Elasticsearch性能区分 

[](https://www.cnblogs.com/chowmin/articles/4629220.html)

