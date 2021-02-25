---
layout:     post
title:      "mongodb docker 集群搭建"
subtitle:    "Replica Set 架构 mongodb docker 集群搭建"
date:       2020-05-15 09:00:00
author:     Shunyu
header-img: img/post-bg-2015.jpg
header-mask: 0.1
catalog: true
tags:
    - docker
    - mongodb
---



Replica Set 架构 mongodb docker 集群搭建。

结构：Replica Set 架构（一主一从一仲裁）

优点：具备故障转移能力、仲裁节点起到选举作用节省部分资源



## 集群搭建配置

0、若测试时需要删除当前集群，重新建集群

```bash
cd /home/ls/mongodb_cluster
docker-compose -f mongodb_cluster.yml down
rm -rf /home/ls/mongodb_cluster/mongodb.key
rm -rf /home/ls/mongodb_cluster/data 
```



1、生成 keyFile

```bash
mkdir /home/ls/mongodb_cluster
cd /home/ls/mongodb_cluster
openssl rand -base64 756 > mongodb.key
chmod 400 mongodb.key
```



2、编写配置文件

```bash
cd /home/ls/mongodb_cluster
vim mongodb_cluster.yml
```

内容如下：

```yaml
version: '3.6'
services:
  master:
    image: mongo:3.6.4
    container_name: qs_master
    volumes:
      - /home/ls/mongodb_cluster/data/replset/master:/data/db
      - /home/ls/mongodb_cluster/mongodb.key:/data/mongodb.key
    ports:
      - 8027:27017
    networks:
      - mongoNet
    environment:
      MONGO_INITDB_ROOT_USERNAME: ***
      MONGO_INITDB_ROOT_PASSWORD: ***
    command: mongod --dbpath /data/db --replSet qs_mongo --oplogSize 128 --keyFile /data/mongodb.key
    entrypoint:
      - bash
      - -c
      - |
        chmod 400 /data/mongodb.key
        chown 999:999 /data/mongodb.key
        exec docker-entrypoint.sh $$@

  slave:
    image: mongo:3.6.4
    container_name: qs_slave
    volumes:
      - /home/ls/mongodb_cluster/data/replset/slaver:/data/db
      - /home/ls/mongodb_cluster/mongodb.key:/data/mongodb.key
    ports:
      - 8028:27017
    networks:
      - mongoNet
    environment:
      MONGO_INITDB_ROOT_USERNAME: ***
      MONGO_INITDB_ROOT_PASSWORD: ***
    command: mongod --dbpath /data/db --replSet qs_mongo --oplogSize 128 --keyFile /data/mongodb.key
    entrypoint:
      - bash
      - -c
      - |
        chmod 400 /data/mongodb.key
        chown 999:999 /data/mongodb.key
        exec docker-entrypoint.sh $$@

  arbiter:
    image: mongo:3.6.4
    container_name: qs_arbiter
    volumes:
      - /home/ls/mongodb_cluster/data/replset/arbiter:/data/db
      - /home/ls/mongodb_cluster/mongodb.key:/data/mongodb.key
    ports:
      - 8029:27017
    networks:
      - mongoNet
    environment:
      MONGO_INITDB_ROOT_USERNAME: ***
      MONGO_INITDB_ROOT_PASSWORD: ***
    command: mongod --dbpath /data/db --replSet qs_mongo --smallfiles --oplogSize 128 --keyFile /data/mongodb.key
    entrypoint:
      - bash
      - -c
      - |
        chmod 400 /data/mongodb.key
        chown 999:999 /data/mongodb.key
        exec docker-entrypoint.sh $$@

networks:
  mongoNet:
    driver: bridge
```



3、根据配置文件创建容器

注：这里使用 `docker-compose` 而不是 `docker`，`docker-compose` 是 docker 中用于集群管理的工具。

```bash
docker-compose -f mongodb_cluster.yml up -d
```



4、可查看刚刚创建的容器

通过 docker 查看

```bash
docker ps -a
# 注意到这里显示的容器名为 qs_master、qs_slave、qs_arbiter（跟配置文件的 container_name 一致）
```

通过 docker-compose 查看

```bash
docker-compose -f mongodb_cluster.yml ps -a
# 注意这里要指定配置文件是什么，该命令只会显示该配置文件下的集群容器信息
# 注意到这里显示的容器名为 master、slave、arbiter（看配置文件每个容器的头部名一致）
```



5、在 master 节点上配置主从仲裁信息

```bash
docker-compose -f mongodb_cluster.yml exec master mongo admin -u *** -p ***
```

```bash
# 在 master 节点上配置从和仲裁节点的信息
# ！！！有时候会报错时间戳哈希错误，请记得再输一次！！！
rs.initiate()
rs.add('slave:27017')
rs.add('arbiter:27017',true)

# 查看配置与副本级状态
rs.conf()
rs.status()

# 退出
exit
```



至此搭建成功



## 集群测试

### 测试同步功能

1、在 master 节点上插入信息

```bash
docker-compose  -f mongodb_cluster.yml exec master mongo admin -u *** -p ***
```

```bash
use test
db.test.insert({msg: 'this is from primary', ts: new Date()})
exit
```



2、在 slave 中检测信息是否同步

```bash
docker-compose -f mongodb_cluster.yml exec slave mongo admin -u *** -p ***
```

```bash
# 因为默认从节点是不可读的，所以先需要开启读权限
rs.slaveOk()

use test
db.test.find()
exit
```



3、在 arbiter 中检测信息是否同步

```bash
docker-compose -f mongodb_cluster.yml exec arbiter mongo admin -u *** -p ***
```

（这里会显示读取失败，因为仲裁节点不是用来读的）

```bash
rs.slaveOk()
use test
db.test.find()
exit
```



### 故障转移测试

1、查看节点当前的性质

```bash
# master 节点是 PRIMARY
docker-compose  -f mongodb_cluster.yml exec master mongo admin -u *** -p ***
# 显示 newset:PRIMARY>

# slave 节点是 SECONDARY
docker-compose -f mongodb_cluster.yml exec slave mongo admin -u *** -p ***
# 显示 newset:SECONDARY>

# arbiter 节点是 ARBITER
docker-compose -f mongodb_cluster.yml exec arbiter mongo admin -u *** -p ***
# 显示 newset:ARBITER>
```



2、假设 master 节点掉线了

```bash
docker-compose -f mongodb_cluster.yml stop master
```



3、查看节点当前的性质，发现 slave 节点变为 PRIMARY，故障转移成功

```bash
# slave 节点是 PRIMARY
docker-compose -f mongodb_cluster.yml exec slave mongo admin -u *** -p ***
# 显示 newset:PRIMARY>

# arbiter 节点是 ARBITER
docker-compose -f mongodb_cluster.yml exec arbiter mongo admin -u *** -p ***
# 显示 newset:ARBITER>
```



## 参考资料及致谢

[基于 Docker 的 MongoDB 主从集群](https://zhuanlan.zhihu.com/p/42836964)

[docker使用 docker-compose 部署MongoDB4.0 部署replica set(副本集)集群](https://blog.csdn.net/biao0309/article/details/87641272)

[容器化 MongoDB 集群](https://zhuanlan.zhihu.com/p/60183273)

[手把手教你用Docker部署一个MongoDB集群](http://dockone.io/article/181)

[Mongodb集群搭建的三种方式](https://blog.csdn.net/luonanqin/article/details/8497860)

