# docker之mysql

```docker
docker run -d -p 5506:3306 --privileged=true -v /f/docker/my.cnf:/etc/mysql/my.cnf -v /f/docker/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql02 mysql:5.5
```

