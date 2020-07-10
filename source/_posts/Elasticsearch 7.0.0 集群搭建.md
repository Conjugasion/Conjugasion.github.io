---
title: Elasticsearch 7.0.0 集群搭建
tag: [elasticsearch]
category: elasticsearch
excerpt: 单机集群搭建踩坑
cover: /image24.jpg
---
主节点一定要设置transport.tcp.port: 9300，

从节点 discovery.seed_hotst : [ "127.0.0.1:9300"]

只有这样主从才能通信

master配置
```
# 集群名称
cluster.name: "es_cluster"
 
# 节点名称,这儿我直接取名为 master
node.name: master
# 是否可以成为master节点
node.master: true
# 是否允许该节点存储数据,默认开启
node.data: true
 
# 网络绑定,这里我绑定 0.0.0.0,支持外网访问
network.host: 0.0.0.0
 
# 设置对外服务的http端口，默认为9200
http.port: 9200
 
# 支持跨域访问
http.cors.enabled: true
http.cors.allow-origin: "*"
 
# 设置节点间交互的tcp端口,默认是9300
transport.tcp.port: 9300
 
# 手动指定可以成为 mater 的所有节点的 name 或者 ip，这些配置将会在第一次选举中进行计算
cluster.initial_master_nodes: ["127.0.0.1"]
```
slave配置：
```
# 集群名称，处于同一个集群所有节点，该名称必须相同
cluster.name: "es_cluster"
 
# 节点名称,这儿我直接取名为 slave1
node.name: slave1
# 是否可以成为master节点
node.master: false
# 是否允许该节点存储数据,默认开启
node.data: true
 
# 网络绑定,这里我绑定 0.0.0.0,支持外网访问
network.host: 0.0.0.0
 
# 设置对外服务的http端口，默认为9200，这里我们修改为 9201，不然会有端口冲突
http.port: 9201
 
# 支持跨域访问
http.cors.enabled: true
http.cors.allow-origin: "*"
 
# 集群发现
discovery.seed_hosts: ["127.0.0.1:9300"]
```