---
title: yapi本地模拟部署
date: 2019-05-27 17:09:44
categories: yapi
tags: tool
---

yapi 本地部署流程

<!-- more -->

## [YApi 官网文档](https://yapi.ymfe.org/index.html)

YApi 有官网，有文档，也有部署教程，但是比较简单，没有服务端或者 mongodb 基础的小伙伴部署起来还是有一定困难的。这里本地部署一遍流程。

## 部署前置准备

明确环境要求：
nodejs（7.6+)
mongodb（2.6+）

## 安装

1. [安装 homebrew](https://brew.sh/index_zh-cn)

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

将以上命令粘贴至终端。
请耐心等待，这里需要一段时间.

2. [安装 mongodb]

```
brew install mongodb
```

通过 brew 安装的程序目录在 /usr/local/Cellar 下面，可以 cd 切换路径查看

3. 创建数据表 yapi
   mongodb 用法可自行查阅官网教程或者[查看这里](http://donglegend.com/tags/mongodb/)

```
# 建立yapi工作目录
mkdir yapimongodb && cd yapimongodb

mkdir bin
mkdir conf
mkdir data
mkdir log
# 创建数据库启动配置文件
cd conf && touch mongod.conf
# 输入以下配置内容
port = 27017
dbpath = data
logpath = log/mongod.log
fork = true
auth = true
# 拷贝mongo执行程序到bin目录
cp /usr/local/Cellar/mongodb/4.0.3_1/bin/mongod ./bin/
cp /usr/local/Cellar/mongodb/4.0.3_1/bin/mongo ./bin/
```

然后启动数据库并且创建 yapi 数据表

```
# 带配置启动数据库
./bin/mongod -f conf/mongod.conf
# 链接本地数据库
./bin/mongo 127.0.0.1:27017
# 进入admin数据库
use admin
# 增加一个mongodb超级管理员账户
db.createUser({
    user: "root",
    pwd: "123",
    roles:[
       {
           role:"userAdminAnyDatabase",
           db:"admin"
       }
    ]
})
# 创建数据库，名字是yapi(因为yapi那边配置数据库叫ypai)
use yapi
# 增加一个yapi管理员账户
db.createUser({
    user: "admin",
    pwd: "123456",
    roles:[
       {
           role:"readWrite",
           db:"yapi"
       }
    ]
})

# 退出mongo，带配置重新启动
./bin/mongod -f conf/mongod.conf
```

4. 开始部署 yapi

这里采用官网的方式二：命令行部署

```
mkdir yapi
cd yapi
git clone https://github.com/YMFE/yapi.git vendors //或者下载 zip 包解压到 vendors 目录（clone 整个仓库大概 140+ M，可以通过 `git clone --depth=1 https://github.com/YMFE/yapi.git vendors` 命令减少，大概 10+ M）
cp vendors/config_example.json ./config.json //复制完成后请修改相关配置
cd vendors
npm install --production --registry https://registry.npm.taobao.org
npm run install-server //安装程序会初始化数据库索引和管理员账号，管理员账号名可在 config.json 配置
node server/app.js //启动服务器后，请访问 127.0.0.1:{config.json配置的端口}，初次运行会有个编译的过程，请耐心等候
```

部署就是上面整个过程，比价简单，这里着重说一下 修改 config.json 配置文件

```
# 最简配置，注意下面配置中的 登录账号密码，并不是数据库中初始化的那个user
{
  "port": "3000", // 服务启动端口号
  "adminAccount": "admin@admin.com", // 登录系统账号,默认密码是ymfe.org
  "closeRegister": true, // 关闭注册功能
  "db": {
    "servername": "127.0.0.1",
    "DATABASE": "yapi", // 第3步中建立的 yapi数据库
    "port": 27017, // mongodb端口号
    "user": "admin",
    "pass": "123456",
    "authSource": ""
  },
  "mail": {
    "enable": false,
    "host": "smtp.163.com",
    "port": 465,
    "from": "***@163.com",
    "auth": {
      "user": "***@163.com",
      "pass": "*****"
    }
  }
}

```
