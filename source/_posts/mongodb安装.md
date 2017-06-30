---
title: mongodb安装
date: 2017-06-30 17:29:12
categories: javascript
tags: mongodb
---

安装mongodb的各种方法

<!-- more -->

## 安装参考文档
[官方文档有各个系统的安装教程，入口点击这里](https://docs.mongodb.com/manual/administration/install-community/)

- [Install on Linux](https://docs.mongodb.com/manual/administration/install-on-linux/)
- [Install on OS X](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)
- [Install on Windows](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

[菜鸟教程Mac OSX 平台安装](http://www.runoob.com/mongodb/mongodb-osx-install.html)

本人使用mac，结合官网采用了 brew 安装

## mac brew 安装

打开终端窗口

```
brew update

brew install mongodb
# 安装时间有点长，耐心等待
```

所有用brew安装的程序，安装路径都在 <b style="color: green;">/usr/local/Cellar</b> 下面