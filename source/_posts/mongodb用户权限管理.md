---
title: mongodb用户权限管理
date: 2017-07-01 12:32:26
categories: javascript
tags: mongodb
---

数据库 分配用户权限

<!-- more -->

接着上次实践的部分，启动mongodb并连接
```
./bin/mongod -f conf/mongod.conf
./bin/mongo 127.0.0.1:12345
```
查看默认的数据库情况
```
> show dbs
admin  0.000GB
local  0.000GB

> use admin
switched to db admin
> show tables
system.version
```
可以看到，目前数据库里除了一些基本信息，什么都没有

在创建设置用户权限之前，先了解一下文档知识

## [创建用户](https://docs.mongodb.com/manual/tutorial/create-users/)
```
# demo
db.createUser(
  {
    user: "reportsUser",
    pwd: "12345678",
    roles: [
       { role: "read", db: "reporting" },
       { role: "read", db: "products" },
       { role: "read", db: "sales" },
       { role: "readWrite", db: "accounts" }
    ]
  }
)
```
## [数据库内建角色](https://docs.mongodb.com/manual/reference/built-in-roles/#built-in-roles)
- 数据库用户角色
	* read (读取指定数据库)
	* readWrite (读写指定数据库)
- 数据库管理角色
	* dbAdmin (用户在指定数据库中执行管理函数)
	* dbOwner (管理数据库行为，readWrite, dbAdmin and userAdmin roles.)
	* userAdmin (用户向system.users集合写入，可以找指定数据库里创建、删除和管理用户)
- 集群管理角色
	* clusterAdmin
	* clusterManager
	* clusterMonitor
	* hostManager
- 备份恢复角色
	* backup
	* restore
- 所有数据库角色
	* readAnyDatabase
	* readWriteAnyDatabase
	* userAdminAnyDatabase
	* dbAdminAnyDatabase
- 超级用户角色
	* root  
- 内部角色
	* __system
