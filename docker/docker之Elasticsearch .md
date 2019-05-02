# docker之Elasticsearch 

## （1）下载镜像

```docker
docker pull elasticsearch:5.6.8
```

## （2）创建容器 

```docker
docker run ‐di ‐‐name=elasticsearch ‐p 9200:9200 ‐p 9300:9300 elasticsearch:5.6.8
```

## （3）浏览器输入地址 

```docker
http://127.0.0.1:9200/ 即可看到如下信息


{
"name" : "WmBn0H‐",
"cluster_name" : "elasticsearch",
"cluster_uuid" : "2g‐VVbm9Rty7J4sksZNJEg",
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

### 这是因为elasticsearch从5版本以后默认不开启远程连接，需要修改配置文件 

```
NoNodeAvailableException[None of the configured nodes are available:
[{#transport#‐1}{exvgJLR‐RlCNMJy‐hzKtnA}{192.168.184.135}
{192.168.184.135:9300}]
]
at
org.elasticsearch.client.transport.TransportClientNodesService.ensureNodes
AreAvailable(TransportClientNodesService.java:347)
at
org.elasticsearch.client.transport.TransportClientNodesService.execute(Tra
nsportClientNodesService.java:245)
at
org.elasticsearch.client.transport.TransportProxyClient.execute(TransportP
roxyClient.java:59)
```

## （4）我们进入容器

```elasticsearch
docker exec ‐it elasticsearch /bin/bash
```

 此时，我们看到elasticsearch所在的目录为/usr/share/elasticsearch ,进入config看到了配置文件
elasticsearch.yml 

我们通过vi命令编辑此文件，尴尬的是容器并没有vi命令 ，咋办？我们需要以文件挂载的
方式创建容器才行，这样我们就可以通过修改宿主机中的某个文件来实现对容器内配置
文件的修改 

## （5）拷贝配置文件到宿主机 

首先退出容器，然后执行命令： 

```docker
docker cp
elasticsearch:/usr/share/elasticsearch/config/elasticsearch.yml
/usr/share/elasticsearch.yml
```

## （6）停止和删除原来创建的容器 

```dokcer
docker stop elasticsearch
docker rm elasticsearch
```

## （7）重新执行创建容器命令 

```docker
docker run ‐di ‐‐name=elasticsearch ‐p 9200:9200 ‐p 9300:9300 ‐v
/usr/share/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch
.yml elasticsearch:5.6.8
```

## （8）修改/usr/share/elasticsearch.yml 

将 transport.host: 0.0.0.0 前的#去掉后保存文件退出。其作用是允许任何ip地址访问elasticsearch .开发测试阶段可以这么做，生产环境下指定具体的IP 

## （9）重启启动 

```docker
docker restart 	elasticsearch
```

重启后发现重启启动失败了，这时什么原因呢？这与我们刚才修改的配置有关，因为
elasticsearch在启动的时候会进行一些检查，比如最多打开的文件的个数以及虚拟内存
区域数量等等，如果你放开了此配置，意味着需要打开更多的文件以及虚拟内存，所以
我们还需要系统调优。 

## （10）系统调优 

我们一共需要修改两处
修改/etc/security/limits.conf ，追加内容 

```docker
* soft nofile 65536
* hard nofile 65536
```

nofile是单个进程允许打开的最大文件个数 soft nofile 是软限制 hard nofile是硬限制 

修改/etc/sysctl.conf，追加内容 

```doker
vm.max_map_count=655360
```

限制一个进程可以拥有的VMA(虚拟内存区域)的数量
执行下面命令 修改内核参数马上生效 

```docker
sysctl ‐p
```

## （11）重新启动虚拟机，再次启动容器，发现已经可以启动并远程访问 

## IK分词器安装 

在宿主机中将ik文件夹拷贝到容器内 /usr/share/elasticsearch/plugins 目录下 

```docker
docker cp ik elasticsearch:/usr/share/elasticsearch/plugins/
```

重新启动，即可加载IK分词器 

## HEAD插件安装 

修改/usr/share/elasticsearch.yml ,添加允许跨域配置 

```docker
http.cors.enabled: true
http.cors.allow‐origin: "*"
```

重新启动elasticseach容器 

### 下载head镜像 

```docker
docker pull mobz/elasticsearch‐head:5
```

### 创建head容器 

```docker
docker run ‐di ‐‐name=myhead ‐p 9100:9100 docker pull mobz/elasticsearch‐
head:5
```

