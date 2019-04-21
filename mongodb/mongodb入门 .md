# mongodb入门 

## 基础概念 

**在mongodb中是通过数据库、集合、文档的方式来管理数据，下边是mongodb与关系数据库的一些概念对比：** 

![](img/01.png)



![](img/02.png)

1、一个mongodb实例可以创建多个数据库
2、一个数据库可以创建多个集合
3、一个集合可以包括多个文档。 



## 连接mongodb 

mongodb的使用方式是客户服务器模式，即使用一个客户端连接mongodb数据库（服务端）。

### 命令格式 

```mongodb
mongodb://[username:password@]host1[:port1][,host2[:port2],...[,hostN[:portN]]][/[database][?
options]]
```

mongodb:// 固定前缀
username：账号，可不填
password：密码，可不填
host：主机名或ip地址，只有host主机名为必填项。
port：端口，可不填，默认27017
/database：连接某一个数据库
?options：连接参数，key/value对 

### 例子： 

```mongodb
mongodb://localhost 连接本地数据库27017端口
mongodb://root:itcast@localhost 使用用户名root密码为itcast连接本地数据库27017端口
mongodb://localhost,localhost:27018,localhost:27019，连接三台主从服务器，端口为27017、27018、27019
```

### 使用mongodb自带的javascript shell（mongo.exe）连接 

**windows版本的mongodb安装成功，在安装目录下的bin目录有mongo.exe客户端程序** 

**cmd状态执行mongo.exe：** 

**此时就可以输入命令来操作mongodb数据库了，javascript shell可以运行javascript程序。** 

### 使用studio3T客户端连接 

### 使用java程序连接 

详细参数：http://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/connect-to-mongodb/ 

**添加依赖：** 

```maven
<dependency>
  <groupId>org.mongodb</groupId>
  <artifactId>mongo‐java‐driver</artifactId>
  <version>3.4.3</version>
</dependency>
```

**测试程序：** 

```java
@Test
public void testConnection(){
//创建mongodb 客户端
MongoClient mongoClient = new MongoClient( "localhost" , 27017 );
//或者采用连接字符串
//MongoClientURI connectionString = new
MongoClientURI("mongodb://root:root@localhost:27017");
//MongoClient mongoClient = new MongoClient(connectionString);
//连接数据库
  MongoDatabase database = mongoClient.getDatabase("test");
// 连接collection
MongoCollection<Document> collection = database.getCollection("student");
//查询第一个文档
Document myDoc = collection.find().first();
//得到文件内容 json串
String json = myDoc.toJson();
System.out.println(json);
}
```

## 数据库 

### 查询数据库 

```mongodb
show dbs 查询全部数据库
db 显示当前数据库
```

### 创建数据库 

**命令格式：** 

```mongodb
use DATABASE_NAME
```

**例子：**
use test02
有test02数据库则切换到此数据库，没有则创建。
注意：
新创建的数据库不显示，需要至少包括一个集合。 

### 删除数据库（慎用！！！） 

**命令格式：** 

```mongodb
db.dropDatabase()
```

**例子：**
删除test02数据库
先切换数据库：use test02
再执行删除：db.dropDatabase() 

## 集合 

**集合相当于关系数据库中的表，一个数据库可以创建多个集合，一个集合是将相同类型的文档管理起来。** 

### 创建集合 

```mongodb
db.createCollection(name, options)
name: 新创建的集合名称
options: 创建参数
```

### 删除集合 

```mongodb
db.collection.drop()
例子：
db.student.drop() 删除student集合
```

## 文档 

### 插入文档 

**mongodb中文档的格式是json格式，下边就是一个文档，包括两个key：_id主键和name** 

```mongodb
{
"_id" : ObjectId("5b2cc4bfa6a44812707739b5"),
"name" : "程序员"
}
```

**插入命令** 

```mongodb
db.COLLECTION_NAME.insert(document)
```

**每个文档默认以_id作为主键，主键默认类型为ObjectId（对象类型），mongodb会自动生成主键值** 

**例子：** 

```mongodb
db.student.insert({"name":"黑马程序员","age":10})
```

**注意：同一个集合中的文档的key可以不相同！但是建议设置为相同的。** 

### 更新文档 

**命令格式：** 

```mongodb
db.collection.update(
<query>,
<update>,
<options>
) 
query:查询条件，相当于sql语句的where
update：更新文档内容
options：选项
```

### **替换文档** 

将符合条件 "name":"程序"的第一个文档替换为{"name":"程序员","age":10}。 

```mongodb
db.student.update({"name":"程序员"},{"name":"程序员","age":10})
```

**$set修改器** 

使用$set修改器指定要更新的key，key不存在则创建，存在则更新。
将符合条件 "name":"程序"的所有文档更新name和age的值。 

```mongodb
db.student.update({"name":"程序员"},{$set:{"name":"程序员","age":10}},{multi:true})
```

multi：false表示更新第一个匹配的文档，true表示更新所有匹配的文档。 

### **删除文档** 

**命令格式：** 

```mongodb
db.student.remove(<query>)
query：删除条件，相当于sql语句中的where
```

1、删除所有文档
db.student.remove({})
2、删除符合条件的文档
db.student.remove({"name":"程序员"}) 

### 查询文档 

**命令格式：** 

```mongodb
db.collection.find(query, projection)
query：查询条件，可不填
projection：投影查询key，可不填
```

**查询全部** 

```mongodb
db.student.find()
```

**查询符合条件的记录** 

查询name等为"程序员"的文档。 

```mongodb
db.student.find({"name":"程序员"})
```

**投影查询** 

只显示name和age两个key，_id主键不显示。 

```mongodb
db.student.find({"name":"黑马程序员"},{name:1,age:1,_id:0})
```

## 用户 

### 创建用户 

**语法格式：** 

```mongodb
mongo>db.createUser(
{ user: "<name>",
pwd: "<cleartext password>",
customData: { <any information> },
roles: [
{ role: "<role>", db: "<database>" } | "<role>",
...
]}
)
```

**例子** 

创建root用户，角色为root 

```mongodb
use admin
db.createUser(
{
user:"root",
pwd:"root",
roles:[{role:"root",db:"admin"}]
}
)
```

内置角色如下：

1. 数据库用户角色：read、readWrite;
2. 数据库管理角色：dbAdmin、dbOwner、userAdmin； 
3. 集群管理角色：clusterAdmin、clusterManager、clusterMonitor、hostManager；
4. 备份恢复角色：backup、restore；
5. 所有数据库角色：readAnyDatabase、readWriteAnyDatabase、userAdminAnyDatabase、
  dbAdminAnyDatabase
6. 超级用户角色：root 

### 查询用户 

```mongodb
查询当前库下的所有用户：

show users 
```

### 删除用户 

```mongodb
db.dropUser("用户名")
```

**例子：**
删除root1用户
db.dropUser("root1") 

### 修改用户 

**语法格式：** 

```mongodb
db.updateUser(
"<username>",
{
customData : { <any information> },
roles : [
{ role: "<role>", db: "<database>" } | "<role>",
...
],
pwd: "<cleartext password>"
},
writeConcern: { <write concern> })
```

**例子：**
修改root用户的角色为readWriteAnyDatabase 

```mongodb
use admin
db.updateUser("root",{roles:[{role:"readWriteAnyDatabase",db:"admin"}]})
```

### 修改密码 

**语法格式：** 

```mongodb
db.changeUserPassword("username","newPasswd")
```

**例子：**
修改root用户的密码为123 

```mongodb
use admin
db.changeUserPassword("root","123")
```

