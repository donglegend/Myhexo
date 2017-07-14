---
title: mongodb数据库命令
date: 2017-06-30 19:22:08
categories: javascript
tags: mongodb
---
常用数据库命令汇总
<!-- more -->

[mongo Shell Quick Reference](https://docs.mongodb.com/manual/reference/mongo-shell/)
[Database Commands Api](https://docs.mongodb.com/manual/reference/command/)

下面简单列一下Shell常用的基本命令

## 启动连接Mongodb
```
#带配置信息启动
mongod -f xxx.conf 
#连接
mong 127.0.0.1:12345 
```

## 查看
```
#查看所有用户 
show users 
#查看所有数据库 
show dbs
#查看所有的collection 
show collections
```



## 创建切换数据库
use  databaseName(存在即切换，不存在即创建)

```
use admin
```

## 插入数据
db.[collectionName].insert({...}) (集合不存在会自动创建)
```
db.books.insert({book: '笑傲江湖'})
```

## 查看数据
db.[collection].find()
```
# 查看全部数据
db.books.find() 

# 可传入参数按条件查找等
```

## 删除数据
db.[collection].remove(query)
```
db.books.remove({book: '笑傲江湖'})
```

这里仅仅列几个学习入门使用的，详细请查看官方文档
