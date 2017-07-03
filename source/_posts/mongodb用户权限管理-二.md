---
title: mongodb用户权限管理(二)
date: 2017-07-03 10:34:37
categories: javascript
tags: mongodb
---
数据库 分配用户权限
<!-- more -->

有了创建语法，和参数说明，接下来开始实践.

<b style="color: red;">注意，还有一点，账号是跟着数据库绑定的，在那个库里授权，就在那个库里验证(auth)
否则会失败</b>

## 创建 账号管理授权权限 的账号
```
> db.createUser(
... {
... user: 'admin',
... pwd: '123456',
... roles: [{role: 'userAdminAnyDatabase', db: 'admin'}]
... }
... )
Successfully added user: {
        "user" : "admin",
        "roles" : [
                {
                        "role" : "userAdminAnyDatabase",
                        "db" : "admin"
                }
        ]
}
```
然后退出数据库
```
> use admin
switched to db admin
> db.shutdownServer()
```
重新启动mongodb，记得在配置文件mongod.conf里加上 auth = true
```
./bin/mongod -f conf/mongod.conf
./bin/mongo 127.0.0.1:12345
> show dbs # 没有验证，没有权限，会出错
"errmsg" : "not authorized on admin to execute command
> use admin
> db.auth('admin', '123456')
1
# 返回 1 表示授权成功，0表示失败
> show dbs #已经授权，可以查看了
```

## 创建 读、读写权限的账户
```
> use book
switched to db book
> db.createUser(
... {
... user: 'zhangsan',
... pwd: 'zhangsan',
... roles: [{role: 'read', db: 'book'}]
... }
... )
Successfully added user: {
        "user" : "zhangsan",
        "roles" : [
                {
                        "role" : "read",
                        "db" : "book"
                }
        ]
}
> db.createUser(
... {
... user: 'lisi',
... pwd: 'lisi',
... roles: [{role: 'readWrite', db: 'book'}]
... }
... )
Successfully added user: {
        "user" : "lisi",
        "roles" : [
                {
                        "role" : "readWrite",
                        "db" : "book"
                }
        ]
}
> show users
{
        "_id" : "book.lisi",
        "user" : "lisi",
        "db" : "book",
        "roles" : [
                {
                        "role" : "readWrite",
                        "db" : "book"
                }
        ]
}
{
        "_id" : "book.zhangsan",
        "user" : "zhangsan",
        "db" : "book",
        "roles" : [
                {
                        "role" : "read",
                        "db" : "book"
                }
        ]
}
```

然后验证用户权限是否正确
```
> db.book.insert({book: '小人书'}) # 没验证，会出错
WriteResult({
        "writeError" : {
                "code" : 13,
                "errmsg" : "not authorized on book to execute command { insert: \"book\", docum
ents: [ { _id: ObjectId('5959b56edcc047dfe5c9b336'), book: \"小人书\" } ], ordered: true }"
        }
})
> db.auth('lisi', 'lisi')
1
> db.book.insert({book: '小人书'})
WriteResult({ "nInserted" : 1 })
> db.auth('zhangsan', 'zhangsan') # 用户切到 zhangsan
1
> db.book.find() # 可以查看
{ "_id" : ObjectId("5959b59fdcc047dfe5c9b337"), "book" : "小人书" }
> db.book.insert({book: '择天记'}) # 没有write权限，会失败
WriteResult({
        "writeError" : {
                "code" : 13,
                "errmsg" : "not authorized on book to execute command { insert: \"book\", docum
ents: [ { _id: ObjectId('5959b650dcc047dfe5c9b338'), book: \"择天记\" } ], ordered: true }"
        }
})
```

## 创建 root 超级权限账号
这个超级权限包括 授权 和 操控数据库集合数据，比较简单，只需要把role设置成 root
```
> use admin
switched to db admin
> db.auth('admin', '123456')
1
> db.createUser(
... {
... user: 'dongsheng',
... pwd: '123456',
... roles: [{role: 'root', db: 'admin'}]
... }
... )
Successfully added user: {
        "user" : "dongsheng",
        "roles" : [
                {
                        "role" : "root",
                        "db" : "admin"
                }
        ]
}
> db.auth('dongsheng', '123456')
1
> use book
switched to db book
> db.book.insert({book: '笑傲江湖'})
WriteResult({ "nInserted" : 1 })
> db.book.find()
{ "_id" : ObjectId("5959b59fdcc047dfe5c9b337"), "book" : "小人书" }
{ "_id" : ObjectId("5959b7abdcc047dfe5c9b339"), "book" : "笑傲江湖" }
```