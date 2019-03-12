# MongoDB非关系型数据库

## 一、Docker下MongoDB

### 1.获取 mongo 镜像

```xml
docker pull mongo
```

### 2. 查看镜像

```xml
docker images 
```

### 3.运行 mongodb 服务

```xml
docker run -d -p 27017:27017 --name mongodb registry.docker-cn.com/library/mongo:latest
```

### 4.查看启动的镜像

```xml
docker ps
```

### 5.进入容器

```xml
docker exec -it 容器id /bin/bash
```

### 6.进入mongodb

```
mongo
```

### 7.创建管理账户，然后退出

```xml
use admin
db.createUser(
{
user: "admin",
pwd: "password",
roles: [ { role: "root", db: "admin" } ]
}
);

exit; （退出mongo）
```

### 8. 以刚建立的用户登录数据库 创建test用户

```xml
mongo --port 27017 -u admin -p password --authenticationDatabase admin
```

```xml
use test
db.createUser(
{
user: "tester",
pwd: "password",
roles: [
{ role: "readWrite", db: "test" }
]
}
);

exit;
```

### 9.以刚创建的test用户登录

```xml
mongo -u tester -p --authenticationDatabase test 
```

### 10.其它操作

```xml
show dbs （显示数据库）

use  dbname (切换到数据库)

show collections （显示表）

db.find.表名 （查看表数据）
```









