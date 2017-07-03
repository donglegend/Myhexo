---
title: mongodb Shell 启动
date: 2017-06-30 17:55:58
categories: javascript
tags: mongodb
---
开始运行mongodb
<!-- more -->

## 准备
上篇说过，通过brew安装的程序目录在 *<b style="color: green;">/usr/local/Cellar</b>*下面

下面，我们先看一下 mongodb的可执行程序命令

```
cd /usr/local/Cellar/mongodb/3.4.4/bin
# 注意我安装的是 3.4.4 版本的，所以各位请注意 正确的路径

ls -a
.		mongo		mongoexport	mongooplog	mongos
..		mongod		mongofiles	mongoperf	mongostat
bsondump	mongodump	mongoimport	mongorestore	mongotop
```

其中，<b style="color: red;">mongod 和 mongo</b>会是 我们接下来主要使用的命令
* mongod 启动mongodb
* mongo  连接mongodb数据库

## 创建项目目录
官网和其他参考网站 都是直接用 命令启动，参数配置都是默认的，不太适用

```
# 路径切换(cd)到一个你喜欢的目录
mkdir mongodbtest && cd mongodbtest

mkdir bin
mkdir conf
mkdir data
mkdir log
```
在 conf 目录下新建 文件mongod.conf
```
cd conf && touch mongod.conf
# 输入以下配置内容
port = 12345
dbpath = data
logpath = log/mongod.log
fork = true
```
然后把 mongodb安装目录下的bin下面的 mongo 和 mongod 复制到当前目录的 bin目录下

```
cp /usr/local/Cellar/mongodb/3.4.4/bin/mongod ./bin/
cp /usr/local/Cellar/mongodb/3.4.4/bin/mongo ./bin/
```

## 启动 mongodb

然后用官网文档语法启动 mongodb
```
./bin/mongod -f conf/mongod.conf
/*
about to fork child process, waiting until server is ready for connections.
forked process: 19250
child process started successfully, parent exiting
*/
```
看到上面 消息证明，已经成功启动了了 mongodb，可以看到data 和 log目录下已经有数据和日志了

## 连接mongodb

```
# 连接本地，端口12345在 conf 里配置
./bin/mongo 127.0.0.1:12345
```
会有连接成功提示，不过应该会看到警告，这是因为默认没有 用户名和密码登录